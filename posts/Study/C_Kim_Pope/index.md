---
layout: page
title: Unmanged Language C - Kim pope
---

{% assign imgurl=site.imgbase|append: page.categories[-1] %}
{% assign dirs = page.url | split: "/" %}
{% assign category = dirs[-1] %}





## 근본 찾기 프로젝트

---

- &nbsp;프로그래밍을 시작한지 1년이 지난 시점에서 갑자기 C 언어 강의를 듣는 이유는 '근본 찾기 프로젝트'의 일환입니다.
- &nbsp;본 포스팅은 Kim Pope 선생님의 [C 언매니지드 프로그래밍](https://www.udemy.com/course/c-unmanaged-programming-by-pocu/)강의를 정리한 것입니다.

{: .p_img}
![imgurl]({{ imgurl }}{{category}}/main.png)<small>출처 : https://www.udemy.com/course/c-unmanaged-programming-by-pocu/</small>

- &nbsp;본 강의의 개인적인 **학습 목표는 언매니지드 언어를 학습해 하드웨어 수준에서 코드가 어떻게 돌아가는지, 하드웨어 수준에서 어떤 코드가 좋은 코드인지 아는 것**입니다. (C 개발자가 되고자 함이 아닙니다.)
- &nbsp;총 14장, 342개, 38시간 분량의 강의입니다. 포스팅은 각 장별로 나눠 올립니다.





## 목록

---

1. [과목 소개]({{ site.url }}{{ site.studybase }}{{ category | downcase  }}/C1)
2. C언어 기본 문법 1 [4/22 예정]
3. C언어 기본 문법 2, 빌드 단계 [4/24, 26 예정]
4. 포인터 [4/28 예정]
5. C 스타일 문자열, 출력 [4/30 예정]
6. 콘솔 입력, 파일 입출력, 커맨드 라인 인자 [5/4 예정]
7. 구조체, 공용체, 함수 포인터 [5/6 예정]
8. 가변 인자 함수, 올바른 오류 처리 방법 [5/8 예정]
9. 레지스터, 스택&힙, 동적 메모리, 다중 포인터 [5/12 예정]
10. 자료구조 [5/14 예정]
11. 전처리기 [5/18 예정]
12. 나만의 라이브러리 만들기, C99 [5/20 예정]
13. C99, C11 [5/22 예정]
14. Type-Generic 함수 만들기, 정적어서트, 메모리 정렬, 멀티스레딩 [5/25 예정]





## 후기

---

- 다 듣고 작성 예정