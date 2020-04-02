---
layout: post
title: 목록 뿌리기 테스트 페이지
---
{% assign dirs = page.dir | split: "/" %}
{% assign category = dirs[-1] %}

## {{ category }}/index.html

---

카테고리에 대한 간략한 설명



### 카테고리 : {{ category }}
---

{% for post in site.categories[category] %}

[{{ forloop.index }}. title : {{ post.title }}]({{ post.url }})

{% endfor  %}

---

