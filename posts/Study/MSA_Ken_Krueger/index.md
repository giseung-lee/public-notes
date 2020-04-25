---
layout: page
title: Mircroservices with Spring Cloud  - Ken Krueger
---

{% assign imgurl=site.imgbase|append: page.categories[-1] %}
{% assign dirs = page.url | split: "/" %}
{% assign category = dirs[-1] %}


## 개요

---

- &nbsp;여기저기 들쑤시고 다녀보니 Monolithic 구조보단 이제 MSA가 기본이 되어 가는 것 같아 MSA를 알아보려 합니다.
- &nbsp;본 포스팅은 Ken Krueger 선생님의 [Microservices with Spring Cloud](https://www.udemy.com/course/microservices-with-spring-cloud/)를 정리한 것입니다.

![main.png]({{ imgurl }}{{ category }}/main.png)

- &nbsp;본 강의의 학습목표는 MSA의 개념 및 간단한 구축 방법을 파악해 Sagyo-reboot에 적용하는 것입니다.
-  &nbsp;3개의 장, 30개의 강의, 4시간 40분 분량의 간단한 강의입니다.



## 목록

---

1. [Introduction to Microservices]({{ site.url }}{{ site.studybase }}{{ category | downcase  }}/MSA1)
2. [Modern Spring: Spring Boot, Spring Data, and Spring Data REST]({{ site.url }}{{ site.studybase }}{{ category | downcase  }}/MSA2)
3. [Spring Cloud 1 - Overview, Config]({{ site.url }}{{ site.studybase }}{{ category | downcase  }}/MSA3)
4. [Spring Cloud 2 - Eureka, Ribbon, Feign]({{ site.url }}{{ site.studybase }}{{ category | downcase  }}/MSA4)
5. [Spring Cloud 3 - Hystrix, Bus]({{ site.url }}{{ site.studybase }}{{ category | downcase  }}/MSA5)
6. [Spring Cloud 4 - API Gateway]({{ site.url }}{{ site.studybase }}{{ category | downcase  }}/MSA6)



## 후기

---

- 좋았던 점
  - 강의 목적에 충실한 강의입니다. MSA가 어떤 것이고 MSA를 이루는 구성요소들이 어떤 역할을 하는지 짧고 함축적으로 배울 수 있었습니다.
  - MSA가 저 같은 주니어 개발자는 덤비지 못할 영역이라고 생각했는데 막상 들어보니 그렇게 덤비지 못할 영역까진 아니라고 생각하게 됐습니다.
  - 스택오버플로우와 레퍼런스에 빠지기 전에 준비운동으로 적합한 것 같습니다.
- 아쉬웠던 점
  - 강의 슬라이드가 안보이길래 제공 안해주나 싶었는데 마지막 강의 강의자료에 전체 강의 슬라이드를 넣어 놨습니다. 의도한건지... 🤔🤔
  - 실습 과제가 너무 간단합니다. 어차피 실습 과제 따로 영상 안찍으시는거 많이 시키고 소스 좀 많이 넣어주시지..



## 여담

---

- 영어 강의를 들을 때 가장 힘든 점은 귀로 영어를 들어면서 한글 타이핑이 안 된다는 것입니다.🤣🤣 뇌가 싱글 코어밖에 안돼서 이걸 영어에 맞춰야 할 지 한글에 맞춰야 할 지 헷갈려 하는 것 같습니다.

- 두 번째로 힘든 점은 하루에 영어 청취 한계가 3시간 남짓 하다는 것입니다. 3시간 이상 들으면 정신이 혼미해집니다.

  

