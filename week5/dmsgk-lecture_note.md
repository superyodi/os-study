# Chap 8. Memory Management

*주소변환에 있어 운영체제의 역할은 없음. 전부 하드웨어적 역할임*

## Logical vs Physical Address

- Logical address(= virtual address)

  - 프로세스마다 독립적으로 가지는 주소 공간
  - 각 프로세스마다 0번지부터 시작
  - **cpu가 보는 주소는 logical address임**

- Physical address

  - 메모리에 실제 올라가는 위치

- 주소 바인딩 : 주소를 결정하는 것

  **Symbolic Address -> Logical Address -> Physical Address**

  (프로그래머는 숫자가 아닌 심볼로 된 주소를 사용하게 되고, 컴파일이 되면 논리적 주소로, 실행이 되려면 물리적 주소로 변환이 되어야 한다. )

  - 주소는 언제 결정되는가? 각 프로그램이 가지고 있는 논리적 주소가 물리적 메모리 주소로 언제 결정되는가. 이를 결정하는 방식 3가지를 살펴본다. 

## 주소 바인딩(Address Binding)

- **Compile time binding**
  - 물리적 메모리 주소가 **컴파일 시** 알려짐
    - 따라서 논리적 주소가 곧 물리적 주소가 된다. 
    - 물리적 주소가 미리 결정되어야 하므로 매우 비효율적이므로 지금의 컴퓨터 시스템에서는 잘 사용하지 않는다. 
  - 시작 위치 변경시 재컴파일
  - 컴파일러는 절대코드(**absolute code**)생성!
- **Load time binding**
  - **실행시작**될 때 주소변환
  - loader의 책임하에 물리적 메모리 주소 부여
  - 컴파일러가 **재배치가능코드(relocatable code)**를 생성한 경우 가능
- **Execution time binding(= Run time binding)**
  - 수행이 시작된 이후에도 프로세스의 메모리 상 위치를 옮길 수 있음
  - cpu가 주소를 참조할 때마다 binding을 점검(address mapping table)
    - 위 두 방식과 다르게 프로그램이 실행 중일 때에도 주소가 계속 바뀌므로.
  - 하드웨어적인 지원이 필요(e.g. base and limit registers, **MMU**)

<img width="510" alt="스크린샷 2021-04-27 오후 5 06 09" src="https://user-images.githubusercontent.com/72622744/116235287-a0d34500-a798-11eb-91ac-ecd39bf51320.png">



## Memory-Management Unit(MMU)

- MMU(Memory-Management Unit)

  -  logical address를 physical address로 매핑해주는(= 주소변환을 해 주는) **Hardware Device**

- MMU scheme

  - 사용자 프로세스가 cpu에서 수행되며 생성해내는 모든 주소값에 대해 base register( = relocation register)의 값을 더한다.

- User program

  - **local address만을 다룬다.**

  - 실제 physical address를 볼 수 없으며 알 필요가 없다.

    

<img width="512" alt="스크린샷 2021-04-27 오후 5 26 47" src="https://user-images.githubusercontent.com/72622744/116235396-c5c7b800-a798-11eb-9c57-4481e9502256.png">

- 가장 간단한 MMU는 base register와 limit register를 이용한 방법
  - base register(=relocation register)에는 프로그램의 시작위치를 저장해둔다.(14000) 주소변환을 할 때는 논리적 주소에 시작위치를 더해서 물리적 주소로 변환한다. 
  - limit register는 프로그램의 크기를 담고 있음. 프로그램 크기보다 더 큰 논리주소를 요청하여 다른 프로세스의 메모리를 보려는 악의적인 프로그램인지 확인하는 용도로 사용한다. 벗어나는 요청이면 trap이 걸려 cpu제어권이 운영체제에게 넘어감. 프로그램 크기 이내의 요청이라면 주소변환이 이루어진다. 

<img width="512" alt="스크린샷 2021-04-27 오후 5 33 15" src="https://user-images.githubusercontent.com/72622744/116235534-f3146600-a798-11eb-8da5-237f5dd6e4f0.png">


## Some Terminologies

### Dynamic Loading

** loading : 메모리로 올리는 것

