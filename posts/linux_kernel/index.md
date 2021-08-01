---
layout: page
title: Devops와 SE를 위한 리눅스 커널 이야기
---

---

{% for post in site.categories.linux_kernel %}
  [{{ post.date | slice: 0, 10 }} {{ post.title }}]({{ post.url }})
{% endfor %}
