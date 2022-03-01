---
layout: page
title: Web
---

---

{% for post in site.categories.web %}
  [{{ post.date | slice: 0, 10 }} {{ post.title }}]({{ site.baseurl }}{{ post.url }})
{% endfor %}
