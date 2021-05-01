# 4주차 오에스 퀴즈





## 세마포어



+ 세마포어에 대해 설명하라
  + 답: 공유데이터에 동시에 접근하여 발생하는 문제를 해결하기 위해 동기화 
+ 세마포어 연산 두가지를 적고 어떤 역할을 하는지 설명하라
  + (   counting semaphore     ):   0 이상의 정수로 구성. 자원이 있는지 확인함
  + (      binary semaphore      ):  어떤 프로세스가 공유 데이터에 접근했는지 여부를 나타내는 세마포어로 0과 1로 구성. 주로 뮤텍스로 구현

+ Mutex에 대해 설명하라
  + 답: mutual exclusion의 약자로 어떤 프로세스가 공유데이터에 접근하면 lock을 걸고, 프로세스가 공유데이터에서 나올 때 unlock을 하는 기능을 한다. 





## 프로세스 동기화 문제



다음은 프로세스 동기화 상황에서 발생할 수 있는 고전적인 문제 3가지다.

각 코드를 읽고 함수들의 역할을 대략적으로 설명하라 

### 생산자와 소비자

<img width="820" alt="스크린샷 2021-04-19 오후 7 05 04" src="https://user-images.githubusercontent.com/31922389/115224980-8837af00-a148-11eb-844b-d8ce023da50a.png">

1. Producer
   + P(empty): 자원이 비었는지 여부를 확인한다. 자원이 비었으면 들어갈 수 있다. 
   + P(mutex): 프로듀서 프로세스가 들어갈 때 lock을 거는 연산 수행
   + V(mutex): 공유데이터에서 빠져나올 때 lock을 푼다.
   + V(full): 공유데이터에서 빠져나올 때 자원이 가득 찼는지 확인한다. 
2. Consumer
   + P(full): 자원이 찼는지 여부를 확인한다. 
   + P(mutex): 공유데이터에 접근할 때 lock을 거는 연산 수행
   + V(mutex): 공유데이터에서 빠져나올 때 lock을 푼다.
   + V(empty): 공유데이터에서 빠져나올 때 자원이 비었는지 여부를 확인한다. 





### Writer와 Reader

<img width="872" alt="스크린샷 2021-04-19 오후 7 14 14" src="https://user-images.githubusercontent.com/31922389/115225086-a4d3e700-a148-11eb-8823-49d33e673495.png">



+ <u>Reader</u> 부분의 코드를 대략적으로 설명하여라  (세마포어 연산들을 기준으로)
  + 답: reader가 있는 경우 lock, reader가 빠져나오고 하나도 없을 때 unlock



+ Writer 부분에서 starvation이 발생하는 이유는 무엇인가?
  + 답: reader가 없을 때에만 writer가 접근할 수 있기 때문에 reader가 계속해서 있으면 starvation이 발생할 수 있다. 





### 식사하는 철학자



+ 아래 코드가 실행되면 발생하는 문제에 대해 설명하라
  + 답: deadlock이 발생할 수 있음. 철학자 배고파요..



<img width="837" alt="스크린샷 2021-04-20 오후 4 55 45" src="https://user-images.githubusercontent.com/31922389/115382027-27bf7500-a20f-11eb-8973-d84ad244fdac.png">





+ 위의 코드로 발생할 수 있는 문제를 해결하기 위해 젓가락을 두쪽 다 들 수 있는 상황에서 젓가락을 들도록 하는 코드를 작성하였다.

  코드에 대한 대략적인 설명을 작성하라 





<img width="910" alt="스크린샷 2021-04-20 오후 5 02 19" src="https://user-images.githubusercontent.com/31922389/115382143-4f164200-a20f-11eb-930f-23d31124ed91.png">



<u>변수 설명</u>

+ self[len(philosopher)] : 

+ state[len(philosopher)] : 

  + thinking, hungry, eating 세가지 상태가 있음 

+ mutex:

  



<u>함수 설명</u>

+ pickup(int i) : 철학자 i가 젓가락을 들때 사용하는 함수

  

  (대략적인 설명 ㄱㄱ)

  

  

+ putdown(int i): 철학자 i가 젓가락을 놓을때  사용하는 함수



​		(대략적인 설명 ㄱㄱ)



+ test() 

  

  (대략적인 설명 ㄱㄱ)



## 모니터

+ 세마포어의 문제점은 무엇인가? (세마포어를 사용하면 발생할 수 있는 상황)

  + 답: 자발적 협력을 필요로 한다. 코드를 작성하기 힘들다. 

    

    

+ 모니터에 대해 설명하라.

  + 답: 모니터는 세마포어에서 발생하는 문제를 해결할 수 있는 구조로, 동기화문제를 해결하기 위해 세마포어를 사용한다면 직접 개발자가 코드를 짜서 대응해야 했다면, 모니터는 모니터 차원에서 동기화문제를 해결하므로 lock/unlock를 하지 않고도 문제를 해결할 수 있다. 

    
    



## 데드락

