

# [week5] OS lecture note



## [Chapter8] Memory Management



> 주소 바인딩



메모리는 주소를 통해 접근하는 매체



### 논리적, 물리적 주소



+ 논리적 주소 (*Logical address* / *Virtual address*  )
  +  프로세스마다 독립적으로 가지는 주소공간
  +  각 프로세스마다 0번지부터 시작
  +  CPU가 보는 주소는 논리적 주소
  
+ 물리적 주소 (*Physical address*)
  + 메모리에 실제 올라가는 위치



+ 주소 바인딩: 주소를 결정하는 것

  + Symbolic Address ---> Logical Address ---(<u>이 시점이 언제인가?</u>)---> Physical address

  + 심볼릭 어드레스

    + 프로그래머는 심볼로 된 주소 사용

    + 이게 컴파일 되면 숫자로 된 로지컬 어드레스가 만들어진다. 

      

### 주소 바인딩

CPU가 바라보는 주소는 logical address다. 

주소를 바인딩하는 방식은 프로그램이 적재되는 물리적 메모리의 주소가 결정되는 시기에 따라 세 가지로 분류될 수 있다. 



+ 컴파일 타임 바인딩 (Compile time binding)
  + **프로그램을 컴파일할때** 물리적 메모리의 주소가 결정됨
  + 컴파일 이후 프로그램이 올라가 있는 물리적 메모리의 위치를 변경하고자한다면 다시 컴파일 해야한다.
  + 현대의 시분할 컴퓨팅 환경에선 잘 사용하지 않는다. 
  
+ 로드 타임 바인딩 (Load time binding)
  + **프로그램의 실행이 시작될때** 프로그램의 물리적 메모리 주소가 결정됨
  + 컴파일러가 relocatable code를 생성한 경우 가능하다. 
  
+ 실행 타임 바인딩 (Execution time binding / Run time binding)
  + 프로그램 실행 이후에도 프로세스의 메모리상 위치를 옮길 수 있다.
  + CPU가 주소를 참조할때마다 해당 데이터가 물리적 메모리의 어느 위치에 존재하는지 address mapping table을 통해 binding을 점검해야한다.
  + **MMU** 라는 하드웨어적인 지원이 필요하다. 



<img width="926" alt="스크린샷 2021-04-27 오후 8 14 40" src="https://user-images.githubusercontent.com/31922389/116249190-848ad480-a7a7-11eb-9dbf-a3c70e7b8175.png">



### MMU (Memory Management Unit)

+ 논리적 주소를 물리적 주소로 매핑해주는 하드웨어적 장치 (기준 레지스터와 한계 레지스터로 이루어짐)
+ MMU 기법은 CPU가 특정 프로세스의 논리적 주소를 참조하려할때 그 주소값에 *기준 레지스터*의 값을 더해 물리적 주소값을 얻어낸다. 
  + **<u>기준 레지스터(basic register)</u>**
    + 재배치 레지스터(relocation register) 라고도 한다
    + 해당 프로세스의 물리적 메모리 시작주소를 가지고 있다. 
+ MMU 기법은 프로그램의 주소공간이 물리적 메모리의 한 장소에 연속적으로 적재되는 것으로 가정한다. 
  + 그러므로 해당 프로그램이 적재되는 물리적 메모리상의 시작 주소만 알면 주소 변환을 쉽게 할 수 있다.
+ MMU 기법에서 사용자 프로그램이나 CPU는 <u>논리적 주소만 다룬다.</u> 
+ CPU가 논리적 주소 100을 참조한다고 할 때 재배치 레지스터에 저장된 프로세스의 물리적 시작주소에 100을 더해서 CPU가 요청하는 프로세스의 물리적 주소를 알아낼 수 있다.
  + Context Switching마다 재배치 레지스터에 저장된 프로세스의 물리적 시작주소가 Update된다. 
