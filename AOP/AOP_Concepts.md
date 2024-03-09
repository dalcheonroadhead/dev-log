# AOP가 담고있는 발상 

AOP가 가지고 있는 몇몇 중심적인 생각들과 용어들을 정의하는 것으로 시작해보자! 여기서 쓰이는 용어들은 스프링 맞춤이 아니다. 따라서 불행하게도 AOP의 용어들은 특별히 직관적이진 못하다. 하지만, 만약 스프링이 AOP 개념 도입을 위한 그것만의 용어를 썼다면, 그것이 더 우리를 혼란스럽게 만들 것이다! 



## 1. 용어설명 

- Aspect(관점):  복수의 Class들을 가로지르는 관심사에 대한 모듈화를 의미한다. 
  (기존의 우리가 서버 로직을 공부할 때, 기능을 무엇이라 생각했는가? Service Class 하나가 곧 기능이라고 생각했다. 하지만 AOP에서는 관점을 달리하여, 클라이언트로부터 요구가 들어와서 Controller -> 복수의 서비스 로직 -> 다시 응답 까지의 전 과정을 하나의 관심사로 보겠다는 뜻이다. 그리고 이 관심사를 모듈화 하겠다는 의미이다. )
- Join Point(부가 기능의 합류 지점): 프로그램을 실행하는 동안의 어떠한 지점을 의미한다. 예를 들어서 매소드를 실행하는 지점이나, 예외를 처리하는 지점 등이 있다. Spring AOP에서는 Join Point는 언제나 매소드 실행 지점을 의미한다. 
- Advice(부가 기능): 특정 Join Point에서 Aspect에 의해 실행되어질 행동을 의미한다. advice에는 "around", "before", "after"를 포함해 다양한 유형이 존재한다. (advice의 유형들에 대해선 나중에 다시 토의하겠다.) 스프링을 포함한 많은 AOP 프레임워크들은 advice를 Interceptor처럼 구현하였다. 그리고 일련의 Interceptor들을 join Point 주변에 유지시켰다. (join point를 일련의 advice 고리로 둘러쌀 수 있다는 것을 말하는 것 같다.)
- Pointcut: Join Point들과 연결시키는 하나의 술부이다. (Pointcut은 여러 Join Point 중 advice가 실행될 지점 혹은 실행되지 말아야할 지점을 서술하는 하나의 술부임.) Advice는 하나의 pointcut 표현식과 연관되어져있고, 그 pointcut에 의해 연결되어진 어떠한 join point에서 실행되어진다. [예를 들어, 특정 이름을 가진 매소드의 실행지점을 advice A 위에 pointcut 표현식으로 적으면 advice A는 그 매소드 실행 지점에서 실행됨. ] **'pointcut 표현식에 의해 연결되어지는 Joint point들' 이란 개념은 AOP의 핵심적인 개념이다. ** 그리고 Spring은 표준으로 AspectJ 포인트 컷 표현식을 사용한다. 
- Introduction(도입):  유형을 대표하여, 추가적인 필드 혹은 매소드를 선언하는 것을 의미한다. (현재 사용하는 advice의 유형을 말하는 듯..?) Spring AOP는 당신이 새로운 interface(혹은 그에 상응하는 구현체)를 부가기능이 붙어있는 객체 속에 도입할 수 있도록 해준다. 예를 들어 당신은  캐싱을 단순화 하는 용도인, *IsModified* 라는 인터페이스를 구현한 Bean 객체를 만들기 위해 Introduction을 사용할 수 있다. (introduction은 AspectJ 커뮤니티에서 inter-type 선언으로서 알려져 있다.)
- Target Object: 하나 혹은 그 이상의 Aspect에 의해 조언된(adivised: 즉 부가기능이 붙은) 객체를 의미한다. 또한 "advised Object"라는 이름으로도 언급되어질 수 있다. Spring AOP가 런타임 Proxy들을 사용하여 구현되어진 후 부터, 이 객체는 언제나 proxy화된 Object 이다. (Proxy에 대한 추가 설명은 공부해서 다른 페이지 파서 정리하겠다!) 
- AOP proxy: Aspect 원리를 구현하기 위해 AOP 프레임워크에 의해 생성된 객체를 의미한다. (Aspect 원리: 매소드 실행 시 부가 기능 부착과 같은 것) Spring Framework에서는 AOP proxy는 JDK dynamic proxy 이거나 혹은 CGLIB proxy이다. 
- Weaving:  부가 기능이 부착된 객체를 생성하기 위해, 모듈화된 기능들을 다른 응용 adviced들 혹은 응용 객체들에 연결시키는 과정을 의미한다. 이것은 컴파일 시간, 로드 시간 혹은 런타임에 처리될 수 있다. (예를 들어, AspectJ 컴파일러를 사용한다면 컴파일 시간에 처리된다.) Spring Aop에서는, 다른 순수 Java AOP 프레임워크들 처럼, 런타임에 weaving을 수행한다. 

**Spring AOP는 후술된 adivce 유형들을 포함한다.**

