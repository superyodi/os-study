



# [week3] OS lecture note 



## [Chapter5] CPU Scheduling

> 지난시간 관련 내용

### CPU and I/O Bursts in Program Execution

<img width="872" alt="스크린샷 2021-04-08 오전 11 40 48" src="https://user-images.githubusercontent.com/31922389/113978110-7d466a00-987e-11eb-8e64-82de30cb3d78.png">

사용자 프로그램이 수행되는 과정은 CPU 작업과 IO 작업의 반복으로 구성된다. 

#### CPU burst

+ 사용자 프로그램이 CPU를 직접 가지고 빠른 명령을 수행하는 일련의 단계
+ 프로그램이 IO를 한번 수행하고 다음번 IO를 수행하기까지 직접 CPU를 가지고 명령을 수행하는 일련의 작업



#### I/O burst

+ IO 요청이 발생해 커널에 의해 입출력 작업을 진행하는 비교적 느린 단계
+ IO 작업이 요청된 후 완료되어 다시 CPU 버스트로 돌아가기까지 일어나는 일련의 작업



여러종류의 job(=process)이 섞여있기 때문에 CPU 스케쥴링이 필요하다

+ interactive job에게 적절한 response 제공 요망
+ CPU와 IO 장치 등 시스템 자원을 골고루 효율적으로 사용



### 프로세스의 특성 분류

프로세스는 특성에 따라 두 가지로 나뉜다

+ IO-bound process (Interactive job: 사용자와 소통을 많이하는)
  + IO 버스트를 많이 사용
  + many short CPU bursts

+ CPU-bound process
  + CPU 버스트를 많이 사용
  + few very long CPU bursts



### CPU Scheduler & Dispatcher

#### CPU Scheduler

+ Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다



#### Dispatcher

+ <u>새롭게 선택된 프로세스가 CPU를 할당받고 작업을 수행할 수 있도록 환경설정</u>하는 운영체제의 코드

+ CPU 스케줄러가 어떤 프로세스에게 CPU를 할당할지 결정했다면 **Dispatcher는 선택된 프로세스에게 실제로 CPU를 이양하는 작업을 담당**

  > Dispatcher 실행 프로세스

  + 현재 수행중이던 프로세스의 Context를 그 프로세스의 PCB에 저장한다
  + 새롭게 선택된 프로세스의 Context를 해당 프로세스의 PCB에서 복원한다
  + 시스템 상태를 사용자 모드로 전환해 사용자 프로그램에게 CPU의 제어권을 넘긴다
  + 사용자 프로그램은 복원된 Context 중 프로그램 카운터로부터 현재 수행할 주소를 찾을 수 있게 된다

  ---> 이런 과정을 거처  사용자 프로그램은 이전에 작업하던 것을 이어서 할 수 있다 (이 과정을 **context switch**라고 한다)

+ 디스패치 지연시간(Dispatch latency) : 디스페처가 하나의 프로세스를 정지시키고 다른 프로세스에게 CPU를 전달하기까지 걸리는 시간



CPU 스케쥴링이 필요한 경우는 프로세스에게 다음과 같은 상태변화가 있는 경우다

1. Running -> Blocked (eg, IO 요청하는 시스템콜)
2. Running -> Ready (eg, 할당시간만료로 tiemer interrupt)
3. Blocked -> Ready (eg, IO 완료 후 인터럽트)
4. Terminate

 

*1,4에서 스케줄링은 **nonpreemptive*** (강제로 빼앗지 않고 자진반납)

*All other scheduling is **preemptive*** (강제로 빼앗음)



---

### cpu 스케쥴링

 현재 ready queue에 들어있는 작업 중 어떤것을 CPU에 줄것인가



**크게 두가지 이슈**

+ cpu를 어떤 task에 줄것인가
+ cpu를 사용하고 있는 task가 일이 다 끝날때까지 기다릴것인가 뺏을 것인가



스케쥴링 유형

+ non-preemptive (비선점형)
+ preemptive (선점형)



### Scheduling Criteria (성능 척도)

> 시스템 입장

+ CPU utilization (이용률)
  + 전체시간 중 CPU가 일한 시간의 비율
  + 가능한 CPU를 일하게 해라
+ Throughput (처리량)
  + 주어진 시간동안 ready queue에 있는 작업 중 몇 개의 작업을 완료했는지
    + CPU 버스트를 완료한 프로세스의 개수
  + 최소시간 최대처리일수록 좋은 알고리즘