+ 다중 프로그래밍 환경에서 물리적 메모리 안에 여러개의 프로세스가 동시에 올라와 있는 경우가 대부분이다. 
  + 위 방식을 선택했을 경우 [CPU가 요청한 논리적 주소 + 재배치 레지스터 안의 물리적 시작주소] 의 결과가 실제 메모리에서 벗어나는 문제가 발생할 수 있다.
    + 이런 상황을 방지하기 위해 **한계 레지스터**를 사용한다.
  + **<u>한계 레지스터(limit register)</u>**
    + 프로세스가 자신의 주소 공간을 넘어서는 메로리 참조를 하려하는지 체크
    + 현재 CPU에서 수행중인 프로세스의 크기를 담고 있다. 
    + 만약 실제 프로그램 크기보다 논리적 주소 크기가 크다면 trap 걸어서 addressing error





<img width="921" alt="스크린샷 2021-04-27 오후 8 27 23" src="https://user-images.githubusercontent.com/31922389/116403454-89fc2380-a868-11eb-80df-5138ed60a13b.png"  >





---



> 메모리와 관련된 용어



+ 동적 로딩
+ 동적 연결
+ 중첩 (overlays)
+ 스와핑 (swapping)

---



### Dynamic Loading



+ 프로세스 전체를 메모리에 다 올리는 방식이 아닌 해당 루틴이 불려질때 메모리에 load하는 방식

+ 프로그램의 대부분을 차지하는 코드들은 오류처리 루틴이다. 이 루틴들은 늘 사용되진 않는다. 

  + 처음에 올려놓지 않고 필요한 상황이 생기면 그때 load한다. 

+ 운영체제의 특별한 지원 없이 프로그램 자체에서 구현 가능

+ 운영체제가 라이브러리를 통해 지원할 수 있다. 

+ 지금 OS의 페이징 기법은 운영체제가 직접 관리

  + 하지만 개념적인건 프로그래머가 결정

  + 프로그래머가 직접하는건 아니고 라이브러리의 힘을 빌려 구현

  

### Overlays

+ 프로세스의 주소공간을 분할해 실제 필요한 부분만을 메모리에 적재하는 기법
+ 동적 로딩과 개념적으로 유사하지만 사용하는 이유는 다르다.
  + 동적 로딩
    + 다중 프로그래밍 환경에서 메모리의 이용률을 향상시키기 위해
    + 메모리에 더 많은 프로세스를 동시에 올려놓고 실행하기 위한 <u>전략</u>
  + 오버레이
    + 단일 프로세스만을 메모리에 올려놓는 환경에서 메모리 용량보다 큰 프로세스를 실행하기 위한 <u>어쩔수없는 선택</u>
+ 프로그래머가 프로그램을 실행시킬때 <u>수작업으로 쪼개서 메모리에 올리도록 함</u>
+ 운영체제의 지원이 없음





### Swapping

스와핑: 메모리에 올라온 프로세스의 주소공간 전체를 디스크의 스왑영역에 일시적으로 내려 놓는 것



+ Backing Store (= swap area)
  + 디스크 내 파일 시스템과는 별도로 존재하는 일정 영역
    + 파일 시스템:  비휘발성 저장공간
    + 스왑 영역: 프로세스가 수행중일 경우에만 디스크에 일시적으로 저장 (휘발성)
  + 다수의 사용자 프로세스를 담을 수 있을만큼 용량이 크고 접근 속도가 빨라야한다.



+ Swap in / Swap out

  + Swap in : 디스크에서 메모리로 올리는 작업

  + Swap out: 메모리에서 디스크로 내리는 작업
  + 스와핑: 중기 스케쥴러에 의해 스왑 아웃시킬 프로세스를 선택한다. 

+ 컴파일 타임 바인딩 or 로드 타임 바인딩에선 원래 메모리 위치로 swap in

+ 실행 타임 바인딩에선 빈 위치에 아무데나 swap in 가능

+ 역할: 메모리에 있는 프로세스의 수 조절