- Before advice: join Poin 이전에, 실행되는 Advice를 뜻한다. 하지만, Before Advice는 joinpoint로 나아가는 실행 흐름을 차단할 능력은 없다. (Before Advice가 에러를 내뱉지 않는 한)
- After returning advice: join point에 해당하는 객체가 무사히 자기 작업을 끝냈을 경우, 실행되는 advice이다. (예를 들어, 만약에 매소드가 에러를 뱉지 않고 무사히 return을 한 경우가 이에 속한다.)
- After throwing advice: 만약에 매소드가 예외를 내뱉고 종료 되었다면, 해당 advice가 실행된다. 
- After (finally) advice: join point에 해당하는 객체가 에러를 내뱉고 종료했든, 정상 종료 했든 그 근거에 의미를 두지 않고, 실행되는 advice
- Around advice: 매소드 호출과 같은 join point를 둘러싸는 advice. 이것은 가장 강력한 유형의 advice이다. Around advice는 매소드 호출 이전과 이후에 맞춤 행동을 수행할 수 있다. 이것은 또한 join point로 나아갈 것인지 아닌지 혹은 이 advice 자체의 return 값을 반환하거나 예외를 던져서 대상 객체 실행 자체를 생략할지에 대해서도 책임지고 있다. 

Around advice는 가장 일반적인 유형의 advice이다.  AspectJ같은 Spring AOP가 광범위한 advice 유형들을 제공한 뒤로, 우리는 당신들이 요구된 행동을 구현한 가장 최소로 강력한 advice 유형을 사용하길 권한다. 예를 들어, 만약 당신이 오직 매소드의 반환 값과 관련된 캐시를 업데이트할 필요가 있다면, 당신은 after returning advice를 구현하는 것이 around advice를 구현 하는 것보다 더 나을 것이다.  around advice가 같은 일을 달성할 수 있음에도 불구하고, 가장 필요한 일에 맞춰져 있는 advice를 사용하는 것은 에러가 날 가능성이 더 적은, 더 단순한 프로그래밍 모델 구현으로 가는 지름길 이다. 예를 들어, 당신은  





> 추신: 해당 스크립트에서 type을 advice의 유형들 그러니까 after Advice, before Advice를 지칭하는 것으로 사료되어 type이란 말의 번역은 adivce의 유형이라고 의역했습니다. 혹시 틀렸다면 ISSUE에 Question으로 넣어주세요.



## 몰랐던 단어 정리 

| Engilsh        | Korean                                                       |
| -------------- | ------------------------------------------------------------ |
| central        | 중심적인                                                     |
| specific       | 특정한 (여기서는 스프링 맞춤 용어라는 뜻으로 쓰인 듯)        |
| particularly   | 특별히                                                       |
| intuitive      | 직관적인                                                     |
| aspect         | 측면, 양상, 면 -> 여기서는 문제를 바라보는 관점이라 쓰였다.  |
| cut across     | 가로지르다.                                                  |
| Modularization | 모듈화<br />모듈이란? : 모듈은 소프트웨어 설계할 때, 기능별로 코드들을 분해하고 추상화하여, 어디든 재사용<br />가능하고, 공유 가능한 수준으로 만들어진 단위를 의미한다. <br />이렇게  코드들을 기능별로 분해하여 모듈로 하나씩 만든다는 것을 의미한다. |
| represent      | ...에 해당한다. (여기서는 대표하다의 뜻이 아닌 해당하다라는 의미로 쓰였다.) |
| model          | ~의 견본, 모형을 만들다. / 만들다.                           |

| English        | Korean                                                       |
| -------------- | ------------------------------------------------------------ |
| chain          | 사슬, 묶다, 일련                                             |
| predicate      | 술부<br />(John went home에서 went home 처럼, 문장 속에서 주어에 대해 진술하는 동사이하 부분 ) |
| match          | ~와 연결시키다. / 일치하다. / 맞먹다. / 대응하다.            |
| behalf         | 이익, 원조, 지지                                             |
| on behalf of ~ | ~을 대신[대표]하여                                           |
| introduce      | (사용할 수 있도록 무언가를) 도입하다.                        |
| corresponding  | (~에) 해당(상응)하는                                         |
| proxy          | 대리 혹은 대리인 -> java에서의 proxy는 대리로 일을 수행하는 클래스를 의미한다. |
| proceed        | ~으로 나아가다. 계속해서 ~~을 하다. 진행하다.                |

| exit            | 나가다. 종료하다. 퇴장하다.                                  |
| --------------- | ------------------------------------------------------------ |
| invocation      | 발동 -> java에서 method invocation이라 적혀있다면 '매소드 호출'을 의미한다. |
| surround        | 둘러싸다. 포위하다.                                          |
| shortcut        | 지름길, 단축하다? <br />(여기서는 동사로 쓰였는데, 명사 의미밖에 찾을 수가 없네요. <br />구글 번역기는 단축하다로 해석하여 이렇게 올립니다. ) |
| responsible     | ~을 책임 지고 있는, ~에 대해 책임이 있는                     |
| least           | 가장 최소의, 가장 적은                                       |
| be better off ~ | ~이 더 좋다                                                  |
|                 |                                                              |
|                 |                                                              |
|                 |                                                              |