- 프로세스 전체를 메모리에 미리 다 올리는 것이 아니라 해당 루틴이 불려질 때 메모리에 load하는 것
- memory utilization의 향상
- 가끔씩 사용되는 많은 양의 코드일 경우 유용
  - 예- 오류처리 루틴
- **운영체제의 특별한 지원 없이** 프로그램 자체에서 구현 가능(OS는 라이브러리를 통해 지원 가능)
  - OS의 paging 기법과 구분되는 개념. 그러나 최근 혼용되어 사용되기도 한다.

### Dynamic Linking

- Linking을 실행시간(execution time)까지 미루는 기법
- **Static linking**
  - 라이브러리가 프로그램의 실행 파일 코드에 포함됨
  - 실행파일의 크기가 커짐
  - 동일한 라이브러리를 각각의 프로세스가 메모리에 올리므로 메모리낭비(eg. `printf` 함수의 라이브러리코드)
- **Dynamic linking**
  - 라이브러리 실행 시 연결(link)됨
  - 별도의 위치에 라이브러리 존재함
  - 라이브러리 호출 부분에 라이브러리 루틴의 위치를 찾기 위한 **stub**이라는 작은 코드를 둠
  - 라이브러리가 이미 메모리에 있으면 그 루틴의 주소로 가고 없으면 디스크에서 읽어옴
  - 운영체제의 도움이 필요

### Overlays

- 메모리에 프로세스의 부분 중 실제 필요한 정보만을 올림
- 프로세스의 크기가 메모리보다 클 때 유용
- 운영체제의 지원없이 사용자에 의해 구현
- 작은 공간의 메모리를 사용하던 초창기 시스템에서 수작업으로 프로그래머가 구현

### Swapping

- **Swapping**
  - 프로세스를 일시적으로 메모리에서 backing store로 쫓아내는 것
- **Backing store(=swap area)**
  - 디스크
    - 많은 사용자의 프로세스 이미지를 담을 만큼 충분히 빠르고 큰 저장공간
- **Swap in / Swap out**
  - 일반적으로 중기 스케줄러(swapper)에 의해 swap out 시킬 프로세스 선정
  - Prioirty-based CPU scheduling algorithm
    - Priority가 낮은 프로세스를 swapped out 시킴
    - prioirity가 높은 프로세스를 메모리에 올려놓음
  - Compile time 혹은 load time binding에서는 원래 메로리 위치로 swap in해야 함
  - **Execution time binding**에서는 추후 빈 메모리 영역 아무 곳에나 올릴 수 있음
  - Swap time은 대부분 transfer time(swap되는 양에 비례하는 시간)임 



## Allocation of Physical Memory

- 메모리는 일반적으로 두 영역으로 나뉘어 사용

  - <u>**os 상주영역**</u>
    - Interrpt vector와 함께 낮은 주소영역 사용
  - <u>**사용자 프로세스 영역**</u>
    - 높은 주소영역 사용

- 사용자 프로세스 영역의 할당방법

  - **Contiguous allocation**

    각각의 프로세스가 메모리의 연속적인 공간에 적재되도록 하는 것. 통째로 올라가는 것. 

    - **Fixed partition allocation**
    - **Variable partition allocation**

  - **Noncontiguous allocation**

    하나의 프로세스가 메모리의 여러 영역에 분산되어 올라갈 수 있음

    - **Paging**
    - **Segmentation**
    - **Paged Segmentation**

### Contiguous Allocation

- #### **고정분할(Fixed partition)방식**

  - 물리적 메모리를 몇 개의 영구적 분할(Partition)로 나눔
  - 분할의 크기가 모두 동일한 방식과 서로 다른 방식이 존재
  - 분할 당 하나의 프로그램 적재
  - 융통성이 없음
    - 동시에 메모리에 load되는 프로그램의 수가 고정됨
    - 최대 수행 가능 프로그램 크기 제한
  - Internal fragmentation 발생(external fragmentation도 발생)

