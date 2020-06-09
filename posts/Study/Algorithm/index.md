---
layout: page
title: [알고리즘]
---

{% assign imgurl=site.imgbase|append: page.categories[-1] %}
{% assign dirs = page.url | split: "/" %}
{% assign category = dirs[-1] %}

## 알고리즘 관련 포스팅

---

{% assign list = site.categories['Algorithm'] %}

{% for post in list %}

	[{{ post.title }}]({{ post.url }})

{% endfor  %}