+ 소요 시간: 탐색 시간 (seek time)  or 회전지연시간(rotational latenct) <<<<< 전송시간(transfer time)





### Dynamic Linking

+ linking: 목적파일과 라이브러리 파일들을 묶어 하나의 실행파일을 생성하는 과정
  + objet file: 프로그래머가 작성한 소스코드를 컴파일해서 생성된 파일
  + library file: 이미 컴파일된 파일
+ Static linking
  + 라이브러리가 프로그램의 실행파일 코드에 포함됨
  + 실행파일의 크키가 커짐
  + 동일한 라이브러리를 각가의 프로세스가 메모리에 올리므로 메모리 낭비
    + eg. printf 함수의 라이브러리 코드

+ Dynamic linking
  + <u>Linking을 execution time 까지 미루는 기법</u>
  + 라이브러리가 실행시 link됨
  + 라이브러리 호출 부분에 라이브러리 루틴의 위치를 찾기 위한 stub(작은 코드)을 둠
  + 라이브러리가 이미 메모리에 있으면 그 루틴의 주소로 가고 없다면 디스크에서 읽어옴
  + 운영체제의 도움 필요
  + 다이나믹 링킹을 하는 shared library 파일
    + 윈도우에서는 DLL , 리눅스에선 shared object





> 물리적 메모리의 할당 방식

+ 연속 할당 방식
  + 고정 분할 방식
  + 가변 분할 방식
+ 불연속 할당 방식
  + 페이징 기법
  + 세그멘테이션 기법
  + 페이지드 세그멘테이션 기법



### Allocation of Physical Memory

+ 메모리는 두 영역으로 나눠서 사용
  + OS 상주 영역
    + 낮은 주소
  + 사용자 프로세스 영역
    + 높은 주소
+ 사용자 프로세스 영역의 할당 방법
  + Contiguous Allocation (연속적 할당)
    + 물리적 메모리를 다수의 분할로 나누어 <u>하나의 분할에 하나의  프로세스가 적재되도록 함</u>	
      + Fixed Partition Allocation (고정분할)
      + Variable Paritition Allocation (가변분할)
    
  + Noncontiguous Allocation (불연속적 할당)
    + <u>하나의 프로세스를 물리적 메모리의 여러 영역에 분산하여 적재되도록 함</u>
    + *현대 시스템에선 불연속적 할당 사용*
      + Paging
      + Segmentation
      + Paged Segmentation





### 연속할당

<img width="944" alt="스크린샷 2021-04-27 오후 8 57 12" src="https://user-images.githubusercontent.com/31922389/116403688-d8a9bd80-a868-11eb-9d5c-07a397d964ca.png"> 

#### 고정분할

+ 물리적 메모리를 주어진 개수만큼의 영구적인 분할로 미리 나눠두고 각 분할에 하나의 프로세스를 적재해서 실행되도록 한다. 

+ 단점: 낭비되는 조각들이 생김
  + 외부 조각
    + 프로세스를 할당하기에 메모리 조각의 크기가 작아서 놀고있는 메모리 조각
  + 내부 조각
    + 프로세스의 크기보다 더 큰 메모리 조각이 할당돼서 남아서 놀고있는 메모리 조각

#### 가변분할

+ 메모리에 적재되는 프로그램의 크기에 따라 분할의 크기 및 개수가 동적으로 변하는 방식
+ 내부조각은 발생하지 않음
+ 외부조각 발생 가능성
  + 프로그램이 종료하고 나서 빈 메모리 공간에 들어갈 프로세스가 없을 경우 (프로세스가 들어가기에 공간이 적음)



### Hole

+ 가용 메모리 공간
+ 다양한 크기의 hole들이 메모리 여러곳에 흩어져있음
+ 프로세스가 도착하면 수용가능한 hole을 할당
+ 운영체제는 Hole과 사용되고 있는 메모리 공간을 각각 관리하고 있어야한다. 