- #### **가변분할(Variable partition) 방식**

  - 프로그램의 크기를 고려해서 할당
  - 분할의 크기, 개수가 동적으로 변함
  - 기술적 관리기법 필요
  - External fragmentation 발생
  - **Hole**
    - 가용메모리공간
    - 다양한 크기의 hole이 메모리 여러 곳에 흩어져 있음
    - 프로세스가 도착하면 수용가능한 hole을 할당
    - 운영체제는 다음의 정보를 유지
      - 할당공간
      - 가용공간(hole)

<img width="515" alt="스크린샷 2021-04-27 오후 7 47 14" src="https://user-images.githubusercontent.com/72622744/116235634-1212f800-a799-11eb-8446-8ffb5258be02.png">

- 외부조각:  분할이 프로그램 크기보다 작아서 생기는 조각
- 내부조각: 분할의 크기가 프로그램크기보다 커서 남는 조각

##### Dynamic Storage-Allocation Problem

가변분할방식에서 size n인 요청을 만족하는 가장 적절한 hole을 찾는 문제

- **First-fit**
  - Size가 n 이상인 것 중 최초로 찾아지는 hole에 할당
- **Best-fit**
  - Size가 n 이상인 가장 작은 hole을 찾아서 할당
  - Hole의 리스트가 크기 순으로 정렬되지 않은 경우 모든 hole의 리스트를 탐색해야 함
  - 많은 수의 아주 작은 hole들이 생성됨
- **Worst-fit**
  - 가장 큰 hole에 할당
  - 역시 모든 리스트를 탐색해야 함
  - 상대적으로 아주 큰 hole들이 생성됨

#### Compaction

- External fragmentation 문제를 해결하는 한 가지 방법
- 사용 중인 메모리 영역을 한 군데로 몰고 hole들을 다른 한 곳으로 몰아 큰 block을 만드는 것
- 매우 비용이 많이 드는 방법임
- 최소한의 메모리 이동으로 compaction하는 방법(매우 복잡한 문제)
- Compaction은 프로세스의 주소가 실행 시간에 동적으로 재배치 가능한 경우에만 수행될 수 있다.



### Noncontiguous Allocation

### 1. Paging

- Paging
  - Process의 virtual memory를 **동일한 사이즈의 page단위로** 나눔
  - virtual memory의 내용이 page단위로 noncontiguous하게 저장됨
  - 일부는 backing storage에, 일부는  physical memory에 저장
- Basic Method
  - Physical memory를 동일한 크기의 frame으로 나눔
  - Logical memory를 동일 크기의 page로 나눔(frame과 같은 크기)
  - 모든 가용 frame들을 관리
  - **Page table**을 사용하여 logical address를 physical address로 변환
  - External fragmentation 발생 안 함
  - Internal fragmentation 발생 가능

<img width="513" alt="스크린샷 2021-04-28 오후 5 50 45" src="https://user-images.githubusercontent.com/72622744/116687990-ff92fb80-a9f0-11eb-8dc8-36f0657dd12f.png">

논리적 페이지 번호에 해당하는 엔트리를 page table에서 위에서 p번째 찾아가면 f라는 frame번호가 나온다. 그러면 논리적 주소를 물리적 주소로 바꾸게 되는데 이는 논리적 페이지 번호를 프레임 번호로 바꾸어주면 되는 것이다. 

#### Implementation of Page Table

- Page table은 **main memoy**에 상주

- **Page-table base register(PTRB)**가 page table을 가리킴

- **Page-table length register(PTLB)**가 테이블 크기를 보관

- 모든 메모리 접근 연산에는 **2번**의 **memory access** 필요

- Page table 접근 1번, 실제 data/instruction 접근 1번

- 속도 향상을 위해 

  **Associative register** 혹은 **translation look - aside buffer(TLB)** 라 불리는 고속의 lookup hardware cache 사용 

<img width="510" alt="스크린샷 2021-04-28 오후 6 56 55" src="https://user-images.githubusercontent.com/72622744/116688051-176a7f80-a9f1-11eb-8fc3-8045b94344ef.png">

 메모리 상의 page table에 먼저 접근하기 전에 TLB를 먼저 검색해봄. 혹시 tlb에 저장된 주소정보를 이용하여 주소변환이 가능한지. 그렇다면 바로 주소변환을 하면 되므로 메모리를 1번만 접근해도 된다. 

