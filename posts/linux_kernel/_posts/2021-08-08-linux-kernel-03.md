---
layout: post
title: Load Average와 시스템 부하
---

{% assign imgurl=site.baseurl |append: site.data.common.path.image|append: '/'|append: page.categories[1] %} 



# Load Average의 정의

```
$ top
top - 05:02:06 up  3:39,  1 user,  load average: 0.00, 0.00, 0.00
```

`top` 커맨드 첫 줄에서 볼 수 있는 load average.

**Load Average : R or D 상태에 있는 프로세스 갯수의 1분, 5분, 15분 마다의 평균값**

- R : Running. 실행중인 프로세스
- D : Uninterruptible sleep. 디스크, 네트워크 I/O 대기중인 프로세스

Load average는 단순히 프로세스의 평균 갯수이다. 따라서 값 자체가 의미가 있다기보단 CPU Core 갯수 등을 함께 보아야 한다. (`CPU Core = 1` 일때 `load average = 3` 과 `CPU Core = 3` 일때 `load average = 3` 은 의미가 다르다)



# Load Average의 계산 과정

Load Average의 계산과정 자체보다 리눅스에서 어떻게 디버깅을 하는 지 위주로 보면 좋다.

```
$ strace uptime
	:
openat(AT_FDCWD, "/proc/loadavg", O_RDONLY) = 4
lseek(4, 0, SEEK_SET)                   = 0
read(4, "0.00 0.00 0.00 3/107 638\n", 8191) = 25
fstat(1, {st_mode=S_IFCHR|0620, st_rdev=makedev(0x88, 0), ...}) = 0
write(1, " 05:05:31 up  3:42,  1 user,  lo"..., 61 05:05:31 up  3:42,  1 user,  load average: 0.00, 0.00, 0.00
) = 61
	:
```

`uptime` 커맨드를 사용하면 load average를 확인할 수 있다. 이때 `strace uptime` 을 실행하면 load average를 계산하기 위해 어떤 작업을 하는지 추적할 수 있다.

중간에 `/proc/loadavg` 라는 파일을 열어 읽고(`read()`) 통계를 내어(`fstat`) 출력(`write()`) 하는 걸 볼 수 있다.



```
$ cat /proc/loadavg
0.00 0.00 0.00 1/105 646
```

위 파일을 열어보면 loadavg 값을 확인할 수 있다. 이 값이 어떻게 만들어지는지 궁금하다면 리눅스 소스코드의 [fs/proc/loadavg.c](https://github.com/torvalds/linux/blob/9269d27e519ae9a89be8d288f59d1ec573b0c686/fs/proc/loadavg.c) 를 참고하면 된다. 



# CPU Bound vs I/O Bound

앞서 말했듯이, Load Average는 R 상태와 D 상태의 프로세스 갯수이다. 따라서 Load Average가 높을때 어디에 병목이 걸리고 있는지는 확인해 봐야 한다. (I/O에 병목이 있을 수도 있으므로)

R 상태의 프로세스는 CPU 자원을 소모하고, D 상태 프로세스는 I/O 자원을 소모한다. 



# vmstat으로 부하의 정체 확인

```
$ vmstat
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 0  0      0 408776   2088 494664    0    0    15    22   19   46  0  0 100  0  0
```

Load Average가 높을때 어떤 부하가 걸리는지 확인하려면 `vmstat` 을 사용해볼 수 있다.

가장 왼쪽에 r 필드와 b 필드를 볼 수 있다.

- r : 실행대기 중 또는  실행중인 프로세스 갯수
- b : I/O를 위해 대기열에 있는 프로세스

같은 수준의 Load Average라도 vmstat을 사용해 확인하면 다른 원인으로 인해 부하가 걸린걸 확인 해볼 수 있다.



# Load Average가 시스템에 끼치는 영향

Load Average가 CPU 기반인지, I/O 기반인지에 따라 시스템에 끼치는 영향이 다르다.

예를들어, 현재 Load Average가 높인 이유가 I/O 대기 프로세스 때문이라면 CPU 자원만 소모하는 프로세스에는 영향이 적을 수 있다.

반대로, CPU 자원 소모 때문에 Load Average가 높다면 I/O 자원 소모의 비중이 큰 프로세스의 영향은 적을 수도 있다. 



# OS 버전과 Load Average - 저자 사례

저자는 같은 애플리케이션을 다른 OS 버전(CentOS 6.5, CentOS 7.2)에서 일정기간 운영해야할 일이 있었다고 한다. 그런데 두 OS에서 Load Average가 상당히 다르게 집계가 되었었는데, 원인을 파악해보니 CentOS 6.5의 커널 버그였다고 한다.

해당 사례는 명확한 버그였지만, OS에 따라서 단순히 집계 방식이 조금 달라 Load Average등의 수치가 달라질 수 있다고 한다. 