### 동적 메모리 할당 문제

주소공간의 크기가 n인 프로세스를 메모리에 올릴때 물리적 메모리 내 가용 공간중 어떤 위치에 올릴것인지 결정

+ First-fit
  + 크기가 n이상인 것 중 최초로 발견되는 hole에 할당
+ Best-fit
  + 크기가 n이상인 **가장 작은 hole을 찾아서 할당**
  + hole들의 리스트가 크기순으로 정렬되지 않은 경우 모든 hole들의 리스트들을 탐색해야 함
  + 많은 수의 아주 작은 hole들이 생성됨 
+ Worst-fit
  + 가장 큰 hole에 할당
  + 역시 모든 hole 리스트를 탐색해야 함
  + 상대적으로 아주 큰 hole들이 생성됨 
+ *First-fit, Best-fit이 Worst-fit 보다 속도와 공간 이용률 측면에서 효과적인 것으로 알려짐*






### Compaction

+ 외부조각 문제를 해결하는 한가지 방법
+ 여러가지에 모여있는 hole을 한곳으로 미는 작업

+ 매우 비용 많이 듦

+ 런타임 바인딩이 지원되는 상황에서 가능



------> <u>컴팩션보다 최소한의 이동으로 큰 hole을 만드는게 좋다.</u> 









---

> 페이징 기법



+ 주소변환 기법
+ 페이지 테이블의 구현
+ 계층적 페이징 (2단계 페이지 테이블)
+ 역페이지 테이블
+ 공유 페이지
+ 메모리 보호





연속 할당방식에선 논리 주소 ---> 물리 주소변환 쉽게 이루어짐





### Paging

프로그램을 구성하는 주소공간이 동일한 크기의 페이지로 잘려져 메모리의 어느 위치에도 올라갈 수 있는 방법

+ 일부는 백킹 스토어에, 일부는 물리적 메모리에 혼재시키는 게 가능하다



<dir>

연속 할당방식에선 논리 주소에서 물리 주소 변환이 쉽게 이루어진다. 하지만 페이징 기법에선 하나의 프로그램이 여러개의 페이지로 분할되어 메모리에 적재되기 때문에 <u>각각의 페이지가 어느 위치의 메모리에 올라가있는지 물리적 주소를 알아내기 위해선 페이지 단위로 주소 계산을 해야한다.</u> (단점) 





+ 페이지 프레임
  + 물리적으로 페이지가 들어갈 수 있도록 하는 공간
  + 빈 프레임이 있으면 어떤 위치든 사용될 수 있음
    + (장점) 동적 메모리 할당에서 어느 위치에 어떤 프로세스를 올릴것인지 고민해야하는 문제를 해결한다.

+ 페이지 테이블
  + 모든 프로세스가 각각의 주소 변환을 위한 페이지 테이블을 가진다.
  + 프로세스가 가질 수 있는 페이지의 갯수만큼 주소 변환 엔트리를 가지게 된다.
  + 각각의 엔트리에 페이지가 어떤  frame number를 가지고 있는지 알려준다. 



<img width="847" alt="스크린샷 2021-04-28 오후 7 20 13" src="https://user-images.githubusercontent.com/31922389/116403879-0e4ea680-a869-11eb-8f18-9c77c2d07162.png">





#### Address Translation Architecture (주소 변환 기법)



CPU가 사용하는 논리적 주소를 페이지 주소(p)와 페이지 오프셋(d)으로 나눠서 주소 변환에 사용한다

+ 페이지 주소(p)
  + 페이지 테이블 접근시 인덱스로 사용됨
  + 인덱스의 entry에는 해당 페이지의 물리적 메모리의 시작 주소(base address)가 저장된다.
  
+ 페이지 오프셋(d)
  + 하나의 페이지 내에서의 변위(*한 점의 최종 위치와 처음 위치 간의 차이*)를 나타낸다.
  + base address + 변위(displacement) ---> 실제 메모리 주소



