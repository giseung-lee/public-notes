---
layout: post
title: TCP 재전송과 타임아웃
---

{% assign imgurl=site.data.common.path.image|append: '/'|append: page.categories[1] %}



# TCP 재전송과 RTO

TCP는 항상 패킷 유실에 대비해야 한다. 그렇기 때문에 보낸 패킷에 대한 응답 패킷을 받았을 때 통신이 제대로 이루어 졌다고 판단한다.

보낸 패킷에 대한 응답을 받지 못했을 경우 보낸 패킷이 유실된 것이라 판단해 다시 보내게 되는데 이 과정을 TCP 재전송이라고 한다.

TCP는 이렇게 보낸 패킷에 대한 응답을 항상 확인하기 때문에 흔히 '신뢰성 있는' 통신이라고 한다. 반대로, UDP는 이런 패킷 유실에 대해 신경쓰지 않는다. (대신, 그만큼 전송이 빠르다. 중간에 데이터가 조금 유실되어도 큰 상관없는 서비스 or 신뢰성 보다 속도가 더 중요한 서비스는 UDP를 사용한다.)

![linux-kernel-13.png]({{ imgurl }}/linux-kernel-13.png)

패킷을 주고 받을때 보낸쪽이 SYN, FIN 등을 보내고 받는쪽의 ACK 패킷을 기다린다. 

**ACK을 얼마나 기다려야 하는 지에 대한 값이 RTO(Retransmission Timeout)이다.**

보낸쪽이 RTO 안에 ACK을 받지 못하면 패킷 유실이라 판단해 TCP 재전송을 진행한다.

- initRTO : TCP handshake 과정에서 첫 SYN 패킷에 대한 RTO. 최초 연결 시 두 종단간 소요시간을 알 수 없기 때문에 별도의 RTO를 적용한다. 리눅스에 기본값은 1초로 설정됨.
- RTO : 외에 일반적으로 적용되는 RTO. RTT(Round Trip Time)을 기준으로 설정된다. 

```
[ec2-user@ip-172-31-36-100 ~]$ ss -i | grep rto
		:
		:
		tcp                        ESTAB                        0                             36                                                                          172.31.36.100:ssh                                                  223.38.80.203:61216
	 cubic wscale:6,7 rto:492 rtt:258.792/36.054 ato:40 mss:1360 pmtu:9001 rcvmss:1340 advmss:8949 cwnd:10 bytes_acked:53629 bytes_received:3669 segs_out:91 segs_in:105 data_segs_out:83 data_segs_in:30 send 420.4Kbps lastsnd:4 lastrcv:4 lastack:4 pacing_rate 840.8Kbps delivery_rate 363.7Kbps busy:7020ms unacked:1 rcv_rtt:298 rcv_space:26847 rcv_ssthresh:34887 minrtt:200.001
```

`ss -i` 를 사용하면 세션별 RTO값을 확인할 수 있다. ec-2 인스턴스의 기본 세션중 하나는 RTO가 492ms 로 설정되어 있다.





# 재전송을 결정하는 커널 파라미터

```
[ec2-user@ip-172-31-36-100 ~]$ sudo sysctl -a | grep retries
net.ipv4.tcp_syn_retries = 6
net.ipv4.tcp_synack_retries = 5
net.ipv4.tcp_orphan_retries = 0
net.ipv4.tcp_retries1 = 3
net.ipv4.tcp_retries2 = 15
```

`sysctl -a` 커맨드로 커널 파라미터들을 확인할 수 있고, 이중에 `retries` 를 뽑으면 tcp 재전송 관련 커널 파라미터들을 확인할 수 있다.

- net.ipv4.tcp_syn_retries : 연결 시도시 SYN 패킷에 대한 재전송 횟수 
- net.ipv4.tcp_synack_retries : 연결 시도시 SYN 패킷에 대한 응답인 SYN + ACK 패킷의 재전송 횟수
- net.ipv4.tcp_orphan_retries : orphan socket(FIN_WAIT1 상태의 소켓, 연결을 끊을 때 FIN 패킷을 보내고 소켓의 상태는 FIN_WAIT1이 되는데 이때부터 소켓은 프로세스에 바인딩 되지 않고 커널에 귀속됨) 상태의 소켓들의 재전송 횟수.
- net.ipv4.tcp_retries1: 네트워크가 잘못 됐는지 확인하도록 지시하는 임계 재전송 횟수
- net.ipv4.tcp_retries2 : 통신 불가라고 판단하는 임계 재전송 횟수



