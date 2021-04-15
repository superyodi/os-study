# Chap 5. CPU Scheduling

## CPU Scheduling의 필요성 - cpu burst time의 분포

- Cpu-burst-time을 볼 때 cpu burst가 짧은, 즉 중간에 I/O가 많이 끼어드는**(I/O bound job**) 빈도가 매우 높았으며 cpu burst가 아주 긴(**cpu bound job**) 프로세스의 빈도가 낮았다.
  - 참고 - 프로세스의 특성 분류	
    - I/O bound process
      - cpu를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job (many short CPU bursts)
    - Cpu bound process
      - 계산 위주의 job (few very long CPU bursts)



- 여러 종류의 job(=process) 가 섞여 있기 때문에 cpu 스케줄링이 필요함
  - Interactive job에게 적절한 response 제공 요망(cpu를 공평하게 주기보다 사람과 interaction을 하는 job에게 스케줄링을 하도록, 사람을 오래 기다리지 않도록 하는 효율적인 방식)
  - cpu와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용



## Cpu Scheduler & Dispatcher

-  **Cpu Scheduler**
   - Ready 상태의 프로세스 중에서 이번에 cpu를 줄 프로세스를 고른다.

-  **Dispatcher**
   - cpu의 제어권을 cpu scheduler에 의해 선택된 프로세스에 넘긴다.
   - 이 과정을 context switch(문맥교환) 라고 한다.



- Cpu scheduling이 필요한 경우는 다음 경우와 같이 프로세스에게 상태변화가 있는 경우이다.

  1. Running -> Blocked  (ex.  i/o 요청하는 시스템 콜)
  2. Running -> Ready  (ex. 할당시간 만료로 timer interrupt)
  3. Blocked -> Ready  (ex. I/O 완료 후 인터럽트)
  4. Terminate

  위의 1, 4의 스케줄링은 **nonpreemptive(= 강제로 빼앗지 않고 자진 반납)** - 비선점형.

  나머지는 **preemptive(= 강제로 빼앗음)** - 선점형. 대부분의 경우 여기에 해당함




## Scheduling Criteria 

Performance Index(= Performance Measure, 성능 척도)

스케줄링의 성능을 측정할 수 있는 기준

- <u>시스템 입장</u>에서의 성능척도

  cpu 하나로 최대한 일을 많이 시킬수록 성능이 좋음

  - **CPU utilization(이용률)**
    - 전체 시간 중 cpu가 일한 시간의 비율
  - **Throughput(처리량)**
    - 주어진 시간 동안 몇 개의 작업을 했는지.

- <u>프로세스 입장</u>에서의 성능척도

  cpu를 빨리 얻어서 일을 빠르게 끝낼수록 성능이 좋음

  - **Turnaround time(소요시간, 반환시간)**
    - cpu를 쓰러 들어와서 다 쓰고 나갈 때까지 걸리는 시간. 
  - **Waiting time(대기시간)**
    - ready queue에서 기다린 시간.
  - **Response time(응답시간)**
    - ready queue에 들어와서 처음으로 cpu를 얻기까지 걸린 시간. 
    - waiting time과 구별: 선점형 프로그래밍의 경우 줄 서서 기다리는 시간이 여러 번 존재하므로 wating time과 차이가 존재하는 개념



## 스케줄링 방법

### FCFS(First-Come First-Served)

- 먼저 온 작업을 먼저 처리하는 스케줄링 방법
- **문제점**
  - **Convoy effec**t가 발생할 수 있음
    - **Convoy effect** : 긴 시간을 요하는 프로세스 뒤에 짧은 시간이 걸리는 프로세스가 있는 경우, 짧은 프로세스가 지나치게 오래 기다리는 현상을 의미함.

### SJF(Shortest-Job-First)

- 시간이 짧게 걸리는 작업 순으로 처리하는 스케줄링 방법
- 주어진 프로세스에 대해 **minimum average wating time**을 보장한다.
- Two schemes
  - <u>Nonpreemptive</u> 
    - 일단 cpu를 잡으면 그보다 짦은 프로세스가 나타나더라도 이번 cpu burst가 완료될 때까지 cpu를 선점(preemptive )하지 않음
  - <u>Preemptive</u> 
    - cpu를 잡았더라도 더 짦은 cpu burst time을 가지는 새로운 프로세스가 도착하면 cpu를 빼앗김
    - 이 방법을 **Shortest-Remaining-Time-First(SRTF)**라고도 부른다. 
