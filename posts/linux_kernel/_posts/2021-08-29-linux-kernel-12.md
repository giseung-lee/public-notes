---
layout: post
title: 애플리케이션 성능 측정과 튜닝
---

{% assign imgurl=site.baseurl |append: site.data.common.path.image|append: '/'|append: page.categories[1] %} 

# 테스트 환경 구성

책에선 redis에 put, get을 하는 간단한 flask 어플리케이션과 reverse proxy용 nginx를 두고 테스트 환경을 구성했다.

테스트를 위해 [siege](https://www.joedog.org/siege-home/)라는 툴을 이용했다. 해당 툴이 좋아 보인다. 이런거 만드는 사람 멋져.

# 성능 측정 및 튜닝
 
테스트 및 튜닝은 할당 받은 물리적인 리소스(CPU or memory)가 병목이 될 때 까지 서버 및 애플리케이션을 튜닝 하는 것이다.

받은 리소스를 탈탈 다 털어 썼다면 그게 해당 서버 + 어플리케이션의 최대 성능이고, 이를 넘고 싶다면 서버 스펙을 높이거나 서버를 늘려야 한다.

물론, 리소스를 탈탈 다 쓸때까지 최적화 해서 튜닝 하는게 힘들다 ㅎㅎ
