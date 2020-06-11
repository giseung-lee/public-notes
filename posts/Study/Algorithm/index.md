---
layout: page
title: 알고리즘 관련 포스팅
---
{% assign dirs = page.dir | split: "/" %}
{% assign category = dirs[-1] %}

### 글 목록
---

{% for post in site.categories[category] %}

​	[{{ post.date | slice: 0, 10 }} {{ post.title }}]({{ post.url }})

{% endfor  %}
