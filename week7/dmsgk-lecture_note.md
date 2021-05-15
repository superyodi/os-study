# Chap 11. File System Implementation



## Allocation of File Data in Disk

파일을 디스크에 저장하는 방식(이론적 내용)

- Contiguous Allocation
- Linked Allocation
- Indexed Allocation



### Contiguous Allocation

- 하나의 파일이 디스크 상 연속하여 저장되는 방식

<img width="482" alt="스크린샷 2021-05-10 오후 3 16 11" src="https://user-images.githubusercontent.com/72622744/117813940-a2276600-b29e-11eb-825d-9f577982cd96.png">

- 단점

  - 외부조각이 생길 수 있다.
  - File grow가 어려움
    - 뒤에 빈 블록의 개수가 제한적인 경우 파일 크기를 키우는데 있어 한계가 있다.
    - File 생성시 얼마나 큰 hole을 할당할 것인가?
    - grow 가능 vs 낭비(내부조각 internal framgmentation 발생)

- 장점

  - Fast I/O
    - 한 번의 seek/rotation 으로 많은 바이트 transfer
    - Realtime file용으로, 또는 이미 run 중이던 process의 swapping용
  - Direct access(=random access) 가능

  

### Linked Allocation

- file data를 디스크에 연속적으로 저장하지 않고 빈 위치면 아무데나 들어갈 수 있도록 하는 방식

<img width="495" alt="스크린샷 2021-05-10 오후 3 27 55" src="https://user-images.githubusercontent.com/72622744/117814005-b9665380-b29e-11eb-8518-98046a30415b.png">

- file의 시작위치만 directory가 가지고 있고 가면서 다음위치는 알게 되고 끝날 경우 끝났다고 표시
- 장점
  - 외부조각이 발생하지 않는다. (빈곳이면 어디든 들어가도 되므로)
- 단점
  - 직접탐색이 불가능하다(No Random Access)
  - Reliability 문제
    - 한 sector가 고장나 pointer가 유실되면 많은 부분을 잃음
  - **Pointer를 위한 공간이 block의 일부가 되어 공간효율성을 떨어뜨림**
    - 512 bytes/sector, **4 bytes/pointer**
- 변형
  - File-allocation table(FAT) 파일 시스템
    - 포인터를 별도의 위치에 보관하여 reliability와 공간효율성 문제 해결



### Indexed Allocation

- 직접접근이 가능하게 하기 위해 directory에 index block에 모든 위치를 저장해놓음

<img width="486" alt="스크린샷 2021-05-10 오후 3 36 21" src="https://user-images.githubusercontent.com/72622744/117814055-cc792380-b29e-11eb-9e32-90a9f908ce5d.png">

- 장점
  - 외부조각이 생기지 않음
  - 직접접근 가능
- 단점
  - Small file의 경우 공간낭비 (실제로 많은 file들이 small)
  - Too Large File의 경우 하나의 block으로 index를 저장하기에 부족
    - 해결방안
      1. Linked scheme
      2. Muliti-level-index

---



## UNIX 파일시스템의 구조

<img width="512" alt="스크린샷 2021-05-10 오후 3 43 25" src="https://user-images.githubusercontent.com/72622744/117814125-e0248a00-b29e-11eb-9113-da3b2e08e1e6.png">

- 유닉스 파일 시스템의 중요 개념
  - Boot block
    - 부팅에 필요한 정보(bootstrap loader)
    - 유닉스 파일시스템 뿐 아니라 모든 파일 시스템에서 맨 첫 번째에 온다. 
  - Super block
    - 파일시스템에 관한 총체적인 정보를 담고 있다(어디가 빈 블록이고 어디가 실제로 사용중인 블록인지, 어디까지가 inode list이고  등)
  - Inode list
    - 파일 이름을 제외한 파일의 모든 메타데이터를 저장
      - Cf. 파일이름은 디렉토리가 가지고 있다
  - Data block
    - 파일의 실제 내용을 보관

## FAT File System

<img width="498" alt="스크린샷 2021-05-11 오후 8 19 43" src="https://user-images.githubusercontent.com/72622744/117814190-f4688700-b29e-11eb-83c7-f79f6da778c2.png">

위 경우에서는 파일의 첫 번째 블록이 217번에 있고, 두 번째 블록의 위치를 알기 위해서는 FAT에서 217번 엔트리에 있는 618임을 알 수 있고... 계속하여 339번 엔트리에 가면 더 이상의 블럭은 없다는 것을 알게 된다. 다음 위치를 찾기 위해 블럭을 접근하는 것이 아니라 FAT만 접근하면 된다. 직접접근이 가능하다는 장점. 데이터블록이 유실되더라도 FAT에 정보가 있다. FAT에 있는 정보는 중요한 정보이므로 2카피 이상을 두고 있다.