<img width="847" alt="스크린샷 2021-04-28 오후 7 22 24" src="https://user-images.githubusercontent.com/31922389/116403939-1d355900-a869-11eb-94c7-d17d568cba98.png">





### Page Table 구현



+ 보통 페이지 하나 크기는 4KB

+ 페이지 테이블은 메인 메모리에 상주
+ PTBR(Page-Table-Base-Register)
  + 페이지 테이블의 시작 주소
+ PTLR(Page-Table-Length-Resister)
  + 페이지 테이블의 크기

+ 메모리 접근 2번 필요
  1. 페이지 테이블 접근(주소 변환)을 위해
  2. 실제로 데이터 접근을 위해

+ 메모리 접근 2번하면 속도 넘넘 느린데 어떡함??
  + **TLB라고 불리는 캐시 사용**



### TLB

속도 향상을 위해 별도의 하드웨어 사용 ---> TLB라는 일종의 캐시

+ TLB(Translation Look-aside Buffer)

+ 메인메모리와 CPU 사이에 있음

+ 메모리 주소변환을 위해 별도의 캐시메모리를 두고 있음

+ TLB는 페이지 테이블에서 **빈번히 참조되는 일부 엔트리를 캐싱**

  + 가격이 비싸서 페이지 테이블의 모든 정보를 담을 수 없다
  + 접근속도 빠름
  + CPU가 주소를 주면 메모리 접근 전에 TLB 먼저 check

+ 모든 페이지에 대한 정보를 가지고 있지 않기 때문에 논리적인 테이블번호 P와 주소 변환된 프레임번호 F를 쌍으로 가지고 있어야 함

  + TLB에 해당하는 페이지가 없을땐 페이지 테이블에서 찾는다

+ TLB를 구현하기 위해선 병렬탐색이 가능한 연관 레지스터 (Associative Resister)를 사용한다. 

+ 페이지 테이블이 각각의 프로세스마다 존재하듯이 TLB도 프로세스마다 존재한다.

  + 다른 프로세스로 CPU가 넘어가면 TLB도 달라져야하므로 <u>Context Swith발생할때 flush 해서 TLB 비워야한다.</u> 

  

  



<img width="829" alt="스크린샷 2021-04-28 오후 7 30 21" src="https://user-images.githubusercontent.com/31922389/116403999-2cb4a200-a869-11eb-9722-d278d0cd6e8c.png">





<img width="867" alt="스크린샷 2021-04-28 오후 7 44 24" src="https://user-images.githubusercontent.com/31922389/116404049-3b02be00-a869-11eb-9dcf-5efe98cdd0c2.png">





---



### Two-Level Page Table (계층적 페이징)

현대 컴퓨터는 address space가 매우 큰 프로그램을 지원한다. 

<div>
#### 문제 상황

각 프로그램의 물리적 크기와 실제 논리적 크기는 독립적이다. 

프로그램마다 가지고 있는 가상 메모리의 크기는 최대 어디까지 가능할까? 

32bit address를 사용한다 가정할때, 최대 2<sup>32</sup>(4G)의 주소 공간을 제공한다. 



> **참고**
>
> 
>
> 2<sup>10</sup>B ----> 1K
>
> 2<sup>20</sup>B ----->  1M
>
> 2<sup>30</sup> B ----> 1GB
>
> 2<sup>32</sup>B  ----> 4GB



+ 페이지 사이즈가 4K일때, 4GB로 표시할 수 있는 공간을 4KB로 쪼개면 전체 1M개의 페이지 갯수가 나온다. 
+ 하지만 대부분 프로그램은 4G의 주소공간 중 지극히 일부분만 사용하므로 **page table 공간이 심하게 낭비됨** 



</div>

#### **해결 방안**

+ 페이지 테이블 공간이 낭비되는 것을 막기 위해 **Two-level**로 구성한 테이블을 사용한다.
  + page table 자체를 page로 구성
