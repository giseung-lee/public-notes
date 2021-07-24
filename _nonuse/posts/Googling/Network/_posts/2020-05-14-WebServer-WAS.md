---
layout: post
title: Web Server와 WAS의 차이점
---

{% assign imgurl=site.imgbase|append: page.categories[-1] %}

- 본 포스팅은 [https://gmlwjd9405.github.io/2018/10/27/webserver-vs-was.html](https://gmlwjd9405.github.io/2018/10/27/webserver-vs-was.html)를 참고하였습니다.

## Static Resource vs Dynamic Resource

---

- Web Server와 WAS의 차이를 알려면 Static Resource와 Dynamic Resource를 알아야 합니다. 
- 둘의 차이는 말 그대로 어떤 요청에도 변하지 않는 정적인 자원이냐, 요청에 따라 변하는 동적인 자원이냐입니다.
- Static Resource
  - 예시 - 이미지, 비디오, 오디오, html 페이지, css파일, js 파일 등 
  - 어떤 클라이언트가 요청하든 같은 Resource를 받게 됩니다.
- Dynamic Resource
  - 예시 - jsp, asp php 페이지 등
  - 요청한 클라이언트에 따라 바뀌는 자원입니다. 
    - 예를들어, 네이버 카페에 들어가면 로그인한 유저에 따라 가입한 카페의 내역이 다를 것이고, 마이페이지에 로드되는 내용들이 다를 것입니다. 



## Web Server vs Web Application Server

---

- 웹 서버와 WAS의 차이를 정말 단순화 시켜 말한다면, 웹 서버는 Static Resource을 제공해주고, WAS는 Static, Dynamic Resource 모두를 제공해준다는 것입니다.

### Web Server

- 예시 - Apache, Nginx, IIS 등
- 웹 서버는 클라이언트와 HTTP 메세지를 주고 받는 역할을 합니다. 
- 메세지를 주고 받을 때 따로 추가적인 처리가 필요하지 않은 Static Resource를 웹 서버 차원에서 제공해 줄 수 있습니다.

### Web Application Server

- 예시 - Tomcat, JBoss, JEUS 등
- WAS = 웹 서버 + 웹 컨테이너
- 웹 컨테이너
  - 웹 컨테이너란 jsp, asp, php 등 동적인 페이지를 만들어주는 어플리케이션을 실행시킬 수 있는 환경입니다.
  - WAS는 jsp, asp 등의 실행 환경을 제공한다고 볼 수 있습니다.



## Web Server는 왜 필요할까

---

- 앞서 WAS = Web Server + Web Container 라고 했습니다. 하지만 **실제론 WAS 앞에 Web Server를 따로 두기도 합니다.** WAS에 Web Server가 있는데 왜 Web Server를 따로 둘까요?
- Web Server의 필요성
  - Static Resource 처리
    - Static Resource 요청이 WAS 까지 가지 않고 Web Server 단에서 처리되게 할 수 있습니다. 
    - 이를 통해 WAS의 부하를 줄일 수 있습니다. WAS는 Dynamic Resource를 처리하는 것으로도 부담이 큽니다.
  - 보안 강화
    - HTTPS 프로토콜 같은 경우 SSL 암복호화 처리를 해야 하는데 이를 WAS가 하지 않고 Web Server 단에서 처리해 줄 수 있습니다.
    - 또한, 접근 허용 IP 관리를 앞의 Web Server 단에서 처리할 수 있습니다.
  - 로드밸런싱
    - 하나의 웹 서버 뒤에 여로 복제 WAS를 두어 로드밸런싱을 통해 각 WAS의 부담을 줄일 수 있습니다.
  - 무중단 운영
    - 여러 복제 WAS를 두고 새로운 WAS 배포시 차례차례 WAS를 배포해 무중단 운영을 이어갈 수도 있습니다.
    - 또한, 하나의 WAS가 죽어도 다른 WAS를 사용하도록 할 수 있습니다.
  - A/B 테스트
    - 두 가지 버전의 UI의 유저 반응을 테스트할 때 한쪽 클라이언트 집단에겐 A버전의 WAS를, 다른 클라이언트 집단에겐 B버전의 WAS를 연결해 주어 A/B 테스트를 진행할 수도 있습니다.

