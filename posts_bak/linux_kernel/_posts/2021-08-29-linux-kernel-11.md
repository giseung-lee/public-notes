---
layout: post
title: I/O 작업이 지나가는 관문, I/O 스케줄
---

{% assign imgurl=site.baseurl |append: site.data.common.path.image|append: '/'|append: page.categories[1] %} 

# I/O 스케줄러의 필요성

![linux-kernel-03.jpeg]({{ imgurl }}/linux-kernel-03.jpeg)<small>출처 : https://www.wikihow.com/Recover-a-Dead-Hard-Disk#/Image:Recover-a-Dead-Hard-Disk-Step-3-Version-3.jpg</small>

디스크 I/O 작업은 상당히 느린 작업이다. 
SSD, HDD중 HDD를 사용할 경우 더 느리다. 
HDD는 디스크 헤드라고 불리는 기계장치가 직접 움직이며 읽고 쓰기 때문이다.

SSD의 경우 HDD보다 낫지만 그래도 다른 장치들에 비해 느린건 마찬가지이다. 

HDD는 헤드를 움직여 메모리를 읽고 쓰기 때문에 헤드가 움직이는 거리를 최소화 하는 것이 성능 향상의 가장 중요한 포인트이다. 

이를 가능하게 해주는게 커널의 I/O 스케줄러이다. 
커널의 I/O 스케줄러가 I/O 작업을 처리할때 헤드의 움직임을 최소화 하도록 스케줄링을 해준다.

I/O 스케줄러는 병합, 정렬 작업을 통해 스케줄링을 한다.

## 병합

병합은 여러 개의 요청을 하나로 합치는 것이다.

예를들어, 아래와 같은 작업이 할당 되어 있다고 가정하자.

- 작업1 : 접근 블록 주소: 10, 읽어야 하는 블록: 1
- 작업2 : 접근 블록 주소: 11, 읽어야 하는 블록: 1
- 작업3 : 접근 블록 주소: 12, 읽어야 하는 블록: 1

이렇게 연속된 주소의 작업들을 아래와 같이 하나의 작업으로 병합한다.

- 작업a : 접근 블록 주소: 10, 읽어야 하는 블록: 3

헤드의 움직인 총 거리는 같지만, 디스크로의 명령 전달을 최소화 할 수 있다.

## 정렬

정렬은 여러 I/O 요청을 섹터 순서대로 재배치 하는 것이다.

예를들어, 아래와 같은 순서로 섹터를 방문해야 한다고 가정해보자.

- 섹터 접근 : 1 -> 7 -> 3 -> 10

이런 경우에 헤드는 총 17만큼 움직어야 한다. 섹터 순서대로 아래대로 재정렬 할 수 있다.

- 섹터 접근 : 1 -> 3 -> 7 -> 10

이 경우 헤드는 총 9만큼만 움직이면 된다. 

SSD의 경우 헤드가 없고 메모리 주소마다 접근 시간이 동일하므로 SSD의 I/O 스케줄러에서 정렬은 의미가 없다. 
오히려, 정렬하는 시간만큼 손해를 볼 수 있다.  

# I/O 스케줄러 설정

```bash
$ cat /sys/block/{device}/queue/scheduler
noop [deadline] cfq
```

위 경로를 읽으면 디바이스에서 사용하는 I/O 스케줄러를 확인할 수 있다. 
(위 결과는 책의 결과를 가져온 )

```bash
$ ls /sys/block/vda/queue/iosched
fifo_batch  front_merges  read_expire  write_expire  writes_starved
```

위 경로에선 설정 가능한 파라미터를 확인 할 수 있다. 설정된 I/O 스케줄러에 따라 위 경로에서 설정 가능한 파라미터가 달라진다.

# cfq I/O 스케줄러

cfq(Completely Fair Queueing, 완전 공정 큐잉) I/O 스케줄러는 I/O 요청이 프로세스별로 공정하게 실행된다. 

![linux-kernel-04.png]({{ imgurl }}/linux-kernel-04.png)

각 프로세스에서 I/O 요청을 발생시키면 Block I/O Layer를 거쳐 cfq I/O 스케줄러에 도달한다. 

cfq에선 I/O 작업이 RT(Real Time), BE(Best Effort), IDLE 중 하나로 분류된다. 
이 분류는 우선순위 산정을 위한 것으로, RT 요청을 가정 먼저 처리하고, IDLE를 가장 마지막에 처리한다. 대부분 작업은 BE로 분류된다.

RT, BE, IDLE로 분류된 후 SYNC, SYNC_NOIDLE, ASYNC로 다시 분류된다. 

