# [week2] OS lecture note 

## [Chapter3] Process
### 프로세스의 개념

>  프로세스: 실행중인 프로그램



**프로세스의 문맥(context)**

*프로그램이 태어나서 죽는 동안  특정 시점을 놓고 봤을때* 

*프로세스가 어디까지 수행을 했고(프로그램 카운터가 어느 부분을 가리키는가)*

*데이터 영역의 변수 값은 얼마이며 CPU 레지스터에 어떤 값을 넣어놓았고 어디까지 실행했는가 등등*

*<u>프로세스의 현재 상태를 나타내는 모든 요소</u> -------> Context(문맥)*



+ CPU 수행상태를 나타내는 하드웨어 문맥
  --> CPU에서 코드의 어느 부분까지 실행했는가, 프로그램 카운터가 어느 부분을 가리키고 있는가

  + Program Counter

  + 각종 register

    

+ 프로세스의 주소 공간

  + code, data, stack 에 어떤 내용들이 들어있는가

+ 프로세스 관련 커널 자료 구조

  + PCB(Process Control Block)
  + Kernel Stack
    + 프로세스가 시스템콜을 실행한경우 Kernel에서 함수호출이 이루어지고 Kernel Stack에 프로세스별로 스택을 별도로 두고 있다. 



시분할 프로그래밍이기때문에 현재 문맥을 제대로 알고있어야 다음 시점부터 instruction을 실행할 수 있다. 



### 프로세스의 상태

프로세스는 상태(state)기 변경되며 수행된다.

+ Running
  + CPU를 잡고 instruction을 수행중인 상태
+ Ready
  + CPU를 기다리는 상태(메모리 등 다른 조건들은 만족함)
+ Blocked (wait, sleep)
  + CPU를 줘도 당장 instruction을 수행할 수 없는 상태
  + 프로세스 자신이 요청한 event가 즉시 만족되지않아 이를 기다리는 상태
    + 예) 디스크에서 file을 읽어와야하는 경우
+ New: 프로세스가 생성중인 상태
+ Terminated: 수행이 끝난 상태





### 프로세스 상태도



<img width="940" alt="스크린샷 2021-04-06 오후 5 44 52" src="https://user-images.githubusercontent.com/31922389/113704375-0347a100-9717-11eb-9720-005d4b7225f6.png">



### PCB

<img width="916" alt="스크린샷 2021-04-06 오후 5 57 37" src="https://user-images.githubusercontent.com/31922389/113704423-0e9acc80-9717-11eb-93ed-2e4fdd5ead4a.png">



### 문맥교환(Context Switch)

+ CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
+ CPU가 다른 프로세스에게 넘어갈때 운영체제는 아래와 같은 일을 함
  + CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장
  + CPU를 새롭게 얻는 프로세스의 상태를 PCB에서 읽어옴

+ **System call이나 Interrupt 발생시 반드시 context switch가 일어나는 것은 아님**
  + 사용자 프로세스 A ------**kernel mode** (interrupt 또는 system call) ------------> 사용자 프로세스 A
    + Context switch 아님
  + 사용자 프로세스 A ----- **kernel mode** (timer interrupt 또는 IO 요청으로 blocked 상태)------> 사용자 프로세스 B
    + Context switch 맞음



### 프로세스 스케줄링을 위한 큐

+ Job queue
  + 현재 시스템 내에 있는 모든 프로세스의 집합
+ Ready queue
  + 현재 Ready 상태에 있는 프로세스의 집합
+ Device queue
  + IO 디바이스의 처리를 기다리는 프로세스의 집합



### 스케줄러

+ Long-term scheduler (Job scheduler)

  + 시작 프로세스(new 상태의 프로세스)가 메모리에 올라오는걸 관리

  + 시작 프로세스 중 어떤 것들을 ready queue로 보낼지 결정

  + degree of multi-programming을 제어

    + multi-programming: 

      2개 이상의 프로그램을 주기억장치에 기억시키고, 중앙처리장치를 번갈아 사용하는 처리기법

  + <u>time sharing system에는 보통 장기 스케줄러가 없음</u> (**무조건 ready**)

    

+ Short-term scheduler (CPU scheduler)

  + 짧은 시간 안에 스케줄이 이루어져야함

    --> 충분히 빨라야함 (millisecond) 단위

  + 어떤 프로세스를 다음번에 running시킬지 결정

    --> 프로세스에 CPU를 주는 문제

+ Medium-term scheduler (Swapper)

  + 여유공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄
  + 프로세스에게서 memory를 빼앗는 문제
  + **degree of multi-programming을 제어**





