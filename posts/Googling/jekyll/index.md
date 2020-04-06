---
layout: page
title: Jekyll 관련 구글링
---
{% assign dirs = page.dir | split: "/" %}
{% assign category = dirs[-1] %}

### 글 목록
---

{% for post in site.categories[category] %}

​	[{{ post.date | slice: 0, 10 }} {{ post.title }}]({{ post.url }})

{% endfor  %}