+ 사용되지 않는 주소 공간에 대한 outer page table의 엔트리 값은 NULL (대응하는 inner page table) 없음 



 

<img width="881" alt="스크린샷 2021-04-28 오후 7 46 59" src="https://user-images.githubusercontent.com/31922389/116775694-522af100-aa9f-11eb-8479-cdf20d3b968e.png">



#### 사용하는 이유 



+ 페이지 테이블은 프로그램마다 entry가 100만개 이상 필요하다

+ 2단계 테이블 사용하면 여전히 안쪽 테이블은 entry 100만개 이상 필요하다
  
+ Outer table이 하나 더 만들어지기 때문에 메모리적으로도 손해를 볼 것같은데 
  
+ **왜? 사용해야할까?**

  +   프로그램을 구성하는 공간 중 상당부분이 사용이 안되는데  사용 안되는 부분을 건너뛰고 엔트리를 만들 순 없다.

  + 비록 사용이 안되는 페이지들이 있다해도 Maximum 크기만큼 outer page를 만들어야함

    +   하지만 page table에서는 실제로 사용되는 부분만 실제 주소 할당되고 <u>사용 안되는 부분은 null을 가리키도록 함</u> 

    

+ offset

  논리적 주소에서 바깥 테이블의 index를 가지고 주소 변환정보를 얻음

  + 안쪽 페이지 테이블중 어떤 테이블인지  알려줌
  + 안쪽 페이지 테이블로 가서 페이지 번호를 가면 물리적인 페이지 프레임 번호를 얻게 됨 

#### Two-Level Paging Example

+ logical address 구성 ( 32bit machine + 4K page size 일때)
  + 20bit : page number
  + 12 bit: page offset
+ page table 자체가 page로 구성되기 때문에 page number는 다음과 같이 나뉜다. 
  (각 page table entry가 4KB)
  + 10bit의 page number
  + 10bit의 page offset
+ **따라서 logical address는 다음과 같다.** 
  
  + | page number   |               | page offset |
    | ------------- | ------------- | ----------- |
    | p<sub>1</sub> | p<sub>2</sub> | d           |
    | 10            | 10            | 12          |
    
    p<sub>1</sub> 은 outer page table의 index
    
  + p<sub>2</sub>는 outer page table의 page에서의 변위





<img width="919" alt="스크린샷 2021-04-28 오후 8 04 15" src="https://user-images.githubusercontent.com/31922389/121283657-77204880-c916-11eb-96be-88b15e041fcd.png">



<u>64bit 컴퓨터에서 1Page당 4KB일때 2단계 페이지 테이블에서  P1,P2는 몇 비트가 필요할끼?</u>

+ 서로 다른 n개의 정보를 구분하기 위해서 몇 비트가 필요한가?
+ n비트로 구분 가능한 서로 다른 위치가 몇 군데인가? 



#### Multilevel Paging and Performance

+ Address space가 더 커지면 다단계 페이지 테이블 필요

+ 각 단계 페이지 테이블이 메모리에 존재하므로 logical address의 physical address 변환에 더 많은 메모리 접근 필요

+ 캐쉬 메모리를 통해 메모리 접근 시간을 줄일 수 있음

+ 4단계 페이지 테이블을 사용하는 경우

  + 메모리 접근 시간이 100ns(나노초),  TLB 접근 시간이 20n일때, 
  + TLB hit ratio가 98%인 경우

  ====> EAT(Effective memory Access Time) = [0.98 * (100+20)] + [0.02 * (100 * 4 + 20 + 100)] = 128 ns

  **결과적으로 메모리 접근 시간을 28%만 다운 시킴** 

  

+ 대부분의 주소변환은 TLB를 이용하기때문에 다단계 페이지를 사용해도 메모리 접근시간 크지 않다. 

  

<img width="828" alt="스크린샷 2021-05-01 오전 11 32 41" src="https://user-images.githubusercontent.com/31922389/121285136-ca939600-c918-11eb-90ec-565211615df6.png">