> 프로그램 입장



+ Turnaround time (소요시간)
  + CPU를 사용하러 와서 CPU를 <u>나올때까지</u> 걸리는 시간
  + 프로세스가 CPU를 쓰러 들어와서 IO하러 나가기까지
  + 예시) CPU를 사용하다가 IO연산을 위해 CPU를 자진반납했다면 CPU를 사용하기 위해 ready queue에 들어왔을때부터 CPU를 자진반납하기까지 걸리는 시간
+ Waiting time (대기 시간)
  + CPU를 사용하고자할때 기다린 시간
  + CPU가 선점형이라면 CPU 박탈, 선점을 계속 반복하며 중간중간 기다리는 시간들이 있을 것이다. <u>이런 기다리는 시간들을 합친 것</u>
+ Response time (응답 시간)
  + ready 큐에 들어와서 <u>처음 CPU를 얻기까지</u> 걸리는 시간



#### 예시

상황: 중국집



**주인 입장 (시스템)**

CPU utilization(이용률) : 주방장 총 얼마나 일함?

Throughput (처리량): 단위시간당 방문한 손님 수



**고객 입장 (프로그램)**

Turnaround time: 손님이 들어와서 나갈때까지 걸리는 시간

Waiting time(대기시간): 손님이 가게에서 기다린 총 시간

Response time(응답시간): 첫 음식이 나올때까지 기다리는 시간





#### FCFS (First-Come-First-Served)

+ 비선점형
+ 썩 효율적이진 않다
  + 먼저 도착한 긴 프로세스가 선점해버리면 다른 프로세스들은 오래 기다려야함
+ 앞에 있는 프로세스들의 시간에 따라 성능 차이가 많이 난다
+ **Convoy effect: ** 실행시간이 긴 프로세스가 먼저 도착해서 시간이 짧은 프로세스가 오래 기다려야하는 현상



#### SJF (Shortest-Job-First)

+ 제일 실행시간이 적은 프로세스에게 우선권을 준다

+ average waiting time을 최소화하는 알고리즘 

+ Two schemes

  + <u>Nonpreemptive</u>

    + 일단 CPU를 잡으면 해당 CPU burst가 완료될때까지 CPU를 preemption 당하지 않음

      

  + <u>Preemptive</u>

    + 현재 수행중인 프로세스의 남은 burst time 보다 더 짧은 CPU  burst time을 가지는 새로운 프로세스가 도착하면 CPU를 빼앗김
    + 이 방법을 **SRTF** (Shortest-Remaining-Time-First)
      + 남은 시간이 제일 짧은 CPU에게 우선권
      + average waiting time을 최소화 (optimal)
      
    
    
    

+ **문제점**: Starvation( 기아 현상 )

  CPU 사용시간이 긴 프로세스는 영원히 기다릴 수 있다.

+ 실제로는 CPU Burst Time을 알 수 없다 (프로세스를 얼마나 쓸지 정확히 모름)

  + 하지만 과거의 CPU burst time을 통해 추정은 가능

    + exponential averaging

      현재를 기준으로 최근건 가중치 크게, 오래된건 가중치 적게 반영한 결과가 얻어진다 



#### Priority Scheduling

+ 우선순위가 제일 높은 프로세스에게 CPU를 준다.
  + *preemptive*
  + *nonpreemptive*
+ SJF도 일종의 priority scheduling이다.
+ Problem
  + **Starvation**
+ Solution
  + **Aging** (경로우대)
    + 우선순위가 낮아도 오래기다린 프로세스는 우선순위를 높여준다





#### Round Robin (RR)

현대 컴퓨터에서 사용하고 있는 방식



+ CPU를 줄 때 동일한 크기의 할당시간(time quantum)을 주고 preemptive 한다

+ 장점: 응답시간이 매우 빠르다

  + n개의 프로세스가 ready queue에 있고 할당시간이 q time unit인 경우 

    각 프로세스는 최대 q time unit 단위로 CPU시간의 1/n을 얻는다.

    ----> 어떤 프로세스도 (n-1)* q time unit 이상 기다리지 않는다.

  

+ Perfomance

  + q too large ==> FCFS
  + q too small ==> context switch 오버헤드가 커진다. 
  + 일반적으로 q는 10 - 100 milliseconds 정도

    