##### Associative Register

- **Associative registers(TLB)**: parallel search가 가능
  - **TLB에는 page table 중 일부만 존재**
- Address translation
  - page table 중 일부가 associative register에 보관되어 있음
  - 만약 해당 page #가 associative register에 있는 경우 곧바로 frame #를 얻음
  - 그렇지 않을 경우 main memory에 있는 page table로부터 frame #을 얻음
  - TLB는 context switch 때 flush(모든 엔트리를 비운다.)



#### Two- level Page Table

- 현대의 컴퓨터는 address space가 매우 큰 프로그램 지원
  - 32bit address 사용시: 4GB의 주소공간
    - Page size가 4K시 1M의 page table entry 필요
    - 각 page entryrk 4B시 프로세스당 4M의 page table 필요
    - 그러나 대부분의 프로그램은 4G의 주소공간 중 지극히 일부분만 사용하므로 page table 공간이 심하게 낭비됨
- Page table 자체를 page로 구성
- 사용되지 않는 주소공간에 대한 outer page table의 엔트리값은 null(대응하는 inner page table이 없음)

<img width="511" alt="스크린샷 2021-04-28 오후 7 21 23" src="https://user-images.githubusercontent.com/72622744/116688133-3832d500-a9f1-11eb-82e3-dcd872bc2279.png">

<img width="515" alt="스크린샷 2021-04-28 오후 7 23 49" src="https://user-images.githubusercontent.com/72622744/116688205-526cb300-a9f1-11eb-9bd1-a386e0c19232.png">


#### Multilevel Paging and Performance

- Address space가 더 커지면 다단계 페이지 테이블 필요

- 각 단계의 페이지 테이블이 메모리에 존재하므로 logical address의 physical address변환에  더 많은 메모리접근 필요

- TLB를 통해 메모리 접근시간을 줄일 수 있음

- 4단계 페이지 테이블을 사용하는 경우

  - 메모리 접근시간이 100ns, TLB 접근 시간이 20 ns이고

  - TLB hit ratio가 98%인 경우

    Effective memory access time = 0.98 * 120 + 0.02 * 520 = 128 ns

    결과적으로 주소변환을 위해 28ns만 소요, 다단계 paging이 그렇게 큰 오버헤드가 들지는 않는다. 


<img width="514" alt="스크린샷 2021-04-29 오전 10 57 03" src="https://user-images.githubusercontent.com/72622744/116688266-6b756400-a9f1-11eb-96ca-d8d8e3bc0abd.png">


#### Memory Protection

페이지 테이블의 각 entry마다 아래의 bit를 둔다. 

- **Protection bit**
  - page에 대한 접근권한(read/wirte/read-only) . 연산에 대한 권한 표시
- **Valid-invalid bit**
  - **"valid"**는 해당 주소의 frame에 그 프로세스를 구성하는 유효한 내용이 있음을 뜻함(접근 허용)
  - **"Invalid"**는 해당 주소의 frame에 유효한 내용이 없음을 뜻함(접근불허)
    - 프로세스가 그 주소부분을 사용하지 않는 경우
    - 해당 페이지가 메모리에 올라와있지 않고 swap area에 있는 경우

#### Inverted Page Table

- **page table이 매우 큰 이유**

  - 모든 process별로 그 logical address에 대응하는 모든 page에 대해 page table entry가 존재
  - 대응하는 page가 메모리에 있든 아니든 간에 page table에는 entry로 존재

