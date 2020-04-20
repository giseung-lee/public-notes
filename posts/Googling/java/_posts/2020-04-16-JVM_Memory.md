---
layout: post
title: JVM 메모리 구조(작성중)
---

{% assign imgurl=site.imgbase|append: page.categories[-1] %}

- (작성중) - Stack, Heap, Method 메모리 다이어그램만 그리던가 괜찮은거 찾아오고 끝내면 될 것 같음
- 이미 많은 포스팅들이 JVM 메모리 구조를 설명하고 있습니다. 본 포스팅은 저의 이해를 돕고자 남기는 포스팅입니다.
- 많은 곳을 참고했고 내용 역시 거진 비슷하여 따로 출처를 남기지 않았습니다. 다만, PC Register와 Native Method Stack의 경우 포스팅 마다 내용이 상이해서 jvm 공식 문서를 참고했습니다.


{: .p_img}

![jvm-architecture.png]({{ imgurl }}/jvm-architecture.png)<small>출처 : https://javatutorial.net/jvm-explained</small>

- 이번 포스팅에선 위의 다이어그램중 Runtime Data Area에 대해 알아봅니다.
- Runtime Area에는 크게 다섯가지 영역이 존재합니다.
  - Heap Area
  - Method Area
  - Stack Area
  - PC Register
  - Native Method Stacks


### Method Area

---

- 로드한 class 와 관련한 데이터들이 저장됩니다.

  - JVM이 시작될 때 생성됩니다.

  - 클래스의 메서드에 관한 정보들이 저장됩니다.
  - static 변수(class 변수, 멤버 변수), static 메서드들이 이곳에 저장됩니다.
    - static 변수는 한 class의 인스턴스들이 모두 공유하기 때문에 class 변수라고도 합니다.

    - static 변수는 static 초기화 블럭에서 초기화되는데, static 블럭은 클래스가 로딩될때 생성됩니다. 즉, 한 클래스의 인스턴스가 생성되지 않더라도 해당 class의 static 변수는 생성됩니다.

    - static 메서드는 객체를 만들지 않아도 사용할 수 있는데, 클래스를 로드할 때 static 메서드를 미리 로드하기 때문입니다.

      

### Heap Area

---

- 흔히 알고있는 객체 인스턴스들이 저장됩니다.
- JVM이 시작될 때 생성됩니다.
- 하나의 JVM에 하나의 Heap Area가 존재하며, JVM안의 여러 스레드가 공유하는 영역입니다.
  - thread-safe 하지 않다고 합니다.
  - 멀티 스레드 환경에서 작업한다면 주의해야 합니다.
- GC의 대상이 되며, GC의 대상이 됐는지 안됐는지에 따라 Eden-Survivor-Tenure 영역으로 나뉩니다.




### Stack Area

---

- 하나의 스레드에 하나의 스택이 할당됩니다.

  - 스레드가 생성될 때 하나의 Stack이 생성됩니다.
  - 크기를 고정적으로 줄 수도 있고, 동적으로 할당되게 할 수도 있습니다.

- 하나의 메소드를 호출 할 때마다 메소드의 frame을 스택에 추가합니다. 메소드가 종료되면 프레임이 제거 됩니다.  frame을 통해 흔히 알고 있는 변수들의 scope가 정해집니다.

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

  >  Each Java Virtual Machine thread has its own `pc` (program counter) register

- **현재 수행중인 JVM 명령 주소가 저장됩니다.** 단, 현재 수행중인게 자바 프로그램이 아닌 외부 Native 프로그램이라면 저장되지 않습니다.

  > If that method is `not native`, the pc register contains the address of the Java Virtual Machine `instruction currently being executed.` If the method currently being executed by the thread is `native`, the value of the Java Virtual Machine's pc register is `undefined.` 

  <small>출처 : https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-2.html#jvms-2.5.1</small>
  
  

### Native Method Stack

---

- **Java 이외의 언어로 작성된 프로그램, API 등을 운용하기 위해 필요한 영역**입니다. (외부 프로그램들이 주로 C언어로 작성되어서 'C stacks'라고도 불리는 것 같습니다.)

  > An implementation of the Java Virtual Machine may use conventional stacks, `colloquially called "C stacks,"` `to support native methods` (methods written in a language other than the Java programming language)

- native 메서드를 지원하지 않는 JVM은 Native Method Stack을 갖지 않을 수 있습니다.

  >  Java Virtual Machine implementations that `cannot load native methods` and that do not themselves rely on conventional stacks `need not supply native method stacks.` 

- Stack 메모리처럼 쓰레드 마다 할당되며, 쓰레드가 생성될 때 생성됩니다.

  > native method stacks are typically allocated per thread when each thread is created.

- 크기는 고정적으로 할당 할 수도 있고, 동적으로 계산 될 수도 있습니다. 

  > This specification permits native method stacks either to be of a fixed size or to dynamically expand and contract as required by the computation.

  - 정적 할당시 할당한 양을 초과하면 *StackOverFlow* 날 수 있고, 동적으로 할당해도 메모리가 부족하면 *OutOfMemory* 날 수 있습니다.

    > If the computation in a thread requires a larger native method stack than is permitted, the Java Virtual Machine throws a `StackOverflowError`.
    >
    > If native method stacks can be dynamically expanded and native method stack expansion is attempted but insufficient memory can be made available, or if insufficient memory can be made available to create the initial native method stack for a new thread, the Java Virtual Machine throws an `OutOfMemoryError`.
  
    <small>출처 : https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-2.html#jvms-2.5.6</small>