+  일반적으로 SJF보다 average turnaround time이 길지만 response time은 더 짧다

+ CPU시간이 길고 짧은 여러 프로세스가 임의로 섞여있고 어떤 프로세스가 얼마나 사용할지 알 수 없는 상황에 사용하기에 적절한 스케줄링

  + 하지만 CPU사용시간이 100으로 동일한 프로세스 n개가 존재한는 경우 , 모든 프로그램이 각자 종료될때까지 n *100 시간 걸릴 수 있다 (모든 프로세스들이 다 동시에, 듣게 끝날수도 있다)



#### Multilevel Queue

(신분 극복 못함)

+ Ready queue를 여러개로 분할
  + foreground (interactive)
  + background (batch - no human interaction)
+ 각 큐는 독립적인 스케줄링 알고리즘을 가짐
  + foreground: **RR**
  + backgroundL **FCFS**
+ 큐에 대한 스케줄링이 필요
  + Fixed priority scheduling
    + 일단 foreground부터 처리하고 다음에는 background 처리한다
    + 문제: **Starvation** 가능성
  + Time slice
    + 각 큐에 CPU time을 적절한 비율로 할당
    + Eg. *80% to foreground in RR, 20% to background in FCFS*







<img width="865" alt="스크린샷 2021-04-13 오후 8 39 38" src="https://user-images.githubusercontent.com/31922389/114562334-61d9c580-9ca9-11eb-861c-3f115bbe85ff.png">







#### Multilevel Feedback Queue

<img width="878" alt="스크린샷 2021-04-13 오후 8 50 35" src="https://user-images.githubusercontent.com/31922389/114562379-6bfbc400-9ca9-11eb-853a-83ac6ede146d.png">

(프로세스 프롤레타리아 혁명 가능)

+ 여러개의 큐에 줄을 세우는건 멀티레벨 큐와 같지만 **큐들 간의 계층이동이 가능**하다는 차이가 있다
+ 계층이동 방법도 다양하다
  + case 1. 우선순위 큐에서 오래 기다린 프로세스는 우선 순위가 높은 큐로 승격
  + case 2. (대표적 방식, 사진)  빨리 들어오는 프로세스일수록 우선순위 높은 큐 주는데 q시간은 짧음.  아래로 갈수록 우선순위 낮은 큐지만 q시간 길게 줌





#### Multi-Processor Scheduling

<img width="840" alt="스크린샷 2021-04-13 오후 8 54 58" src="https://user-images.githubusercontent.com/31922389/114562545-8afa5600-9ca9-11eb-8372-c49ecfd7a1ca.png">







#### Real time Scheduling

+ Hard real-time systems
+ Soft real-time computing



#### Thread Scheduling

+ Local Scheduling (사용자가 결정)
  + 운영체제가 스레드 존재 모름
+ Global Scheduling (운영체제가 결정)
  + 운영체제가 스레드 존재 알고있음





#### Algorithm Evaluation

+ Queueing models

  + <img width="464" alt="스크린샷 2021-04-13 오후 9 03 04" src="https://user-images.githubusercontent.com/31922389/114562563-8fbf0a00-9ca9-11eb-8ae3-47385eab40ba.png">

    ​	

  + 확률분포로 주어지는 arrival rate와 service rate 등을 통해 각종 performance index 값을 계산

+ Implementation & Measurement (실측)
  
  + **실제 시스템**에 알고리즘을 구현하여 실제 작업에 대해서 성능 측정 비교
+ Simulation (모의 실험)
  
  + 알고리즘을 모의 프로그램으로 작성 후 trace(실제 프로그램에서 뽑은 예시)를 입력하여 결과 비교



---



## [Chapter6] Process Synchronization



### 데이터의 접근

<u>데이터 저장된 위치에서 데이터를 읽어 와서 연산을 한 후 결과를 다시 데이터 저장소에 저장한다.</u> 

데이터를 읽은 후에 데이터를 수정 후 결과를 다시 저장하기 때문에

**누가 어떤 데이터를 먼저 읽어갔느냐에 따라 Process Synchronization 문제가 발생**한다.



### Race Condition (경쟁상태)

여러 주체가 하나의 데이터를 동시에 접근하려할 때 Race Condition 가능성이 있다.

*문제가 되는 상황*

Storage의 count는 0일때 excution1에서 count(0)을 가져와서 --을 한다 

