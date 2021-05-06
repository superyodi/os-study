# Chap 9. Virtual Memory

*이전까지 논의해온 메모리관리기법은 프로세스 전체가 실행되기 전에 메모리로 올라와야 한다는 것을 전제로 하고 있다. 프로세스 전체가 메모리 내에 올라오지 않더라도 실행이 가능하도록 하는 기법을 가상메모리라 한다.*

*virtual memory 기법은 전적으로 운영체제가 관여*

## Demand Paging

- 실제로 필요할 때 page를 메모리에 올리는 것

  - 얻는 효과
    - I/O의 감소
    - Memory 사용량 감소
    - 빠른 응답시간
    - 더 많은 사용자 수용

- Valid/ Invalid bit의 사용

  - Invalid의 의미

    - 사용되지 않는 주소영역인 경우
    - 페이지가 물리적 메모리에 없는 경우

  - 처음에는 모든 page entry가 invalid로 초기화

  - address translation 시에 invalid bit이 set되어 있으면

    => **Page fault** 발생

## Page Fault

- Invalid page를 접근하면 MMU가 trap을 발생시킴(page fault trap)
- Kernel mode로 들어가서 page fault handler가 invoke됨
- 다음과 같은 순서로 page fault를 처리한다
  1. Invalid reference?  => abort process
  2. Get a empty page frame( 없으면 뺏어온다: replace -> Free frame이 없는 경우 파트 참조)
  3. 해당 페이지를 disk에서 메모리로 읽어온다
     1. Disk I/O가 끝나기까지 이 프로세스는 CPU를 preempt당함 (block)
     2. Disk read가 끝나면 page tables entry 기록, valid/invalid bit = "valid"
     3. ready queue에 process를 insert -> dispatch later
  4. 이 프로세스가 cpu를 잡고 다시 running
  5. 아까 중단되었던 instruction을 재개

<img width="515" alt="스크린샷 2021-05-03 오후 8 50 14" src="https://user-images.githubusercontent.com/72622744/116877195-957c8f80-ac58-11eb-820d-e0ad34b9b11f.png">

메모리 레퍼런스가 있었는데 invalid로 표시가 된 경우. trap이 걸려서 cpu가 운영체제로 넘어간다. 운영체제는 backing store에 있는 페이지를 물리적 메모리로 올려둔다. 올려둔 작업이 끝나면 해당하는 frame #를 entry에 적어두고 valid로 바꾼다. 이후 cpu를 얻어서 주소변환을 하면 valid로 되어 있으니 주소변환이 정상적으로 되어 해당하는 물리적 메모리의 page frame을 접근할 수 있게 된다.



<img width="512" alt="스크린샷 2021-05-03 오후 8 54 56" src="https://user-images.githubusercontent.com/72622744/116877275-b04f0400-ac58-11eb-8883-bc0542f4f67b.png">



### Free frame이 없는 경우

- **Page replacement**

  - 어떤 frame을 빼앗아올지 결정해야 함
  - 곧바로 사용되지 않을 page를 쫓아내는 것이 좋음
  - 동일한 페이지가 여러 번 메모리에서 쫓겨났다가 다시 들어올 수 있음

- <u>**Replacement Algorithm**</u>

  - **Page-fault rate를 최소화**하는 것이 목표

  - 알고리즘의 평가

    - 주어진 page reference string에 대해 page fault를 얼마나 내는지 조사

  - Reference string의 예

    1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5.



## Replacement Alogrithm	

### Optimal Algorithm

- MIN(OPT): 가장 먼 미래에 참조되는 page를 replace
- 미래의 참조를 어떻게 아는가?
  - Offline algorithm	( 미리 알고있다는 가정하에 운영하는 알고리즘. 온라인이 아니라)
- 실제로 사용할 수는 없지만 다른 알고리즘의 성능에 대한 upper bound 제공(가장 적거나 같은 page fault를 발생시킨다.)
  - Belady's optimal algorithm, MIN, OPT 등으로 불림

<img width="499" alt="스크린샷 2021-05-03 오후 9 08 38" src="https://user-images.githubusercontent.com/72622744/116877341-c78df180-ac58-11eb-9022-1f7b92b29807.png">


### FIFO(First In First Out) Algorithm

- FIFO: 먼저 들어온 것을 먼저 내쫓음

<img width="514" alt="스크린샷 2021-05-03 오후 9 13 30" src="https://user-images.githubusercontent.com/72622744/116877445-eb513780-ac58-11eb-94e5-0e1a65f8222e.png">

