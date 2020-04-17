---
layout: page
title: Sagyo-Reboot
---


{% assign dirs = page.dir | split: "/" %}
{% assign category = dirs[-1] %}



## Sagyo-reboot :fire::fire::fire: 
---

- Sagyo-reboot 프로젝트는 2019.06~2019.07에 진행한 Sagyo 프로젝트를 업그레이드하는 프로젝트입니다.

- 본 프로젝트는 끝이 없는 프로젝트로, 적용해 보고 싶은 기술이 있을 때 적용해 보는 곳입니다.



## 목차
---

{% for post in site.categories[category] %}

[{{ forloop.index }}. {{ post.title }}]({{ post.url }})

{% endfor  %}




## 예정
---
- 프로젝트 폴더 구조 정리 (현재는 한 폴더에 모든 파일...)
- Ant 혹은 Maven 적용 (현재는 모조리 수동...)
- 윈도우에서 war 배포하고, 리눅스에서 war받아서 Apache/Tomcat 구동
- OAuth2.0 혹은 JWT로 로그인 인증 방식 변경 (현재는 로그인 성공시 세션에 유저 id 넣어두고, 요청 올때 세션을 검사)


