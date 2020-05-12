---
layout: page
title: 일상 및 잡생각들
---
{% assign dirs = page.dir | split: "/" %}
{% assign category = dirs[-1] %}

---

 평소에 생각하는 것들, 그날 있었던 일들을 편하게 풀어보는 곳입니다.



### 글 목록
---

{% for post in site.categories['Daily Thoughts'] %}

​	[{{ post.date | slice: 0, 10 }} {{ post.title }}]({{ post.url }})

{% endfor  %}

​	{% comment %}

​	forloop.index 로 for문 인덱스 찍을 수 있음.

​		post.title이 파일 안에 써놓은 보여질 제목, post.url은 그 파일로 갈 수 있는 url, post.id는 url하고 거의 유사한데 파일명에 퍼센트 인코딩이 안걸림. 나중에 퍼센트 인코딩 안걸린 파일명이 필요하면 post.id '/'로 스플릿하고 맨 뒤에 꺼 쓰면됨

​	{% endcomment %}