FIFO Anomaly : 메모리 프레임을 늘려주어도 page fault가 더 많이 발생하는 상황이 있을 수 있다.



### LRU (Least Recently Used) Algorithm

- 가장 오래 전에 참조된 것을 지움

<img width="493" alt="스크린샷 2021-05-03 오후 9 16 49" src="https://user-images.githubusercontent.com/72622744/116877542-0a4fc980-ac59-11eb-966e-21c6755a5940.png">


### LFU(Least Freqeuntly Used) Algorithm

- LFU: 참조횟수(reference count)가 가장 적은 페이지를 지움
  - 최저 참조 횟수인 page가 여럿 있는 경우
    - LFU 알고리즘 자체에서는 여러 page 중 임의로 선정한다
    - 성능 향상을 위해 가장 오래 전에 참조된 page를 지우게 구현할 수도 있다
  - 장단점
    - (+) LRU처럼 직전 참조 시점만 보는 것이 아니라 장기적인 시간 규모를 보기 때문에 page의 인기도를 좀 더 정학히 반영할 수 있음
    - (-) 참조 시점의 최근성을 반영하지 못함
    - (-) LRU보다 구현이 복잡함



<img width="514" alt="스크린샷 2021-05-03 오후 9 29 15" src="https://user-images.githubusercontent.com/72622744/116877608-218eb700-ac59-11eb-9725-fd84760b056e.png">





## 다양한 Caching 환경

- Caching 기법

  - 한정된 빠른공간(=캐쉬)에 요청된 데이터를 저장해두었다가 후속요청시 캐쉬로부터 직접 서비스하는 방식
  - paging system 외에서도 cache memory, buffer caching, Web caching 등 다양한 분야에서 사용

- **캐쉬 운영의 시간제약**

  - 교체알고리즘에서 삭제할 항목을 결정하는 일에 지나치게 많은 시간이 걸리는 경우 실제시스템에서 사용할 수 없음

  - Buffer caching이나 Web caching의 경우

    - O(1)에서 O(log n) 정도까지 허용

  - **Paging system인 경우**

    - page fault인 경우에만 OS가 관여함
    - 페이지가 이미 메모리에 존재하는 경우 참조시각 등의 정보를 OS가 알 수 없음
    - O(1)인 LRU의 list 조작조차 불가능

    -> <u>LRU, LFU 가능하지 않음</u>



## Clock Algorithm

- Clock Algorithm
  - LRU의 근사 알고리즘
  - 여러 명칭으로 불림
    - Second chance algorithm
    - NUR(Not Used Recently) 또는 NRU(Not Recently Used)
  - Reference bit을 사용해서 교체대상페이지 선정(circular list)
  - Reference bit가 0인 것을 찾으면 그 페이지를 교체
  - 한 바퀴 되돌아와서도(=second chance) 0이면 그때에는 replace 당함
  - 자주 사용되는 페이지라면 second chance가 올 때 1
- Clock Algorithm의 개선
  - Reference bit과 modified bit을 함께 사용
  - Reference bit = 1: 최근에 참조된 페이지
  - Modified bit = 1 : 최근에 변경된 페이지(I/O를 동반하는 페이지)

<img width="503" alt="스크린샷 2021-05-05 오후 3 58 36" src="https://user-images.githubusercontent.com/72622744/117246994-7ceac000-ae78-11eb-8665-61795a2b1c8c.png">



## Page frame의 Allocation

- Allocation problem: 각 프로세스에 얼마만큼의 page frame을 할당할 것인가
- Allocation의 필요성
  - 메모리 참조 명령어 수행 시 명령어, 데이터 등 여러 페이지 동시 참조
    - 명령어 수행을 위해 최소한 할당되어야 하는 frame의 수가 있음
  - Loop를 구성하는 page들은 한꺼번에 allocate되는 것이 유리함
    - 최소한의 allocation이 없으면 매 loop마다 page fault
- Allocation Scheme
  - **Equal allocation**: 모든 프로세스에 똑같은 개수 할당
  - **Proportional allocation** : 프로세스 크기에 비례하여 할당
  - **Priority allocation**: 프로세스의 priority에 따라 다르게 할당



### Global vs Local Replacement

- Global replacement
  - Replace시 다른 process에 할당된 frame을 빼앗아 올 수 있다
  - Process 별 할당량을 조절하는 또다른 방법임
  - FIFO, LRU, LFU등의 알고리즘읠 global replacement로 사용시에 할당
  - Working set, PFF 알고리즘 사용
- Local replacement
  - 자신에게 할당된 frame 내에서만 replacement
  - FIFO, LRU, LFU 등의 알고리즘을 process 별로 운영시



