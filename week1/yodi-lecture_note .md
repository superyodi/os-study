# [week1] OS lecture note 

## [Chapter2] System Structure & Program Execution



운영체제 설명에 앞서 하드웨어적 동작을 설명하는 챕터

<img width="935" alt="스크린샷 2021-04-01 오전 9 12 36" src="https://user-images.githubusercontent.com/31922389/113261628-a90ba200-930a-11eb-97a4-8a5b427ef546.png">

### **컴퓨터 시스템 구조**

CPU, Memory, Input과 Output디바이스로 이뤄져 있다. 



#### CPU

+ Interrupt line
  + CPU 내부
  + CPU는 메모리에서 Instruction을 읽어서 실행
  + Instruction을 실행하다가 끝나고 나면 Interrupt line에 테스크가 없는지 확인 (반복)
  + 사용자가 입력한 정보가 buffer에 들어왔다. 그때 device controller가 CPU의 interrupt line에 interrupt 요청을 한다

+ 레지스터
  
+ 메모리보다 빠름, 데이터 저장
  
+ Mode bit

  + CPU를 운영체제가 가지고 있냐, 사용자 프로그램이 가지고 있냐를 표시

  + Mode bit을 통해 하드웨어적으로 두 가지 모드의 operation 지원

    + **1 (사용자 모드)**: 사용자 프로그램 수행

      + 제한된 intruction만 실행 가능

    + **0(모니터 모드)**: OS 코드 수행

      + 모든 instruction 실행 가능
+ IO 디바이스 접근 가능
      
      

---



+ Timer
  + 특정 프로그램이 운영체제 독점하는 것을 방지하기 위해 만들어짐
  + 시분할을 위해 만들어짐 (time sharing)
  + task가 CPU에서 실행될때 타이머에서 실행시간을 정함
  + 실행시간이 넘어가면 CPU는 다음 task로 넘어감, 시간 지나도 이어서 할 수 없음
  + 정해진 시간이 지나면 제어권이 사용자 프로그램에서 운영체제로 넘어오게 하기 위함

</p>



+ 운영체제

  + 타이머의 도움을 받아 CPU가 프로그램(자원)을 효율적으로 처리할 수 있도록 관리한다

  

+ IO device

  + device controller (하드웨어)
    + 디바이스를 전담하는 작은 CPU역할
    + (덧) device driver는 메모리에 있는 소프트웨어
  + local buffer
    + 메모리 처리

+ DMA(Direct Memory Access) controller

  + IO 디바이스가 버퍼에 입력한 내용들을 DMA가 모아서 Memory에 복사한 후 CPU에 interrupt를 한번에 함 



### 입출력의 수행

+ 모든 입출력 명령은 특권 명령 ---> 운영체제를 통해서만 IO 접근 가능
+ **사용자 프로그램은 어떻게 IO를 하는가?**
  + IO 요청 --> **시스템 콜 (소프트웨어 인터럽트)**
    + 사용자 프로그램이 운영체제의 커널(함수)를 호출하는 것
  + IO 작업 완료 --> 인터럽트 (하드웨어적)
    + IO가 운영체제한테 작업이 다 끝났다고 알려주기 위함

**인터럽트**

+ 인터럽트가 들어오면 CPU 제어권이 자연스럽게 OS로 넘어간다

+ Interrupt(하드웨터 인터럽트): 하드웨어가 발생시킨 인터럽트, *보통 이럴때 인터럽트라고 한다*
+ Trap(소프트웨어 인터럽트)
  + Exception: 프로그램이 오류를 범한 경우
  + System call: 프로그램이 커널함수를 호출하는 경우
+ 인터럽트 관련 용어
  + 인터럽트 벡터
    + 인터럽트의 종류를 구분하기 위해 함수의 주소들을 정의해 놓음 
+ *현재의 운영체제는 인터럽트에 의해 구동된다*



**시스템 콜**

+ **사용자 프로그램이** 운영체제의 서비스를 받기 위해 **커널함수를 호출**하는 것



---

> chapter2 -2



### 동기식 입출력과 비동기식 입출력

<img width="866" alt="스크린샷 2021-04-01 오전 9 26 14" src="https://user-images.githubusercontent.com/31922389/113261655-af9a1980-930a-11eb-8b6e-19fc80a5687f.png">






