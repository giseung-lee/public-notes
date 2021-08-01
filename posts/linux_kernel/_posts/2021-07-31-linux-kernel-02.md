---
layout: post
title: top을 통해 살펴보는 프로세스 정보들
---

{% assign imgurl=site.data.common.path.image|append: '/'|append: page.categories[1] %}



# 시스템의 상태 살피기

```
top
```

top 명령어는 시스템의 상태를 전반적으로 파악하는데 도움을 준다. 

// 이미지 첨부

아무 옵션 없이 `top` 을 실행하면 시스템의 기본 interval 간격으로 갱신하면서 뭔가 많이 보여준다.

보여주는 화면은 운영체제 마다 다르다. CentOS 7을 기준으로 보자.

// 이미지 위쪽 박스

첫줄엔 서버의 구동 시간, 커널 좁속중인 유저, load average가 표시된다.
두 번째 줄엔 프로세스와 과련한 것들이 표시된다. 전체 몇 개의 프로세스가 있고, 실제 동작하는 프로세스는 몇 개 인지 등..
세번째 줄엔 CPU와 관련한 값들이 보인다.
네번째, 다섯번째 줄은 메모리와 관련한 값이다.

// 이미지 아래쪽 박스

하단엔 프로세스에 대한 상세 정보들이 표시되어 있다. pid, 실행 유저 등.. 자세한 사항은 뒤에서 설명하겠지만 간단히만 짚어보자면, 

PR : 프로세스의 실행 우선 순위
NI : PR에 더해지는 값. PR + NI로 실제 우선 순위가 결정된다.
VIRT : 프로세스가 사용하는 virtual memory의 총량 - 높아도 큰 문제는 되지 않음. 
RES : 프로세스가 사용하는 물리 메모리의 양
SHR : 다른 프로세스와 공유하고 있는 shared memory의 양. (ex - glibc 라이브러리)
S : 프로세스의 상태. running, sleeping, stopped 등..



# VIRT와 RES 그리고 Memory Commit의 개념





