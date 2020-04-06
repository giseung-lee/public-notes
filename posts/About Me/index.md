---
layout: page
title: About Me
---
{% assign dirs = page.dir | split: "/" %}
{% assign category = dirs[-1] %}



본 About Me 페이지는 구직 기간에 열어놓은 ***TMI가 가득한 버전의 About Me*** 입니다! :muscle:



## 안녕하세요!

---

 안녕하세요 1년차 개발자 이기승입니다. 서울대학교에서 조경학을 전공한 뒤 2019년 부터 본격적으로 프로그래밍을 배우기 시작했습니다.  2019.09 ~ 2020.03 7개월 정도 한 솔루션 회사에 다니다 퇴사한 후, 현재 공부를 하며 구직중에 있습니다. 

 앞으로 저에대한 소개는 아래의 순서로 진행하겠습니다. 순서는 크게 상관 없으니 관심있는 부분부터 태그를 따라오셔도 됩니다.

- [조경학을 선택한 이유와 떠난 이유](#조경학을-선택한-이유와-떠난-이유)
- [방황의 시기](#방황의-시기)
- [개발자가 된 계기](#개발자가-된-계기)
- [학원 출신 프로그래머?](#학원-출신-프로그래머)
- [프로젝트](#프로젝트)
- [사용 가능 기술](#사용-가능-기술)
- [강점 및 약점](#강점-및-약점)
- [관심있는 분야](#관심있는-분야)
- [좋아하는 구절들](#좋아하는-구절들)





## 조경학을 선택한 이유와 떠난 이유

---

 저는 제가 공대생이 될 줄 알았습니다. 어릴적엔 흔히 그렇듯 과학자가 꿈이었고 고등학생 때도 명확한 꿈은 없었지만 막연히 공대생이 되겠구나 생각했습니다. 그러던 와중, 고등학교 3학년 때 건축가 가우디에 관한 다큐멘터리를 보게됩니다. 미적 감각과 공학적 지식을 자유자재로 다루는 **가우디를 보고 건축계열에 꽂혀버렸습니다.**~~(그러지 말았어야 했습니다...)~~ 수능을 보고 지원 가능한 곳을 쭉 훑어보다가 조경학과가 눈에 들었왔고 많은 고등학생들이 그러하듯이 **충동적으로 지원하게 됐습니다.**

![guell_park.jpg]({{ site.imgbase }}About Me/guell_park.jpg)*조경학도가 되면 모두 이런 공원을 만들 수 있을 줄 알았습니다.<br>출처 : https://parkguell.barcelona/*

  **조경학과의 생활은 재미있었습니다.** 수학, 과학이 전부인 줄 알았던 고등학생이 디자인을 하고 있고, 대상지에 대한 문제점 파악 및 해결방안 마련을 위해 **문화와 역사를 탐구하고**, **사회지표들을 훑어보고** 있었습니다. 그리고 현대 조경은 공원이나 정원에 국한되는게 아니어서 용산공원, 세종시 같은 **도시계획 단위의 공부도 하고** **도시 개발과 환경의 연관관계를 공학적으로 탐구하기도 했습니다.**

 일주일에 한, 두번은 캐드, 라이노, 포토샵, 일러스트레이터와 씨름하며 밤을 샜지만 그것도 재미있었습니다. 무언가에 몰입할 때 느낄수 있는 고양감을 매주 느낄 수 있었습니다.

 그러던 저도 4학년이 되고 이제 직업을 가져야 할 때가 되었습니다. 하지만 고민이 됐습니다. 직업은 단순히 흥미만으로 결정할 순 없었기 때문입니다. 흥미도 중요하겠지만 현업에선 무슨 일을 하는지, 현재 시장 상황은 어떻고 앞으로의 전망은 어떤지 등을 생각해야 했습니다. 

 조경학을 배우는건 재미있었지만 조경학과 현업에서 조경이 하는일은 괴리가 컸고, 현재 시장 상황은 안좋았으며 앞으로도 좋아질 전망은 아니었습니다. 다른 직업을 찾는게 좋겠다고 생각하고 조경을 떠나게 됩니다.

 



## 방황의 시기

---

 2018년 2월 졸업을 하고 **다른 직업을 찾기 위한 방황의 시기에 들어갑니다.** 그때도 개발자가 후보에 있었지만 **가장 먼저 한 일은 대학원을 나와 연구원이 되는 것**이었습니다. 조경학과에 **환경 분야를 다루는 연구실**이 있었고 당시 교수님께서 저를 꽤나 마음에 들어하셨습니다. 물리를 잘하고 파이썬을 잘 다룬다는 이유때문이었습니다. 

 당시 그 연구실에선 소프트웨어를 수동으로 조작하는 방식에서 이제 막 벗어나 프로그래밍을 이용해 연구를 시작하고 있었습니다. 학부생들에게도 파이썬을 이용해서 ArcGIS라는 소프트웨어를 다루는 방법을 알려줬고, 제가 학부생들 중에선 파이썬을 꽤 유용하게 사용하고 있었습니다. 그래서 교수님께서 저에게 한번 **대학원 보조연구원(인턴)을 해보고, 연구가 재미있으면 연구실에 들어오라고 제안해주셨습니다.**

그 곳에서 8개월 가량을 보냈지만, **환경분야 연구는 제 적성이 아니었습니다.** 환경 분야의 특성상 연구의 성과가 가시적으로 보이지 않았고, 흔히 말하는 연구를 위한 연구도 많았기 때문입니다. 

 그렇게 대학원을 나오고 **마음이 약해진 저는 너도나도 해본다는 공무원 공부를 하게됩니다.** 뭔가 일의 성과가 눈에 보이고, 의미 있는 일을 하고 싶던 저는 **소방 간부를 준비하게 됩니다.** 2년전부터 운동에 심취해 있던 터라 실기 시험 준비를 핑계로 운동도 할 수 있어서 좋았습니다.

 하지만 운동이 과했던 탓일까요. **허리디스크와 척추분리증이 터지고 말았습니다.** :scream: 검사 결과, 뼈에 10명에 1명정도는 있는, 그렇게 희귀하진 않은 유전적 결함이 있었고, 보통은 나이가 들어 문제가 되는데 과도한 운동으로 일찌감치 터지게 된 것이었습니다. 

 **소방간부와 제 허리를 맞바꿀 순 없었기 때문에 소방간부 역시 3개월만에 막을 내리고 맙니다.** 하는 것 마다 모두 안되는 조금 비참한 시기였습니다. 





## 개발자가 된 계기

---

학부생 시절부터 개발자와 프로그래밍에 대한 관심은 있었습니다. 대학교 3학년 때 쯤 수업에서 파이썬을 처음 접했고, 여름방학엔 위에서 말한 대학원에서 진행한 파이썬 스터디에도 참여 했습니다. 같은 대학생임에도 벌써부터 사회에 의미있는 것들을 만들어내는 대학생 개발자들도 굉장히 멋있었습니다.                                                                                           





## 학원 출신 프로그래머?

---







## 프로젝트

---







## 사용 가능 기술

---

- 아래와 같은 등급을 사용합니다.

| 등급                                                         | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| :star:<span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span> | 사용만 해본 수준입니다. 기본 기능 구현에도 참고자료가 필요합니다. |
| :star::star:<span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span> | 기본 기능 구현 정도는 참고자료가 없이 가능합니다.            |
| :star::star::star:<span style='filter: grayscale(1)'>:star:</span> | 기술이 어떻게 동작하는 지 알고, 참고자료가 있다면 고급 기능도 구현할 수 있습니다. |
| :star::star::star::star:                                     | 참고자료 없이도 고급 기능을 다룰 수 있습니다.                |


- 각 기술별 사용 능력은 다음과 같습니다.

| 기술1              |          등급1           | 기술2          |          등급2           |
| ----------------- | :----------------------: | -------------- | :----------------------: |
| 언어) Java         | :star::star::star:<span style='filter: grayscale(1)'>:star:</span> | 웹) HTML       | :star::star:<span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span> |
| 언어) Python       | :star::star::star:<span style='filter: grayscale(1)'>:star:</span> | 웹) CSS        | :star::star:<span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span> |
| 언어) C#           | :star::star:<span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span> | 웹) JavaScript | :star::star::star:<span style='filter: grayscale(1)'>:star:</span> |
| DB) Oracle / MySQL |          :star::star:<span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span>          | Server) Apache | :star:<span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span> |
| DB) Mongo DB       | :star:<span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span> | Server) WebtoB | :star:<span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span> |
| Framework) Spring  | :star::star:<span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span> | WAS) Tomcat    | :star:<span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span> |
| Framework) Express | :star:<span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span> | WAS) JEUS      | :star:<span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span><span style='filter: grayscale(1)'>:star:</span> |





## 좋아하는 구절들

---





