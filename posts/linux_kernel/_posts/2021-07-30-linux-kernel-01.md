---
layout: post
title: 시스템 구성 정보 확인하기
---

{% assign imgurl=site.data.common.path.image|append: '/'|append: page.categories[1] %}



# 커널 정보 확인

```
$ uname -a
Linux giseung.lee 3.10.0-1160.11.1.el7.x86_64 #1 SMP Fri Dec 18 16:34:56 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
```

`-a` 옴션을 주면 모든 커널 정보를 가져올 수 있다. `man uname` 으로 어떤 옵션을 주면 본인이 필요한 정보만 뽑을 수 있을 지 알 수 있다.



```
dmesg
```

`dmesg` 는 커널 디버그 메세지이다. 커널에서 나오는 로그들을 확인할 수 있다. 커널 부팅시 어떤 파라미터가 쓰였는 지 등..

실행해보면 알 수 없는 수 많은 로그가 나온다. 나중에 필요하면 다시 찍어보자.



# CPU 정보 확인

```
dmidecode
dmidecode -t bios
dmidecode -t system
dmidecode -t processor
```

위 커맨드를 이용해 하드웨어 정보를 확인할 수 있다. 역시 `man dmidecode` 를 통해 필요한 옵션을 주자.

`dmidecode` 가 없다면 설치해주자. `brew install cavaliercoder/dmidecode/dmidecode` 



```
cat /proc/cpuinfo
```

`dmidecode` 커맨드가 없다고 좌절하지 말자. 리눅스라면 위 경로의 파일을 읽으면 cpu 정보들이 나온다.



```
lscpu
```

위 커맨드 역시 cpu에 대한 정보를 보여준다. 본인 리눅스에 있는 커맨드를 잘 찾아서 쓰자.



# 메모리 정보 확인

```
dmidecode -t memory
```

위 커맨드를 통해 현재 메인보드에서 사용할 수 있는 메모리 갯수 및 최대 메모리, 현재 메모리 등을 확인 할 ㅅ ㅜ있다.

```
➜  ~ dmidecode -t memory | grep -i size
	Size: 8192 MB
	Size: 8192 MB
```

그냥 확인하면 부가 저옵가 너무 많으니 size만 뽑아서 출력해보자. 

```
free
```

dmidecode는 하드웨어 수준에서 확인하는 방법이고, free는 시스템이 인식하고 있는 메모리이다. dmidecode로 계산한 메모리와 free로 계산한 메모리 총량이 다르다면 메모리 인식에 문제가 있는 것.



# 디스크 정보 확인

```
➜  ~ df -h
Filesystem      Size   Used  Avail Capacity iused      ifree %iused  Mounted on
/dev/disk1s1   466Gi   10Gi  163Gi     7%  488354 4881964526    0%   /
devfs          195Ki  195Ki    0Bi   100%     674          0  100%   /dev
/dev/disk1s2   466Gi  286Gi  163Gi    64% 4182668 4878270212    0%   /System/Volumes/Data
/dev/disk1s5   466Gi  5.0Gi  163Gi     3%       5 4882452875    0%   /private/var/vm
map auto_home    0Bi    0Bi    0Bi   100%       0          0  100%   /System/Volumes/Data/home
/dev/disk2s2    56Mi   33Mi   23Mi    59%     767 4294966512    0%   /Volumes/Typora
/dev/disk3s1   816Mi  541Mi  275Mi    67%     688 4294966591    0%   /Volumes/Whale
```

시스템이 디스크와 통신하기 위해서 '컨트롤러'를 거침. 시스템과 디스크 사이의 중개자 역할을 하는 것.

컨트롤러에 IDE 타입과 SCSI 타입이 있음.
	IDE : 개인용 컴퓨터에 사용 -> /dev/hda 라고 표시됨
	SCSI : 서버용 컴퓨터에 사용. 더 많은 장치를 연결 할 수 있고 접근 속도도 빠름. -> /dev/sda 라고 표시됨

/dev/vda -> 가상화된 디스크들(VM에서 확인하면 나옴)



```
smartctl -a /dev/sda -d cciss,0
```

smartctl 툴을 이용하면 디스크의 물리 정보를 확인할 수 있다.

시리얼 번호, 펌웨어 버전 까지 알 수 있다. 디스크에 대한 디테일한 정보가 필요할 때 사용해보자.



# 네트워크 정보 확인하기

```
lspci
```

위 명령을 통해 네트워크 카드의 정보를 알아낼 수 있다.



```
ethtool eth0
```

위 명령을 사용하면 네트워크 카드에 대한 보다 상세한 정보를 알 수 있다.

​	네트워크 카드가 어느 속도까지 지원 가능한지.
​	현재 연결되어 있는 속도는 얼마 인지
​	네트워크 연결은 정상적인지 
​	Ring Buffer 크기 (maximum, current)

