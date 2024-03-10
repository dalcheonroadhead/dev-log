# Weaving과 Proxy 객체에 대하여

> 이번 장에서 알아볼 것: 
>
> 1. AOP에서 Weaving이란 무엇인가? Proxy와의 관계는 어떻게 되는가? 
> 2. Proxy 객체란 무엇이고? AOP에서 사용되는 원리는 무엇인가?

## 1. AOP에서 Weaving이란 무엇인가? 

AOP에서 **weaving이란** 부가기능(advice)을 그것이 타겟팅하는 본 기능에 장착하는 작업을 말한다. 따라서 weaving은 본기능에 영향을 주지 않으면서 부가기능을 연결시키는 AOP의 핵심 작업이다.  
weaving은 실행되는 시점에 따라 3가지로 종류가 나뉜다.  

### (1) Compile Time Weaving(CTW)

Spring에서 AOP를 구현하는 방법에는 AspectJ라는 라이브러리를 쓰는 것과, Spring AOP API를 이용하는 것으로 크게 두 가지가 나뉜다. 이 중에서 Compile TIme weaving은 AspectJ로 AOP를 구현할 떄 사용할 수 있는 Weaving 방법이다. 
  AspectJ에게는 AJC(AspectJ Compiler)라는 자바 컴파일러를 확장한 자신만의 컴파일러가 있는데, 이 컴파일러를 사용하면, 프로그램 컴파일 단계에서 컴파일러가 기계어 전 단계인 바이트 코드를 조작해서 부가기능을 직접 본기능에 부착 시킨다. CTW는 컴파일 단계에서 AOP를 수행하므로 3가지 방법 중 제일 속도가 빠르다는 장점이 있다. 하지만, 만약 개발자가 Lombok처럼 컴파일 단계에서 코드를 조작하는 라이브러리를 해당 AJC랑 같이 사용할 경우 바이트 코드 조작 단계에서 충돌 에러가 일어날 가능성이 높다.  (거의 무조건 일어나서 Lombok과 AspectJ는 같이 사용 불가!)

### (2) Runtime Weaving(RTW)

해당 방식은 Spring AOP API를 사용해서 AOP를 구현할 때 사용하는 weaving 방식이다. 여기서 Proxy라는 객체가 쓰인다. RTW는 Proxy 객체를 이용하여 본 기능에 어떠한 영향도 주지 않고 AOP를 구현한다. (RTW가 어떻게 Proxy 객체를 이용하여 AOP를 완성하는지는 후술하겠다.) 해당 기능은 바이트 코드 조작과 같은 과정이 없기 떄문에, 클래스 파일이나 코드 파일이 온전하게 컴파일 된다는 장점이 있지만, 만약 pointcut 당 연결지어야 하는 advice 수가 많을 경우, 속도가 다른 방식들에 비해 상대적으로 더 느려진다는 단점이 있다. 

### (3) Load Time Weaving (LTW) 

ClassLoader를 이용해 클래스가 JVM에 로드될 때, 바이트 조작을 통해 본기능에 advice를 삽입하는 (위빙하는) 방식이다.

 

## 2. Proxy와 weaving의 관계는 어떻게 되는가? 

Proxy는 Runtime weaving을 구현할 경우, 구현의 핵심이 되는 개념이다. 자 이제 Proxy가 무엇이고, RTW의 원리는 무엇인지 한번 살펴보자. 

## 3. Proxy란? + RTW의 원리

proxy의 사전적 의미는 대리인 이다. Spring AOP에서 proxy 라는 개념 또한 이 사전적 의미를 그대로 따라가는데, 원 객체(real Object)를 대신하여 일을 처리하는 객체라는 의미이다.  
  만약 Service 객체 A의 Proxy를  *Z*라고 가정해보자. 만약 클라이언트가 서비스 로직 A에게 요청을 보낸 경우,  그것의 대리인 *Z*가 자신이 A인척 클라이언트를 속여서 요청을 대신 받는다.  그리고 전처리 부가기능을 수행하고 본 객체를 자신의 내부에서 호출하여 본 기능을 수행한다. 그 후 후처리 부가기능을 수행한다. 

![image-20240310231417040](C:\Users\SSAFY\AppData\Roaming\Typora\typora-user-images\image-20240310231417040.png)

> 위의 그림으로 설명했지만, Proxy 객체를 이용한 RTW를 글로 설명해보자면

0. 사전작업: proxy라는 객체를 만들고 그 안을 전처리 부가기능 수행 -> 본기능 호출과 반환 -> 후처리 부가기능 수행 으로 구성한다. (해당 구성은 Spring AOP 어노테이션 사용하면 스프링이 자동으로 해줌)
1. 사용자가 요청하면 본 객체(real Object) 대신에 그에 대응되는 Proxy객체가 대신 호출된다. 
2. Before Advice가 있을 시 그것이 수행된다. 
3. Proxy가 본 객체의 필요한 기능을 호출하고 그 결과를 반환 받는다. 
4. After Advice가 있을 시 그것이 수행된다. 
5. 모든 처리가 끝나면 결과값을 반환한다. 





### 참고문헌

>[AOP_Proxy](https://dahye-jeong.gitbook.io/spring/spring/2020-04-09-aop-proxy) 
>
>[AOP의 기본 개념과 Spring에서의 적용방법](https://velog.io/@youjung/Spring-AOP) 