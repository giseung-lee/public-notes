---
layout: post
title: NUMA, 메모리 관리의 새로운 세계
---

{% assign imgurl=site.baseurl |append: site.data.common.path.image|append: '/'|append: page.categories[1] %} 



# NUMA 아키텍처

![linux-kernel-02-uma-numa.png]({{ imgurl }}/linux-kernel-02-uma-numa.png)<small>출처 : Realizing High Performance NFV Service Chains</small>

NUMA : Non-Uniform Memory Access. 불균형 메모리 접근. 

멀티 프로세서 환경에서 적용되는 메모리 접근 방식이다. 이와 반대는 UMA 이다. 

UMA는 초기 아키텍처로 모든 cpu가 공용 BUS를 통해 메모리에 접근한다. 따라서 한 CPU가 메모리에 접근하는 동안 다른 CPU의 메모리 접근이 제한된다.

NUMA는 CPU마다 메모리를 할당해준다. 본인이 할당 받은 메모리를 로컬 메모리라고 부르고, 로컬 메모리에 접근하는 걸 Local Access 라고 부른다. 물론, 다른 CPU에 할당된 메모리도 사용할 수 있는데, 이렇게 접근하는 걸 Remote Access 라고 부른다.

당연히 Local Access가 많이 일어날 수록 성능은 향상된다. 



# 리눅스에서의 NUMA 확인

```
$ numactl --show
policy: default
preferred node: current
physcpubind: 0
cpubind: 0
nodebind: 0
membind: 0
```

`numactl` 이라는 툴을 이용해 numa 정책을 확인할 수 있다. 

- default : 현재 사용중인 프로세스가 포함된 노드에서 메모리를 먼저 가져와 사용 
- bind : 특정 프로세스를 특정 노드에 바인딩해 해당 노드에서만 메모리를 가져와 사용
- preferred : bind는 바인딩한 노드에서만 메모리를 가져오지만 preferred는 가능하면 바인딩한 노드에서 가져온다. 바인딩한 노드에 여유 메모리가 없을 땐 다른 노드에서 가져온다. 
- interleaved : 다수의 노드에서 거의 동일한 비율로 메모리를 가져온다. 



```
$ numastat
                           node0
numa_hit                 2959243
numa_miss                      0
numa_foreign                   0
interleave_hit             13637
local_node               2959243
other_node                     0
```

`numastat` 을 사용하면 각 노드당 할당된 메모리 상태를 확인할 수 있다. (위는 Core 1개짜리 인스턴스라 node가 하나 밖에 표시되지 않는다.)

`numastat` 을 이용해 **메모리의 불균형 상태를 확인**할 수 있다. 메모리 불균형이 일어나면 정책에 따라 다른 노드의 메모리가 남는데도 swap을 사용할 수도 있다. 



```
$ sudo cat /proc/2912/numa_maps
559f2694c000 default file=/usr/libexec/postfix/master mapped=36 N0=36 kernelpagesize_kB=4
559f26b72000 default file=/usr/libexec/postfix/master anon=2 dirty=2 N0=2 kernelpagesize_kB=4
559f26b74000 default file=/usr/libexec/postfix/master anon=1 dirty=1 N0=1 kernelpagesize_kB=4
559f26b75000 default anon=1 dirty=1 N0=1 kernelpagesize_kB=4
	:
```

`/proc/${PID}/numa_maps` 를 확인하면 프로세스별로 numa 정책이 어떻게 되어 있는 지를 확인할 수 있다. 위는 모두 default로 설정되어 있다. 



# 메모리 할당 정책별 특징

테스트 앱을 만들어 메모리 할당 정책별로 테스트. 책을 참조. 



# numad를 이용한 메모리 할당 관리

`numad` 는 NUMA 정책을 직접 설정하지 않아도 메모리의 지역성을 높여주는 툴이다. 

`numad` 는 백그라운드 데몬으로 실행되어 프로세스들의 메모리 할당 과정을 모니터링 하고 메모리를 최적화한다. 

`numad` 는 대체로 잘 동작하지만, NUMA 정책이 여러 가지로 섞여있는 상황에선 예기치 못한 메모리 불균형이 발생하기도 한다. 내가 직접 쓸일이 있을까.. 생각하지만 사용 하려면 잘 알아보고 쓰자. 



