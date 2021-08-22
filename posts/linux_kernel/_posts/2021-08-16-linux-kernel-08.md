---
layout: post
title: TCP Keepalive를 이용한 세션 유지 [미완]
---

{% assign imgurl=site.data.common.path.image|append: '/'|append: page.categories[1] %}



# TCP Keepalive 란

TCP 커넥션을 맺을 때  3 way handshake는 반드시 필요한 과정이다. 그런데 이 3 way handshake는 시간이 오래걸리는 꽤 무거운 작업이다. 그렇기 때문에 사람들은 지속적으로 이루어지는 통신에 대해 만들어 놓은 세션을 끊지 않고 계속 사용하는 방법을 고민했고 그 결과 TCP Keepalive가 탄생했다.

// TODO : 그림 첨부

TCP Keepalive로 맺어진 커넥션은 일정 시간마다 해당 세션이 살아있는지 작은 패킷을 보낸다. 이때 이 패킷은 클라이언트, 서버 양쪽에서 주고 받는게 아니라 연결을 유지하고 싶은 한 쪽에서만 보낼 수도 있다. 

클라이언트, 서버 둘 중 한 쪽이라도 Keepalive를 사용하면 세션은 유지된다. 

현재 소켓이 keepalive를 사용하는지 확인하려면 `netstat -napo` 를 통해 확인할 수 있다. 

마지막 필드 Timer 에서 keepalive 여부 및 현재 소켓의 timer를 볼 수 있다.

소켓의 Timer에는 TIME_WAIT 타이머, FIN_WAIT 타이머, Keepalvie 타이머 등이 있다.

Keepalive 타이머의 시간이 모두 소모되면 세션 연결을 유지하는 작은 패킷 하나를 보낸다. 

TCP Keepalive를 사용하기 위해선 소켓을 생성할때 setsockopt() 함수에서 SO_KEEPALIVE 옵션을 선택하면 된다. 하지만, 보통 이렇게 C 언어의 소켓 생성 함수를 건드려 애플리케이션을 개발하는 경우는 없다. 대부분의 프레임워크, http 라이브러리에는 따로 TCP Keepalive 설정 옵션이 있다.



# TCP Keepalive의 파라미터들

TCP Keepalive에는 아래 세가지 주요 커널 파라미터가 있다.

### net.ipv4.tcp_keepalive_time

TCP keepalive 소켓의 유지시간. 가장 중요한 파라미터다. 

keepalive 타이머는 이 시간을 기준으로 동작하고, 이 시간이 지나면 keepalive 확인 패킷을 보낸다. 

### net.ipv4.tcp_keepalive_probes

keepalive 패킷 보낼때 최대 전송 횟수를 정한다. keepalive 패킷에 한 번 응답하지 않았다고 바로 연결을 끊는게 아니라 여기 설정된 횟수 만큼 재시도 하고 그래도 응답이 오지 않는다면 연결을 끊는다.

### net.ipv4.tcp_keepalive_intvl

keepalive 패킷을 재전송하는 주기를 의미한다. keepalive 패킷에 몇 초간 응답이 없을시 다시 전송할 지를 설정한다. 





// 그림 첨부

세션 생성 후 `tcp_keepalive_time` 만큼 시간 후에 keepalive 확인 패킷을 보내고, 응답이 오지 않으면 `tcp_keepalive_intvl` 간격으로 `tcp_keepalive_probes` 번 패킷을 더 보낸다.



# TCP Keepalive와 좀비 커넥션

Keepalive가 유용한 이유중 하나는 좀비 커넥션을 방지할 수 있기 때문이다. 

좀비 커넥션이란 클라이언트 및 서버의 비정상적인 종료시 한쪽은 커넥션이 끊겼는데, 한 쪽은 ESTABLISHED 상태로 남아있는 경우이다. 

주로 한쪽이 종료되고 커넥션 종료를 위한 4 way handshake가 제대로 이루어지지 않는 경우에 발생한다. 

Keepalive가 없다면, ESTABLISHED 쪽에서 패킷을 보내보고 응답이 없어야 해당 소켓을 정리하게 된다.

하지만, Keepalive를 사용한다면 keepalive 확인 패킷을 일정 주기로 보내게 되고, keepalive 확인 패킷에 대한 응답이 없는 것으로 소켓을 종료하게 된다. 





# TCP Keepalive와 HTTP Keepalive

TCP Keepalvie와 HTTP Keepalive를 동일시 하는 경우도 있는데, 둘은 다르다. 

HTTP Keepalive는 HTTP/1.1에서 지원하는 keepalive를 위한 설정이다. 

TCP Keepalive는 두 종간 간의 연결을 유지하는 것이 목적이고, HTTP Keepalive는 최대한 연결을 유지하는 것이 목적이다.

예를들어 두 값이 모두 60초일때, TCP Keepalive는 60초 간격으로 연결이 유지되고 있는지 확인하고, 응답을 받으면 계속 연결을 유지한다. HTTP Keepalive는 60초 동안 연결을 유지하고, 60초 후에도 연결이 없다면 연결을 끊는다.

**두 값이 모두 설정 되어 있을땐 두 값의 크기에 상관없이 HTTP Keepalive 값에 맞게 동작하게 된다.**



# 저자 사례 - MQ 서버와 로드 밸런서