### 프로세스의 상태 (시분할 시스템에서)

+ Running
+ Ready
+ Blocked
+ <u>Suspended</u> (stopped)
  + 외부적인 이유로 프로세스의 수행이 정지된 상태
  + 프로세스는 통째로 디스크에 swap out 됨



> Blocked vs Suspended

Blocked : 자신이 요청한 event가 만족되면 Ready

Suspended: 외부에서 resume 해주어야 Active



### 프로세스의 상태도 (시분할 시스템에서)

<img width="933" alt="스크린샷 2021-04-06 오후 8 24 33" src="https://user-images.githubusercontent.com/31922389/113704446-165a7100-9717-11eb-9fa0-a641d00138c4.png">



---

(지난강의 질문)

**동기식 입출력과 비동기식 입출력**

프로세스가 입출력이 진행되는 동안에 instruction을 실행할 수 있으면 -> 비동기

입출력 완료까지 기다리면 -> 동기

+ 일을 못하는 동안 CPU를 가지고 있음
+ 일을 못하는 동안 CPU를 다른 프로세스에 넘겨줌

이렇게 두가지로 구현할 수 있음



---



### Thread

프로세스 내부의 실행 단위

프로세스 하나에 CPU 수행 단위만 여러개 두고있음 ---> 스레드?



프로세스 하나에서 공유할 수 있는것은 공유하고 

CPU 수행에 관한 정보(CPU 카운터, resister 등)는 별도로 가지고 있음



**Thread의 구성**

+ Program counter
+ resister set
+ Stack space



**Thread가 동료 Thread와 공유하는 부분(= Task)**

+ code section
+ data section
+ OS resources

-------> lightweight process

전통적인 개념의 heavyweight process는 하나의 thread를 가지고 있는 task로 볼 수 있다.





**Thread의 장점**



+ Responsiveness (응답성)

  + eg) multi-threaded Web
    + if one thread is blocked (eg network), another thread continues (eg display)

+ Resource Sharing (자원 공유)

  + 여러개의 스레드가 프로세스 내부의 자원들을 공유할 수 있다

+ Economy (경제성)

  + Context Switching을 할 필요가 없다.
    + overhead(어떤 처리를 하기 위해 들어가는 간접적인 처리 시간, 메모리) 를 줄일 수있다.

+ Utilization of MP(Multi-Processor) Architectures

  + 서로 다른 CPU에서 각 스레드들이 병렬적으로 일을 처리하면 결과를 빨리 얻을 수 있다.

  





<img width="937" alt="스크린샷 2021-04-07 오전 11 29 16" src="https://user-images.githubusercontent.com/31922389/113977569-be8a4a00-987d-11eb-99dc-c2dd8fe126d9.png">



<img width="893" alt="스크린샷 2021-04-07 오전 11 30 50" src="https://user-images.githubusercontent.com/31922389/113977632-d366dd80-987d-11eb-90bf-9cba596a9bfb.png">







**Thread 구현**

+ 커널 스레드
  + 프로세스 안에 스레드가 여러개 있다는걸 **운영체제가 알고있음**
  + 커널이 알아서 스레드 관리
+ 유저 스레드
  + 프로세스 안에 스레드가 여러개 있다는걸 **운영체제는 모름**
  + 유저 프로그램이 스레드를 직접 관리
  + 구현상의 제약점들이 있을 수 있다







## [chapter 4] Process Management



### 프로세스의 생성

Copy-On-Write(COW): write가 발생했을때 copy를 하겠다.


+ 부모 프로세스가 자식 프로세스 생성 (복제 생성)
+ 트리형태로 프로세스 계층이 구성됨
+ 프로세스는 자원을 필요로 함
+ 자원의 공유
  + 부모 - 자식이 모든 자원을 공유하는 모델
  + 일부 공유 모델
  + 전혀 공유하지 않는 모델 
+ 수행 (Execution)
  + 부모 - 자식이 공존하며 수행되는 모델
  + 자식이 terminate 될 때까지 부모가 wait 하는 모델
+ 주소 공간
  + 자식은 부모의 공간을 복사함 (binary , OS data)
  + 자식은 그 공간에 새로운 프로그램을 올림 (덮어씌움)
+ 예) 유닉스
  + `fork()`  시스템 콜이 새로운 프로세스를 생성
    + 부모를 그대로 복사 (OS data except PID + binary)
    + 주소공간 할당
  + fork 다음에 이어지는 `exec( )`  시스템 콜을 통해 새로운 프로그램을 메모리에 올림





### 프로세스 종료

