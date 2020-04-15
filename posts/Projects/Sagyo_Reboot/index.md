---
layout: post
title: Sagyo-Reboot
---


{% assign dirs = page.dir | split: "/" %}
{% assign category = dirs[-1] %}



## Sagyo-reboot :fire::fire::fire: 
---

- Sagyo-reboot 프로젝트는 2019.06~2019.07 쯤 진행한 Sagyo 프로젝트를 업그레이드하는 프로젝트입니다.

- Sagyo-reboot는 기존의 Sagyo 소스를 정리하고 새로운 기술 및 기능을 적용해보는 프로젝트입니다.
- 본 프로젝트는 끝이 없는 프로젝트로, 새로 적용해 보고 싶은 기술이 있을때면 적용해 보는 곳입니다.

![main.png]({{ site.imgbase }}main.png)



## 목차
---

- 예정
  1. 기존 프로젝트 Sagyo 알아보기
  2. 







## 목차
---

{% for post in site.categories[category] %}

[{{ forloop.index }}. title : {{ post.title }}]({{ post.url }})

{% endfor  %}