- **문제점**
  - Starvation
    - cpu burst time이 짧은 프로세스가 계속 등장한다면 long process가 실행되지 못한다. 
  - cpu 사용시간을 미리 알 수 없으므로 실제 시스템에서 SJF를 사용하기 어려움. cpu 사용시간을 추정하여 사용
    - 과거의 cpu burst time을 이용해서 추정(**exponential averaging**)

### Priority Scheduling

- 우선순위가 높은 순으로 작업을 처리하는 스케줄링 방법
  - 숫자(정수)로 우선순위를 나타내며, 주로 정수값이 작은 순으로 우선순위가 높게 설정한다.
- Two schemes
  - <u>Nonpreemptive</u>
  - <u>Preemptive</u>
- SJF도 일종의 priority scheduling이다. (우선순위 = cpu burst time)
- **문제점**
  - Starvation
    - 해결방법 : Aging : 시간이 지날수록 우선순위가 높도록 설정하는 방법

### Round Robin(RR)

- cpu를 줄 때 동일한 할당시간을 주고, 그 시간이 끝나면 timer interrupt에 의해 cpu를 빼앗기게 되고, ready queue의 맨 뒤에 가서 줄을 서게 된다. 그 다음 프로세스가 할당 시간만큼 cpu를 쓰고...
- n개의 프로세스가 ready queue에 있고 할당 시간이 q time unit일 경우 각 프로세는 최대 q time unit 단위로 cpu 시간의 1/n을 얻는다. 
  - 어떤 프로세스도 (n-1)q time unit 이상 기다리지 않는다. (응답시간이 빠르다.) response time이 빨라진다.
  - cpu를 누가 빨리 쓸지 예측할 필요 없이 모든 프로세스가 cpu를 한번씩 사용할 수 있다.
- cpu를 길게 쓰는 프로세스는 기다리는 시간도 길어지고, cpu를 짧게 쓰는 프로세스는 기다리는 시간도 짧아지는 특징
- Perfomance(양 극단의 케이스)
  - q large => FCFS와 같아진다.
  - q small => context switch 오버헤드가 커진다. 

### Mulitlevel Queue