# vm.zone_reclaim_mode 커널 파라미터

```
$ cat /proc/buddyinfo
Node 0, zone      DMA      0      0      0      1      2      1      1      0      1      1      3
Node 0, zone    DMA32    395    203     95     65     51     44     65     28     10      5     73
```

지난 장에서 살펴본 `buddyinfo` 이다. 여기서 볼 수 있는 것 처럼 메모리는 Zone으로 나눠 관리된다. 

위는 Node 0가 DMA 존과 DMA32 존으로 나눠 관리되는 것을 확인할 수 있다. 

- DMA, DMA32 : Direct Memory Access. 과거 일부 하드웨으는 DMA 존에서만 동작 했다고 한다. 이를 위해 만들어진 존이다. 요즘 하드웨어는 존을 가리지 않는다.
- Normal : 일반적인 용도로 사용되는 Zone이다. 



`vm.zone_reclaim_mod` 파라미터는 특정 Zone의 메모리가 부족할 경우 다른 Zone의 메모리를 할당 할 수 있게 해주는 파라미터이다. 

해당 파라미터는 NUMA 이전 부터 존재했지만, NUMA가 등장한 이후 더 중요해졌다. 예를들어, Node0의 Normal Zone 메모리가 부족한 상황을 가정하자. 이때 선택지는 다른 Node의 normal zone을 사용하는 것, Node0의 다른 zone을 사용하는 것이다.

- `vm.zone_reclaim_mod=0` : disable. zone 안에서 재할당 하지 않는다. 즉, 위 상황에서 다른 node의 같은 zone을 가져오는게 아니라 다른 zone을 먼저 찾게 된다. 
- `vm.zone_reclaim_mod=1` : enable. zone 안에서 재항당 한다.  다른 node의 같은 zone을 찾아 재할당 한다. 



# NUMA 아키텍처의 메모리 할당 정책과 워크로드

너무 당연한 얘기지만 NUMA 아키텍처의 정책활용은 시스템에 따라 달라진다. 

가장 먼저 파악해야할 건 프로세스의 메모리 크기와 스레드 갯수이다.

| 스레드 개수 | 메모리 크기                            |
| ----------- | -------------------------------------- |
| 싱글 스레드 | 메모리 크기 < 노드 한 개의 메모리 크기 |
| 멀티 스레드 | 메모리 크기 < 노드 한 개의 메모리 크기 |
| 싱글 스레드 | 메모리 크기 > 노드 한 개의 메모리 크기 |
| 멀티 스레드 | 메모리 크기 > 노드 한 개의 메모리 크기 |



##### 싱글 스레드 && (메모리 크기 < 노드 한 개의 메모리 크기)

이런 상황은 거의 없다. 하지만, 이런 상황이 있을 경우엔 BIND 정책으로 특정 CPU에 해당 프로세스를 바인딩 시키는 것이 좋을 수 있다. 



##### 멀티 스레드 && (메모리 크기 < 노드 한 개의 메모리 크기)

전체 메모리 크기가 노드 한 개의 메모리 크기를 넘지 않기 때문에 메모리는 하나의 노드에서만 가져오도록 설정 하는 게 좋다. `cpunodebind` 모드를 통해 프로세스를 여러 CPU 코어에 바인딩 시킬 수 있다.



##### 싱글 스레드 && (메모리 크기 > 노드 한 개의 메모리 크기)

싱글 스레드로 동작하고 메모리 크기가 노드 한 개를 넘을 때는 Remote Access가 발생할 수 밖에 없다. 

싱글 스레드라면 CPU Cache사용을 최적화 하기 위해 하나의 CPU에 바인딩 되도록 `cpunodebind` 정책을 설정 하는 게 좋다. 

메모리는 어차피 여러 노드에서 가져와야 하기 때문에 처음부터 다수의 노드에서 메모리를 가져오는 게 좋다. 



##### 멀티 스레드 && (메모리 크기 > 노드 한 개의 메모리 크기)

대다수의 어플리케이션이 이 경우에 속할 것이다. 멀티 스레드라서 여러 CPU에서 동작하고, Remote Access도 항상 일어날 수 밖에 없다. 

interleave 모드가 최적일 것이다. 