그 결과 count(-1)을 저장한다

그때 excution2에서도 count(0)을 가져와서 ++을 한다 

그 결과 count(1)을 저장해서 결국 count--, count++의 결과는 1이 된다. 

----> **Syncronize 문제**



#### OS에서 race condition은 언제 발생하는가?

 

+ kernel 수행 중 인터럽트 발생시

  ![RaceConditionInOS1](https://camo.githubusercontent.com/6acb19cee4f94770bace9ddef2b14be465e2d9847ac8bd13a485a6060ea3f6fe/68747470733a2f2f6a686939332e6769746875622e696f2f6173736574732f696d672f6f732f52616365436f6e646974696f6e496e4f53312e706e67)

  1. load

     1.  데이터를 읽어온 뒤(count 변수 값은 0) Interrupt Handler에 의해 Context Switch

     2. context에 count값 저장(0)

     3. Interupt Handler에서 context값을 가져와서 count-- 연산 실행

        **하지만 context 업데이트 안함**

  2. Inc
     1. Kernel로 되돌아와서 context에서 count 가져옴 (count == 0) 
     2. count++연산 실행 (count==1)
     3. count값 메모리에 저장

  결국 --, ++ 연산으로 0이 나와야하는데 1이 나옴
  

  **해결방법**

  먼저 하던 일을 마친 후에 Interrupt를 수행한다. 공유자원에 접근하는 동안에는 Disable Interrupt로 접근을 막는다.



+ Process가 system call을 하여 kernel mode로 수행중인데 context switch가 일어나는 경우

  

  + 두 프로세스 간에는 데이터 공유 공간이 없는데 System call을 하는 동안에는 kernel address space의 data에 접근하게 된다 
  + 이 작업 중간에 CPU를 **preempt**하면 Race Condition 발생

  + 해결책
    + **커널모드에서 수행중일때는 CPU를 preempt하지 않음**
    + 커널모드에서 사용자 모드로 돌아갈때 preempt
    
      

+ Multiprocessor에서 shared memory 내의 kernel data
  
  + 하지만 Multiprocessor의 경우 Interrupt Distable Disable/Enable로는 해결되지 않는다
  
  + **해결책**
    + 한번에 하나의 CPU만이 커널에 들어갈 수 있게 하는 방법 ------> 대단히 비효율적
    + **커널 내부에 있는 각 공유 데이터에 접근할때마다 그 데이터에 대한 lock / unlock을 하는 방법**





### The Critical-Section Problem

<img width="480" alt="스크린샷 2021-04-14 오전 10 58 17" src="https://user-images.githubusercontent.com/31922389/114820535-1a168380-9dfa-11eb-98a6-42bb3356e343.png">



### 프로그램적 해결법의 충족 조건

+ Mutual Exclusion (상호 배제 )
  + 특정 프로세스가 cs(critical section)부분 수행중이면 다른 모든 프로세스들은 그들의 cs에 들어가면 안된다
+ Process (진행)
  + 아무도 cs에 있지 않은 상태에서 cs에 들어가고자하는 프로세스가 있으면 cs에 들어가게 해줘야한다.
+ Bounded Waiting( 유한 대기)
  + 기다리는 시간이 유한해야한다
  + 특정 프로세스 입장에서 지나치게 오래 기다리는 starvation 방지
  + 프로세스가 cs에 들어가려고 요청한 후 부터 그 요청이 허용될 때까지 다른 프로세스들이 cs에 들어가는 횟수에 제한이 있어야한다
  + 발생 상황
    + cs에 접근해야하는 프로세스가 3개있을때 2개는 번갈아 들어가는데 나머지 하나는 들어가지 못하고 계속 기다리는 상황

*가정*

1. 모든 프로세스의 수행속도는 0보다 크다
2. 프로세스들 간의 상대적인 수행속도는 가정하지 않는다



프로세스들은 수행의 동기화를 위해 몇몇 변수를 공유할 수 있다 -----> synchronization variable




> 위 조건들을 충족하면서 프로세스 lock/unlock을 잘 하도록 하는 알고리즘들



### Algorithm 1

CPU에서 단일 인스트럭션이 끝나면 CPU를 빼앗길 수 있다. 

아래 코드와 같은 고급언어의 인스트럭션 문장들이 단일 인스트럭션이 아니기때문에 코드를 수행하는 도중에 CPU를 빼앗길 수 있어서 문제가 발생한다. 



<img width="689" alt="스크린샷 2021-04-15 오후 1 45 17" src="https://user-images.githubusercontent.com/31922389/114820555-200c6480-9dfa-11eb-8cf8-281f460afbec.png">

```c
// P_0의 코드

int turn = 0;


do {
  while(turn != 0);    /* My turn? */
  chritical section;
  turn = 1;            /* Now it's your turn */
  remainder section
}while(1)


// P_1의 코드

int turn = 1;

do {
  while(turn != 0);    /* My turn? */
  chritical section;
  turn = 0;            /* Now it's your turn */
  remainder section
}while(1)

```

#### 작동 방식

+ turn: cs에 들어갈 프로세스가 누군지 나타내는 변수
+ 프로세스 입장에서 자기 차례가 아니면 while문을 돌면서 기다린다
+ 자기 차례면 cs에 접근해서 작업 수행 
+ 작업이 끝나면 turn 변수를 다른 프로세스 값으로 바꿔서 그 프로세스 차례로 만들어줌



#### 문제점

mutual exclusion 조건은 만족하지만 **process 조건은 만족하지 못한다**

+ cs에 교대로 들어가도록 만들어져있기때문에 cs에 들어가려는 프로세스의 빈도가 빈번하지 않을때

  p1이 cs를 들어갔다 나와야 p2의 차례가 된다 

+ 턴을 교대로만 해주면 안되겠구나!



### Algorithm 2



프로세스들이 각자 자기의 flag를 가지고 있음

cs에 들어가고자할때 flag = true함

상대방 flag도 true일때 상대방 flag가 false가 될때까지 기다림

<img width="883" alt="스크린샷 2021-04-15 오후 1 57 03" src="https://user-images.githubusercontent.com/31922389/114820568-24d11880-9dfa-11eb-9f55-4d451574ea1e.png">

#### 문제점

mutual exclusion 조건은 만족하지만 **process 조건은 만족하지 못한다**

+ 기다리는 프로세스들이 flag true하고 눈치만 보다가 아무도 못들어갈 수 있음
+ cs에 들어갈때까지 깃발만 들다가 끝날 수 있음




### Algorithm 3 (Peterson's Algorithm)

<img width="799" alt="스크린샷 2021-04-15 오후 2 04 22" src="https://user-images.githubusercontent.com/31922389/114820574-28fd3600-9dfa-11eb-82aa-fc852b33bcb1.png">

turn과 flag 모두 사용

+ cs에 들어가고 싶을때 flag = true해서 cs에 들어가고자하는 의사표현 함
+ cs에 들어가고 나서 turn 값 다른 flag == true인 프로세스로 바꿔준다



**위 세가지 조건 모두 만족**



#### 문제점

**Busy Waiting** (*=spin lock*)

+ 계속 CPU와 memory를 쓰면서 wait
+ 내가 지금 CPU를 돌고있는데 하는거 없이 기다리다가 CPU 다쓰고 메모리로 돌아감 





### Synchronization Hardware

위같은 문제들은 데이터를 읽고 쓰는 것을 하나의 instruction으로 처리할 수 없기때문에 생긴다

하지만 하드웨어에서 읽음&쓰기를 하나의 instruction으로 해결가능하다면 문제 해결

e.g) *Test_and_Set()*

