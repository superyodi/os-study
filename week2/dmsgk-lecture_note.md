# Chap3. Process

## 프로세스의 개념

- 실행중인 프로그램

- 프로세스 개념 이해에 있어 **프로세스의 문맥(context)** 을 이해하는 것이 중요하다.

  아래와 같은 상황을 알면 프로세스가 현재 어떤 상황에 처해 있는지 알 수 있다.

  - **Cpu** 수행 상태를 나타내는 하드웨어 문맥
    - 주로 register가 어떤 값을 가지고 있었는가를 나타냄
    - Program counter
  - 프로세스의 주소 공간
    - code,data,stack
  - 프로세서 관련 커널 자료 구조
    - PCB(Process Control Block)
    - Kernel Stack

## 프로세스의 상태(Process Status)

- 프로세스는 상태(state)가 변경되며 수행된다.

  - **Running** 

    - cpu를 잡고 instruction을 수행중인 상태

  - **Ready** 

    - cpu를 기다리는 상태(메모리 등 다른 조건은 충족한 상태임)

  - **Blocked(wait, sleep)**

    - cpu를 주어도 당장 instruction을 수행할 수 없는 상태
    - process 자신이 요청한 event(예 i/o)가 즉시 만족되지 않아 이를 기다리는 상태
    - (예) 디스크에서 파일을 읽어와야 하는 경우

  - <u>**Suspended (stopped)**</u>

    - 외부적인 이유로 프로세스의 수행이 정지된 상태

    - 프로세스는 통째로 디스크에 swap out 된다.

    - (예) 사용자가 프로그램을 일시정지시킨경우 (break key)

      ​	시스템이 여러 이유로 프로세스를 잠시 중단시킴 (중기스케줄러에 의해. 메모리에 너무 많은 프로세스가 올라와 있을 때)

  경우에 따라 다음의 프로세스의 상태에 두 가지를 추가하기도 한다.

  - <u>New</u>: 프로세스가 생성중인 상태
  - <u>Terminated</u>: 수행이 끝난 상태



### 프로세스 상태도

