> chapter 8. Memory Management



### 논리적, 물리적 주소



#### 논리적 주소와 물리적 주소에 대해 설명하라



### 주소 바인딩

+ 컴파일 타임 바인딩
+ 로드 타임 바인딩
+ 실행 타임 바인딩





### MMU

<img width="921" alt="스크린샷 2021-04-27 오후 8 27 23" src="https://user-images.githubusercontent.com/31922389/116403454-89fc2380-a868-11eb-80df-5138ed60a13b.png"  >

#### MMU의 기능에 대해 설명하라









#### 한계 레지스터(limit register)를 사용하는 이유와 가능을 말하라





> 메모리와 관련된 용어





### Dynamic Loading

+ 다이나믹 로딩은 무엇인가?
+ 다이나믹 로딩을 사용하는 이유는 무엇인가?



### Overlays

+ 오버레이 기법은 무엇인가?

+ 오버레이 기법은 어떤 상황에서 사용하는가?

+ 다이나믹 로딩과 차이점이 무엇인가? (사용하는 이유를 중심으로)

  

### Swapping

+ 스와핑 기법은 무엇인가?
+ Backing Store인 Swap Area는 휘발성 저장공간인가? 비휘발성 저장공간인가?
  + Yes || No
+ 스와퍼의 역할은 무엇인가?



### Dynamic Linking

+ Linking은 무엇인가?
+ Static Linking은 무엇인가?
+ Dynamic Linking은 무엇인가?





> 물리적 메모리의 할당방식



### 연속 할당 방식

+ 고정분할은 무엇인가?

  + 고정분할에서 발생하는 문제점은 무엇인가?

    

+ 가변분할은 무엇인가?

  + 가변분할에서 발생하는 문제점은 무엇인가?





----

### 페이징 기법





+ 페이징 기법은 무엇인가?
+ 페이징 기법의 단점은 무엇인가?



+ 페이징 테이블은 무엇인가?
+ 페이징 프레임은 무엇인가?



+ 페이징 기법에서 주소를 변환하는 상황을 나타낸 그림이다. 그림을 보고 주소 변환 과정을 설명하라 

<img width="847" alt="스크린샷 2021-04-28 오후 7 22 24" src="https://user-images.githubusercontent.com/31922389/116403939-1d355900-a869-11eb-94c7-d17d568cba98.png">









+ 페이징 테이블을 사용할때 메모리에 2번접근 해야해서 속도가 느려지는 문제가 발생하는데 이를 해결하기 위한 하드웨어적 장치는 무엇인지 말하고, 그 기능을 설명하라





### 계층적 페이징



+ 계층적 페이징을 사용하는 이유를 설명하라
+ 다음은 Two-level-table 을 나타낸 그림이다. 그림을 보고 주소가 변환되는 과정을 설명하라

<img width="881" alt="스크린샷 2021-04-28 오후 7 46 59" src="https://user-images.githubusercontent.com/31922389/116775694-522af100-aa9f-11eb-8479-cdf20d3b968e.png">





### Inverted Page Table

+ page table이 매우 큰 이유는 무엇인가?
+ Inverted page table은 무엇인가?



### Shared Page

+ Shared Page를 사용하는 이유는 무엇인가?
+ Shared Page를 사용할때 논리적 주소는 어때야 하는가?





### Segmentation

+ 세그멘테이션은 무엇인가?

+ 세그멘테이션을 사용하는 이유는 무엇인가?

+ 다음은 세그멘테이션의 구조를 나타낸 그림이다. segmentaion의 구조와 limit과 base의 기능을 설명하라

  <img width="867" alt="스크린샷 2021-05-01 오전 11 52 46" src="https://user-images.githubusercontent.com/31922389/116776064-a2a34e00-aaa1-11eb-8fa2-7fae3f69e782.png">



