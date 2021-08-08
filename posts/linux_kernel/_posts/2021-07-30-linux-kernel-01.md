---
layout: post
title: 시스템 구성 정보 확인하기
---

{% assign imgurl=site.data.common.path.image|append: '/'|append: page.categories[1] %}



글에서 쓰인 서버는 AWS EC2 프리티어로 생성한 인스턴스, macos 두 가지이다.



# 커널 정보 확인

```
$ uname -a
Linux ip-172-31-36-100.us-east-2.compute.internal 4.14.238-182.422.amzn2.x86_64 #1 SMP Tue Jul 20 20:35:54 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
```

`-a` 옴션을 주면 모든 커널 정보를 가져올 수 있다. `man uname` 으로 어떤 옵션을 주면 본인이 필요한 정보만 뽑을 수 있을 지 알 수 있다.



```
$ dmesg
[    0.000000] Linux version 4.14.238-182.422.amzn2.x86_64 (mockbuild@ip-10-0-1-132) (gcc version 7.3.1 20180712 (Red Hat 7.3.1-13) (GCC)) #1 SMP Tue Jul 20 20:35:54 UTC 2021
[    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz-4.14.238-182.422.amzn2.x86_64 root=UUID=04b92f2f-4366-4687-868b-7c403cc59901 ro console=tty0 console=ttyS0,115200n8 net.ifnames=0 biosdevname=0 nvme_core.io_timeout=4294967295 rd.emergency=poweroff rd.shell=0
[    0.000000] x86/fpu: Supporting XSAVE feature 0x001: 'x87 floating point registers'
[    0.000000] x86/fpu: Supporting XSAVE feature 0x002: 'SSE registers'
[    0.000000] x86/fpu: Supporting XSAVE feature 0x004: 'AVX registers'
[    0.000000] x86/fpu: xstate_offset[2]:  576, xstate_sizes[2]:  256
[    0.000000] x86/fpu: Enabled xstate features 0x7, context size is 832 bytes, using 'standard' format.
[    0.000000] e820: BIOS-provided physical RAM map:
```

`dmesg` 는 커널 디버그 메세지이다. 커널에서 나오는 로그들을 확인할 수 있다. 커널 부팅시 어떤 파라미터가 쓰였는 지 등..

`Command line` 에서 커널 실행시 파라미터 들을 확인 할 수 있다. 추후에 궁금해지면 다시 보자.



# CPU 정보 확인

```
$ man dmidecode
```

`dmidecode` 커맨드를 이용해 하드웨어 정보를 확인할 수 있다. 역시 `man dmidecode` 를 통해 필요한 옵션을 주자.



```
$ sudo dmidecode -t bios
# dmidecode 3.0
Getting SMBIOS data from sysfs.
SMBIOS 2.7 present.

Handle 0x0000, DMI type 0, 24 bytes
BIOS Information
	Vendor: Xen
	Version: 4.2.amazon
	Release Date: 08/24/2006
	Address: 0xE8000
	Runtime Size: 96 kB
	ROM Size: 64 kB
	Characteristics:
		PCI is supported
		EDD is supported
		Targeted content distribution is supported
	BIOS Revision: 4.2
```

아마존의 EC2는 Xen 사에서 만든 BIOS를 사용중이다.



```
$ sudo dmidecode -t system
# dmidecode 3.0
Getting SMBIOS data from sysfs.
SMBIOS 2.7 present.

Handle 0x0100, DMI type 1, 27 bytes
System Information
	Manufacturer: Xen
	Product Name: HVM domU
	Version: 4.2.amazon
	Serial Number: ec******-****-****-****-******f2520
	UUID: EC******-****-****-****-******2520
	Wake-up Type: Power Switch
	SKU Number: Not Specified
	Family: Not Specified

Handle 0x2000, DMI type 32, 11 bytes
System Boot Information
	Status: No errors detected
```

Xen사에서 만든 HVM domU 라는 모델을 사용중이다.  시리얼 넘버도 확인할 수 있다.



```
$ sudo dmidecode -t processor
# dmidecode 3.0
Getting SMBIOS data from sysfs.
SMBIOS 2.7 present.

Handle 0x0401, DMI type 4, 26 bytes
Processor Information
	Socket Designation: CPU 1
	Type: Central Processor
	Family: Other
	Manufacturer: Intel
	ID: F2 ** ** ** ** ** ** 17
	Version: Not Specified
	Voltage: Unknown
	External Clock: Unknown
	Max Speed: 2395 MHz
	Current Speed: 2395 MHz
	Status: Populated, Enabled
	Upgrade: Other
```

CPU 정보를 확인해 볼 수도 있다. Intel사의 CPU를 사용중이다. 1코어 짜리를 빌려서 하나 밖에 안나오지만 여러 코어를 가지고 있으면 여러 개의 프로세스 정보가 나온다.



```
$ cat /proc/cpuinfo
$ lscpu
```

cpu에 대한 정보는 위 커맨드로도 찾아볼 수 있다.