![best finisher: Operating System | Multilevel Queue Scheduling | Multilevel  Feedback Queue Scheduling](https://lh4.googleusercontent.com/proxy/lYxelQRhUPRflIM9oycgdw517LlY4B5v8lMNCQLEPiyKwi45u8i-v9o8WKTFEyChyUjSafPQHWwMm3chpsD6sUvuOCH2EZTo0pNDQiwbm25Kkqe98_wrdya-5jgkIqWv6O_6UQ=s0-d)



- Ready queue를 여러 개로 분할
  - foreground queue(interactive)
  - background queue(batch - no human interaction)
- 각 큐는 독립적인 스케줄링 알고리즘을 가진다.
  - Foreground - RR
  - Background - FCFS
- 큐에 대한 스케줄링이 필요
  - Fixed priority scheduling
  - Time slice

### Multilevel Feedback Queue

- 프로세스가 다른 큐로 이동

- Cpu burst가 짧은 프로세스를 우선순위를 더 많이 주고 긴 프로세스는 점점 다른 큐로 이동하면서 우선순위에서 밀려남. 

  ![Multilevel Feedback Queues | Download Scientific Diagram](https://www.researchgate.net/profile/Jyotirmay-Patel/publication/265184702/figure/fig1/AS:295890351869964@1447557165376/Multilevel-Feedback-Queues.png)

---

cpu가 여러 개 있는 경우의 스케줄링

### Multiple-Processor-Scheduling

- cpu가 여러 개인 경우 스케줄링은 더욱 복잡해짐
- Homogeneous processor 인 경우
- Load sharing
  - 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘 필요
  - 별개의 큐를 두는 방법 vs 공동 큐를 사용하는 방법
- Symmetric Multiprocessing(SMP)
  - 모든 cpu가 대등하여 각 프로세서가 알아서 스케줄링 결정
- Asymmetric multiprocessing
  - 하나의 프로세서가 전체적인 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따름

---

### Real-Time Scheduling

데드라인 안에 무조건 프로세스가 종료되어야 하는 스케줄링

- Hard real time systems
- Soft real time computing
  - 데드라인을 반드시 보장하기보다 다른 프로세스에 비해 우선순위를 주는.

### Thread Scheduling

- Local Scheduling
  - User level thread의 경우 사용자 수준의 thread library에 의해 어떤 thread를 스케줄할지 결정
- Global Scheduling
  - Kernel level thread의 경우 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 어떤 thread를 스케줄할지 결정



## Algorithm Evaluation

- Queing models
  -  확률분포로 주어지는 arrival rate와 service rate 등을 통해 각종 performance index값을 계산
- Implementation(구현) & Measurements(성능 측정)
  - 실제 시스템에 알고리즘을 구현하여 실제작업(workload)에 대해서 성능을 측정 비교
- Simulation(모의 실현)
  - 알고리즘을 모의프로그램으로 작성 후 trace를 입력으로 하여 결과 비교





# Chap 6. Process Synchronization

### OS 에서 race condition은 언제 발생하는가?

1. Kernel 수행 중 인터럽트 발생 시
2. Pocess가 system call을 하여 kernel mode로 수행 중인데 context switch가 일어나는 경우
   - 해결책: 커널모드에서 수행 중일 때는 cpu를 preempt하지 않음. 커널모드에서 사용자모드로 돌아갈 때 preempt
3. Multiprocessor에서 shared memory 내의 kernel data
   - **해결책1.** 한 번에 하나의 cpu만이 커널에 들어갈 수 있게 하는 방법
   - **해결책2.** 커널 내부에 있는 각 공유 데이터에 접근할 때마다 그 데이터에 대한 lock/unlock 방법



## Process Synchronization 문제

- 공유데이터의 동시 접근은 데이터의 불일치문제를 발생시킬 수 있다.
- 일관성 유지를 위해서는 협력 프로세스 간의 실행순서를 정해주는 매커니즘 필요
- **Race Condition**
  - 여러 프로세스들이 동시에 공유데이터를 접근하는 상황
  - 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라짐
- Race condition을 막기 위해서는 concurrent process는 **동기화(synchronize)**되어야 한다. 



## The Critical-Section Problem

- n 개의 프로세스가 공유데이터를 동시에 사용하기를 원하는 경우
- 각 프로세스의 code segment에는 **공유데이터를 접근하는 코드인** **critical section**이 존재



### 프로그램적 해결법의 충족 조건

- #### **Mutual Exclusion**(상호 배제)

  - 하나의 프로세스가 critical section부분을 수행 중이면 다른 모든 프로세스들은 그들의 critical section에 들어가면 안 된다.

- #### **Progress** 

  - 아무도 critical section에 있지 않은 상태에서 critical section에 들어가고자 하는 프로세스가 있으면 critical section에 들어가게 해 주어야 한다.

- #### **Bounded Waiting**

  - 프로세스가 critical section에 들어가려고 요쳥한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 critical section에 들어가는 횟수에 한계가 있어야 한다.  -> starvation 방지



### Algorithm 1

```c
// int turn;   /*initially turn = 0; */

do {
  while (turn != 0);			/*My turn?*/
  
  **critical section**
    
  turn = 1;								/**/
  remainder section
} while (1);
```

- 다른 프로세스가 critical section에 있는 경우 자신의 차례가 아니기 때문에 while문에서 기다리다가, 상대방의 critical section에서 빠져나올 때 turn 해주기 때문에 자신이 critical section으로 들어갈 수 있음.
- **문제점**: Mutual Section은 만족하나 Progress를 충족하지 않아 critical section을 해결하는 제대로 된 알고리즘이라고 할 수 없다.

### Algorithm 2

```c
//initially flag[모두] = false. /*no one is in CS*/

//하나의 프로세스 (Pi) cs 알고리즘
do {
  flag[i] = true; 			/* Pretend I am in */
  while (flag[j]);			/*Is he also in? then wait*/
  
  **critical section**
  
 flag[i] = false; 			/*I am out now*/
} while (1);
```

- 처음에는 모든 flag가 false로, 모두 critical section에 들어가고 싶어하지 않는 상태
- 본인이 critical section에 들어가고 싶으면 flag를 true로 만들고, 상대방의 flag를 확인한다. 상대방의 flag가 true이면 기다리고 false이면 본인이 critical section에 들어간다. 
- Critical section에서 나올 때에는 flag를 false로 바꾸어 상대방이 들어갈 수 있도록 한다. 
- **문제점:** 둘 다 2행까지 수행 후 서로 기다리는 상황이 발생할 수 있다. Progress 조건을 충족하지 못한다. 



### Algorithm 3 (Peterson's Algorithm)

```c
do {
  flag[i] = true;          				/*My intention is to enter....*/
  turn = j;												/*Set to his turn*/
  while (flag[j] && turn == j); 	/*wait only if ...*/
  
  **critical section**
    
  flag[i] = false;
  remainder section
} while(1);
```

- turn과 flag 변수를 모두 사용한다. 
- cs에 들어가고자 할 때 flag를 true로 바꾸고 turn을 상대방으로 바꾼다. 
- 상대바이 깃발을 들고 있으며, 상대방 차례일 때는 상대방이 들어가고 아닌 경우 본인이 들어간다. 
- flag를 false로 바꾼다. 
- **Mutual Exclusion, Progress, Bounded Waiting** 세 조건을 모두 만족하는 알고리즘
- **문제점**
  - **Busy Waiting(=spin lock)** :계속 cpu와 memory를 쓰면서 wait. 비효율성



### Synchronization Hardware

- 문장 하나하나가 실행되는 도중에도 cpu를 빼앗길 수 있기 때문에, 중간중간 cpu가 뺏긴다는 가정 하에 알고리즘을 짜다 보니 앞에서와 같이 소프트웨어적으로 복잡한 코드가 만들어졌다. 
  - 데이터를 읽고 쓰는 것을 하나의 instruction으로 수행할 수 없기 때문에. 
- 인스트럭션 하나를 가지고 데이터를 읽는 작업과 데이터를 쓰는 작업을 동시에 수행할 수 있는 (task & modify) 하드웨어적인 인스트럭션이 지원된다면 **간단하게** lock을 걸고 푸는 일을 해결할 수 있다.

```c
do {
  while (Test_and_Set(lock));
  
  critical section
    
  lock = false;
  remainder section
}
```

- critical section에 들어가기 전 다른 lock이 있는지 확인하고 lock이 없으면 lock을 걸고 들어가고, 빠져나올 때 lock을 풀어준다. 



### Semaphores

- 앞의 방식들을 추상화시킴
- Semaphore S
  - integer variable
  - 아래의 두 가지 atomic 연산에 의해서만 접근 가능(p연산, v연산)
    - P(S) : 공유데이터를 획득하는 과정
    - V(S) : 다 사용하고 반납하는 과정
- **Two types of semaphore**
  - Counting semaphores
    - 도메인이 0이상인 임의의 정수값
    - 주로 resource counting에 사용
  - Binary semaphores
    - 0 또는 1 값만 가질 수 있는 semaphore
    - 주로 mutual exclusion(lock/unlock)사용



#### Critical Section of n Processes

- 앞에서처럼 프로그래머가 일일이 코딩을 하기보다 추상화된 자료구조를 주고, 프로그래머는 semaphore를 통해 프로그래밍을 하면 훨씬 간단하게 작성할 수 있음

- ##### Busy-wait 방식의 구현 (=spin lock) 

  ```c
  // semaphore mutex; /* initially 1: 1개가 CS에 들어갈 수 있다.*/
  
  do {
    P(mutex);						/*If positive, dec & enter, otherwise wait*/
    critical section
    V(mutex);						/*Increment semaphore*/
    remainder section
    
  }while (1);
  ```

- ##### BlocK & Wakeup 방식의 구현(= sleep lock)

  - Semaphore를 다음과 같이 정의

    ```c
    typedef struct {
      int value;					/*semaphore*/
      struct process *L;	/*process wait queue*/
    } semaphore
    ```

    - p연산

      ```c
      S.value--;									/*prepare to enter*/
      if (S.value < 0) {					/*negative,I cannot enter*/
        add this process to S.L;
        
        block();
      }
      ```

      자원의 여분이 있다면 획득, 그렇지 않으면 sleep상태(blocked)로 바뀐다.

    - v연산

      ````c
      S.value++;
      if (S.value <= 0) {
        remove a process P from S.L;
        
        wakeup(P)
      }
      
      //자원을 다 내놓았는데도 불구하고 그 값이 0이하라는 것은 이 프로세스를 기다리면서 잠들어있는 프로세스가 있다는 의미
      ````

      자원을 다 쓰고 반납하면서 잠들어있는 프로세스가 있다면 **깨워주는(wakeup)과정도 포함**되어 있다.

- Which is Better?

  - 보통은 block & wakeup이 효율적. 구체적으로는
  - **Block/wakeup의 overhead vs Critical section 길이** 를 비교하여 결정한다.
    - Critical section의 길이가 긴 경우 block/wakeup이 적당
    - Critical section의 길이가 매우 짧은 경우 block/wakeup 오버헤드가 busy-wait 오버헤드보다 더 커질 수 있음



#### Deadlock and Starvation

- **Deadlock**
  - 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 event를 무한히 기다리는 현상
  - ex) Dining- Philosophers Problem에서 모두가 왼쪽 젓가락을 집는 경우
- **Starvation**
  - Indefinite blocking : 프로세스가 suspend된 이유에 해당하는 세마포어 큐에서 빠져나갈 수 없는 현상



