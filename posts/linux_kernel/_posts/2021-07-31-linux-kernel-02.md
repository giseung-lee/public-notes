---
layout: post
title: top을 통해 살펴보는 프로세스 정보들
---

{% assign imgurl=site.data.common.path.image|append: '/'|append: page.categories[1] %}



# 시스템의 상태 살피기

```
$ top
top - 02:14:06 up 51 min,  1 user,  load average: 0.00, 0.00, 0.00
Tasks:  85 total,   1 running,  48 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1006892 total,   478848 free,    85400 used,   442644 buff/cache
KiB Swap:        0 total,        0 free,        0 used.   785240 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
    1 root      20   0  125564   5480   3992 S  0.0  0.5   0:02.06 systemd
    2 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kthreadd
    4 root       0 -20       0      0      0 I  0.0  0.0   0:00.00 kworker/0:0H
    5 root      20   0       0      0      0 I  0.0  0.0   0:00.04 kworker/u30:0
    6 root       0 -20       0      0      0 I  0.0  0.0   0:00.00 mm_percpu_wq
    	:
```

top 명령어는 시스템의 상태를 전반적으로 파악하는데 도움을 준다. 



````
top - 02:14:06 up 51 min,  1 user,  load average: 0.00, 0.00, 0.00
````

첫줄엔 서버의 구동 시간, 커널 좁속중인 유저, load average가 표시된다. 

```
Tasks:  85 total,   1 running,  48 sleeping,   0 stopped,   0 zombie
```

두 번째 줄엔 프로세스와 과련한 것들이 표시된다. 전체 몇 개의 프로세스가 있고, 실제 동작하는 프로세스는 몇 개 인지 등..

```
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
```

세번째 줄엔 CPU와 관련한 값들이 보인다.

```
KiB Mem :  1006892 total,   478848 free,    85400 used,   442644 buff/cache
KiB Swap:        0 total,        0 free,        0 used.   785240 avail Mem
```

네번째, 다섯번째 줄은 메모리와 관련한 값이다.



```
  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
    1 root      20   0  125564   5480   3992 S  0.0  0.5   0:02.06 systemd
    2 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kthreadd
    4 root       0 -20       0      0      0 I  0.0  0.0   0:00.00 kworker/0:0H

```

하단엔 프로세스에 대한 상세 정보들이 표시되어 있다. pid, 실행 유저 등.. 자세한 사항은 뒤에서 설명하겠지만 간단히만 짚어보자면, 

- PR : 프로세스의 실행 우선 순위
- NI : PR에 더해지는 값. PR + NI로 실제 우선 순위가 결정된다.
- **VIRT** : 프로세스가 사용하는 virtual memory의 총량 - 높아도 큰 문제는 되지 않음. 
- **RES** : 프로세스가 사용하는 물리 메모리의 양
- **SHR** : 다른 프로세스와 공유하고 있는 shared memory의 양. (ex - glibc 라이브러리)
- S : 프로세스의 상태. running, sleeping, stopped 등..



# VIRT와 RES 그리고 Memory Commit의 개념

위에 기술했듯이 메모리에는 할당 받은 가상 메모리(VIRT)와 할당 받아 실제 사용하는 물리 메모리(RES)가 있다. 

프로세스가 커널에 메모리를 요청하면 커널은 곧 바로 물리 메모리를 할당 해주는 게 아니라 우선 가상 메모리를 할당하고, 실제 쓰기 작업이 일어날때 물리 메모리를 할당한다. 이를 Memory Commit 이라고 부른다. 

`vm.overcommit_memory` 커널 파라미터를 이용해 VIRT의 정책을 정할 수 있다. (무한정 계속 할당 받을 건지, 상한을 정할 건지 등..)



Memory Commit을 사용하는 이유는 메모리를 효율적으로 사용하기 위해서이다. 특히, 새로운 프로세스를 만들때 우선 `fork()` 를 하여 실행 중인 프로세스와 같은 프로세스를 만들고,  `exec()` 을 하여 다른 프로세스를 만드는데, `fork()` 시 바로 물리 메모리를 할당하면 메모리의 낭비가 심해지기 때문에 실제 쓰기 작업이 발생해야 물리 메모리를 할당한다. (Copy-On-Write 기법이라고도 한다)



