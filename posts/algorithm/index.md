---
layout: page
title: Algorithm
---

---

{% for post in site.categories.algorithm %}
  [{{ post.date | slice: 0, 10 }} {{ post.title }}]({{ post.url }})
{% endfor %}