![프로세스 상태와 스케줄링 알고리즘 | gracieuxyh.dev](https://s3.ap-northeast-2.amazonaws.com/static.gracieuxyh.dev/os/process-life-cycle.png)

![img](https://blog.kakaocdn.net/dn/cYbSlt/btqBRvcdMbg/UxGjUbkL06YvBpL7RwVNl1/img.png)





<img src="/Users/johyeonyoon/Library/Application Support/typora-user-images/스크린샷 2021-04-06 오후 4.42.49.png" alt="스크린샷 2021-04-06 오후 4.42.49" style="zoom: 33%;" />

cpu에서 running을 하다가  무엇을 읽어와야 한다면 (예를 들어 키보드 입력) cpu의 프로세스는 키보드 입력큐에 줄을 서게 되고 상태는 blocked상태로 바뀌고, 키보드 입력이 오면 인터럽트로 cpu에 알려주고 키보드 입력을 받았으므로 ready queue에 집어넣게 된다. 하드웨어뿐 아니라 소프트웨어, 공유자원에서도 발생한다. 



## Process Control Block(PCB)

<img src="https://i.imgur.com/EcicPWH.jpg" alt="Short note on process control block" style="zoom: 50%;" /> 

- 운영체제가 각 프로세스를 관리하기 위해 프로세스당 유지하는 정보
- 다음의 구성요소를 가진다.(구조체로 유지)
  - **OS가 관리상 사용하는 정보**
    - Process state, Process ID
    - scheduling information, priority
  - **CPU 수행 관련 하드웨어 값**
    - Program counter, registers
  - **메모리 관련**
    - code, data, stack의 위치정보
  - **파일 관련**
    - Open file descriptors 등등

## 문맥교환(Context Switch)

- cpu를 한 프로세서에서 다른 프로세스로 넘겨주는 과정
- cpu가 다른 프로세스에게 넘어갈 때 운영체제는 다음을 수행
  - cpu를 내어주는 **프로세스의 상태**를 그 **프로세스의 pcb에 저장**
  - **cpu를 새롭게 얻는 프로세스의 상태를 pcb에서 읽어옴**

**-참고-**

System call이나 interrupt 발생 시 반드시 context switch가 일어나는 것은 아님

<img src="/Users/johyeonyoon/Library/Application Support/typora-user-images/스크린샷 2021-04-06 오후 5.14.37.png" alt="스크린샷 2021-04-06 오후 5.14.37" style="zoom: 33%;" />



## 프로세스를 스케줄링하기 위한 큐

- **Job queue**
  - 현재 시스템 내에 있는 모든 프로세스의 총집합
- **Ready queue**
  - 현재 메모리 내에 있으면서 cpu를 잡아서 실행되기를 기다리는 프로세스의 집합
- **Device queues**
  - I/O device의 처리를 기다리는 프로세스의 집합
- 프로세스들은 각 큐를 오가며 수행된다.

![OS - 스케줄링 큐(Scheduling Queue) : Queueing diagram : 네이버 블로그](https://lh3.googleusercontent.com/proxy/y99VqpSdKSmBHWhTxmwrAm7uzhaZE-tJlBuA1l5Fcw6QpbLeMgClSVQrJyEbWYk8eMH06q0MX-STyNjncK_rDCGNfIBKijiGk8kQvwTe_bG2XwbytUPkJXesPKuhDbMPSZu9BJtJXKsm7vvJHxZkOwczeSfwM0ux6vZo)





## 스케줄러(Scheduler)

- **Long-term scheduler**(장기스케줄러 or job scheduler)
  - 시작 프로세스 중 어떤 것들을 **ready queue**로 보낼지 결정
  - 프로세스에 memory(및 각종 자원)을 주는 문제
  - Degree of Multiprogramming을 제어. 몇 개를 올려놓을지.
  - Time sharing system에는 보통 장기 스케줄러가 없음(무조건 ready) -> 그 대신 medius-term scheduler를 둔다. 
- **Short-term scheduler**(단기스케줄러 or **CPU scheduler**)
  - 어떤 프로세스를 다음번에 running시킬지 결정
  - 프로세스에 cpu를 주는 문제
  - 충분히 빨라야 함(milisecond 단위)
- **Medium-Term scheduler**(중기스케줄러 or Swapper)
  - **여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄**
  - 프로세스에서 memory를 뺏는 문제
  - Degree of Multiprogramming을 제어

## Thread

- 프로세스 내부에 cpu 수행 단위가 여러 개 있는 경우 그것을 thread라고 한다.
- ligthweight process라고도 한다.
- thread끼리 메모리 주소 공간을 공유하고 프로세스도 하나이므로 thread끼리 공유하나, **cpu 수행과 관련된 기능**은 thread마다 별도로 갖게 된다.
- thread의 구성
  - Program counter
  - register set
  - stack space
- Thread 가 동료 thread와 공유하는 부분(=task)
  - code section
  - data section
  - OS resources

- 전통적인 개념의 heavyweight process는 하나의 thread를 가지고 있는 task로 볼 수 있다.

### Thread의 이점

- **Responsiveness**
  - 다중 스레드로 구성된 테스크 구조에서는 하나의 서버 스레드가 blocked(waiting) 상태인 동안에도 동일한 태스크 내의 다른 스레드가 실행되어 빠른 처리를 할 수 있다. 사용자에게 빠른 응답성을 제공한다.
- **Resource Sharing**
  - n개의 thread는 code, data 등 프로세스의 자원을 공유한다.
- **Economy**
  - 프로세스를 하나 만드는 것에 비해 프로세스 내에 thread를 만드는 것이 오버헤드가 적음
  - 문맥교환은 오버헤드가 크나 프로세스 내 thread 간의 cpu switch는 간단하다.
  - Solaris의 경우 위 두 가지 overhead가 각각 30배, 5배이다.
- **Utilization of Multi-Processor Architectures**
  - 스레드를 사용하면 병렬성을 높일 수 있다.
  - 동일한 일을 수행하는 다중 스레드가 협력하여 높은 처리율(throughput)과 성능향상을 얻을 수 있다.

### Implementation of Threads

- kernel 형태로 구현되는 경우: **Kernel Threads**
  - 스레드가 여러 개 있다는 사실을 운영체제가 알고 있어서 하나의 스레드에서 다른 스레드로 cpu가 넘어가는 것도 커널이 cpu스케줄링을 하듯이 넘겨준다.
- library 형태로 구현되는 경우: **User Threads**
  - 커널은 모르고 프로세스 내부에서 cpu수행단위를 여러 개 두고 관리. 구현상의 제약이 존재할 수도 있음.
- **Real-time Threads**





# Chap 4. Process Management

## 프로세스 생성(Process Creation)

- **부모프로세스가 자식 프로세스 생성**
  - **프로세스의 문맥**을 모두 복사한다. 
- 프로세스의 트리(계층구조)형성
- 프로세스는 자원을 필요로 함
  - 운영체제로부터 받는다
  - 부모와 공유한다
- *자원의 공유*
  - 부모와 자식이 모든 자원을 공유하는 모델
  - 일부를 공유하는 모델
  - 전혀 공유하지 않는 모델
- 수행(Execution)
  - 부모와 자식은 공존하며 수행되는 모델
  - 자식이 종료될 때까지 부모는 기다리는 모델
- 주소 공간(Address space)
  - 자식은 부모의 주소공간을 복사함
  - 자식은 그 공간에 새로운 프로그램을 올림



## 프로세스 종료(Process Termiation)

- 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려줌(**exit**)
  - 자식이 부모에게 output data를 보냄(**wait** 시스템콜을 통해서)
  - 프로세스의 각종 자원을이 운영체제에게 반납됨
- 부모 프로세스가 자식의 수행을 종료시킴(**abort**)
  - 자식이 할당 자원의 한계치를 넘어섬
  - 자식에게 할당된 태스크가 더이상 필요하지 않음
  - 부모가 종료(exit)되는 경우
    - 운영체제는 부모 프로세스가 종료하는 경우 자식이 더이상 수행되도록 두지 않는다.
    - 단계적인 종료 (자식의 자식 종료 -> 자식 종료 -> 부모 종료)



## 프로세스와 관련한 시스템 콜

### fork() 시스템 콜

- fork() 시스템 콜에 의해 프로세스가 생성된다. 
- 부모프로세스의 문맥을 그대로 복사한다.
- fork()가 이루어진 그 다음 시점부터 자식프로세스가 실행된다. 프로그램카운터를 복제하였으므로 fork() 바로 다음부터 실행해야 한다는 것을 알기 때문
- fork()의 결과값이 달라 부모프로세스와 자식프로세스의 구분이 가능하고, 각각 다른 작업의 수행이 가능하다.

### exec() 시스템 콜

- 어떤 프로그램을 완전히 새로운 프로세스로 거듭날 수 있도록 한다.
- 프로그램을 실행하다가 exec()를 만나면 새로운 프로그램을 실행하게 된다. 
- 한번 exec()을 하면 다시 되돌아올 수 없다. 따라서 exec() 이후는 영원히 실행이 되지 않는 코드이다.

### wait() 시스템 콜

- 프로세스 A가 wait 시스템콜을 호출하면
  - 커널은 **child가 종료될 때까지** 프로세스 A를 sleep시킨다.(bock 상태) 
  - Child process가 종료되면 커널은 프로세스 A를 깨운다. (ready 상태)

### exit() 시스템 콜

- 프로세스를 종료시킬 때

  - 자발적 종료

    - 마지막 statement 수행 후 exit() 시스템 콜을 통해 
    - 프로그램에 명시적으로 적어주지 않아도 main 함수가 리턴되는 위치에 컴파일러가넣어줌

  - 비자발적 종료

    - 부모 프로세스가 자식 프로세스를 강제적으로 종료시킴

    - 키보드로 kill, break 등을 친 경우

    - 부모가 종료하는 경우

      - 부모 프로세스가 종료하기 전에 자식들이 먼저 종료되므로.

        

## 프로세스 간 협력

- 독립적 프로세스(Independent process)

  - 프로세스는 각자의 주소공간을 가지고 수행되므로 원칙적으로 하나의 프로세스는 다른 프로세스이 수행에 영향을 미치지 못함

- 협력 프로세스(Cooperate process)

  - 프로세스 협력 매커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있음

- **프로세스 간 협력 매커니즘(IPC: Interprocess Communication)**

  - 메시지를 전달하는 방법 : **message passing**
    - 커널을 통해 메시지 전달
    - Message system: 프로세스 사이에 공유변수(shared variable)를 일체 사용하지 않고 통신하는 시스템
      - Direct Communication	:  통신하려는 프로세스의 이름을 명시적으로 표시
      - Indirect Communication : mailbox를 토해 메시지를 간접 전달
  - 주소공간을 공유하는 방법: **shared memory**
    - 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는 shared memory 매커니즘이 있음

  --**thread**: thread는 사실상 하나의 프로세스이므로 프로세스 간 협력으로 보기는 어렵지만 동일한 프로세스를 구성하는 thread 간에는 주소공간을 공유하므로 협력이 가능



# Chap 5. CPU Scheduling

## CPU Scheduling의 필요성 - cpu burst time의 분포

- Cpu-burst-time을 볼 때 cpu burst가 짧은, 즉 중간에 I/O가 많이 끼어드는**(I/O bound job**) 빈도가 매우 높았으며 cpu burst가 아주 긴(**cpu bound job**) 프로세스의 빈도가 낮았다.
  - 참고 - 프로세스의 특성 분류	
    - I/O bound process
      - cpu를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job (many short CPU bursts)
    - Cpu bound process
      - 계산 위주의 job (few very long CPU bursts)



- 여러 종류의 job(=process) 가 섞여 있기 때문에 cpu 스케줄링이 필요함
  - Interactive job에게 적절한 response 제공 요망(cpu를 공평하게 주기보다 사람과 interaction을 하는 job에게 스케줄링을 하도록)
  - cpu와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용



## Cpu Scheduler & Dispatcher

-  **Cpu Scheduler**
  - Ready 상태의 프로세스 중에서 이번에 cpu를 줄 프로세스를 고른다.

- **Dispatcher**
  - cpu의 제어권을 cpu scheduler에 의해 선택된 프로세스에 넘긴다.
  - 이 과정을 context switch(문맥교환) 라고 한다.



- Cpu scheduling이 필요한 경우는 다음 경우와 같이 프로세스에게 상태변화가 있는 경우이다.

  1. Running -> Blocked  (ex.  i/o 요청하는 시스템 콜)
  2. Running -> Ready  (ex. 할당시간 만료로 timer interrupt)
  3. Blocked -> Ready  (ex. I/O 완료 후 인터럽트)
  4. Terminate

  위의 1, 4의 스케줄링은 **nonpreemptive(= 강제로 빼앗지 않고 자진 반납)**

  나머지는 **preemptive(= 강제로 빼앗음)**