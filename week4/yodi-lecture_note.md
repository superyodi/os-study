# [week4] OS lecture note

## [Chapter6] Process Synchronization



Semaphore를 통해 lock을 걸고 풀 수 있다.

### Classical Problem of Synchronization



#### Bounded-Buffer Problem (Producer-Customer Problem)

버퍼의 크기가 유한한 환경에서 생산자-소비자 문제

+ 생산자에게 자원: 비어있는 버퍼의 갯수
+ 소비자에게 자원: 채워져있는 버퍼의 갯수



<img width="926" alt="스크린샷 2021-04-19 오후 6 58 01" src="https://user-images.githubusercontent.com/31922389/115224887-69d1b380-a148-11eb-9c2e-1fc2f013ed72.png">



**Shared data**

버퍼 자체 및 버퍼 조작 변수(empty 또는 full 버퍼를 가리키는 pointer)



**Synchronization variables**

1. mutual exclusion

   + 데이터 입출력때 공유버퍼에 lock을 걸고 풀기 위해

   +  Need **binary semaphore**
     + shared data의 mutal exclution(상호배제)을 위해

2. resource count
   +  Need **integer semaphore** 
     + 남은 full/empty buffer의 개수 표시



<img width="820" alt="스크린샷 2021-04-19 오후 7 05 04" src="https://user-images.githubusercontent.com/31922389/115224980-8837af00-a148-11eb-844b-d8ce023da50a.png">

  



#### Readers and Writers Problem

+ 한 프로세스가 DB에 <u>write 중일때 다른 process가 접근하면 안된다.</u>

+ <u>read는 동시에 여럿이 해도 된다.</u> 

  

<img width="872" alt="스크린샷 2021-04-19 오후 7 14 14" src="https://user-images.githubusercontent.com/31922389/115225086-a4d3e700-a148-11eb-8823-49d33e673495.png">

reader의 경우 읽을때도 lock을 걸어야하는데 (writer의 접근을 막기 위해)

다른 reader들도 접근할 수 없는 문제가 발생한다. 그래서 readcount라는 공유변수를 둬서 제일 처음 reader가 접근하는 상황(readcount == 1)에서 P(db)로 db에 lock을 걸어주고 그게 아니라면 lock을 걸지않는다. 

하지만 데이터 일관성을 위해 readcount 자체에도 lock을 걸어줄 필요가 있다. 그래서 사용하는게 *mutex*

내가 마지막 reader일땐 나가면서 db에 건 lock을 풀어줘야한다. 그래서 if (readcount == 0) V(db) 한다.



**Shared data**

+ DB 자체
+ readcount (현재 DB에 접근중인 Reader의 수)



**Synchronization variables**

+ mutex
  + 공유변수 readcount를 접근하는 코드(critical section)의 mutual exclusion 보장을 위해 사용
+ db (DB접근을 위한 변수)
  + Reader와 Writer가 공유 DB에 올바르게 접근하게 하는 역할 (binary type)



**Starvation 발생 가능**

writer와 reader가 동시에 왔는데 writer가 reader가 n개 있을때 n개가 다 읽을때까지 주구장창 기다려야한다.

그 사이에 writer 굶어주거.... :- (



해결책) 우선순위 큐 또는 신호등처럼 일정시간을 주고 읽도록 하는 방법 등등



---



#### Dining-Philosophers Problem

<u>식사하는 철학자</u>

철학자 다섯명이 원탁에 앉아있다.

철학자들은 두가지 일을 한다. 

1. 생각한다
2. 밥먹는다



밥을 먹기 위해선 젓가락 왼쪽과 오른쪽을 모두 잡아야한다. 

젓가락은 공유 자원이다. 

젓가락은 1로 초기화되어있어 동시에 젓가락을 잡을 수 없다.



밥을 다 먹으면 젓가락을 놓고

배가 고픈 다른 철학자가 젓가락으로 밥을 먹는다. 