<img width="879" alt="스크린샷 2021-04-14 오전 11 43 20" src="https://user-images.githubusercontent.com/31922389/114820584-2c90bd00-9dfa-11eb-84bd-4d0a284c8727.png">



 



### Semaohores

+ 앞의 방식들을 추상화시킨 자료형

+ 프로그래머 입장에선 구현에 신경쓸게 아님

  

추상자료형: 기능의 구현부분을 나타내지 않고 기능 목록 부분만 나열한 것



**Semaphore S**

+ integer variable (정수값)

  + 자원의 갯수라고 생각하면됨

+ 아래의 두가지 atomic 연산에 의해서만 접근 가능

  

+ <img width="670" alt="스크린샷 2021-04-15 오전 10 25 08" src="https://user-images.githubusercontent.com/31922389/114820596-30244400-9dfa-11eb-8c37-e29875be1c38.png">


+ 실행과정
  + P(S): S를 획득하는 과정 (lock을 거는 과정)
  + V(S): S를 반납하는 과정 (lock을 푸는 과정)
    + (자원을 다 사용하고 나서 내어놓는 것)

automic하게 이뤄져야함

공유자원을 획득, 반납하는 과정을 처리



문제점: 자원이 없을때 P연산을 하게되면 while을 돌아도 어떤것도 하지 못하고 cpu시간을 다 쓰고 반납된다 