# 메모리 정보 확인

```
$ dmidecode -t memory
# dmidecode 3.1
Getting SMBIOS data from Apple SMBIOS service.
SMBIOS 3.1 present.

Bad address
Handle 0x0000, DMI type 16, 23 bytes
Physical Memory Array
	Location: System Board Or Motherboard
	Use: System Memory
	Error Correction Type: None
	Maximum Capacity: 32 GB
	Error Information Handle: Not Provided
	Number Of Devices: 2

Handle 0x0001, DMI type 17, 84 bytes
Memory Device
	Array Handle: 0x0000
	Error Information Handle: Not Provided
	Total Width: 64 bits
	Data Width: 64 bits
	Size: 8192 MB
	Form Factor: **********
	Set: None
	Locator: **********
	Bank Locator: **********
	Type: DDR4
	Type Detail: Synchronous
	Speed: 2667 MT/s
	Manufacturer: **********
	Serial Number: **********
	Asset Tag: **********
	Part Number: **********
	Rank: 1
	Configured Clock Speed: 2667 MT/s
	Minimum Voltage: Unknown
	Maximum Voltage: Unknown
	Configured Voltage: 1.2 V

Handle 0x0002, DMI type 17, 84 bytes
Memory Device
	Array Handle: 0x0000
	Error Information Handle: Not Provided
	Total Width: 64 bits
	Data Width: 64 bits
	Size: 8192 MB
	Form Factor: **********
	Set: None
	Locator: **********
	Bank Locator: **********
	Type: DDR4
	Type Detail: Synchronous
	Speed: 2667 MT/s
	Manufacturer: **********
	Serial Number: **********
	Asset Tag: **********
	Part Number: **********
	Rank: 1
	Configured Clock Speed: 2667 MT/s
	Minimum Voltage: Unknown
	Maximum Voltage: Unknown
	Configured Voltage: 1.2 V
```

`dmidecode` 에 `-t memory` 옵션을 주면 메모리 하드웨어 정보를 확인할 수 있다. 



![linux-kernel-01-numa.png]({{ imgurl }}/linux-kernel-01-numa.png)
<small>출처 : https://software.intel.com/content/www/us/en/develop/articles/use-intel-quickassist-technology-efficiently-with-numa-awareness.html</small>

위 메모리 정보는 Physical Memory Array와 Memory Device로 나눠볼 수 있다.

요즘 CPU에는 NUMA라는 개념이 적용되어 CPU 마다 메모리를 할당해준다. 위의 Physical Memory Array 가 할당 할 수 있는 메모리의 수준을 보여준다. 위에서 보면 2개의 메모리를 장착할 수 있고 최대 크기는 32GB 이다.

Memory Device는 실제 탑재된 메모리이다. 위에 보면 두 개가 하나의 Array Handle에 연결되어 있고, 각각 8GB이다.

즉, 위 맥북은 최대 메모리를 32GB 까지 올릴 수 있지만 현재 16GB만 사용중이고, 메모리를 올리기 위해선 메모리를 현재 상태에서 추가하는 게 아니라(최대 연결 갯수가 2개 이기 때문에) 탑재하고 있는 메모리를 교체해야 한다.

 

```
free
```

dmidecode에서 알아본 메모리는 하드웨어 수준에서 확인하는 방법이다. 

메모리를 확인할때 주로 사용하는 `free` 커맨드는 시스템이 인식하고 있는 메모리를 보여준다. dmidecode로 계산한 메모리와 free로 계산한 메모리 총량이 다르다면 메모리 인식에 문제가 있는 것이다.



# 디스크 정보 확인

책에 소개된 것들은 VM 환경에서는 확인 할 수 없는 것들이 대부분이라 스킵.

추후 sda, vda, IDE, SCSI, SATA, SAS 등의 키워드가 궁금해지면 확인.



# 네트워크 정보 확인하기

```
$ lspci
00:00.0 Host bridge: Intel Corporation 440FX - 82441FX PMC [Natoma] (rev 02)
00:01.0 ISA bridge: Intel Corporation 82371SB PIIX3 ISA [Natoma/Triton II]
00:01.1 IDE interface: Intel Corporation 82371SB PIIX3 IDE [Natoma/Triton II]
00:01.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 01)
00:02.0 VGA compatible controller: Cirrus Logic GD 5446
00:03.0 Unassigned class [ff80]: XenSource, Inc. Xen Platform Device (rev 01)
```

위 명령을 통해 네트워크 카드의 정보를 알아낼 수 있다. 어떤 제조사의 어떤 모델을 쓰는 지 등..



```
ethtool eth0
```

위 명령을 사용하면 네트워크 카드에 대한 보다 상세한 정보를 알 수 있다.

​	네트워크 카드가 어느 속도까지 지원 가능한지.
​	현재 연결되어 있는 속도는 얼마 인지
​	네트워크 연결은 정상적인지 
​	Ring Buffer 크기 (maximum, current)

EC2 인스턴스에선 나오는게 없어서 스킵!