+ 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려줌 (**exit**)

  + 자식이 부모에게 output data를 보냄 (**wait**을 통해서)
  + 프로세스의 각종 자원들이 운영체제에게 반납됨

+ 부모 프로세스가 자식의 수행을 종료시킴 (**abort**)

  <u>*자식을 종료시키는 상황*</u>

  + 자식이 할당 자원의 한계치를 넘어섬

  + 자식에게 할당된 테스크가 더이상 필요하지 않음

    

  + 부모 프로세스가 종료(exit)하는 경우

    + 운영체제는 부모가 종료하는 경우 자식도 같이 종료시킨다

    + 자식 ----> 부모 순으로 단계적인 종료

      

>  프로세스와 관련된 시스템 콜
>
> + fork(): create a child (copy)
> + exec(): overlay new image
> + wait(): sleep until child is done
> + exit(): frees all the resources. notify parent



### fork() 시스템 콜

프로세스는 fork() 시스템 콜에 의해 생성된다

fork를 하면 자식 프로세스는 fork가 일어난 다음 시점부터 수행한다.

그 이유는 부모 프로세스의 context를 복제하기때문에 program counter로 어느 부분까지 수행됐는지 알 수 있고

자연스럽게 다음 instruction을 수행한다



자식, 부모는 fork()의 return 값으로 구분한다

자식의 pid는 0, 부모는 자연수로 설정해서 부모-자식 프로세스를 구분한다



```c
int main()
{
  int pid;
  /* 이 부분은 부모 프로세스에서만 출력 */
  printf("\n Start Program");
  
  pid = fork();
  if (pid == 0)
    printf("\n Hello, I am child\n");
  else if (pid>0)
		printf("\n Hello, I am parent\n");
}
```



### exec() 시스템 콜

A process can execute a different program by the `exec()` system call

+ Replace the memory image of the caller with a new program



```c
int main()
{
  
  printf("1");
  execlp("echo", "echo", "hello","3",(char *)0); 
  // >>> echo hello 3 
  //hello 3
  print("2") /* 2는 출력 안됨 */
    
}
```



### wait() 시스템 콜

![스크린샷 2021-04-08 오전 11.21.39](/Users/superyodi/Desktop/스크린샷 2021-04-08 오전 11.21.39.png)

### exit() 시스템 콜

​	![스크린샷 2021-04-08 오전 11.25.40](/Users/superyodi/Desktop/스크린샷 2021-04-08 오전 11.25.40.png)





### 프로세스 간 협력

+ 독립적 프로세스
+ 협력 프로세스
+ 프로세스 간 협력 메커니즘 (IPC: Interprocess Communication)
  + 메세지를 전달하는 방법
    + ***Message passing***: 커널을 통해 메세지 전달
    + <img width="873" alt="스크린샷 2021-04-08 오전 11 29 42" src="https://user-images.githubusercontent.com/31922389/113977855-2c367600-987e-11eb-8bf4-40d6a55edc99.png">
  + 주소공간을 공유하는 방법
    + ***Shared memory***: 서로 다른 프로세스 간에도 일부 주소공간을 공유하게 하는  shared memory 메커니즘이 존재
    + <img width="858" alt="스크린샷 2021-04-08 오전 11 32 56" src="https://user-images.githubusercontent.com/31922389/113977956-4cfecb80-987e-11eb-81f0-627298ae19f2.png">
    + (참고) thread
      + 스레드는 사실상 하나의 프로세스이므로 프로세스간 협력으로 보기는 어렵지만 동일한 process를 구성하는 thread 들 간에는 주소 공간을 공유하므로 협력이 가능



## [chapter 5] CPU Scheduling



### CPU and I/O Bursts in Program Execution

<img width="872" alt="스크린샷 2021-04-08 오전 11 40 48" src="https://user-images.githubusercontent.com/31922389/113978110-7d466a00-987e-11eb-8e64-82de30cb3d78.png">

프로그램은 CPU 버스트와 IO 버스트의 연속이지만 프로그램의 종류에 따라 각 버스트들의 빈도 및 길이가 다르다



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

+ CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에 넘긴다
+ 이 과정을 context switch라고 한다



CPU 스케쥴링이 필요한 경우는 프로세스에게 다음과 같은 상태변화가 있는 경우다

1. Running -> Blocked (eg, IO 요청하는 시스템콜)
2. Running -> Ready (eg, 할당시간만료로 tiemer interrupt)
3. Blocked -> Ready (eg, IO 완료 후 인터럽트)
4. Terminate

 

*1,4에서 스케줄링은 **nonpreemptive*** (강제로 빼앗지 않고 자진반납)

*All other scheduling is **preemptive*** (강제로 빼앗음)



