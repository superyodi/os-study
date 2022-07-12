# Chapter 1. Intro

## 운영체제란?

컴퓨터 하드웨어 바로 위에 설치되어 사용자 및 다른 소프트웨어와 하드웨어를 연결하는 소프트웨어 계층

![os_info_1](/Users/johyeonyoon/Library/Application Support/typora-user-images/os_info_1.png)

- 협의의 운영체제(커널) : 운영체제의 핵심 부분으로 메모리에 상주하는 부분

- 광의의 운영체제: 커널 뿐 아니라 각종 주변 시스템 유틸리티를 포함한 개념

  

### 운영체제의 목표

- 컴퓨터시스템을 편리하게 사용할 수 있는 기능을 제공
- 컴퓨터의 **자원을 효율적으로 관리**(프로세서, 기억장치, 입출력 장치 등의 효율적 관리)



### 운영체제의 분류

#### 동시 작업 가능 여부

- 단일 작업(single tasking)
- 다중작업(multi tasking)

#### 사용자의 수

컴퓨터 한 대를 여러 사용자가 동시에 접속하여 사용할 수 있는지 여부

- 단일 사용자

  ex) Ms-dos, ms windows

- 다중 사용자

  ex) Unix, nt server

#### 처리 방식

- 일괄 처리(batch processing)
  - 작업 요청의 일정량 모아서 한꺼번에 처리
- 시분할(time sharing) 
  -  우리가 사용하는 컴퓨터 그 자체를 생각하면 된다
  - 여러 작업을 수행할 때 컴퓨터 처리능력을 일정 시간단위로 분할하여 사용하기 때문에 일괄처리시스템에 비해 짧은 응답시간을 가짐
- 실시간(Realtime OS)
  - 정해진 시간 안에 어떠한 일이 반드시 종료됨이 보장되어야 하는 실시간 시스템을 위한 OS
  - Ex) 원자로/공장 제어, 미사일 제어, 반도체 장비
  - 최근 실시간 시스템의 개념이 **확장**
    - Hard realtime system
    - **Soft realtime system**(영화를 보거나 하는 등의 데드라인이 있는 경우 사용할 수 있음)

### 몇 가지 용어

아래의 용어는 컴퓨터에서 여러 작업을 동시에 수행하는 것을 의미한다. 

- Multitasking
- Multiprogramming 
- Time sharing
- Multiprocess

Multiprogramming은 여러 프로그램이 메모리에 올라가 있음을 강조, Time sharing은 CPU의 시간을 분할하여 나누어 쓴다는 의미를 강조

- Multiprocessor: 하나의 컴퓨터에 cpu가 여러 개 붙어 있음을 의미



# Chapter 2. System Structure & Program Execution

<img src="https://media.vlpt.us/images/infoqoch/post/c380970b-14ae-4d07-a5de-b67dd5b60b82/1.jpg" alt="컴퓨터 시스템 구조" style="zoom:67%;" />

### Mode bit

- 사용자 프로그램의 잘못된 수행으로 다른 프로그램 및 운영체젱 피해가 가지 않도록 하기 위한 보호장치 필요
- Mode bit을 통해 하드웨어적으로 두 가지 모드의 operation지원
  - **1**: 사용자모드: 사용자 프로그램 수행, 모든 instruction 수행 가능함
  - **0**: 모니터모드: OS 코드 수행
    - 보안을 해칠 수 있는 중요한 명령어는 모니터모드에서만 수행가능한 명령으로 규정(특권명령)
    - Interrupt Exception 발생시 하드웨어가 mode bit을 0으로 바꿈
    - 사용자프로그램에게 cpu를 넘기기 전에 mode bit을 1로 세팅

### Timer

- 타이머
  - 정해진 시간이 흐른 뒤 운영체제에 제어권이 넘어가도록 인터럽트 발생시킴
  - 매 클럭 틱마다 1씩 감소
  - 타이머 값이 0이 되면 타이머 인터럽트 발생
  - cpu를 특정 프로그램이 독점하는 것으로부터 보호
- 타이머는 time sharing을 구현하기 위해 널리 이용
- 타이머는 현재 시간을 계산하기 위해서도 사용

### Device Controller

- I/o device controller
  - 실제 I/O 장치유형을 관리하는 일종의 작은 cpu
  - 제어 정보를 위해 control register, status register를 가짐
  - Local buffer를 가짐(일종의 data register)
- I/O는 실제 device와 local buffer사이에서 일어남
- Device controller는 I/O가 끝났을 경우 interrupt로 Cpu에 그 사실을 알림



**device driver**(장치 구동기) : os코드 중 각 장치별 처리루틴 -> software

**device controller(**장치제어기): 각 장치를 통제하는 일종의 작은 cpu -> hardware

### 입출력(I/O)의 수행

- 모든 입출력 명령은 특권명령
- 그렇다면 사용자프로그램은 어떻게 I/O를 하는가?
  - *시스템콜(system call)로 사용자프로그램은 운영체제에게 I/O요청
  - trap을 사용하여 인터럽트 벡터의 특정위치로 이동
  - 제어권이 인터럽트 벡터가 가르키는 인터럽트 서비스루틴으로 이동
  - 올바른 I/O 요청인지 확인후 I/O 수행
  - I/O 완료 시 제어권을 시스템콜 다음명령으로 옮김

### *시스템콜(System Call)

​	:사용자 프로그램잉 운영체제의 서비스를 받기 위해 커널함수를 호출하는 것