+ 데드락에 대해 설명하라 (어떤 상황인지)
  + 답: 여러 프로세스가 서로가 자원을 갖고 있으면서 교착상태가 발생하는 것



### 데드락 발생조건 4가지

1.   상호배제
2.   비선점
3.    순환구조
4. 자원을 갖고 있으면서 보유한 자원을 놓지 않아야 한다. 







+ 다음의 자원 할당 그래프를 보고 데드락 여부를 판단하고 그 이유를 써라

  + <img width="343" alt="스크린샷 2021-04-21 오전 11 17 12" src="https://user-images.githubusercontent.com/31922389/115659950-455b1e80-a376-11eb-90fc-c8edbb6fb157.png" style="zoom: 100%;">
    + 답: 데드락이 아니다. p2가 필요한 R3는 p3가 사용한 이후에 사용할 수 있으므로 데드락 문제가 발생하지 않는다. 
  + <img width="361" alt="스크린샷 2021-04-21 오전 11 18 45" src="https://user-images.githubusercontent.com/31922389/115660404-ed70e780-a376-11eb-9a6e-81c9cf5bc2d8.png">
    + 답: 데드락이다. 순환구조를 이루고 있으며 서로 보유한 자원을 쥐고 놓지 않아 교착상태인 데드락이 발생한다. 

  

  + <img width="387" alt="스크린샷 2021-04-21 오전 11 18 45 복사본" src="https://user-images.githubusercontent.com/31922389/115660438-f9f54000-a376-11eb-9639-ce7f680c9046.png">

    + 답: 데드락이 아니다. p1과 p2가 순환구조에 있으나 인스턴스가 두 개씩 있어 p2, p4가 사용하고 난 후 사용하면 교착상태에 빠지지 않는다. 

      





### 데드락 처리방법 4가지

다음은 데드락을 처리하는 4가지 방식이다. 

어떤 방식으로 처리하는지 대략적으로 작성해라



 

데드락 사전 방지하는 방식

1. Deadlock Prevention      
   +  설명: 데드락이 발생하는 4가지 조건 중 하나를 만족시키지 못하도록 하여 데드락을 예방한다.
2. Deadlock Avoidance
   + 설명: 데드락이 발생할 가능성이 있는 경우 가용자원이 있음에도 자원을 주지 않음으로써 데드락이 발생하지 않도록 하는 방식
   + 즉, safe state가 있을 때에만 자원을 할당함.
     + Safe sequence 가 있는 경우 safe state라고 한다. 



데드락 사후 대처 방식

3. Deadlock Detection and recovery

   + 설명: deadlock 발생을 허용하고 데드락이 발생했을 경우 recovery를 하는 방식

     

4. .Deadlock Ignorance
   
   + 설명: 데드락이 발생해도 시스템이 이를 해결하지 않고 사용자가 직접 제어하도록 하는 방식으로, 데드락이 실제로 많이 발생하지 않기 때문에 데드락을 감지하고 처리하는데 많은 오버헤드가 들기 때문에 대부분의 현대적인 운영체제에서 많이 채택하는 방식이다. 
   + unix, windows 등 범용 운영체제에서 많이 사용



### Deadlock Avoidance



Deadlock Avoidance는 자원의 상황에 따라 사용하는 알고리즘이 다르다. 

두가지 상황과 각 상황에서 사용되는 알고리즘을 설명하라. (알고리즘 이름을 정확히 몰라도 괜찮음, 대략적인 방식을 설명)

1. (상황 설명) 자원 당 인스턴스가 하나씩 있을 때 사용
   + (알고리즘 설명)
   + 자원할당그래프를 사용해서 wait- ? graph을 사용하여 사이클이 있는 경우 데드락으로 판단한다.  
2. (상황 설명) 자원 당 여러 개의 인스턴스가 있을 때 사용한다.
   - (알고리즘 설명)  Banker's Algorithm
   - 미리 최대 요청을 받아놓음. 
   - 자원을 요청할 때 최대로 필요한 자원을 충족할 수 없을 때는 deadlock이 발생할 가능성이 있으므로 요청을 받아들이지 않아 데드락을 방지한다. 



+ Banker's Algorithm

  다음 그림을 보고 질문에 답하라

  

  <img width="937" alt="스크린샷 2021-04-21 오전 11 52 57" src="https://user-images.githubusercontent.com/31922389/115661691-d0d5af00-a378-11eb-8d69-88335da2dcbd.png">
  
  + Allocation:  각 프로세스마다 할당된 자원
  + Max:  최대로 필요한 자원 수
  + Available: 가용자원
  + Need: 프로세스마다 최대로 필요할 수 있는 자원의 개수. 

  <img width="905" alt="스크린샷 2021-04-21 오후 12 00 36" src="https://user-images.githubusercontent.com/31922389/115661792-fa8ed600-a378-11eb-81cb-cc391ad62460.png">



+ P4 (3,3,0) 할당 가능? ㄴㄴ
+ P0 (0,2,0) 할당가능? ㄴㄴ



#### 






















