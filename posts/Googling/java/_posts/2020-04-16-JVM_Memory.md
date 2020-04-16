---
layout: post
title: JVM 메모리 구조
---

{% assign imgurl=site.imgbase|append: page.categories[-1] %}

- 이미 많은 포스팅들이 JVM 메모리 구조를 설명하고 있습니다. 본 포스팅은 저의 이해를 돕고자 남기는 포스팅입니다.

- 본 포스팅은 다음 링크들을 토대로 작성되었습니다.
  - [https://javatutorial.net/jvm-explained](https://javatutorial.net/jvm-explained)
  - [https://www.geeksforgeeks.org/java-memory-management/](https://www.geeksforgeeks.org/java-memory-management/)

{: .p_img}

![jvm-architecture.png]({{ imgurl }}/jvm-architecture.png)<small>출처 : https://javatutorial.net/jvm-explained</small>

- class 파일이 로드되고 링크하는 과정이나, GC같은 Execution Engine이 작동하는 방식은 뒤로하고, 이번 포스팅에선 위의 다이어그램중 Runtime Data Area에 대해 알아봅니다.
- Runtime Area에는 크게 다섯가지 영역이 존재합니다.
  - Heap Area
  - Method Area
  - Stack Area
  - PC Register
  - Native Method Stacks



### Heap Area

---

- 흔히 알고있는 객체 인스턴스들이 저장됩니다.
- JVM이 시작될 때 생성됩니다.
- 하나의 JVM에 하나의 Heap Area가 존재하며, JVM안의 여러 스레드가 공유하는 영역입니다.
  - thread-safe 하지 않다고 합니다.
  - 멀티 스레드 환경에서 작업한다면 주의해야 합니다.
- GC의 대상이 되며, GC의 대상이 됐는지 안됐는지에 따라 Eden-Survivor-Tenure 영역으로 나뉩니다.



### Method Area

---

- Heap Area의 logical part입니다. 
  - 마찬가지로 JVM이 시작될 때 생성됩니다.
  - 마찬가지로 한 JVM에 하나의 Method Area가 존재합니다.
  - 역시 마찬가지로 여러 스레드가 공유합니다.
- class 레벨의 데이터가 저장됩니다.
  - static 변수(class 변수, 멤버 변수)들이 이곳에 저장됩니다.
    - static 변수는 한 class의 인스턴스들이 모두 공유하기 때문에 class 변수라고도 합니다.
    - static 변수는 static 초기화 블럭에서 초기화되는데, static 블럭은 클래스가 로딩될때 생성됩니다. 즉, 한 클래스의 인스턴스가 생성되지 않더라도 해당 class의 static 변수는 생성됩니다.
  - 이름 그대로 메서드에 관한 정보들도 저장됩니다.



### Stack Area

---

- 하나의 스레드에 하나의 스택이 할당됩니다.

  - 스레드가 생성될 때 하나의 Stack이 생성됩니다.
  - 크기를 고정적으로 줄 수도 있고, 동적으로 할당되게 할 수도 있습니다.

- 지역 변수들이 저장됩니다. 지역변수들은 Heap에 할당된 인스턴스 참조 주소를 갖고 있습니다.

- 원시 타입(byte, short, int, long, double, float, boolean, char) 변수가 '값과 함께' 저장됩니다.

  - 기본적으로 값과 함께 저장되기 때문에 원시타입은 null 을 갖을 수 없습니다. 아래 구문은 컴파일 과정에서 에러가 납니다.

    ```java
    int a = null;
    ```

  - 반면, 원시타입의 Wrapper 클래스는 변수명은 Stack에, 값은 Heap에 저장되기 때문에 null값을 선언할 수 있습니다.

    ```java
    Integer a = null;
    ```



### PC Register

---

- PC = Program Counter
- 한 스레드에 하나의 PC Register가 생성됩니다.
- 명령이 실행될 때, 현재 실행중인 명령의 주소를 가지고 있습니다.(말 그대로 Program을 Count 합니다.)



### Native Method Stack

---

- Stack Area와 마찬가지로 각 스레드당 하나의 Native method stack이 생성됩니다.
  - 한 스레드가 생성될 때 생성됩니다.
  - 크기 역시 고정 될 수도, 동적으로 할당할수도 있습니다.
- C stack이라고도 불립니다. 