### Thrashing

- 프로세스의 원활한 수행에 필요한 최소한의 page frame 수를 할당받지 못한 경우 발생
- page fault rate이 매우 높아짐
- cpu utilization이 낮아짐(한번 실행하려면 그 프로그램이 메모리에 없음, I/O를 해야 한다..)
- OS는 MPD를 높여야 한다고 판단
- 또다른 프로세스가 시스템에 추가됨
- 프로세스당 할당된 frame수가 더욱 감소
- 프로세스는 page의 swap in/ swap out으로 매우 바쁨
- 대부분의 시간에 cpu는 한가함
- Low throughput



## Working-Set Model

- Locality of reference
  - 프로세스는 특정시간 동안 일정 장소만을 집중적으로 참조한다
  - 집중적으로 참조되는 해당 page들의 집합을 locality set이라 함
- Working-set Model
  - Locality에 기반하여 프로세스가 일정 시간동안 원활하게 수행되기 위해 한꺼번에 메모리에 올라와있어야 하는 page들의 집합을 **Working Set**이라 정의함
  - Working Set 모델에서는 process의 working set 전체가 메모리에 올라와 있어야 수행되고 그렇지 않을 경우 모든 frame을 반납한 후 swap out(suspend)
  - Thrashing을 방지함
  - **Multiprogramming degree**를 결정함

### Workin-Set Algorithm

- Working-set의 결정
  - Working set window(Δ시간)를 통해 알아냄
  - Window size가 Δ인 경우
    - 시각 t i에서의 window set WS(ti)
      - Time interval [ti-Δ, ti] 사이에 참조된 서로 다른 페이지들의 집합
    - Working set에 속한 page는 메모리에 유지, 속하지 않은 것은 버림 ( 즉, 참조된 후 Δ 시간 동안 해당 page를 메모리에 유지한 후 버림)

<img width="490" alt="스크린샷 2021-05-06 오전 10 33 47" src="https://user-images.githubusercontent.com/72622744/117247060-97249e00-ae78-11eb-9a3d-0a0957145c6c.png">

- Working-Set Algorithm
  - Process들의 working set size의 합이 page frame의 수보다 큰 경우
    - 일부 process를 swap out 시켜 남은 process의 working set을 우선적으로 충족시켜준다(MPD를 줄임)
  - Working set을 다 할당하고도 page frame이 남는 경우
    - Swap out 되었던 프로세ㅔ스에게 working set을 할당(MPD를 키움)
- Window size Δ
  - Working set을 제대로 탐지하기 위해서는 window size를 잘 결정해야 함
  - Δ값이 너무 작으면 locality set을 모두 수용하지 못할 우려
  - Δ값이 크면 여러 규모의 locality set 수용
  - Δ값이 ∞이면 전체 프로그램을 구성하는 page를 working set으로 간주



## PFF(Page-Fault Frequency) Scheme

<img width="406" alt="스크린샷 2021-05-06 오전 10 41 55" src="https://user-images.githubusercontent.com/72622744/117247125-b4f20300-ae78-11eb-961e-986400af904d.png">

직접 page-fault rate를 본다. 

- Page-fault rate의 상한값과 하한값을 둔다
  - Page fault rate이 상한값을 넘으면 frame을 더 할당한다
  - Page fault rate이 하한값 이하이면 할당 frame 수를 줄인다
- 빈 frame이 없으면 일부 프로세스를 swap out



## Page Size의 결정

- Page size를 감소시키면
  - 페이지 수 증가
  - 페이지 테이블 크기 증가
  - International fragmentation 감소
  - Disk transfer의 효율성 감소
    - Seek/rotation vs transfer
    - 가능하면 한 번 disk sector가 이동해서 많은 양의 뭉치를 읽어서 메모리에 올리는 것이 효율적
  - 필요한 정보만 메모리에 올라와 메모리 이용이 효율적
    - Locality의 활용측면에서는 좋지 않음
- Trend
  - Larger page size





## Chap 10. File Systems

## File and File System

- **File**

  - "A named collection of related information"

    관련정보를 이름을 가지고 저장하는 정보

  - 일반적으로는 비휘발성의 보조기억장치에 저장

  - 운영체제는 다양한 저장장치를 file이라는 동일한 논리적 단위로 볼 수 있게 해 줌

  - Operation

    - create, read, write, reposition(lseek), delete, open, close 등