# 재전송 추적하기

TCP 재전송을 추적하는 가장 좋은 방법은 tcpdump를 뜨는 것이다. 

다른 방법으론 [tcpretrans 스크립트](https://github.com/brendangregg/perf-tools/blob/master/net/tcpretrans)를 사용하는 방법이 있다.(해당 깃헙에는 이외에도 다른 유용한 툴들이 많은 것 같다.)

```
[ec2-user@ip-172-31-36-100 ~]$ sudo ./tcpretrans
TIME     PID    LADDR:LPORT          -- RADDR:RPORT          STATE

```

tcpretrans 스크립트를 다운받고 실행 파일로 권한 변경한 다음 실행해보면 되는데.. 트래픽이 좀 있는 곳에서 실행해봐야 될 거 같다.

```
my $interval = 1;
	:
while (1) {
	sleep $interval;

	# buffer trace data
	open TPIPE, "trace" or edie "ERROR: opening trace_pipe.";
	my @trace = ();
	while (<TPIPE>) {
		next if /^#/;
		push @trace, $_;
	}
	:
```

기본 tcpretrans 스크립트는 1초에 한 번씩 재전송 여부를 수집한다. 하는데, RTO 값이 작으면(RTO의 최소값은 200ms) tcpretrancs 스크립트에 걸리지 않을 수도 있다. 사용한다면 조절해서 사용하자.



# RTO_MIN 값 변경하기

기본 RTO_MIN 값은 200ms 이다. 사내망의 경우 재전송의 최소값이 200ms이면 길다고 느낄 수 있다.

```
ip route change default via <GW> dev <DEVICE> rto_min 100ms
```

위 커맨드를 통해 RTO_MIN 값을 변경할 수 있다.

```
[ec2-user@ip-172-31-36-100 ~]$ ip route
default via 172.31.32.1 dev eth0
169.254.169.254 dev eth0
172.31.32.0/20 dev eth0 proto kernel scope link src 172.31.36.100
```

그 전에 `ip route` 명령어로 해당 서버에서 나갈때 어떤 디바이스의 어떤 게이트웨이로 나가는 지 알아야 한다. 

위 결과의 첫번째 줄 `default via 172.31.32.1 dev eth0` 는 기본적으로 외부 통신에서 모든 패킷이 eth0 디바이스의 172.31.32.1 게이트웨이를 통해 나간다는 의미이다.

```
ip route change default via 172.31.32.1 dev eth0 rto_min 100ms
```

위와 같이 설정할 수 있다.



# 애플리케이션 타임아웃

TCP 재전송이 일어나게 되면 애플리케이션에 영향이 가게 된다.

이때, 애플리케이션 쪽에서 발생할 수 있는 timeout은 크게 두 가지로 나뉜다.

|종류|발생경로|최소 권장 설정 값|
|--|--|--|
|Connection Timeout|TCP Handshake 과정에서 TCP 재전송이 일어날 경우 발생|3s 이상|
|Read Timeout|맺어진 세션에서 데이터를 주고 받을 때 발생|300ms 이상|

## Connection Timeout

Connection Timeout은 최초 TCP Handshake 과정시 TCP 재전송이 일어날 때 발생한다. 
SYN 패킷 유실 or SYN+ACK 패킷 유실시 발생 한다는 얘기이다.

timeout 값은 최소 한 번의 재전송은 커버 할 수 있도록 설정해야 한다.(패킷 유실은 불가피하기 때문에)

SYN, SYN+ACK 패킷의 재전송에는 거의 1초 이상이 필요하다. 
클라이언트쪽에서 한 번, 서버 쪽에서 한 번 총 2번의 재전송은 커버 가능하도록 3초 이상을 권장한다. 

## Read Timeout

Read Timeout은 이미 맺어진 세션에서 읽기 작업시 타임아웃이 발생하는 것이다. 

Read Timeout 역시 한 번의 재전송은 허용할 정도로 설정해야 한다. 최소 300ms 이상을 권장하는 바이다.
