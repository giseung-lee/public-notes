---
layout: post
title: swap, 메모리 증설의 포인트
---

{% assign imgurl=site.data.common.path.image|append: '/'|append: page.categories[1] %}



# swap 영역

커널은 프로세스에게 메모리를 할당할 때 다음과 같은 순서로 할당한다.

- 기본 가용 메모리
- buffers/cached 메모리 
- swap 메모리

swap 메모리는 디스크를 메모리처럼 사용하는 비상용 메모리이다. 디스크에 I/O가 일어나야 하기 때문에 시스템의 성능 저하가 일어난다. 

swap 영역을 사용한다는 것 자체가 시스템의 메모리 부족을 의미한다. 

그런데, swap의 사용 여부만큼 중요한게 누가 swap을 사용하는 지이다. 



```
$ cat /proc/2719/smaps
	:
Size:                  8 kB
KernelPageSize:        4 kB
MMUPageSize:           4 kB
Rss:                   4 kB
Pss:                   0 kB
Shared_Clean:          4 kB
Shared_Dirty:          0 kB
Private_Clean:         0 kB
Private_Dirty:         0 kB
Referenced:            4 kB
Anonymous:             0 kB
LazyFree:              0 kB
AnonHugePages:         0 kB
ShmemPmdMapped:        0 kB
Shared_Hugetlb:        0 kB
Private_Hugetlb:       0 kB
Swap:                  0 kB
SwapPss:               0 kB
Locked:                0 kB
VmFlags: rd ex mr mw me de sd
	:
```

프로세스의 PID를 구해 `/proc/${PID}/smaps` 를 읽어보면 프로세스가 사용하는 메모리 정보를 확인할 수 있다. 여기서 Swap 영역을 확인할 수 있다. 그런데 `smaps` 는 프로세스의 메모리 영역별로 보여주기 때문에 한 눈에 파악하기 어렵다.

```
$ cat /proc/2719/status
Name:	dhclient
Umask:	0022
State:	S (sleeping)
	:
VmSwap:	       0 kB
	:
```

`/proc/${PID}/status` 에선 프로세스의 swap 메모리 상태를 합하여 확인할 수 있다.



```
$ smem -t
  PID User     Command                         Swap      USS      PSS      RSS
 2053 ec2-user -bash                              0     2332     2559     4288
 2254 ec2-user python ./smem -t                   0     6880     7133     9128
-------------------------------------------------------------------------------
    2 1                                           0     9212     9692    13416
```

[smem](https://www.selenic.com/smem)  이라는 툴은 `/proc/${PID}` 에 있는 정보를 바탕으로 전체 프로세스의 Swap 메모리 사용을 쉽게 파악할 수 있게 해준다.



# 버디 시스템

커널은 버디 시스템을 이용해 프로세스에게 메모리를 할당한다.

버디 시스템은 기본적으로 4KB 단위로 메모리를 관리하는데, 메모리의 단편화를 막기위해 4KB를 n개씩 묶어 관리한다. (ex - 8KB의 메모리 요청이 들어왔을 때 멀리 떨어진 4KB 두 개를 할당 하는 게 아니라 미리 묶어둔 인접한 4KB 두 개를 할당 한다.)

```
$ cat /proc/buddyinfo
                         4KB    8KB   16KB   32KB   64KB  128KB  256KB  512KB 1024KB 2048KB 4096KB
Node 0, zone      DMA      0      0      0      1      2      1      1      0      1      1      3
Node 0, zone    DMA32    346    105     51     22     11     32     50     24     10     10     80
```

버디 시스템의 현황은 `/proc/buddyinfo` 에서 확인 가능하다.





# 메모리 재할당 과정

커널이 메모리를 재할당 하는 경우는 주로 두 가지이다.

1. 커널이 사용하는 캐시 메모리(Page Cache, Buffer Cache, inode cache, dentry cache) 재할당
2. swap 메모리 재할당

Swap 메모리의 재할당은 다음과 같은 과정으로 일어난다.

1. 프로세스가 메모리 할당을 요청하지만 가용 메모리 및 캐시 메모리를 모두 사용한 상황
2. Swap 메모리 할당 시작
3. 현재 메모리중 Inactive 리스트에 있는 메모리를 Swap 영역으로 이동
4. 옮긴 Inactive 메모리 해제
5. 요청 받은 프로세스에게 메모리 할당
6. swap에 넣어놓은 메모리를 찾으려고 할 땐 다른 메모리를 swap에 넣고 swap에 있던 메모리를 불러들임

디스크 I/O 작업이 일어나 성능이 저하된다. 



# vm.swappiness와 vm.vfs_cache_pressure

##### vm.swappiness

위에서 swap을 사용할 때가 `프로세스가 메모리 할당을 요청하지만 가용 메모리 및 캐시 메모리를 모두 사용한 상황` 라고 했지만, 사실 이건 `vm.swappiness` 커널 파라미터에 따라 달라진다. 

vm.swappiness : 가용 메모리 부족 상황시 캐시 메모리를 사용할 지, swap 메모리를 사용할 지의 정도를 결정. 값이 커질수록 swap을 주로 사용, 작아질수록 캐시 메모리를 먼저 사용.

`vm.swappiness` 같은 파리미터를 제공하는 이유는 **경우에 따라 캐시 메모리를 사용하는 것보다 swap 메모리를 사용하는게 성능에 좋은 수 있기 때문이다.** 

예를들어, 캐시 메모리는 디스크 I/O 작업시 사용하는데 서버의 애플리케이션이 디스크 I/O 작업이 많이 필요한 프로세스일 경우 캐시 메모리를 사용하는 것보다 swap 메모리를 사용하는 게 좋을 수 있다.



##### vm.vfs_cache_pressure

`vm.vfs_cache_pressure` 커널 파라미터는 캐시 메모리를 재할당 할 때 Page Cache를 사용할 지, inode 캐시를 사용할 지의 정도를 결정하는 파라미터이다. 



# 메모리 증설의 포인트

swap을 사용하기 시작했다면 일단 메모리에 문제가 있는 상황이다. 

이때, 메모리 누수가 문제인지, 단순히 메모리의 총량이 문제인지 판단해야 한다.

보통 메모리 누수는 메모리가 선형적으로 증가하는 현상을 보인다. 



# gbd

```
$ gbd -p ${PID}
```

[gbd](https://www.gnu.org/software/gdb/)와 같은 툴을 이용해 메모리 누수를 잡을 수 있다. 

해당 툴은 메모리의 증분에 대해 덤프를 생성할 수 있다. 





