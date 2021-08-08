---
layout: post
title: free 명령이 숨기고 있는 것들
---

{% assign imgurl=site.data.common.path.image|append: '/'|append: page.categories[1] %}



# 메모리 사용량 확인하기

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:            983          97         400           0         485         745
Swap:             0           0           0
```

`free` 커맨드는 메모리 사용량을 가장 간편하게 확인할 수 있는 방법이다.



```
$ free -m
             total       used       free     shared    buffers     cached
Mem:         15909      15728        181          0        353       8655
-/+ buffers/cache:       6720       9189
Swap:         2047          0       2047
```

운영체제의 종류에 따라 위 처럼 `-/+ buffers/cache` 줄이 있을 수도 있다. 이 경우를 알면 위의 `-/+ buffers/cache` 가 없는 경우도 쉽게 파악 가능하다.

결과의 `Mem` 라인을 먼저 보자

- 전체 메모리 (total) : 15909 MB
- 사용중인 메모리 (used) : 15728 MB
- 사용하지 않는 메모리 (free) : 181 MB
- 프로세스 간 공유하는 메모리 (shared) : 0 MB (사실 400KB)
- 버퍼 용도 메모리 (buff) : 353 MB
- 캐시 용도 메모리 (cached) : 8655 MB



두번째인 `-/+ buffers/cache` 라인은 buff/cache 메모리를 used에서 빼고, free에 더한 결과이다.

- 버퍼/캐시 용도 제외 사용중인 메모리 : 6720 MB = 15728(mem used) - 353(buffers) - 8655(cached)
- 버퍼/캐시 용도 제외 사용하지 않는 메모리 : 9189 MB = 181(mem free) + 353(buffers) + 8655(cached) 



세번째 `Swap` 라인은 swap 영역에 대한 정보를 보여준다. 

- 전체 swap : 2047 MB
- 사용중인 swap : 0 MB
- 사용하지 않는 swap : 2047 MB



# buffers 와 cached 영역, swap 영역

## buffers/cached

커널이 디스크에서 파일을 읽어들이는 과정은 다른 장치들에 비해 매우 느리다. 그렇기 때문에 한 번 읽은 내용은 캐시에 저장해 놓는다. 이때 캐시용으로 사용되는 영역이 buffers, cached 이다.

- Page Cache : `free` 에서 `cached` 로 표현되는 영역. **파일 내용을 캐시**할 때 사용하는 영역이다. 
- Buffer Cache : `free` 에서 `buffers` 로 표현되는 영역. 파일 시스템의 메타 데이터를 캐시한다. (ex - 디렉터리 목록, 파일 목록 등..)



buffer / cache 영역은 커널이 남는 메모리를 디스크 접근을 빠르게 하기 위해 사용하는 것이다. 프로세스에서 메모리가 부족하게 되면 언제든지 다시 프로세스로 돌려주는 영역이다. 

그래서 `free` 명령어에서 `-/+ buffers/cache` 한 결과를 추가적으로 보여주는 것이다. (그리고 상위버전에선 아예 미리 계산하여 `-/+ buffers/cache` 줄도 없앤 것이다.)



## swap

swap은 다음 장에서 더 자세한 설명이 있다.

- Swap : 사용할 메모리가 없을 때 디스크를 메모리 대용으로 사용하는 것. swap을 사용하기 시작하면 시스템 성능이 크게 저하된다.



# /proc/meminfo 읽기

```
$ cat /proc/meminfo
MemTotal:        1006892 kB
MemFree:          410256 kB
MemAvailable:     763852 kB
Buffers:            2088 kB
Cached:           453960 kB
SwapCached:            0 kB
Active:           228248 kB
Inactive:         273796 kB
Active(anon):      41048 kB
Inactive(anon):      276 kB
Active(file):     187200 kB
Inactive(file):   273520 kB
Unevictable:           0 kB
Mlocked:               0 kB
SwapTotal:             0 kB
SwapFree:              0 kB
Dirty:                 0 kB
Writeback:             0 kB
AnonPages:         46036 kB
Mapped:            45636 kB
Shmem:               400 kB
Slab:              55508 kB
SReclaimable:      41028 kB
SUnreclaim:        14480 kB
KernelStack:        1900 kB
PageTables:         3856 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:      503444 kB
Committed_AS:     372832 kB
VmallocTotal:   34359738367 kB
VmallocUsed:           0 kB
VmallocChunk:          0 kB
HardwareCorrupted:     0 kB
AnonHugePages:         0 kB
ShmemHugePages:        0 kB
ShmemPmdMapped:        0 kB
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
DirectMap4k:       57344 kB
DirectMap2M:      991232 kB
```

`free` 커맨드보다 상세한 정보가 필요하면 `/proc/meminfo` 를 읽어보면 된다. 많은 값이 있지만 몇 가지만 확인한다.



## SwapCached

SwapCached : swap으로 빠졌다가 다시 메모리로 돌아온 영역.

swap 영역을 제외하고 가용 메모리가 없는데 추가 메모리 할당이 들어오면 커널은 메모리중 일부를 디스크에 잡아놓은 swap으로 옮겨 놓는다.

작업을 처리하고 메모리에 여유가 생기면 swap에 넣어놨던 데이터를 다시 메모리로 불러들인다.(SwapCached)

이때, swap에 썼던 것들은 삭제하지 않고 놓아둔다. (다시 보낼 수도 있으니까)



##### Active(anon)

Active(anon) : Page Cache 영역을 제외한 메모리 영역 중 최근 참조되어 swap으로 보내지 않을 메모리 영역



##### Inactive(anon)

Inactive(anon) : Page Cache 영역을 제외한 메모리 영역 중 참조된 지 오래되어 swap으로 보낼 수 있는 영역



##### Active(file)

Active(file) : 커널이 I/O 성능 향상을 위해 사용하는 영역 중 최근 참조되어 swap으로 보내지 않을 메모리 영역



##### Inactive(file)

Inactive(file) : 커널이 I/O 성능 향상을 위해 사용하는 영역 중 참조된 지 오래되어 swap으로 보낼 수 있는 영역



##### Dirty

dirty : 커널이 I/O 작업시 지연 쓰기 작업에 사용하는 영역. I/O 쓰기 작업 발생시 바로 블록 디바이스에 명령하지 않고 dirty에 모아둿다가 한 번에 쓰기 작업을 수행함.





# slab 메모리 영역

커널도 하나의 프로세스이기 때문에 커널도 메모리를 사용한다. 그런데 커널은 다른 프로세스와 조금 다른 방법으로 메모리를 할당 받는다.

```
Slab:              55508 kB
SReclaimable:      41028 kB
SUnreclaim:        14480 kB
```

`/proc/meminfo` 의 내용중 `Slab` 영역이 커널이 사용하는 메모리 영역이다.

- Slab : 커널이 직접 사용하는 메모리 영역
- SReclaimable : Slab 영역 중 재사용 될 수 있는 영역 (캐시용도로 사용하고 있는 영역, 다른 프로세스의 가용 메모리 부족시 이 부분을 해제해 다른 프로세스에 할당해줌)
- SUnreclaim : Slab 영역 중 재사용 될 수 없는 영역



메모리는 버디 시스템을 통해 할당된다. 그런데, 버디시스템은 기본적으로 4KB 페이지 단위로 메모리를 할당한다. 

그런데 커널은 매번 4KB 까지 큰 영역을 할당 받을 필요가 없다. 그래서 커널이 사용하는 메모리는 버디 시스템으로 부터 4KB를 받고 이걸 좀 더 작은 단위로 나눠서 내부적으로 할당하기 위해 Slab 할당자를 사용한다.



# Slab 메모리 누수 - 저자 사례

저자가 근무시 서버에서 메모리 누수 정황이 포착됐다.

그런데 `free` 명령으로 확인했을땐 많은 메모리를 사용하고 있었지만 `ps` 명령어로 주요 프로세스들의 메모리 크기를 더해보니 절반밖에 되지 않았다.

자세히 살펴보면 Slab 영역에서 상당히 많은 양의 메모리를 사용하고 있었다. 

파일 시스템의 디렉터리에 대한 정보를 캐시하는 `dentry cache` 영역이 많이 사용되고 있었는데 자세히 살펴보니 cron 잡으로 돌려놓은 작업에서 사용한 `nss-softokn` 라이브러리에 버그가 있었다고 한다. 

boo