### Critical Section of n Processes

<img width="883" alt="스크린샷 2021-04-15 오전 10 27 31" src="https://user-images.githubusercontent.com/31922389/114820603-33b7cb00-9dfa-11eb-9936-c3e6ddd50752.png">


+ busy-wait은 효율적이지 못함 (= spin lock)
+ block & wakeup 방식의 구현 (=sleep lock)
  + lock을 못얻으면 걍 blocked 시킴



### Block / Wakeup Implementaion



+ Semaphore를 다음과 같이 정의

  <img width="661" alt="스크린샷 2021-04-15 오전 10 32 57" src="https://user-images.githubusercontent.com/31922389/114820612-37e3e880-9dfa-11eb-9c5e-b5356c175ba9.png">

  + value: 세마포어 변수 값
  + L:  CPU를 획득하기 위해 기다리는 queue
    

+ block과 wakeup을 다음과 같이 가정

  + block
    + 세마포어를 지금 쓸 수 없으면 해당 프로세스 block
    + 커널은 block을 호출한 프로세스를 suspend 시킴
    + 이 프로세스의 PCB를 semaphore에 대한 wait queue에 넣음
  + wakeup(P)
    + 누군가 세마포어 쓰고나서 반납할때 L에서 block된 프로세스를  wakeup 시킴
    + 이 프로세스의 PCB를 ready queue로 옮김
    + <img width="627" alt="스크린샷 2021-04-15 오전 10 39 03" src="https://user-images.githubusercontent.com/31922389/114820623-3c100600-9dfa-11eb-9802-6680fb5fb9b6.png">







+ Semaphore 연산이 이제 다음과 같이 정의

  <img width="767" alt="스크린샷 2021-04-15 오전 10 40 46" src="https://user-images.githubusercontent.com/31922389/114820631-40d4ba00-9dfa-11eb-89ec-738e950dcaf7.png">

  S.val이 0 미만이면 S.L(대기 큐)에 현재 프로세스의 PCB를 추가한다

  그리고 현재 프로세스 block()

  

<img width="765" alt="스크린샷 2021-04-15 오전 10 41 22" src="https://user-images.githubusercontent.com/31922389/114820643-44684100-9dfa-11eb-89d1-e5b5b42e27e3.png">



P(S)에서 자원을 내놓았는데도 0하라는건  위에서 잠들어있는 프로세스들이 있다는 것

  S.L에서 P를 지우고 wakeup(P) 해준다 


+ S.val의 의미
  + S.val은 자원의 갯수를  세는 것과 다름
  + S.val 이 음수면 지금 누군가가 자원을 기다림 
  + 양수면 자원에 여분이 있어서 자원을 기다리지 않고 사용하는 상황
  + **즉, 지금 깨워야 할 프로세스가 있는지 확인하기 위한 변수**

​		



### Which is better?

+ Busy-wait vs Blcok/wakeup
  + 보통 Block/wakeup이 더 효율적 (CPU 계속 일하게 두지않음)
+ Block/wakeup overhead vs Critical section 길이
  + <u>Critical section의 길이가 긴 경우</u> Block/Wakeup이 적당
  + <u>Critical section의 길이가 매우 짧은 경우</u> Block/Wakeup 오버헤드가 busy-wait 오버헤드보다 더 커질 수 있음
  + 일반적으로는 Block/wakeup 방식이 더 좋음



### Two Types of Semaphores

+ Counting semaphore
  + 도멘인이 0이상인 임의의 정수값
  + 주로 resource counting에 사용
    + 여분의 자원을 카운팅하기 위함
+ Binary semaphore (= <u>mutex</u>)
  + 0또는 1 값만 가질 수 있는 semaphore
  + 주로 mutual exclusion (lock/ unlock)에 사용



### Deadlock and Starvation





<img width="896" alt="스크린샷 2021-04-15 오전 10 56 46" src="https://user-images.githubusercontent.com/31922389/114820654-492cf500-9dfa-11eb-9e8d-ecc9435787bc.png">











> 추가로 봐야 할 것
>
> https://asfirstalways.tistory.com/348