# 4주차 오에스 퀴즈





## 세마포어



+ 세마포어에 대해 설명하라
  + 답:
+ 세마포어 연산 두가지를 적고 어떤 역할을 하는지 설명하라
  + (              ):                         
  + (              ):                         

+ Mutex에 대해 설명하라
  + 답: 









## 프로세스 동기화 문제



다음은 프로세스 동기화 상황에서 발생할 수 있는 고전적인 문제 3가지다.

각 코드를 읽고 함수들의 역할을 대략적으로 설명하라 

### 생산자와 소비자

<img width="820" alt="스크린샷 2021-04-19 오후 7 05 04" src="https://user-images.githubusercontent.com/31922389/115224980-8837af00-a148-11eb-844b-d8ce023da50a.png">

1. Producer
   + P(empty): 
   + P(mutex):
   + V(mutex):
   + V(full):
2. Consumer
   + P(full):
   + P(mutex):
   + V(mutex):
   + V(empty):





### Writer와 Reader

<img width="872" alt="스크린샷 2021-04-19 오후 7 14 14" src="https://user-images.githubusercontent.com/31922389/115225086-a4d3e700-a148-11eb-8823-49d33e673495.png">



+ <u>Reader</u> 부분의 코드를 대략적으로 설명하여라  (세마포어 연산들을 기준으로)
  + 답: 



+ Writer 부분에서 starvation이 발생하는 이유는 무엇인가?
  + 답: 





### 식사하는 철학자



+ 아래 코드가 실행되면 발생하는 문제에 대해 설명하라
  + 답: 



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

  + 답: 

    

    

+ 모니터에 대해 설명하라.

  + 답: 

    
    



## 데드락





+ 데드락에 대해 설명하라 (어떤 상황인지)
  + 답: 



### 데드락 발생조건 4가지

1.   
2.   
3.    
4. 







+ 다음의 자원 할당 그래프를 보고 데드락 여부를 판단하고 그 이유를 써라

  + <img width="343" alt="스크린샷 2021-04-21 오전 11 17 12" src="https://user-images.githubusercontent.com/31922389/115659950-455b1e80-a376-11eb-90fc-c8edbb6fb157.png" style="zoom: 100%;">
    + 답:
  + <img width="361" alt="스크린샷 2021-04-21 오전 11 18 45" src="https://user-images.githubusercontent.com/31922389/115660404-ed70e780-a376-11eb-9a6e-81c9cf5bc2d8.png">
    + 답:

  

  

  

  

  

  

  

  + <img width="387" alt="스크린샷 2021-04-21 오전 11 18 45 복사본" src="https://user-images.githubusercontent.com/31922389/115660438-f9f54000-a376-11eb-9639-ce7f680c9046.png">

    + 답: 

      

      





### 데드락 처리방법 4가지

다음은 데드락을 처리하는 4가지 방식이다. 

어떤 방식으로 처리하는지 대략적으로 작성해라



 

데드락 사전 방지하는 방식

1. Deadlock Prevention      
   +  설명: 
2. Deadlock Avoidance
   + 설명: 



데드락 사후 대처 방식

3. Deadlock Detection and recovery

   + 설명: 

     

4. .Deadlock Ignorance
   
   + 설명:



### Deadlock Avoidance



Deadlock Avoidance는 자원의 상황에 따라 사용하는 알고리즘이 다르다. 

두가지 상황과 각 상황에서 사용되는 알고리즘을 설명하라. (알고리즘 이름을 정확히 몰라도 괜찮음, 대략적인 방식을 설명)

1. (상황 설명)
   + (알고리즘 설명)
2. (상황 설명)
   - (알고리즘 설명)



+ Banker's Algorithm

  다음 그림을 보고 질문에 답하라

  

  <img width="937" alt="스크린샷 2021-04-21 오전 11 52 57" src="https://user-images.githubusercontent.com/31922389/115661691-d0d5af00-a378-11eb-8d69-88335da2dcbd.png">
  
  + Allocation:  
  + Max: 
  + Available: 
  + Need: 

  <img width="905" alt="스크린샷 2021-04-21 오후 12 00 36" src="https://user-images.githubusercontent.com/31922389/115661792-fa8ed600-a378-11eb-81cb-cc391ad62460.png">



+ P4 (3,3,0) 할당 가능?
+ P0 (0,2,0) 할당가능?



#### 























