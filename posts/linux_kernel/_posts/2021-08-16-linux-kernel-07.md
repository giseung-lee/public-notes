---
layout: post
title: TIME_WAIT 소켓이 서비스에 미치는 영향
---

{% assign imgurl=site.data.common.path.image|append: '/'|append: page.categories[1] %}

# TCP 통신 과정

![linux-kernel-07.png]({{ imgurl }}/linux-kernel-07.png)

## 3 way handshake : 커넥션 연결

|from|packet|to|description|
|--|--|--|
|Client|SYNC|Server|연결 요청|
|Server|SYNC+ACK|Client|연결 요청에 대한 응답|
|Client|ACK|Server|연결 요청 응답에 대한 응답|

## 4 way handshake : 연결 종료 (서버에서 먼저 끊을 경우 가정)

|from|packet|to|description|
|--|--|--|
|Server|FIN|Client|연결 종료 통보|
|Client|ACK|Server|연결 종료 통보에 대한 응답|
|Client|FIN|Server|연결 종료 통보|
|Server|FIN|Client|연결 종료 통보에 대한 응답|

# TIME_WAIT 소켓의 문제점

TCP 연결을 끊을때, 먼저 연결을 끊는쪽을 active closer 라고 하고, 그 반대를 passive closer 라고 부른다.

active closer 쪽에 TIME_WAIT 소켓이 생성된다. (상황에따라 클라이언트, 서버 양쪽 모두 TCP 커넥션을 끊을 수 있기 때문에 둘 다 active closer가 될 수 있다.)

`netstat -napo | grep -i time_wait ` 커맨드를 이용하면 TIME_WAIT 상태의 소켓을 확인할 수 있다.

소켓은 출발지 IP, Port, 목적지 IP, Port가 하나의 셋트를 이루며, 네 가지가 같으면 같은 소켓이다.

TIME_WAIT에 걸린 소캣은 종료될 때 까지 사용할 수 없다. 비정상적으로 TIME_WAIT 소켓이 많아지면 포트 고갈 문제가 생길 수 있다. -> 애플리케이션에 타임아웃이 발생.

`net.ipv4.ip_local_port_range` 커널 파라미터에서 로컬 포트의 범위를 지정할 수 있다.

또한, TCP 커낵션 생성 및 종료가 자주 발생하면 응답 속도 저하가 발생할 수도 있다. 

대부분의 어플리케이션에선 Connection Pool을 이용해 한 번 연결한 TCP 커넥션을 재사용 한다. 



# 클라이언트에서의 TIME_WAIT

TIME_WAIT 소켓은 먼저 TCP 연결을 끊는 쪽에 생긴다. 대게 서버가 먼저 끊는 경우가 많아 TIME_WAIT는 서버의 문제라고 착각하기도 하는데, 클라이언트에서도 TIME_WAIT 소켓은 생길 수 있다. 

클라이언트쪽에서 TIME_WAIT 소켓이 많이 생길 경우엔 서버보다 포트 고갈 문제가 더 크다. 목적지의 IP, Port는 서버로 고정되어 있기 때문에 클라리언트의 로컬 포트만 다 쓴다면 바로 포트 고갈로 이어지기 때문이다. 

(클라이언트의 포트는 소켓을 생성할 때 남는 포트중에서 임의로 배정 받는다. (클라이언트 애플리케이션이 listen 하는 포트와 무관함!))

테스트를 해보고 싶다면 `sysctl -w "net.ipv4.ip_local_port_range=32768 32769"` 와 같이 할당 가능한 port를 조절한 뒤 의도적으로 포트 고갈을 내볼 수 있다.





# net.ipv4.tcp_tw_reuse

`net.ipv4.tcp_tw_reuse` 파라미터를 1로 설정하면 TIME_WAIT 상태의 소켓을 재사용 할 수 있게 된다.



# ConnectionPool 방식 사용하기