- Boot block
- FAT : 파일의 메타데이터 중 극히 일부를 가지고 있음
- Root directory
- Data block

---



## Free- Space Management

비어있는 블럭을 어떻게 관리할지

- **Bit map or bit vector**

  <img width="243" alt="스크린샷 2021-05-11 오후 8 29 40" src="https://user-images.githubusercontent.com/72622744/117814270-0c400b00-b29f-11eb-8136-b5368db1ae36.png">

  - 비트를 사용하여 블록이 비었는지 여부를 표시함

  - Bit map은 부가적인 공간을 필요로 함
  - 연속적인 n개의 free block을 찾는데 효과적

- **Linked List**

  - 모든 free block들을 링크로 연결(free list)
  - 연속적인 가용공간을 찾는 것은 쉽지 않다
  - 공간의 낭비가 없다

- **Grouping**

  - linked list 방법의 변형
  - 첫 번째 free block이 n개의 pointer를 가짐
  - n-1 pointer는 free data block을 가리킴
  - 마지막 pointer가 가리키는 block은 또 다시 n pointer를 가짐

- **Counting**

  - 프로그램들이 종종 여러 개의 연속적인 block을 할당하고 반납한다는 성질에 착안
  - (First free bock, # of contiguous free blocks)을 유지



## Directory Implementation

Directory file에 내용을 어떻게 저장할 것인가.

- Linear list
  - <file, name, file의metada>의 list
  - 구현이 간단
  - 디렉토리 내에 파일이 있는지 찾기 위해서는 linear search가 필요(time-consuming)
- Hash Table
  - Linear list + **hashing**
    - 해시 함수를 적용하면 값이 특정 범위 내에서 나오게 된다.
  - Hash table은 file name을 이 파일의 linear list의 위치로 바꾸어줌
  - Search time을 없앰
  - Collision 발생가능(해시 함수를 사용하기 때문에 서로 다른 파일에 대해 같은 결과값으로 매핑되는)



- File의 metadata의 보관위치

  - 디렉토리 내에 직접보관
  - 디렉토리에는 포인터를 두고 다른 곳에 보관
    - Inode, FAT 등

- Long file name의 지원방식

  - <file name, file의 메타데이터>의 list에서 각 entry는 일반적으로 고정크기
  - File name이 고정 크기의 entry 길이보다 길어지는 경우 entry의 마지막 부분에 이름의 뒷부분이 위치한 곳의 포인터를 두는 방법
  - 이름의 나머지 부분은 동일한 directory file의 일부에 존재

  <img width="491" alt="스크린샷 2021-05-11 오후 8 49 36" src="https://user-images.githubusercontent.com/72622744/117814351-2843ac80-b29f-11eb-9c72-4635b1dd3afc.png">

  어느 정도 길이로 한정해 놓고 파일명이 이를 넘는 경우 포인터를 둬서 이름의 뒷부분의 이 위치한 곳의 포인터를 둔다.

---



## VFS and NFS

<img width="497" alt="스크린샷 2021-05-11 오후 8 53 20" src="https://user-images.githubusercontent.com/72622744/117814410-3abde600-b29f-11eb-8ea8-4e9b496f9ba8.png">

- Virtual File System(VFS)
  - **서로 다른 다양한 file system**에 대해 **동일한 시스템콜 인터페이스(API)를 통해** 접근할 수 있게 해 주는 OS의 layer
- Network File System(NFS)
  - 분산 시스템에서는 네트워크를 통해 파일이 공유될 수 있음
  - NFS는 분산 환경에서의 대표적인 파일 공유 방법임



## Page Cache and Buffer Cache

- **Page cache**
  - Virtual memory의 paging system에서 사용하는 **page frame**을 caching의 관점에서 설명하는 용어
  - Memory-Mapping I/O를 쓰는 경우 file의 I/O에서도 page cache 사용
- **Memory-Mapping I/O**
  -  file의 주소공간 중 일부를 **virtual memory**에 **mapping** 시킴
  -  매핑시킨 영역에 대한 메모리 접근 연산은 파일의 입출력을 수행하게 함
- **Buffer Cache**
  - **파일시스템을 통한 I/O 연산**은 메모리의 특정 영역인 buffer cache 사용
  - File 사용의 locality 활용
    - 한 번 읽어온 block에 대한 후속 요청시 buffer cache에서 즉시 전달
  - 모든 프로세스가 공용으로 사용
  - Replacement algorithm 필요(LRU, LFU 등)
- **Unified Buffer Cache**
  - 최근의 OS에서는 기존의 buffer cache가 page cache에 통합됨

<img width="502" alt="스크린샷 2021-05-11 오후 9 09 23" src="https://user-images.githubusercontent.com/72622744/117814472-4e694c80-b29f-11eb-87f8-99c84d71f9fa.png">



<img width="545" alt="스크린샷 2021-05-14 오전 10 49 05" src="https://user-images.githubusercontent.com/72622744/118217091-bb533100-b4af-11eb-859b-6d1fedfc4beb.png">

- 기존 unified buffer cached를 사용하지 않는 경우, 두 가지 인터페이스 존재. 
  - 하나,  파일을 오픈한후 read, write 시스템콜을 하는  것. 시스템콜을 하면 파일시스템에 있는 내용을 버퍼캐시로 읽어오고 사용자프로그램에 전달함. 사용자프로그램은 자신의 주소영역에 있는 페이지에 카피해서 사용.
  - 둘,  memory mapped I/O를 사용하는 것. 시스템콜을 하여 memory mapped I/O를 쓰겠다고. 자신의 주소공간 중 일부를  파일에 매핑하는 것. 그 내용을 페이지캐시에 카피하여 주면 read, write가 되는 것. 그 다음부터는 운영체제의 간섭없이 내 메모리 영역에 데이터 접근하는 방식으로 파일입출력
  - 그러나 두 경우 모두 파일입출력을 하기 위해서는 buffer cache를 통과해야 한다. 양쪽 모두 buffer cache를 자신의 페이지에 카피해야 하는 오버헤드가 존재함
- Unified buffer cache를 사용하는 경우, 경로가 단순해짐
  - Read/write 시스템콜을 하는 경우, 운영체제에게 cpu제어권이 넘어가고, 운영체제는 이미 메모리영역에 올라온 경우 사용자 주소영역에 카피해주면 되고 메모리에 올라와있지 않은 경우에는 파일을 읽어와서 그 내용을 사용자 주소영역에 카피하여 전달한다. 
  - memory mapped I/O인 경우, 자신의 주소영역 중 일부를 파일에 매핑하는 과정을 거친 다음에는 페이지캐시 자체를 읽고 쓰고 할 수 있음

> 정리:
>
> 사용자 프로그램이 파일을 접근하는 방식은 인터페이스가 두 개임. Read write 시스템콜을 사용하면 캐쉬에 있는 내용을 카피해서 자신의 주소공간으로 가져오는 것. Memory mapped I/O를 사용하면 카피가 아니라 물리적 메모리를 매핑하여 사용하는 것. 카피하는 오버헤드가 없고 매핑하여 쓰기 때문에 빠르다. 주의할 점은 다른 사용자 프로그램이 동일한 메모리를 매핑하여 쓰는 경우 일관성 문제가 발생할 수 있다.  





# Chap12. Disk Management and Scheduling

## Disk Structure

- **Logical block**
  - 디스크의 외부에서 보는 디스크의 단위 정보 저장 공간들
  - 주소를 가진 1차원 배열처럼 취금
  - 정보를 전송하는 최소단위
- **Sector**
  - 디스크 내부에서 디스크를 관리하는 최소단위
  - logical block이 물리적인 디스크에 매핑된 위치
  - Sector 0은 최외곽 실린더의 첫 트랙에 있는 첫 번째 섹터이다. 



## Disk Scheduling

- Access time의 구성
  - Seek time
    - 헤드를 해당 실린더로 움직이는데 걸리는 시간
  - Roational latency
    - 헤드가 원하는 섹터에 도달하기까지 걸리는 회전지연시간
  - Transfer time
    - 실제 데이터의 전송시간
- Disk bandwidth
  - 단위시간 당 전송된 바이트의 수
- Disk Scheduling
  - Seek time을 최소화하는 것이 목표
  - Seek time ≈ seek distance



## Disk Management

- **physical formatting(low-level formatting)**
  - 디스크를 컨트롤러가 읽고 쓸 수 있도록 섹터들로 나누는 과정
  - 각 섹터는 **header** + **실제 data** + **trailer**로 구성
  - header와 trailer는 sector number, ECC(Error- Correcting Code) 등의 정보가 저장되며 controller가 직접 접근 및 운영
- **Partitioning**
  - 디스크를 하나 이상의 실린더 그룹으로 나누는 과정
  - OS는 이것을 독립적 disk로 취급 ( logical disk)
- **Logical formatting**
  - 파일시스템을 만드는 것
  - FAT, inode, free space 등의 구조 포함
- **Booting**
  - ROM에 있는 "smalll bootstrap loader"의 실행
  - Sector 0을 load하여 실행
  - Sector 0은 "full Bootstrap loader program"
  - OS를 디스크에서 load하여 실행



## Disk Scheduling Algorithms

- 큐에서 다음과 같은 실린더 위치의 요청이 존재하는 경우 디스크 헤드 53번에서 시작한 각 알고리즘의 수행 결과는? (실린더 위치는 0-199)

  98, 183, 37,122,14,124,65,67

### FCFS

들어온 순으로 처리하는 알고리즘

<img width="398" alt="스크린샷 2021-05-14 오전 11 41 35" src="https://user-images.githubusercontent.com/72622744/118217293-1dac3180-b4b0-11eb-8e03-bfa557a80b3a.png">

### SSTF(Shortest Seek Time First)

- 큐에 있는 것 중 현재 head의 요청과 가장 가까운 순으로 처리

- Starvation문제

<img width="402" alt="스크린샷 2021-05-14 오전 11 42 45" src="https://user-images.githubusercontent.com/72622744/118217322-2ac92080-b4b0-11eb-9646-08f5e49a5bbc.png">

### SCAN

- Disk arm이 디스크의 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리한다.
- 다른 한쪽 끝에 도달하면 역방향으로 이동하며 오는 길목에 있는 모든 요청을 처리하며 다시 반대쪽 끝으로 이동한다
- 문제점: 실린더 위치에 따라 대기시간이 다르다.

### C-SCAN(Circular-SCAN)

- 헤드가 한쪽 끝에서 다른 쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리
- 다른 쪽 끝에 도달했으면 요청을 처리하지 않고 곧바로 출발점으로 다시 이동
- SCAN보다 균일한 대기시간을 제공한다.

### N-SCAN

- SCAN의 변형 알고리즘
- 일단 arm이 한 방향으로 움직이기 시작하면 그 시점 이후에 도착한  job은 되돌아올때 service

### LOOK and C-LOOK

- SCAN이나 C-SCAN은 헤드가 디스크 끝에서 끝으로 이동
- LOOK과 C-LOOK은 헤드가 진행 중이다가 **그 방향에 더 이상 기다리는 요청이 없으면** 헤드의 이동방향을 즉시 그 반대로 이동한다.

<img width="478" alt="스크린샷 2021-05-14 오전 11 54 17" src="https://user-images.githubusercontent.com/72622744/118217353-403e4a80-b4b0-11eb-932f-0b384aef7165.png">



## Disk-Scheduling Algorithm의 결정

- SCAN, C-SCAN 및 그 응용 알고리즘은 LOOK, C-LOOK 등이 일반적으로 디스크 입출력이 많은 시스템에서 효율적인 것으로 알려져있음
- File의 할당 방법에 따라 디스크 요청이 영향을 받음
- 디스크 스케줄링 알고리즘은 필요할 경우 다른 알고리즘으로 쉽게 교체할 수 있도록 OS와 별도의 모듈로 작성되는 것이 바람직하다. 



## Swap-Space Management

- Disk를 사용하는 두 가지 이유
  - memory의 volatile(휘발성)한 특성 -> file system
  - 프로그램 실행을 위한 메모리공간부족 -> swap space(swap area)
- Swap-space
  - Virtual memory system에서는 디스크를 memory의 연장공간으로 사용
  - 파일시스템 내부에 둘 수도 있으나 별도 partition 사용이 일반적
    - 공간 효율보다는 속도 효율성이 우선
    - 일반 파일보다 훨씬 짧은 시간만 존재하고 자주 참조됨
    - 따라서 block의 크기 및 저장방식이 일반 파일시스템과 다름

<img width="446" alt="스크린샷 2021-05-14 오후 12 05 23" src="https://user-images.githubusercontent.com/72622744/118217416-619f3680-b4b0-11eb-8c4b-e9edb93a0d6a.png">




## RAID

- RAID(Redundant Array of Independent Disks)
  - 여러 개의 디스크를 묶어서 사용
- RAID의 사용 목적
  - 디스크 처리 속도 향상
    - 여러 디스크에 block의 내용을 분산저장
    - 병렬적으로 읽어옴(interleaving, striping)
  - 신뢰성(reliability) 향상
    - 동일 정보를 여러 디스크에 중복저장
    - 하나의 디스크가 고장 시 다른 디스크에서 읽어옴(Mirroring, shadowing)
    - 단순한 중복 저장이 아니라 일부 디스크에 parity를 저장하여 공간의 효율성을 높일 수 있다.