**동기식 입출력 (synchronous I/O)**

+ IO **요청 후** 입출력 **작업이 완료된 후에야**, 제어가 사용자 프로그램에 넘어감
+ IO 작업을 기다림
+ 구현 방법 1
  + IO가 끝날때까지 CPU를 낭비시킴
  + 매 시점 하나의 IO만 일어날 수있음
+ 구현방법 2
  + IO처리가 완료될때까지 해당 프로그램에서 CPU를 뺏음
  + IO처리를 기다리는 줄에 그 프로그램 줄 세움 (?)
  + 다른 프로그램에게 CPU를 줌



**비동기식 입출력(asynchronous IO)**

+ IO가 시작된 후 입출력 작업이 끝나기를 기다리지 않고 제어가 사용자 프로그램에 즉시 넘어감

  

<u>*동기, 비동기 입출력 모두 IO완료는 **인터럽트**로 알려준다*</u>



### DMA(Direct Memory Access)

+ 입출력 장치를 메모리에 가까운 속도로 처리하기 위해 사용
+ CPU interrupt 없이 device controller가 device의 buffer stotage의 내용을 **메모리에 block 단위로 직접 전송**
+ Byte 단위가 아니라 block단위로 인터럽트를 발생시킴



### 저장장치 계층 구조

휘발성: 컴퓨터의 전기가 나가면 휘발되는 메모리

Caching: 더 빠른 저장장치에 많이 쓰이는 데이터들을 복사해 놓는 것



(아래에서 위로 갈수록 더 빠르고 비싸고 휘발성 )

| Primary, 휘발성 메모리 |
| ---------------------- |
| Registers              |
| Cache Memory           |
| Main Memory            |

| Secondary, 비휘발성 메모리 |
| -------------------------- |
| Magnetic Disk              |
| Optical Disk               |
| Magnetic Tape              |



### 프로그램의 실행, 메모리 적재 과정을 중심으로

<img width="911" alt="스크린샷 2021-04-01 오전 10 21 32" src="https://user-images.githubusercontent.com/31922389/113261693-bb85db80-930a-11eb-9306-a819407fc2c9.png">



+ 디스크에 존재하던 실행파일이 메모리에 적재(load)됨
  + A라는 프로그램의 모든 실행파일이 메모리에 적재되는게 아니고 당장 필요한 일부분만 메모리에 적재된다
  + 프로그램 A에서 당장 필요한 부분을 a, 나머지 부분을 b라고 한다면
    + a는 메모리에 적재되고
    + b는 디스크의 swap공간에 놓여진다
+ 프로그램이 CPU를 할당받고 instruction을 수행할 수 있는 상태



### 프로세스 구조

[참고](https://velog.io/@underlier12/OS-14-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EA%B5%AC%EC%A1%B0)

+ Stack
  + 함수 호출 주소
  + 지역 변수
+ Data
  + 전역 변수
+ Code(Text)
  + 기계어



**가상 메모리**

+ 프로세스(실행되고있는 프로그램)는 각자의 주소공간을 가지는데 이것을 가상메모리라고 한다
+ 프로세스마다 실제 물리적 메모리의 주소와 독립적으로 각자의 주소를 가지기때문에 가상메모리 개념이 생겨남 





### 커널의 주소 공간

<img width="905" alt="스크린샷 2021-04-01 오전 10 22 17" src="https://user-images.githubusercontent.com/31922389/113261759-cc365180-930a-11eb-829f-2a8116839763.png">


+ Code
  + 코드 저장
+ Data
  + 자원(CPU, Disk 등)을 관리하기 위한 자료구조
  + 프로세스들을 저장하기 위한 자료구조(PCB)
+ Stack
  + 함수 호출시 복귀 주소를 저장하기 위해 사용
  + 수행중인 프로세스마다 별도의 스택을 둬서 관리
    + 커널은 일종의 공유 코드.  모든 사용자 프로그램이 시스템 콜을 통해 커널의 함수를 접근할 수 있으므로, 일관성 유지를 위해 각 프로세스마다 커널 내에 별도의 스택을 둠




 
