# Concept-of-AOP 스스로 정리

> 목차
>
> 1. AOP란 무엇인가?
>
> 2. 그림과 함께 알아보는 용어 정리
>    1. Advice
>    2. Join Point
>    3. pointcut
>    4. Aspect
>    5. Introduction
>    6. Target Object
>    7. AOP proxy
>    8. Weaving 
>    9. Cross-cutting concerns



## 1. AOP란 무엇인가? 

Aop는 **관점 지향 프로그래밍(Aspect Oriented Programming)**을 말한다.  Java라는 언어를 배울 때 우리는 자바가 **객체지향프로그래밍(Object Oriented Programming)**을 지향하는 언어라고 배웠다. 객체는 Class라는 하나의 설계도에서 나온 구현체였다. 지금까지 우리는 이 객체 하나하나에 집중하여 프로그래밍 했다. 

![image-20240311000934541](https://github.com/dalcheonroadhead/Spring-framework-theory/assets/102154788/56410503-9c49-4c72-bb62-05c66ffacf86)


> "그래, 우리는 지금까지 이런 방식으로 잘해왔는데 왜 AOP가 필요한 거지?" 

이제 이 질문에 답을 하려고 한다. 여러분이 설계도를 가지고 자동차, 비행기 혹은 세탁기를 만드는데, 만들기 전에 기획부 직원이 와서 설계 전 과정에 걸리는 시간을 세고 싶다고 한다. 정확한 시간을 측정하려면, 구현 시작 직전에 스톱워치를 누르고, 완성되자마자 스톱워치를 정지해야 한다. 만약 자동차와 비행기, 세탁기가 각기 다른 공장에서 만들어진다고 가정한다면? 기획부에서는 각 공장마다 직원을 비치해야한다. 

![image-20240311001403321](https://github.com/dalcheonroadhead/Spring-framework-theory/assets/102154788/f801fb0c-ff5f-4118-a42c-78e6c7f53615)


이제 이 비유를 실제 프로젝트에 대입해서 생각해보자. 세탁기, 비행기, 자동차를 만드는 것을 Service Logic 이라고 하고, 시간을 세는 과정 또한 하나의 로직이라고 생각해보자. 그러면 우리는 각 서비스 로직마다 시작 시간과 끝 시간을 다 측정하고 그 차를 구하는 로직을 전부 넣어야 한다!! 
  이는 배보다 배꼽이 더 큰 경우를 만들 수 있다. 이 스톱워치 로직이 제대로 동작하지 않는다고 하자. 그러면 여러분은 진짜 설계를 구현하는 로직에 집중하지 못하고 그것을 하나하나 고치는데 주력해야한다. 
  이처럼 OOP에서는 부가기능을 서비스 로직마다 추가하고 싶을 때, 다음과 같은 귀찮은 점이 생긴다. 이때 이러한 OOP의 단점을 보완해주는 것이 바로 AOP이다. 

먼저 위의 스톱워치 기능 같은 것을 횡단 관심사 (cut-acrossing concern)이라고 한다. 모든 기능들이 공통으로 실행하길 원하는 부가기능을 뜻하는데, AOP에서는 이 부가기능을 본기능과 분리해두는 것이 가능하다. (방식은 후술하겠다.) 이렇게 부가기능을 본기능과 분리하게되면, 부가기능이 본 기능들을 가로지르며 적재적소의 위치에 실행되는데, 이 모습이 횡단하는 것과 같아 보여서 붙여진 이름이다. 

![image-20240311002701864](https://github.com/dalcheonroadhead/Spring-framework-theory/assets/102154788/98d8fb40-3590-4878-87c4-f40d8f769a5e)


**AOP는 객체 하나하나에 집중하던 기존 관점에서 벗어나, 기능간의 관계에 집중하고, 본기능과 부가기능을 분리하는 프로그래밍 **을 뜻한다. 객체의 설계도인 클래스는 하나의 관심사에 대한 설계이다. (ex- 자동차 설계, 비행기 설계 등), 하지만 엄밀히 따지면 객체 내에서도 위처럼 관심사가 나뉠 수 있다. (ex - 서비스 로직 <-> 서비스 로직의 부산물을 DB에 저장할 때 트랜잭션 처리 로직 등) AOP는 이 객체 내부에서 나뉘는 관심사를 분리하는 역할을 한다. 
  AOP를 프로그램에 도입하면 개발자는 본 기능 자체에 더 집중할 수 있다는 강력한 이점을 얻는다. 왜냐하면 부가 기능 코드는 딱 한번만 작성하고 이를 Spring AOP 혹은 AspectJ가 자동으로 본기능 실행 전 후에 부착해주기 때문이다. 

## 2. 그림과 함께 알아보는 용어 정리 

![image-20240311003724771](https://github.com/dalcheonroadhead/Spring-framework-theory/assets/102154788/fe8aa4cc-7dab-473a-b76e-4c7228e5e3b5)


### 1. Advice

우리가 본 기능에 부착하고 싶은 부가기능 자체를 의미한다. 부가 기능이 부착되는 시점 (본 기능 실행 전이냐, 후냐) 혹은 본 기능의 반환 값에 따라서 그 유형이 나뉜다. (자세한 유형은 제 Concept of AOP 번역본에 적혀 있으니 생략하겠습니다.)

### 2. Join Point

Join Point는 Advice가 합류하는 시점을 뜻한다. 위의 Advice 유형이 Before 혹은 After로 나뉘어지는 것은 이 녀석 때문이다. Spring AOP에서 join Point는 언제나 매소드 실행 시점 (실행 전 혹은 실행 후)만으로 제한된다.  

### 3. pointcut

Join Point가 위치라면 pointcut은 그 위치를 나타내는 주소이다. SpringAOP는 이 pointcut이란 표현식을 통해 부가기능이 실행되어야할 위치를 파악하고 부가기능을 연결한다. pointcut 표현식은 AspectJ의 정규식을 따른다. 

### 4. Aspect

관점 -> AOP에서 모듈화된 부가기능 자체를 의미한다. Aspect는 Advice(내용물) 과 pointcut(내용물이 실행되어야 할 주소) 2가지를 합친 개념이다. 

### 5. Introduction

AOP는 본 기능에 어떠한 간섭도 하지 않는 것을 지향한다. 하지만 드물게, 본 기능 실행 전에 본 기능에 field값이 Method를 넣어야하는 일이 생길 수 있겠다. (이런 일은 되도록 피하는 것이 좋다!) 이때 사용하는 것이 AOP의 introduction이라는 기능이다. introduction의 사전적 의미 중 하나는 '도입'이란 의미가 있다. 그 뜻 그대로 introduction을 통해 우리는 본 기능에 새로운 필드나 매소드를 추가할 수 있다. 

### 6. Target Object

부가기능이 장착될 본 기능 객체 자체를 뜻한다.  Spring AOP가 runtime에 본 기능을 대리하는 Proxy 객체를 만드는데 성공한 후 부터는 Target Object는 진짜 본래의 객체 (real Object)를 뜻하는 것이 아니라, 그것을 대리하는 객체 (Proxy 객체)를 의미한다. 



### 7. Weaving

weaving은 부가기능을 본 기능에 연결시키는 작업 자체를 의미한다. 해당 작업이 수행되는 과정은 AspectJ를 쓰는지, 아니면 Spring AOP를 쓰는지에 따라 나뉘는데 자세한 내용은 [Weaving과 Proxy에 관하여](https://github.com/dalcheonroadhead/Spring-framework-theory/blob/main/AOP/relatedStudy/ProxyAndWeaving.md) 에서 더 알아보았다.

### 8. AOP proxy

AspectJ를 쓰지 않는 한 Spring은 Spring AOP API를 이용해서, 런타임에 Weaving을 수행한다.  Spring Aop는 Weaving 수행 시 이 proxy라는 대리 객체를 사용한다. 똑같이 자세한 내용은 [Weaving과 Proxy에 관하여](https://github.com/dalcheonroadhead/Spring-framework-theory/blob/main/AOP/relatedStudy/ProxyAndWeaving.md) 에서 알아보았다. 