**정리** ( [나무위키-식사하는 철학자](https://namu.wiki/w/%EC%8B%9D%EC%82%AC%ED%95%98%EB%8A%94%20%EC%B2%A0%ED%95%99%EC%9E%90%20%EB%AC%B8%EC%A0%9C))

1. 철학자들은 생각을 한다
2. 왼쪽 젓가락이 사용가능할때까지 대기한다. 만약 사용가능하다면 집는다
3. 오른쪽 젓가락이 사용가능할때까지 대기한다. 만약 사용가능하다면 집는다
4. 젓가락 두짝을 모두 집으면 일정 시간만큼 식사한다
5. 오른쪽 젓가락을 내려놓는다
6. 왼쪽 젓가락을 내려 놓는다
7. 다시 1번으로 되돌아간다



이 상황에서 철학자들 모두 왼쪽 젓가락을 집어들고 자신의 오른쪽 젓가락이 사용가능할때까지 기다리고 있는 상황을 **deadlock**이라고 한다. 





아래 코드는 deadlock이 발생하는 잘못된 코드 

<img width="837" alt="스크린샷 2021-04-20 오후 4 55 45" src="https://user-images.githubusercontent.com/31922389/115382027-27bf7500-a20f-11eb-8973-d84ad244fdac.png">

+ 앞 solution의 문제점
  + Deadlock 가능성이 있다
  + 모든 철학자가 동시에 배가 고파져 왼쪽 젓가락을 먼저 잡은 경우

  

+ 해결 방안

  1. 4명의 철학자만 테이블에 동시에 앉도록 한다

  2. 젓가락을 두 짝 다 집을 수 있는 상황에만 젓가락을 들도록 한다

  2. 비대칭

  + 짝수 철학자는 왼쪽부터
  + 홀수 철학자는 오른쪽부터
    

+ 해결방안 2에 대한 코드

<img width="910" alt="스크린샷 2021-04-20 오후 5 02 19" src="https://user-images.githubusercontent.com/31922389/115382143-4f164200-a20f-11eb-930f-23d31124ed91.png">

<u>변수 설명</u>

+ self[len(philosopher)] : i번째 철학자가 젓가락을 잡을 수 있는 권한이 있는 지 (0 || 1)
+ state[len(philosopher)] : i번째 철학자들의 상태를 나타내는 변수 
  + thinking, hungry, eating 세가지 상태가 있음 
+ mutex: lock
  + 여러 철학자의 공유변수에 대한 접근을 막기 위한 mutex



<u>함수 설명</u>

+ pickup(int i) : 철학자 i가 젓가락을 들때 사용하는 함수
  1. mutex로 lock을 건다
  2. 자신(i)의 상태를 hungry로 바꾼다
  3. test(i)
     1. 지금 내가 젓가락을 두쪽 다 잡을 수 있는지
  4. 행위가 끝났으니 mutex를 unlock한다
  5. P(self[i])
     + 만약 test()에서 조건이 참이 아니여서 권한을 얻지 못했다면 P(self[i])에서 권한이 생길때까지 기다려야한다 (*여기 뭔말인지 모르겠음*)

+ test(int i): 지금 i 철학자가 eating할 수 있는지 check
  + **조건: ** 철학자 i의 왼쪽과 오른쪽 철학자들이 밥을 먹고 있지 않으면서 내가 지금 배가 고플때
    + 참인 경우
      + state[i] = eating
      + V(self[i]) 
        + 여기서 V연산은 0이었던 값을 1로 바꾸는 연산
        + 초반에 0으로 초기화된 self[i]를 1로 바꾼다 ===> **철학자 i는  젓가락을 잡을 수 있는 권한을 얻게된다.** 
          + test()가 끝나면 pickup()으로 돌아가 P(self[i])를 통해 1이었던 값을 0으로 바꾸고 권한을 반납한다. 
+ putdown(int i)
  1. mutex로 lock을 건다
  2. 자신(i)의 상태를 thinking으로 바꾼다
  3. 인접한 철학자들 test
     + 아까 내가 밥먹고 있을 동안 내 양옆 사람들이 못먹고 있었으면 어떡하지? test 해주자
  4. mutext unlock



위 코드는 세마포어의 철학과 맞지 않는다.

세마포어는 자원의 갯수를 세는 역할, 그러므로 초기값은 자원의 전체 갯수로 두고 일을 하게 된다. (자원 얻으면 --)

하지만 위 코드는 <u>젓가락을 잡을 수 있는 권한</u>을 세마포어로 두 권한을 0으로 초기화, 만족될때 1을 줘서 권한을 주도록 구현했기때문에 세마포어의 철학과는 맞지 않아 어려움이 있다. 





위 세 문제를 통해 세마포어에 대해 알아봤고 세마포어를 이용해서 synchronization 문제들에대한 해결방식에 대해 알아봤다. 

---

> 덧, 세마포어 P, V 연산 다시 정리



+ P(S): 자원 획득

  + S.value--

  + if S.value < 0: 

    add this process to S.L;  // 대기 리스트에 추가

    block();  // 그리고 block

+ V(S): 자원 반납

  + S.value++

  + if S.value <= 0:

    // 나 말고 기다리고있던 프로세스가 있었단 소리

    remove a process P from S.L // 대기 리스트에 있던 P 제거

    wakeup(P) // 대기리스트에 있던 P 깨워서 실행시킴 







---





Process synchronization : 프로세스 동기화 (= concurrency control : 병행 제어)



여러가지 싱크로나이즈 관련 문제들이 있는데 프로그래머 입장에서의 해결으로 세마포어가 있다.

세마포어: P, V 연산으로 이루어진 추상 자료형

+ P 연산: 자료를 획득하는 과정
+ V 연산: 자료를 반납하는 과정



여러 프로세스가 동일한 공유 자원에 접근하려하는 상황에서 S라는 세마포어 변수에 P연산을 해주면 자원이 1일때 하나의 프로세스만 들어갈 수 있고 나머지 프로세스는 기다려야함. 



고전적 프로세스 동기화문제 3가지

1. 프로듀스 - 컨슈머
2. 리더 - 라이터
3. 식사하는 철학자



### Semaphore의 문제점



세마포어가 P,V로 동기화하지만 프로그래머가 수동으로 구현해야하므로 프로그래머가 실수를 하게되면 동기화 깨져서 문제가 발생한다

<u>예시</u> 

![스크린샷 2021-04-20 오후 5.36.49](/Users/superyodi/Library/Application Support/typora-user-images/스크린샷 2021-04-20 오후 5.36.49.png)





### Monitor

공유데이터 접근하는걸 모니터가 알아서 해결해줌

모니터는 <u>프로그래밍 언어 차원에서 프로세스들이 자원을 동시에 접근하는 상황을 근본적으로 막는다</u>



#### 동작방식

공유데이터 접근하는 코드를 모니터 안에 정의해서 모니터를 통해서만 접근하도록 함

만약 프로세스가 공유 데이터에 접근하려하면 모니터 안에 정의된 코드를 이용해서만 접근할 수 있다

모니터에 들어와서 active한 프로세스가 하나밖에 없도록 모니터가 제어한다 



#### 상황

프로세스A가 모니터 안의 코드를 실행하다가 CPU를 빼앗겼다. 

그 뒤에 프로세스B가 모니터 안으로 들어와 코드를 실행하려 한다.

모니터에서 프로세스A가 active 상태로 남아있기때문에 프로세스B는 모니터에 접근하지 못하고 모니터 밖 Queue에 줄서서 기다린다. 프로세스 B는 언제 모니터 속 코드에 접근할 수 있는가.

+ 프로세스A가 작업을 다 끝내고 빠져 나갔을때
+ 프로세스 A가 내부에서 잠들었을때

모니터 안의 activate process 수가 0이 되고. 그때 프로세스 B가 접근할 수 있다. 



![스크린샷 2021-04-21 오전 10.45.20](/Users/superyodi/Library/Application Support/typora-user-images/스크린샷 2021-04-21 오전 10.45.20.png)



+ 모니터 내에서는 한번에 하나의 프로세스만 활동 가능
+ 프로그래머가 동기화 제약 조건을 명시적으로 코딩할 필요 없음
+ 프로세스가 모니터 안에서 기다릴 수 있도록 하기 위해 condition variable 사용
  + 세마포어처럼 값을 가지는 변수가 아니라 줄세우기 위한 변수
  + condition x,y;
+ Condition variable은 wait과 signal 연산에 의해서만 접근 가능
  + **x.wait()**
    + x.wait()을 invoke한 프로세스는 다른 프로세스가 x.signal()을 invoke하기 전까지 suspend된다.
  + **x.signal()**
    + x.signal()은 정확하게 하나의 <u>**suspend**</u>된 프로세스를 **resume**한다.
    +  suspend된 프로세스가 없으면 아무일도 일어나지 않는다. 



### Bounded-Buffer Problem  (Monitor로 구현 버전)

 <img width="798" alt="스크린샷 2021-04-21 오전 10 30 44" src="https://user-images.githubusercontent.com/31922389/115659288-49d30780-a375-11eb-897c-bbc8c5127793.png">







### Dining Philosophers Example (Monitor로 구현 버전)

<img width="941" alt="스크린샷 2021-04-21 오전 10 37 05" src="https://user-images.githubusercontent.com/31922389/115659338-6111f500-a375-11eb-9c57-b9a565c0e16c.png">







---

## [Chapter7] Deadlock

 

데드락, 교착상태

더이상 방법이 없는 상황. 자원을 가지고있으면서 못가진 자원은 요청하는 상황



<img width="668" alt="스크린샷 2021-04-21 오전 11 10 23" src="https://user-images.githubusercontent.com/31922389/115659446-87379500-a375-11eb-84ab-438ffd622d03.png">





### 프로세스 자원 사용 절차

+ 요청, 할당, 사용, 해제





### Deadlock 발생의 4가지 조건

+ Mutual exclution(상호 배제)

  + 매 순간 하나의 프로세스만이 자원을 사용할 수 있음

+ No Preemption (비선점)

  + 프로세스는 스스로 자원을 내어놓을뿐 강제로 빼앗기지 않음

+ Hold and wait (보유대기)

  + 자원을 가진 프로세스가 다른 자원을 기다릴때 보유자원을 놓지 않고 쥐고있음

    (마치 수강신청 정정기간의 나,,,, )

+ Circular wait (순환대기)

  + 자원을 기다리는 프로세스간에 사이클이 형성되어야함
  
    
  
  

### Resource-Allocation Graph (자원할당그래프)

+ 데드락 아닌 상황 (사이클 없음)

  

<img width="343" alt="스크린샷 2021-04-21 오전 11 17 12" src="https://user-images.githubusercontent.com/31922389/115659950-455b1e80-a376-11eb-90fc-c8edbb6fb157.png" style="zoom: 100%;">

+ 데드락인 상황

   <img width="361" alt="스크린샷 2021-04-21 오전 11 18 45" src="https://user-images.githubusercontent.com/31922389/115660404-ed70e780-a376-11eb-9a6e-81c9cf5bc2d8.png">
">

+ 데드락이 아닌 상황 (자원이 여러개)

<img width="387" alt="스크린샷 2021-04-21 오전 11 18 45 복사본" src="https://user-images.githubusercontent.com/31922389/115660438-f9f54000-a376-11eb-9639-ce7f680c9046.png">





+ 그래프에 cycle이 없으면 deadlock이 아니다
+ 그래프에 cycle이 있으면
  + 자원당 인스턴스가 하나밖에 없으면 (Ri당 점이 하나밖에 없으면) deadlock
  + 자원당 인스턴스가 여러개라면 deadlock일수도, 아닐수도 있다
    + 그림의 위쪽 그래프는 데드락, 아래쪽 그래프는 데드락 아님



### Deadlock 처리 방법



(데드락을 미연에 방지하는 방법)

+ Deadlock Prevention
  
  + 데드락을 만족시키는 4가지 필요조건중 어느 하나가 만족되지 않도록 한다
+ Deadlock Avoidance
  + 자원 요청에 대한 부가적인 정보를 이용해서 데드락이 <u>발생하지 않을것이 확실한 경우에만</u> 자원 할당
    + 프로세스가 시작될때 해당 프로세스가 최대로 사용할 자원을 미리 declare
    + 어떤 프로세스가 어떤 자원을 몇개까지 사용할 수 있는지 미리 알려줌
    + 이 정보를 바탕으로 자원을 요청한 프로세스가 데드락을 발생시킬 여지가 있다면 비록 지금 할당할 자원이 남아있어도 자원 요청을 받아들이지 않는다. 
  + 방법
    + (*case*) Single Instance per resource type
      + (*solution*) **Resource Allocation Graph algorithm** 사용
    + (*case*) Multiple Instances per resource type
      + (*solution*) **Banker's Algorithm** 사용
    
    

(데드락이 생기도록 놔두고 발생하면 처리)

+ Deadlock Detection and recovery
  + 데드락 발생은 허용하되 그에 대한 detection 루틴을 둬서 데드락 발견시 recover
+ Deadlock Ignorance
  + Deadlock을 시스템이 책임지지 않음
    + 사람이 이상하게 느껴지면 알아서 하겠지
    + 대부분의 현대 OS들이 사용중인 방법



#### Deadlock Prevention

 ![스크린샷 2021-04-21 오전 11.27.13](/Users/superyodi/Desktop/스크린샷 2021-04-21 오전 11.27.13.png)



사이클을 막기 위해 자원마다 순서를 정해서 숫자가 낮은것부터 선점하게한다. 



#### Deadlock Avoidance

프로그램 시작될때 프로세스가 평생 사용할 자원을 미리 declare



#####  Resource Allocation Graph algorithm

<img width="886" alt="스크린샷 2021-04-21 오전 11 48 50" src="https://user-images.githubusercontent.com/31922389/115661616-adaaff80-a378-11eb-8c1f-c74d4c380edb.png">





##### Banker's Algorithm

프로세스들이 자원을 요청했을때 받아들일것인지, 안할것인지 결정



<img width="937" alt="스크린샷 2021-04-21 오전 11 52 57" src="https://user-images.githubusercontent.com/31922389/115661691-d0d5af00-a378-11eb-8d69-88335da2dcbd.png">

Allocation: 할당된 자원

Max: 최대 사용가능한 자원

Available: 전체 자원 - 지금 할당된 자원

Need: 추가 요청 가능 (Max - Allocation)



+ 줄 자원이 있지만 안줌 (항상 프로세스가 최대 요청을 할 것을 염두해둠) 요청 받아들이지 않고 기다림

+ 가용자원이 충족되면 이전에 충족안됐던 프로세스에게 자원 나눠줄수있음

+ **Available 자원으로 Need가 커버되면 요청 받아주는 알고리즘** 

 

**비효율적**

자원이 남아도는데도 혹시모를 가능성때문에 자원할당을 안하기 때문





<img width="905" alt="스크린샷 2021-04-21 오후 12 00 36" src="https://user-images.githubusercontent.com/31922389/115661792-fa8ed600-a378-11eb-81cb-cc391ad62460.png">





#### Deadlock Detection and Recovery



+ 자원당 인스턴스가 하나밖에 없는 상황

<img width="870" alt="스크린샷 2021-04-22 오전 10 44 35" src="https://user-images.githubusercontent.com/31922389/115662161-8a348480-a379-11eb-9ce2-4d8ad15fdb72.png">

<img width="781" alt="스크린샷 2021-04-22 오전 10 45 36" src="https://user-images.githubusercontent.com/31922389/115662176-902a6580-a379-11eb-9dd5-ce393a7e74a5.png">



+ 자원당 인스턴스가 여러개 있는 상황

<img width="901" alt="스크린샷 2021-04-22 오전 10 48 01" src="https://user-images.githubusercontent.com/31922389/115662212-9d475480-a379-11eb-928e-0c551cb65718.png">

데드락이 있는지 확일할땐 낙관적으로 접근



추가로 요청한게 없으면 반납할거라 가정

아무것도 요청하지 않은 프로세스가 가진 자원을 반납할거라 가정한 뒤 해당 프로세스들에게 할당된 자원들을 쌓고 쌓아서 Available 자원을 예상한다. 



**Deadlock Detection**

1. 먼저 요청을 하지 않은 프로세스의 자원을 반납시킨다 

   ----> P_0에서 B 한개 획득

2. 하지만 B자원을 원하는 프로세스 요청이 없으므로 여전히 프로세스들의 요청을 처리할 수 없다

3. 프로세스의 원칙에 의해 Deadlock 발생!!

   + 프로세스 원칙: 본인의 자원을 가지고 있으면서 요청한 자원이 만족될때까지 자원을 내어놓지 않는다



**Deadlock Recovery**

+ Process Termination (프로세스 죽이기)
  + 방법1. 데드락에 연루된 프로세스들을 모두 죽인다
  + 방법2. 데드락이 없어질때까지 연관된 프로세스들을 하나씩 죽여본다
+ Resource Preemption (자원 뺏기)
  + 비용을 최소화할 victim 선정
  + safe state로 roolback해서 process를 restart
  + 데드락 발생했을때 항상 같은 방식으로 자원을 뺏으면 <u>Starvation 문제가 발생할 수 있다.</u>
    + 그러므로 여러가지를 고려해 동일한 프로세스가 victim으로 선정되는걸 방지해야한다. 



