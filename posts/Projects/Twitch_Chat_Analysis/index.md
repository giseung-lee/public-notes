---
layout: page
title: Twitch Chat Analysis
---
{% assign dirs = page.dir | split: "/" %}
{% assign category = dirs[-1] %}

## 개요

---

- 본 프로젝트는 2019.08에 진행했던 Twitch Chat Analysis(이하 TCA)를 다시 이어하는 프로젝트입니다.
- TCA는 인터넷 방송 트위치 다시보기의 최적 지점을 찾아주는 프로젝트입니다. 
- 프로젝트의 개요, 기획은 TCA의 첫 번째 포스팅인 '[지금까지의 Twitch Chat Analysis]({{ page.dir  |  downcase }}TCA1/)'에 기록해 두었습니다. 해당 포스팅을 먼저 읽어주시면 감사하겠습니다.



## 목록
---

{% for post in site.categories[category] %}

[{{ forloop.index }}. {{ post.title }}]({{ post.url }})

{% endfor  %}

---

