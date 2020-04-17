---
layout: page
title: Http 완벽가이드 같이 읽기
---

{% assign imgurl=site.imgbase|append: page.categories[-1] %}
{% assign dirs = page.url | split: "/" %}
{% assign category = dirs[-1] %}

## 1. 근본 찾기 프로젝트

---

 &nbsp;2019년 27의 나이로 뒤늦게 개발의 시작한 저를 괴롭히는 강박은 근본적인 지식이 부족하다는 것이었습니다. Java스택을 이용한 웹 개발 프로젝트, MERN 스택을 사용한 웹 개발 프로젝트, C# Winform을 활용한 PC클라이언트 개발 등 응용 스킬은 전공자들 보다 잘 따라한다고 생각하는데 스스로가 생각해도 근본적인 지식이 부족하다고 생각했습니다. 

![http_the_definitive_guide.png]({{ imgurl }}/Http The Definitive Guide/http_the_definitive_guide.png){: width="50%" height="50%"}{: .center}

 &nbsp;Http 완벽가이드 스터디는 프로그래밍의 근본을 찾기위한 프로젝트 중 하나입니다.

## 2. 포스팅 구성

---

 &nbsp;포스팅이 처음이라 구성을 어떻게 할지 고민했습니다. 정말 단순히 요약하는 방식으로 가야하는지, 기존 내용을 토대로 어느정도 재구성 해도 되는지, 제 사견은 얼마나 붙여도 되는지, 사견을 붙이면 책 내용과 혼동을 주진 않을지 걱정됐습니다.

 &nbsp;포스팅의 첫번째 목적은 어디까지나 저의 학습입니다. 대학생활을 돌아보면, 강의를 듣거나 책을 읽고 그걸 자신만의 언어로 정리하는 것은 저에게 매우 유효했습니다. 물론, 제 언어로 풀어 내는 과정에서 약간의 오역이 있기도 했지만 그걸 감수할 수 있을 만큼 성공적인 학습 방법이었습니다.

 &nbsp;따라서, 본 포스팅은 책을 읽고 정리하면서 제가 이해한 대로 재구성하고 사견을 붙이는 방식으로 진행될 것입니다. 비슷한 수준의 사람들끼리 모여 스터디를 할 때 서로에게 자신의 파트를 설명해주는 느낌으로 읽어주시면 좋을 것 같습니다! :smiley: 



##  3. 목록

---

{% assign list = site.categories['Http The Definitive Guide'] %}

{% assign list = list | reverse %}

{% for post in list %}

​	[{{ post.title }}]({{ post.url }})

{% endfor  %}





## 4. 느낀점

---

&nbsp; HTTP에 관련한 지식들은 여기저기 주워듣고 어깨너머 듣고 해서 많은 지식들이 정리가 안된채로 굴러다니고 있었는데 본 책을 읽은 뒤엔 갈팡질팡 하던 지식들이 '너의 역할은 이거고, 너의 자리는 여기야' 라는 것 처럼 HTTP 완벽가이드 지식 프레임워크에 쏙쏙 들어가는 느낌이었습니다.

 &nbsp;책은 읽는 사람의 수준에 따라 달리보이기 때문에 나중에 경험을더 쌓고 다시봐도 좋을 것 같습니다.