- **File attribute**(혹은 파일의 **metadata**)

  - 파일 자체의 내용이 아니라 파일을 관리하기 위한 각종 정보들
    - 파일이름, 유형, 저장된 위치, 파일 사이즈
    - 접근권한(읽기/쓰기/실행), 시간(생성/변경/사용), 소유자 등

- **File system**

  - 운영체제에서 파일을 관리하는 부분
  - 파일 및 파일의 메타데이터, 디렉토리 정보 등을 관리
  - 파일의 저장방법 결정
  - 파일 보호 등



## Directory and Logical Disk

- **Directory**
  - 파일의 메타데이터 중 일부를 보관하고 있는 일종의 특별한 파일
  - 그 디렉토리에 속한 파일 이름 및 파일 attribute들
  - operation
    - Search for a file, create a file, delete a file
    - List a directory, rename a file, traverse the file system
- **Partition(= Logical Disk)**
  - 하나의 (물리적) 디스크 안에 여러 파티션을 두는 것이 일반적
  - 여러 개의 물리적 디스크를 하나의 파티션으로 구성하기도 함
  - (물리적) 디스크를 파티션으로 구성한 뒤 각각의 파티션에 file system을 깔거나 swapping 등 다른 용도로 사용할 수 있음



## open()

- Open("/a/b/c")

  - 디스크로부터 파일c의 메타데이터를 메모리로 가지고 옴
  - 이를 위하여 directory path를 search
    - 루트 디렉토리 "/"를 open하고 그 안에서 파일 "a"의 위치 획득
    - 파일 "a"를 open 한 후 read하여 그 안에서 파일 "b"의 위치 획득
    - 파일 "b"를 open한 후 read하여 그 안에서 파일 "c"의 위치 획득
    - 파일 "c"를 open한다
  - Directory path의 search에 너무 많은 시간 소요
    - open을 read/write와 별도로 두는 이유임
    - 한번 open한 파일은 read/write 시 directory search가 불필요
  - Open file table
    - 현재 open된 파일들의 메타데이터 보관소(in memory)
    - 디스크의 메타데이터보다 몇 가지 정보가 추가
      - Open 한 프로세스의 수
      - File offset: 파일 어느위치 접근중인지 표시(별도테이블 필요)
    - File desciptor( file handle, file control block)
      - Open file table에 대한 위치정보(프로세스별)

<img width="502" alt="스크린샷 2021-05-06 오전 11 50 35" src="https://user-images.githubusercontent.com/72622744/117247289-f4205400-ae78-11eb-8ebc-71be11794ec0.png">





## File Protection

- 각 파일에 대해 누구에게 어떤 유형의 접근(read/write/execution)을 허락할 것인가?

- Access Control 방법

  - **Access control Matrix**

    <img width="402" alt="스크린샷 2021-05-06 오전 11 54 06" src="https://user-images.githubusercontent.com/72622744/117247346-0bf7d800-ae79-11eb-8c0e-6292ef03b24a.png">


    - 그러나 행렬 방식으로 하면 희소행렬이 되어 메모리가 낭비되므로 linked list로 아래와 같이 두 가지로 access control하는 방법을 생각할 수 있다.
    - Access control list: 파일별로, 누구에게 어떤 접근권한이 있는지 표시
    - Capability: 사용자별로, 자신이 접근권한을 가진 파일 및 해당 권한 표시
    - 그러나 여전히 오버헤드가 크다...

  - **Grouping**

    - 전체 user를 owner, group, public의 세 그룹으로 구분
    - 각 파일에 대해 세 그룹의 접근권한(rwx)을 3비트씩 표시
    - 예) UNIX

  - **Password**

    - 파일마다 password를 두는 방법(디렉토리 파일에 두는 방법도 가능)
    - 모든 접근 권한에 대해 하나의 password: all or nothing
    - 접근권한별  password: 암기문제, 관리 문제



## File System의 Mounting

다른 partition에 있는 root file system에 접근해야 한다면?

<img width="508" alt="스크린샷 2021-05-06 오후 12 02 38" src="https://user-images.githubusercontent.com/72622744/117247603-86c0f300-ae79-11eb-8085-abcc8219e8f8.png">
샷 2021-05-06 오후 12.02.38.png)



## Access Methods

- 시스템이 제공하는 파일정보의 접근 방식
  - **순차접근(sequential access)**
    - 카세트테이프를 사용하는 방식처럼 접근
    - 읽거나 쓰면 offset은 자동적으로 증가
  - **직접 접근(direct access, random access)**
    - LP 레코드 판과 같이 접근하도록 함
    - 파일을 구성하는 레코드를 임의의 순서로 접근할 수 있음