사실은 페이지 테이블에 부가적인 bit 저장중

valid invalidi bit: 정보가 의미가 있는지 없는지 확인 (null인지 아닌지)



페이지의 주소공간이 가질수있는최대크기만큼 테이블 엔트리가 생겨야한다. 

인덱스를 통해 접근해야하는 테이블 자료구조 특성상 사용되지않는 데이터들을 위한 공간도 만들어져야한다. 



#### Memory Protectionn

page table의 각 entry마다 아래의 bit를 둔다.

+ Protection bit
  + page에 대한 접근 권한 (read / write/ read-only)
+ Valid-invalid bit
  + **valid**: 해당 주소의 frame에 그 프로세스를 구성하는 유효한 내용이 있음을 뜻함 (접근 허용)
  + **Invalid**: 해당 주소의 frame에 유효한 내용이 없음을 뜻함 (접근 불허)
    + 프로세스가 그 주소 부분을 사용하지 않는 경우
    + 해당 페이지가 메모리에 올라와 있지 않고 swap area에 있는 경우 



### Inverted Page Table

page table의 가장 큰 단점: page table의 공간이 매우 큼 

+ page table이 매우 큰 이유
  + 모든 process별로 그 logical address에 대응하는 모든 page에 대해 page table entry가 존재 
  + 대응하는 page가 메모리에 있든 아니든간에 page table에는 entry로 존재



#### Inverted page table

원래 process의 page table을 보고 physical memory 주소를 찾아간다

하지만 <u>inverted table은 frame의 갯수만큼 page table을 생성하고 f만큼 떨어진 곳에 주소를 저장한다.</u>



+ 시스템 안에 page table 1개 존재
  + page table의 엔트리가 <u>물리적 메모리의 페이지 프레임 갯수만큼 존재</u>

+ page frame 하나당 page table에 하나의 entry를 둔 것 

+ 각 page table entry는 각각의 물리적 메모리의 page frame이 담고 있는 내용 표시
  + (pid, process의 logical address)
+ 단점
  + 테이블 전체를 탐색해야함 
+ Solution
  + associative register 사용 (expensive)
    ---> TLB처럼 동시에 여러개 탐색 



<img width="935" alt="스크린샷 2021-05-01 오전 11 40 06" src="https://user-images.githubusercontent.com/31922389/121293565-cbcbbf80-c926-11eb-8fd1-302606b30f11.png">







### Shared Page

*하나의 프로그램을 여러개 실행할때 어떻게 메모리에 올려야 할까?*

Data 부분은 달라도 Code부분은 같을 것이다. 동일한 Code 부분을 메모리에 여러개 할당하는 것은 메모리 낭비가 될 수 있다. 

이 때 사용하는 방법 ---> **Shared Page**



+ Shared code
  + <u>**Re-entrant Code** (= Pure code)</u>
  + read-only로 하여 프로세스 간에 하나의 code만 메모리에 올림
    + e.g) text editors, compilers, window systems
  + Shared code는 모든 프로세스의 *logical address space*에서 동일한 위치에 있어야 함



+ Private code and data
  + 각 프로세스들은 독자적으로 메모리에 올림
  + Private data는 logical address space 내부의 어떤 곳에 저장돼도 상관 없음 



<img width="845" alt="스크린샷 2021-05-01 오전 11 45 52" src="https://user-images.githubusercontent.com/31922389/121294700-ca9b9200-c928-11eb-8d13-2a00bbdd2324.png">







---






> 세그멘테이션



### Segmentation

+ 프로세스의 주소 공간을 **의미 단위**인 segment로 나눠서 관리하는 기법
  + 작게는 프로그램을 구성하는 함수 하나하나를 세그먼트로 정의
  + 크게는 프로그램 전체를 하나의 세그먼트로 정의 가능 
  + 일반적으로는 *code, data, stack* 부분이 하나씩의 세그먼트로 정의됨 

