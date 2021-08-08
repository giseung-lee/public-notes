---
layout: page
title: Devops와 SE를 위한 리눅스 커널 이야기
---

---

![book-main.jpeg]({{ site.data.common.path.image }}/linux_kernel/book-main.jpeg)

{% for post in site.categories.linux_kernel %}
  [{{ post.date | slice: 0, 10 }} {{ post.title }}]({{ post.url }})
{% endfor %}