```
$ sar -r
Linux 4.14.238-182.422.amzn2.x86_64 (localhost) 	08/08/2021 	_x86_64_	(1 CPU)

01:22:47 AM       LINUX RESTART

01:30:01 AM kbmemfree kbmemused  %memused kbbuffers  kbcached  kbcommit   %commit  kbactive   kbinact   kbdirty
01:40:01 AM    482964    523928     52.03      2088    412108    368684     36.62    198736    259416       184
01:50:01 AM    482136    524756     52.12      2088    412108    368676     36.62    199748    259160       180
02:00:01 AM    479176    527716     52.41      2088    413748    372596     37.00    199380    262560       220
02:10:01 AM    478584    528308     52.47      2088    413868    372072     36.95    200764    261644       180
02:20:01 AM    478244    528648     52.50      2088    414148    371752     36.92    201028    261664       180
02:30:01 AM    476212    530680     52.70      2088    414156    375688     37.31    202344    261676       184
02:40:01 AM    410744    596148     59.21      2088    452200    371516     36.90    226912    273860       180
02:50:01 AM    410956    595936     59.19      2088    452212    371516     36.90    226912    273876       180
03:00:01 AM    410768    596124     59.20      2088    452344    371516     36.90    226936    273988       180
03:10:01 AM    410808    596084     59.20      2088    452348    371792     36.92    226952    273976       180
03:20:01 AM    411000    595892     59.18      2088    452300    371792     36.92    226684    274008       180
03:30:01 AM    411220    595672     59.16      2088    452304    371792     36.92    226692    273976       180
03:40:01 AM    411088    595804     59.17      2088    452312    371108     36.86    226660    273980       232
03:50:01 AM    411212    595680     59.16      2088    452316    371108     36.86    226676    274020       228
04:00:01 AM    411096    595796     59.17      2088    452316    371108     36.86    226704    274008       228
04:10:01 AM    411096    595796     59.17      2088    452324    371108     36.86    226712    273980       264
04:20:01 AM    414624    592268     58.82      2088    452408    366032     36.35    224008    274124       228
04:30:01 AM    411056    595836     59.18      2088    452480    373880     37.13    227068    273720       228
Average:       434055    572837     56.89      2088    439333    371319     36.88    217829    269646       201
```

시스템의 Memory Commit 상태는 `sar -r` 커맨드를 통해 확인할 수 있다. `%commit` 필드를 확인.



# 프로세스의 상태 보기

```
$ top
top - 04:41:39 up  3:18,  1 user,  load average: 0.00, 0.00, 0.00
Tasks:  84 total,   1 running,  47 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.3 us,  0.3 sy,  0.0 ni, 99.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1006892 total,   411504 free,    99764 used,   495624 buff/cache
KiB Swap:        0 total,        0 free,        0 used.   763692 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
    1 root      20   0  125564   5480   3992 S  0.0  0.5   0:02.14 systemd
    2 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kthreadd
    4 root       0 -20       0      0      0 I  0.0  0.0   0:00.00 kworker/0:0H
    5 root      20   0       0      0      0 I  0.0  0.0   0:00.04 kworker/u30:0
```

다시 `top` 커맨드로 돌아오자. 프로세스 목록의 `S` 필드를 이용해 프로세스의 상태를 확인할 수 있다. (Proccess Status)

- D : uninterruptible sleep. 디스크 or 네트워크 I/O 대기중인 프로세스. Run Queue에서 제거되고 Wait Queue에 들어가있음
- R : running. 실행중인 프로세스. 실제 CPU를 소모중
- S : interruptible sleeping. 작업을 안하고 대기중인 프로세스. D 상태와 다르게 필요하면 바로 사용할 수 있음.
- T : traced or stopped 상태의 프로세스. `strace` 를 사용하면 커널의 시스템 콜을 추적할 수 있는데(ex - `strace ls`), 추적중인 프로세스가 T 상태로 표시됨. 일반적으로 거의 볼일이 없음.
- Z : zombie 상태 프로세스. 부모 프로세스가 죽은 자식 프로세스를 뜻함

S 상태는 많아도 문제되지 않지만 D 상태는 많으면 문제가 발생할 수 있음(D 상태가 끝나고 리소스를 소모할 수 있기 때문에 너무 많이 쌓이면 추후 문제가 될 수도.) 

Z 상태는 그 자체로 예외적인 상황이지만 Z 상태가 많아도 큰 문제는 되지 않음. 단, Z 상태 프로세스가 계속 만들어질 경우 PID 고갈이 올 수도 있음. 



# 프로세스 우선순위

```
$ top
top - 04:41:39 up  3:18,  1 user,  load average: 0.00, 0.00, 0.00
Tasks:  84 total,   1 running,  47 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.3 us,  0.3 sy,  0.0 ni, 99.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1006892 total,   411504 free,    99764 used,   495624 buff/cache
KiB Swap:        0 total,        0 free,        0 used.   763692 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
    1 root      20   0  125564   5480   3992 S  0.0  0.5   0:02.14 systemd
    2 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kthreadd
    4 root       0 -20       0      0      0 I  0.0  0.0   0:00.00 kworker/0:0H
    5 root      20   0       0      0      0 I  0.0  0.0   0:00.04 kworker/u30:0
```

다시 `top` 으로 돌아와 프로세스 목록의 `PR` , `NI` 를 보자. 프로세스의 우선순위와 관련된 필드다.

CPU 마다 Run Queue가 존재하고, 스케쥴러는 Run Queue에서 우선순위가 높은 프로세스를 꺼내 디스패처에 넘겨준다. 디스패처는 현재 실행중인 프로세스 정보를 다른 곳에 저장하고 넘겨받은 작업을 처리한다. 

- PR : Priority. 커널에서 인식하는 프로세스의 우선순위 값. 기본값은 20 
- NI : Nice value. 커맨드를 통해 우선순위를 추가적으로 조정할 때 사용.

위 `top` 을 보면 나머지 프로세스는 모두 PR=20 인데, PID=4 는 NI=-20 을 조정받아 PR=0이 된 상태이다. 



우선순위는 CPU 갯수와도 상관있다. 2개의 CPU를 사용할때 2개의 프로세스를 실행하면 우선순위를 조정하더라도 각각 CPU에서 작업이 수행되기 때문에 의도한대로 동작하지 않을 수도 있다. 

