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

{% assign list = site.categories[category] %}

{% assign list = list | reverse %}

{% for post in list %}

[ {{ forloop.index }}. {{ post.title }} ]( {{ post.url }} )

{% endfor %}




## 예정  <small><small>( 다 할 수 있을까?.. 후... ) </small></small> 
---
- 프로젝트 폴더 구조 정리 (현재는 한 폴더에 모든 파일...)
- 자바스크립트 정리
- DTO를 VO, Map 뭘 써야 할까
- Ant 혹은 Maven 적용 (현재는 모조리 수동...)
- 작업은 윈도우로, 서비스는 리눅스로 엑소더스! (실제 서비스 할 건 아니지만, 개발서버와 운영서버를 나눠 보는 차원에서) 
- OAuth2.0 혹은 JWT로 로그인 인증 방식 변경 (현재는 로그인 성공시 세션에 유저 id 넣어두고, 요청 올때 세션을 검사)
- AOP 적극 활용
- 로그인 필요 기능은 필터에서 거르기 (현재는 로그인 필요한 컨트롤러마다 하드코딩 들어가 있음)
- MSA 구조 적용 - 클라이언트 UI / 로그인 / 글 목록 조회, 글 조회, 작성 / 회완가입. 개인정보 조회, 수정 / CS(Customer Service) 


