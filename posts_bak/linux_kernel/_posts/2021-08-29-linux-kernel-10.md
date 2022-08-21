---
layout: post
title: dirty page가 I/O에 끼치는 영향
---

{% assign imgurl=site.baseurl |append: site.data.common.path.image|append: '/'|append: page.categories[1] %} 

# dirty page란

커널은 I/O 작업의 효율을 위해서 파일을 읽고 PageCache에 저장하며, 쓰기 작업을 할 때도 PageCache에 먼저 작업한다.

PageCache에서 이루어진 쓰기 작업은 언젠가 디스크에 기록이 되어야 하는데, 이를 위해 PageCache에 쓰기 작업시 해당 메모리가 디스크내용과 달라졌다는 Dirty 비트를 켜게 된다.

그리고 그 메모리 영역을 dirty page라고 부른다. 즉, PageCache 중 쓰기 작업이 이루어진 메모리이다. 

# dirty page 관련 커널 파라미터

dirty page는 디스크에 다시 기록되어야한다. 이 작업을 'page writeback', 'dirty page 동기화' 라고 한다.

커널의 스레드중 'flush' 단어가 들어간 스레드(pdflush, flush, bdflush)등이 이 작업을 해준다.

디스크 I/O를 일으키는 작업이기 때문에 이 작업이 어느 빈도로 이루어 지, 어느 크기로 이루어 지는 지에 따라 서버의 성능이 달라진다.

그래서 커널엔 이를 조절하기 위한 파라미터가 있다. 아래 파라미터들은 or 조건으로 동작하기 때문에 의도한 대로 동작하지 않는다면 다른 파라미터에 걸리는 지를 살펴보자. 

### vm.dirty_background_ratio

전체 메모리의 `vm.dirty_background_ratio` 비율만큼 dirty page가 차면 백그라운드 동기화를 진행한다.

ex) 전체 메모리 : 16GB, `vm.dirty_background_ratio`:10 이라면, dirty page가 1.6GB일때 백그라운드 동기화 실행

### vm.dirty_background_bytes

dirty page가 `vm.dirty_background_bytes`만큼 차면 백그라운드 동기화를 진행한다. 

ex) `vm.dirty_background_bytes`: 65535 라면, dirty page가 65535 byte 됐을때 백그라운드 동기화 진행

### vm.dirty_ratio

`vm.dirty_background_ratio` 에서 `background` 가 빠졌다. 동기화 작업을 백그라운드로 하지 않고 진행중인 I/O작업을 멈춘뒤 바로 dirty page 동기화 부터 수행한다.

ex) A 프로세스가 I/O 작업을 하던중 dirty page가 `vm.dirty_ratio` 만큼 차면, A 프로세스의 I/O 작업을 멈추고 dirty page의 동기화부터 수행.

### vm.dirty_bytes

마찬가지로 `vm.dirty_background_bytes`에서 `background`가 빠졌다. bytes 절대치 기준으로 I/O 작업을 멈추고 dirty page 동기화를 수행한다.

### vm.dirty_writeback_centisecs

flush 커널 스레드를 몇 초 간격으로 깨울지를 결정한다. 즉, 위 파라미터들의 limit에 걸리지 않는 보통 수준에서 몇 초 간격으로 dirty page 동기화를 수행할 지 이다. 1/100초 단위이다.

ex) `vm.dirty_writeback_centisecs`: 500 일때, 5초에 한 번 씩 flush 커널 스레드를 깨워 dirty page 동기화를 수행

### vm.dirty_expire_centisecs

flush 커널 스레드가 디스크에 싱크시킬 dirty page의 기준을 시간으로 찾기위해 사용하는 파라미터이다. 동기화 작업이 수행되는 메모리 크기를 간접적으로 조절 할 수 있다.

ex) `vm.dirty_expire_centisecs`: 3000 이라면, dirty page로 분류 후 30초 동안 동기화되지 않은 메모리들을 디스크에 동기화 시킴 

# 백그라운드 동기화

dirty page의 동기화는 백그라운드 동기화, 주기적인 동기화, 명시적인 동기화가 있다.

- 백그라운드 동기화 : 커널 파라미터의 `vm.dirty_background_~~` 파라미터를 넘었을 때 수행 되는 동기화
- 주기적인 동기화 : 주기적으로 수행 되는 동기화
- 명시적인 동기화 : 따로 명령어를 통해 명시적으로 수행 되는 동기화

책에선 백그라운드 동기화, 주기적인 동기화가 이루어지는 테스트 환경을 구성하고 `ftrace` 를 이용해 어떤 커널 함수들이 동기화를 수행하는데 쓰이는지 소스코드를 뜯어보고 있다. 노트에 남기기엔 너무 지엽적이니 필요할때 책을 참고. 

# dirty page 설정과 I/O 패턴

위 테스트에 이어 dirty page 커널 파라미터를 바꾸며 I/O 작업 패턴을 테스트. 자세한건 책을 참조.

## 테스트 요약 

dirty page 동기화 설정시 결국 가장 중요한건 flush 커널 스레드를 어느 주기로 깨울지, 한 번 동기화 작업할 때 어느 크기만큼 수행할 지다.

당연한 얘기지만, 절대적인 기준은 없고 서버의 성능과 애플리케이션의 동작 방식에 따라 설정해야 한다.

ex) 같은 애플리케이션을 A, B 서버에 올렸다고 가정하자. 
이때, A서버는 초당 10MB의 쓰기 작업을 할 수 있고, B 서버는 초당 100MB의 쓰기 작업을 할 수 있다면, A서버는 조금 더 자주 flush 스레드를 깨워야 한다. 
반면, B서버는 flush 스레드를 자주 깨우는게 오히려 성능에 악영햘을 미칠 수 있다.