- **Inverted page table**

  - System-wide 하게 시스템 하나에 page table 하나 존재함
  - 물리적 메모리의 Page frame 하나당 page table에 하나의 entry를 둔 것(system-wide)
  - 각 page table entry는 각각의 물리적 메모리의 page frame이 담고 있는 내용 표시(process-id, process의 logical memeory)
  - 단점
    - 페이지 전체를 탐색해야 함(인덱스를 활용해 바로 page #로 접근할 수 없음)
  - 조치
    - associative register 사용(expensive)

  <img width="515" alt="스크린샷 2021-04-29 오전 11 07 16" src="https://user-images.githubusercontent.com/72622744/116688318-847e1500-a9f1-11eb-83ba-834b14f86cde.png">


- cpu가 논리적 주소를 받으면 process id와 page number를 받고 page table에서 몇 번째에 해당하는지를 보고 frame #를 찾아 주소변환이 이루어짐
- 정방향의 주소변환과 달리 첫 번째 entry에는 frame #가 있고 두 번째 entry에는 논리적 주소의 page #가 나온다. 



#### Shared Page

- **Shared code**
  - Re-entrant Code(= Pure code)
  - **read-only**로 하여 프로세스 간에 하나의 code만 메모리에 올림
  - Shared code는 **모든 프로세스의 logical address space에서 동일한 위치에 있어야 함**
- **Private code and data**
  - 각 프로세스들은 독자적으로 메모리에 올림
  - Private data는 logical address space의 아무 곳에 와도 무방



### 2. Segmentation

- 프로그램은 의미 단위인 여러 개의 segment로 구성

  - 작게는 프로그램을 구성하는 함수 하나하나를 세그먼트로 정의
  - 크게는 프로그램 전체를 하나의 세그먼트로 정의 가능
  - 일반적으로는 code, data, stack 부분이 하나씩의 세그먼트로 정의됨

- segment는 다음과 같은 logical unit 들임

  ```
  main(),
  function,
  global variance,
  stack,
  symbol table, arrays
  ```

#### Segmentation Architecture

- Logical address는 다음의 두 가지로 구성

  **<segment-number, offset>**

- **Segment table**

  - each table entry has:
    - **Base** - starting physical address of the segment
    - **Limit** - length of the segment

- **Segment-table base register(STRB)**

  - 물리적 메모리에서의 **segment table의 위치**

- **Segment-table length register(STLR)**

  - 프로그램이 사용하는 **segment의 수**

    segment number s is legal if s< STLR

- **Protection**

  - 각 세그먼트 별로 protection bit가 있음
  - Each entry: 
    - Valid bit = 0 => ilegal segment
    - **Read/Write/Execution** 권한 bit

- **Sharing**

  - Shared segment
  - Same segment number

  ** segment는 의미 단위이기 때문에 공유(sharing)와 보안(protection)에 있어 paging 보다 훨신 효과적이다.

- **Allocation**

  - First fit / best fit
  - external framentation 발생

  ** segment의 길이가 동일하지 않으므로 가변분할 방식에서와 동일한 문제점들이 발생

<img width="513" alt="스크린샷 2021-04-29 오전 11 46 03" src="https://user-images.githubusercontent.com/72622744/116688411-9d86c600-a9f1-11eb-8349-ce6674a01016.png">

- cpu가 논리주소를 주게 되면 segment # 와 offset  두 부분으로 나눈다.

- 체크해봐야 할 두 가지
  -  segment number s is legal if s< STLR인지. 아니라면 trap. 
  -  세그먼트의 길이보다 세그먼트 안에서 떨어진 offset값이 더 크지는 않은가. 

<img width="512" alt="스크린샷 2021-04-29 오후 12 14 29" src="https://user-images.githubusercontent.com/72622744/116688463-b42d1d00-a9f1-11eb-95f8-769a8ea04572.png">

실제적으로 비교하자면 table을 위한 메모리 낭비가 심한 쪽은 paging이 된다. segmentatation이 낭비가 적다. 

<img width="503" alt="스크린샷 2021-04-30 오후 8 00 41" src="https://user-images.githubusercontent.com/72622744/116688533-cdce6480-a9f1-11eb-9a15-f3c162f84faf.png">

세그먼트를 서로다른 두 프로세스가 공유하는 예제(Shared segment)

### 3. Paged Segmentation

- segment하나가 여러 page로 구성. 메모리에 올라갈 때는 page단위로 올라간다. allocation문제가 생기지 않는다. 
- 의미단위로 해야 하는 공유, 보안 같은 업무는 segment table level에서 하는 것

<img width="508" alt="스크린샷 2021-04-30 오후 8 03 04" src="https://user-images.githubusercontent.com/72622744/116688589-e2126180-a9f1-11eb-91d4-6f7db27127f3.png">