+ 세그먼트는 다음과 같은 *logical unit* 들이다

  + e.g)  `main(), function, global variables, stack, symbol table, arrays`

    





### Segmentaion Architecture

+ logical address는 다음 두 가지로 구성
  + <segment-number, offset >
+ **<u>Segment table</u>**
  + 각각의 테이블 엔트리 구성요소 
    + **base**  - 세그먼트가 시작하는 물리주소
    + **limit** - 세그먼트의 길이
  + **Segment - table base register (STBR)**
    + 물리적 메모리에서의 segment table의 위치
  + **Segment - table length register (STLR)**
    + 프로그램이 사용하는 segment의 수
      + 만약 segment numer s가 STLR 보다 같거나 크다면 잘못된 주소 참조이므로 trap을 발생시킨다. 





<img width="867" alt="스크린샷 2021-05-01 오전 11 52 46" src="https://user-images.githubusercontent.com/31922389/116776064-a2a34e00-aaa1-11eb-8fa2-7fae3f69e782.png">



1. 세그먼트는 자신의 주소 정보뿐 아니라 다음과 같은 정보를 가지고 있다 
   + *s* : segment 번호
   + *d* : offset(세그먼트에서 떨어진 위치)
   
   
   
2. 세그먼트 기법을 실제로 사용하기 위해서 segment table이 필요하다. 
   segment table의 엔트리에는

   + *limit* : 세그먼트의 길이 
   + *base* : 세그먼트가 시작하는 물리적 주소 

   가 저장되어 있다. 

3. e.g ) s = 5인 경우

   1. segment table의 5번 엔트리를 참조해서 limit, base 값을 파악한다. 
   2. limit 값과 d  값을 비교한다.
      + d < limit == true
        + d + base 해서 실제 물리 주소공간을 알아내고 물리공간에 접근해 segment 5번의 값을 찾는다. 
      + d < limit == false
        + 잘못된 주소 참조이므로 **trap**을 발생시킨다. 

   

   

#### Example of Segmentation

<img width="862" alt="스크린샷 2021-05-01 오전 11 56 27" src="https://user-images.githubusercontent.com/31922389/121479969-87632100-ca05-11eb-9ef1-6a0c53d345b6.png">



#### Segmetation Architecture의 특징 

+ <u>Protection</u>
  + 각 세그먼트 별로 protrction bit가 있음
  + Each entry
    + Valid bit 
      + 0 => illegal segment
    + Read/Write/Execution 권한 bit
+ <u>Sharing</u>
  + shared segment
  + same segment numer

***segment는 의미 단위라서 sharing과 protection에서 paging보다 훨씬 효과적이다.*** 

+ <u>Allocation</u>
  + first fit / best fit
  + **외부 조각 발생 **
    + segment의 길이가 동일하지 않으므로 가변분할 방식과 동일한 문제가 일어난다. 



##### 페이징과 세그먼테이션 비교

공유, 보안 등 의미단위로 처리해야하는 일에서는 세그먼트를 사용한다. 

세그먼테이션은 hole이 발생하지만 오히려 페이징이 테이블로 인해 페이징 기법이 메모리 낭비가 더 심하다. 





> 페이지드 세그멘테이션



### Segmentation with Paging 

segmentation과 paging 기법을 함께 사용하는 방식 

<img width="848" alt="스크린샷 2021-05-01 오후 12 12 24" src="https://user-images.githubusercontent.com/31922389/116776085-c8c8ee00-aaa1-11eb-9a10-43b64f69589a.png">



1. 표면적으로는 segment 단위로 나눈다. 
2. 나눠진 segment를 page 단위로 나눠서 관리한다. 



**Pure segmention 과 차이점 **

+ segment table의 entry가 segment의 base address를 가지고 있는 게 아니라 segment를 구현하는 page table의 base address를 가진다. 
+ **외부조각 문제가 생기지 않는다. **




