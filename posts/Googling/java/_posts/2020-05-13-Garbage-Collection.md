---
layout: post
title: JAVA Garbage Collection
---

{% assign imgurl=site.imgbase|append: page.categories[-1] %}

- &nbsp;본 포스팅은 네이버 D2의 [Java Garbage Collection](https://d2.naver.com/helloworld/1329)을 정리한 것입니다.



## GC란?

---

- 메모리를 사용했으면 참조관계를 끊어 해제해야 합니다. C에선 malloc()으로 힙메모리를 할당하고 free()를 해주어 개발자가 직접 메모리 관리를 해줘야 하지만 자바에선 GC가 이를 해줍니다. 
- GC란, 더이상 사용하지 않는 메모리의 참조관계를 끊어주는(메모리를 해제하는) 작업입니다.
- 자바에서도 객체를 null로 둔다거나 System.gc()를 써서 직접 메모리를 해제할 수도 있지만, System.gc()는 잘못 쓰면 성능에 매우 큰 영향을 미치므로 웬만해선 사용을 금지합니다. (저는 사실 처음 봤습니다.. ㅎㅎ)
- Stop The World 
  - GC를 실행하기 위해 JVM이 애플리케이션을 멈추는 것입니다. GC를 수행하는 쓰레드 외에 모든 쓰레드가 멈춥니다.
  - GC튜닝의 핵심은 Stop The World의 시간을 줄이는 것입니다.



## GC가 일어나는 영역

---

- GC는 힙 메모리에서 일어납니다. 그리고 힙 메모리는 객체의 지속 여부에 따라 아래와 같이 나뉩니다.
  - Young Generation
    - Eden
    - Survivor1
    - Survivor2
  - Old Generation
    - Tenured
  - Permanet Generation == Method Area
- Method Area라고 더 잘 알려져 있는 Perm 영역은 클래스 로드시 static 변수, intern 변수 등이 저장됩니다.
- Young / Old Generation이 구성되는 과정은 다음과 같습니다.
  - 객체가 생성되면 Eden 영역에 들어있습니다. 
  - GC가 일어나고 살아남은 Eden의 객체는 Survivor중 하나로 이동합니다.
  -  GC가 일어나며 하나의 Survivor가 가득차면 그 중 살아남은 객체를 다른 Survivor영역으로 옮기고 가득찬 Survivor영역의 참조는 모두 끊깁니다.
  - Survivor영역에서도 계속 살아남는 객체는 Tenured 영역으로 이동합니다.



## GC의 방식

---

- Young Generation에서 GC가 일어나는 방식엔 다음과 같은 것들이 있습니다.
  - bump-the-pointer
  - TLABs(Thread-Local Allocation Buffers)
    - bump-the-pointer는 다중 스레드 환경에서 스레드락이 걸릴수 있기 때문에 각 스레드마다 Eden영역의 작은 부분을 할당해주는 방법입니다.
- Old Generation에서 GC가 일어나는 방법은 JDK7을 기준으로 5가지 방법이 있습니다.
  - Serial GC
  - Parallel GC
  - Parallel Old GC (Parallel Compacting GC)
  - Concurrent Mark & Sweep GC (이하 CMS)
  - G1 (Garbage First) GC