### 인터럽트(Interrupt)

현대의 운영체제는 인터럽트에 의해 구동된다. 

- 인터럽트
  - 주변장치가 cpu의 서비스가 필요한 경우 신호를 발생시켜 서비스를 요청하는데, 이때 발생시키는 신호를 인터럽트라고 한다.
  - 인터럽트 강한 시점의 레지스터와 program counter를 save한 후 cpu의 제어를 인터럽트 처리 루틴에 넘긴다.
- Interrupt(넓은 의미)
  - Interrupt(하드웨어 인터럽트): 하드웨어가 발생시킨 인터럽트
  - Trap(소프트웨어 인터럽트)
    - Exception: 프로그램이 오류를 범한 경우
    - System call: 프로그램이 커널 함수를 호출하는 경우
- 인터럽트 관련 용어
  - 인터럽트 벡터 : 해당 인터럽트의 처리루틴주소를 갖고 있음
  - 인터럽트 처리 루틴(=interrupt service routine, 인터럽트 핸들러): 해당 인터럽트를 처리하는 커널 함수



### 입출력 구조 - 동기식 입출력과 비동기식 입출력

- 동기식 입출력(synchronous I/O)
  - I/O 요청 후 입출력 작업이 완료된 후에야 제어가 사용자 프로그램에 넘어감
  - 구현방법 1
    - I/O가 끝날 때까지 cpu를 낭비시킴
    - 매 시점 하나의 I/O만 일어날 수 있음
  - 구현방법 2
    - I/O 가 완료될 때까지 해당 프로그램에서 cpu를 빼앗음
    - I/O 처리를 기다리는 줄에 그 프로그램을 줄 세움
    - 다른 프로그램에게 cpu를 줌
- 비동기식 입출력(asynchronous I/O)
  - I/O가 시작된 후 입출력 작업이 끝나기를 기다리지 않고 제어가 사용자 프로그램에 즉시 넘어감

두 경우 모두 I/O의 완료는 인터럽트로 알려줌

### DMA(Direct Memory Access)

- DMA(Direct Memory Access)
  - 빠른 입출력 장치를(자주 인터럽트가 일어남) 메모리에 가까운 속도로 처리하기 위해 사용
  - cpu의 중재 없이 device controller가 device의 buffer storage의 내용을 메모리에 block단위로 직접 전송
  - 바이트 단위가 아니라 block단위로 인터럽트를 발생시킴

### 서로 다른 입출력 명령어

- I/O를 수행하는 special instruction에 의해
- Memory mapped I/O에 의해

 ![Screen Shot 2021-03-31 at 10 22 59 PM](https://user-images.githubusercontent.com/72622744/113266940-c2afe800-9310-11eb-891a-77f462667174.png)



### 저장장치 계층 구조

Cpu에서 직접 접근하여 실행할 수 있는 매체를 primary 또는  executable이라고 하며, cpu 가 직접 접근하여 처리할 수 없는 매체를 secondary라고 한다. (아래 그림에서는 내부기억장치와 외부기억장치로 표현)

- Speed
- Cost
- Volatility(휘발성) :  위의 내부 기억장치 - 휘발성 매체 /  아래의 외부기억장치 - 비휘발성 매체

![컴퓨터 시스템의 동작원리-2](https://eunhyejung.github.io/assets/contents/content03.PNG)

  - Cashing : copying information into faster storage system

    

### 프로그램의 실행(메모리 load)

###### <img src="/Users/johyeonyoon/Downloads/Screen Shot 2021-04-01 at 2.26.45 PM.png" alt="Screen Shot 2021-04-01 at 2.26.45 PM" style="zoom: 33%;" />



File System안에 실행파일A, B(일반적으로 Bin). 실행파일을 실행시키게 되면 메모리안에서 프로세스로 올라가게 된다. 정확하게는 중간에 **가상메모리** 를 거치게 된다. **가상메모리는 각 실행파일마다 가지고있는 고유의 메모리 주소 공간이라고 부른다.**

프로세스마다 기계어 코드를 담고 있는 code, 변수 등 프로그램이 사용하는 자료구조를 담은 data, 함수를 실행하고 리턴할 때 데이터를 쌓았다가 내보내는 stack이 있는 address space가 존재한다.  이러한 주소공간을 갖고 있는 것을 물리 공간으로 옮겨서 실행시키게 된다. 물리 공간의 커널 영역은 항상 존재하지만 사용자프로그램은 프로그램 실행이 종료되면 주소공간이 생겼다가 사라지는 부분이다. 

그러나 메모리 낭비를 막기 위해 필요한 부분만 물리적 메모리에 올리고 현재 필요하지 않은 부분은 디스크 swap area에 내려놓게 된다.

하드디스크의 File Area과 하드디스크의 Swap Area는 Swap area는 전원이 나가면 의미가 없으나(휘발성) File area는 비휘발성이다. 


### 커널 주소 공간의 내용

![프로세스 1 - confinalst](https://kouzie.github.io/assets/OS/OS_3_2.png)





### 사용자 프로그램이 사용하는 함수

- 함수
  - 사용자 정의 함수: 자신의 프로그램에서 정의한 함수
  - 라이브러리 함수
    - 자신의 프로그램에서 정의하지 않고 갖다 쓴 함수
    - 자신의 프로그램의 실행 파일에 포함되어 있다.
  - 커널 함수
    - 운영체제 프로그램의 함수
    - 커널 함수의 호출 = 시스템 콜