- SYNC: 순차적인 동기화 I/O 작업. 순차 읽기에 주로 쓰이며, 처리 완료 후 일정 시간 대기하는 특징이 있음. 
순차 작업이기 때문에 잠깐 대기해서 인접한 메모리 읽기 작업이 들어오면 효율이 좋기 때문에. 
- SYNC_NOIDLE: 임의적인 동기화 I/O 작업. 임의 읽기에 주로 쓰임. 읽고 대기시간 없이 다음 큐의 I/O 작업 수행.
- ASYNC: 비동기 I/O 작업. 주로 쓰기 작업.

cfq는 프로세스별로 동기 I/O 작업을 위한 큐를 만듬.
그리고 프로세스별 큐에 동일한 time slice를 할당해 프로세스별로 I/O 작업이 공펴하게 일어날 수 있게함. 

각 프로세스들이 비슷한 수준의 I/O 작업을 수행하면 좋지만, 한 프로세스만 많은 I/O 작업이 일어나는 곳에선 비효율적일 수 있음.

## cfq I/O 스케줄러 튜닝

cfq I/O 스케줄러의 경우 12가지의 튜닝 가능한 파라미터가 있다.

1. `back_seek_max`
2. `back_seek_penalty` 
3. `fifo_expire_async`
4. `fifo_expire_sync` 
5. `group_idle` 
6. `group_isolation`  
7. `low_latency`
8. `slice_idle`
9. `slice_sync`
10. `slice_async`
11. `slice_async_rq` 
12. `quantum`

필요할 때 책을 참고하자.

# deadline I/O 스케줄러

deadline I/O 스케줄러는 I/O 요청별로 완료되어야 하는 deadline을 가지고 있는 스케줄러이다.

ex) 읽기 요청은 500ms 안에 완료, 쓰기 요청은 5s 안에 완료 등..

![linux-kernel-05.png]({{ imgurl }}/linux-kernel-05.png)

위처럼 deadline I/O 스케줄러는 READ sorted list, READ fifo list, WRITE sorted list, WRITE fifo list가 있음

sorted list는 요청을 섹터 기준으로 정렬해 가지고 있고, fifo list는 요청을 발생 시간 기준으로 정렬해 가지고 있음. 

sorted list에서 순서대로 요청을 처리하다가, fifo list에 있는 요청 중 deadline을 넘긴 요청이 있다면 해당 요청을 꺼내 먼저 처리함.

fifo list에서 요청을 꺼내 처리했다면 처리한 요청의 섹터를 기준을 sorted list를 다시 정렬함.

## deadline I/O 스케줄러 튜닝

deadline I/O 스케줄러는 5가지의 파라미터를 가지고 있다.

1. `fifo_batch`
2. `front_merges`
3. `read_expire`
4. `write_expire`
5. `writes_starved`

마찬가지로, 필요할 때 책을 참고하자. 

# noop I/O 스케줄러

noop 스케줄러는 가장 간단한 형태의 스케줄러로 정렬을 하지 않는다. 병합만 한다.

![linux-kernel-06.png]({{ imgurl }}/linux-kernel-06.png)

큐도 하나만 존재한다. noop 스케줄러는 정렬이 필요없는. SSD를 위해 존재한다.

튜닝 가능한 파라미터도 없다!

# cfq와 deadline의 성능 테스트

cfq 스케줄러는 각 프로세스별로 I/O 작업을 공평하게 처리하고, deadline 스케줄러는 요청 시간에 따라 처리한다.

따라서, I/O 작업을 일으키는 프로세스가 여러 개일 경우 스케줄러에 따라 성능 차이를 보일 수 있다.

 또한, 애플리케이션에 따라서도 어떤 스케줄러를 사용할 지가 달라진다. 
 예를들어, 일반적인 웹 서버의 경우 주된 I/O 작업은 로그 기록이다. 
 로그 기록의 경우 로그 파일 끝에 계속 로그를 붙이는 순차 접근이 많고, I/O 요청도 많진 않다.
 반대로, 파일 서버의 경우 I/O 작업이 많고 위치 역시 제각각이다.
 
 자세한건 책을 참조.

# I/O 워크로드 살펴보기 

```bash
$ iotop -P
```

`iotop` 커맨드를 이용해 I/O를 일으키는 프로세스를 파악할 수 있다. 

순차 접근이 많은 지, 임의 접근이 많은 지 확인하기 위해선 기본 툴로는 힘들고 오픈 소스중 [perf-tools의 iosnoop](https://github.com/brendangregg/perf-tools/blob/master/iosnoop)을 이용할 것을 추천한다. 