TIME_WAIT으로 인한 로컬 포트 고갈의 근본적인 문제를 해결하기 위해선 Connection Pool 방식을 사용할 수 있다. 클라이언트와 서버 간에 소켓을 열어놓고 Pool로 관리하며 재사용하는 방식이다. (반대로 매번 소켓을 여는 방식은 Connection less 라고 부름)



# 서버 입장에서의 TIME_WAIT 소켓

서버 쪽에선 사실 로컬 포트 고갈이 잘 일어나지 않는다. 목적지의 포트는 listen 하고 있는 포트로 고정되기 때문이다. 



# net.ipv4.tcp_tw_recycle

그래도 서버에서도 TIME_WAIT 소켓은 발생할 수 있다. 서버에서 TIME_WAIT 소켓을 줄이기 위해선 `net.ipv4.tcp_tw_recycle` 파라미터를 설정할 수 있다. TIME_WAIT 상태의 소켓을 빠르게 회수하고 재사용할 수 있도록 해준다.

`sysctl -w "net.ipv4.tcp_tw_recycle=1"`  커맨드로 셋팅을 하면 TIME_WAIT 소켓을 줄일 수 있다. 해당 파리미터를 설정하면 아래와 같은 일이 일어난다.

1. 특정 소켓에서 가장 마지막에 들어온 timestamp 값 저장
2. TIME_WAIT 소켓 타이머를 RTO 기반의 값으로 변경

RTO는 보통 ms 단위이기 때문에 2번 과정에서 TIME_WAIT 소켓이 크게 줄어든다. 

하지만, 이 방법은 1번 과정 때문에 의도치 않은 문제가 생길 수 있다. 

두 클라이언트 C1, C2가 같은 통신사를 사용한다고 하면 두 클라이언트는 동일한 NAT를 사용할 수 있다. 서버 입장에서 C1, C2를 같은 클라이언트로 보는 것이다.

![linux-kernel-08.png]({{ imgurl }}/linux-kernel-08.png)

서버가 C1과의 통신을 끝내고 커낵션을 정리하는 도중에, C2에서의 연결 요청이 들어오는 상황을 가정해보자. C2가 보낸 연결 요청 패킷이 C1의 FIN 패킷보다 빠르다면 C1과의 커낵션 종료후 C2에서 오는 패킷을 버릴 가능성이 있다.(서버 입장에선 같은 클라이언트에서 오는 것이기 때문에. )



# keepalive 사용하기

keepalive를 사용하는 것도 TIME_WAIT 소켓을 줄이는 좋은 방법이다.

keepalive를 켜면 요청을 주고 받은 후에도 일정시간 세션을 유지한다. 설정한 시간 동안 계속 요청이 없을 경우에 keepalive 세선을 끊게 된다.



# TIME_WAIT 상태의 존재 이유

TIME_WAIT 소켓이 뭔가 문제를 일으키는 것 같이 계속 설명을 했지만, 사실 TCP 통신과정에서 TIME_WAIT 상태는 반드시 필요하다.

TIME_WAIT 소켓의 목적은 연결이 종료된 후에도 소켓을 바로 정리하지 않고 일정시간 연결의 흔적을 남겨두는 것이다.

TCP 패킷은 언제든지 유실될 수 있다. TIME_WAIT가 없이 바로 소켓이 정리되는 상황에서 연결 종료를 위한 4 way handshake시 패킷이 유실된다면 한쪽이 비정상적인 상태로 남아 있을 수 있다.

TCP 패킷의 유실은 TCP 통신을 사용한다면 항상 염두에 두어야 하는 정상적인 상황이기 때문에 TIME_WAIT 소켓이 필요하다.



# nginx upstream에서 발생하는 TIME_WAIT

WAS(ex - tomcat) 앞에 nginx 같은 웹 서버를 두는 경우는 많다. 이때 nginx와 WAS 사이에 keepalive가 켜져 있지 않다면 nginx와 WAS 사이에 불필요한 TIME_WAIT 소켓이 발생할 수 있다.

nginx와 WAS 구간은 웬만해선 keepalive를 켜고 사용하는 것이 좋다.
