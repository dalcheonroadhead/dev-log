# 1. JSON 이란 무엇인가요? 💡

## (1) 정의 

JSON이란 JavaScript Object Notation의 약자로 **Javascript 객체 형태**로 되어있는 **데이터 교환 양식**이다. 

`JS 객체의 형태` :  javascript 객체는 **(key: value)** 형태의 값들이 나열된 구조이다. java에서 HashMap과 유사하다고 볼 수 있다. js에서는 따로 변수에 대한 타입 선언이 필요 없기 때문에 객체 문법이 비교적 간편하다. 

## (2) 특징

 ### a. 언어 독립적인 양식이다.

분명 JS 객체 형태를 기반으로 만들어진 양식이지만, javascript 뿐만 아니라 대다수의 언어, 그 언어 기반 프레임워크에서 활용할 수 있다. 

### b. 상태를 교환한다.

JSON을 통해 주고받는 데이터란 특정 객체 혹은 값들의 `상태`이다. `기능`을 의미하는 Method는 JSON을 통해 주고 받을 수 없다. 

### c. JS 객체와의 차이점 

생김새가 같아서 JS 객체와 JSON 자체를 동일시 할 수 있지만, 둘은 미세한 차이가 있다. JSON은 텍스트 기반의 데이터 교환 양식이라 **JSON의 데이터는 key값이 String 형태여야** 한다.
```javascript
// javascript object
{
  first: 'Bob',
  last: 'Smith'
}
```

```JSON
{
    'first': 'Bob',
    'last': 'Smith'
}
```

# 2. 왜 쓰는 거죠? 🤷‍♂️

 `결론`: 다른 언어 기반의 client와 sever 간에 소통을 위해, 범서버적인 소통을 위해 써야함
  데이터 교환을 소통으로 본다면, JSON은 `영어`의 위치에 있다. JSON의 언어 독립적인 특성 때문에 다양한 프레임워크에서 활용하기 쉬웠고, 그 떄문에 현재 대다수의 프레임워크에서 기본적으로 활용하는 표준 데이터 교환 양식이 되었다. 
  프로젝트를 진행하면 당연하게도 client와 server의 언어가 다를 수 있다. 이때 둘 간의 소통을 원활하게 하기 위해서는 JSON이 필수이다. 또한 서비스가 방대해져서 여러개의 서버를 두고 있고, 그 서버간의 구현 언어가 다른 경우, Open API 데이터를 활용해야 하는 경우 모두 다른 언어간의 소통을 위해서는 **JSON이 필수**적이다. 마치 모두 다른 언어를 쓰지만 UN 회의장에 가면 모두가 영어를 쓰는 것과 같은 이치이다.

![image-20241105165132718](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/JSON 직렬화/image-20241105165132718.png)

![image-20241105165227202](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/JSON 직렬화/image-20241105165227202.png)

# 3. JSON은 어떻게 쓰이는 거죠? 🔍

아까 개념 부분에서 살펴봤듯이 JSON은 JS object와는 다른 존재이고, 같은 구조이지만, 내용물에는 미세한 차이가 있었다. 따라서 JSON을 바로 받아서 JS object 처럼 활용하지 못하고, **JSON ➜ JS object 로 변환해서 프로젝트 내에서 활용**해야 한다. 같은 문법 구조인 JS 기반 프레임워크에서도 이러한 과정을 거쳐야 하는데, 다른 프레임워크에서는 오죽하겠는가. 다음은 JSON을 활용하는 구조와 그 원리에 대해 설명하겠다.

## (1) 대략적인 아키텍쳐

필자가 활용하는 주 프레임워크가 Spring boot라서 이를 기반으로 설명하겠다. 

### a. JSON을 활용해 데이터를 어플리케이션 외부로 전송

이때 객체를 JSON으로 변환하는 작업을 `직렬화`라고 한다.

![image-20241105171114506](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/JSON 직렬화/image-20241105171114506.png)

### b. 외부에서 들어온 JSON 데이터를 Spring 내부에서 활용할 수 있게 변환

이때 JSON을 Java 객체로 변환하는 작업을 `역직렬화`라고 한다.

![image-20241105171250094](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/JSON 직렬화/image-20241105171250094.png)

## (2) 직렬화는 어떻게 이루어지나요? 

![image-20241105172540836](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/JSON 직렬화/image-20241105172540836.png)

### -. 들어가기 앞서

Java에서 직렬화 혹은 역직렬화를 사용하기 위해서는 jackson 라이브러리 나 gson 라이브러리를 활용해야 한다. 우리는 해당 라이브러리의 내부구조가 어떻게 이루어졌는지 알아보겠다.

### a. Reflection API 활용 

java에 존재하는 Reflection API는 구체적인 클래스 타입을 사전에 알지 못해도, 해당 클래스를 구현한 객체를 활용해 역으로 해당 클래스 내 멤버 변수와 매서드, 각 변수들의 타입 등 데이터와 메타데이터에 접근할 수 있도록 해주는 API이다. 
private, protected 와 같은 접근 제어자가 붙은 변수도 Reflection API를 활용하면 접근할 수 있다. 

위의 라이브러리를 활용하면, 해당 라이브러리가 자동으로 대상이 되는 객체에 Reflection API를 적용시켜 객체 내의 필드와 값들을 파악한다.

### b. Key 이름 추출, Key 별 값 읽기

Reflection API를 활용해 객체의 모체가 되는 클래스의 멤버 변수 이름을 모두 읽는다.
만약 `private String name`이란 멤버 변수가 있으면 `"name"`으로 변환하여 추출하는 것이다. 이후 해당 key와 대응되는 값(value) 또한 읽는다. 

### c. Value의 타입 처리 

JSON은 JS 문법 구조라서 Java의 타입과 JSON의 타입은 서로 차이가 있다. 이때 jackson(혹은 gson) 라이브러리는 조우하는 java 객체의 멤버 변수 타입을 다음과 같이 변환시킨다. 

- `Primitive Type 이나 Wrapper Type` : (int,Integer,float,double, Double) ➜ Number, 
  ex) java에서 `int age = 30` 이었으면 JSON에서는 `"age": 30` 이 됨.
- `String 타입`: js에도 string 타입이 있기에 string 타입 그대로 가져감. 
  ex) `String name = "전수민"` ➜ `"name": "전수민"`
- `객체 및 배열` : 이것도 객체는 객체, 배열은 배열 그대로 간다. 
  ex) `List<String> hobbies = Arrays.asList("Reading", "Traveling")` ➜`"hobbies": ["Reading", "Traveling"]`

 이러한 과정을 거쳐 직렬화가 된다. 

### d. 직렬화 코드

대략적인 직렬화 코드는 다음과 같다. 
```java
class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

위와 같은 클래스의 객체를 만들어서 직렬화 시키는 예를 보자. 
```java
import com.fasterxml.jackson.databind.ObjectMapper;

public class JsonSerializeExample {
    public static void main(String[] args) {
        // Java 객체 생성
        Person person = new Person("Alice", 30);

        // ObjectMapper 객체 생성
        ObjectMapper objectMapper = new ObjectMapper();

        try {
            // 객체를 JSON 문자열로 직렬화
            String jsonString = objectMapper.writeValueAsString(person);
            System.out.println("JSON String: " + jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## (3) 역직렬화는 어떻게 이루어지나요? 

![image-20241105220838968](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/JSON 직렬화/image-20241105220838968.png)

역직렬화는 JSON 파일을 프로그램 내에서 쓸 수 있는 하나의 구조로 정형화 하는 것이다. Java에서는 객체가 될 것이고 python에서는 딕셔너리가 될 것이다.

### a. JSON 파싱 

위의 직렬화 과정을 보면 우리는 Reflection API를 활용해 객체를 먼저 해체하였다. 변환에 앞서 해체가 필요했던 이유는 변환하고자 하는 대상을 구분지어 파악하고 원하는 데이터를 뽑아내기 위해서 였다. 현 JSON 파싱도 그와 같은 역할을 하는 것이라 보면 된다. 
`JSON Parsing`은 (`{`, `}`, `[`,`]`, `,`,`:` )를 기준으로 JSON 파일을 해체하여 객체의 시작과 끝 파악, key와 value 구분 등을 하는 과정이다. 

### b. 기본 객체 생성 및 필드 매핑 

우리가 JSON을 변환시켜 만들고자 하는 객체를 미리 `빈 생성자`를 활용해 만든다. (이때 빈 생성자가 미리 정의 되어 있지 않다면, 역직렬화에 활용하는 라이브러리에 따라 오류가 날 수 있다.) 그리고 JSON의 key와 객체의 필드(멤버 변수)를 매핑한다. 매핑한다는 것은 서로 관계 있는 것으로 연결짓는다는 의미이다. 객체의 필드를 ReflectionAPI로 전부 파악하고, 그 중 JSON의 key와 이름이 일치하는 필드를 확인해, 같은 이름의 key의 value를 해당 필드의 값으로 넣을 준비를 한다. 

### c. value 타입 변환 및 대입

이제 이름이 같은 JSON의 key와 객체의 field를 연관지었으니, key에 해당하는 value를 연관된 field의 값으로 대입하는 작업만이 남았다. 이를 위해서는 아까 직렬화 시 했던 것의 반대로 타입을 변환한다. 만약 JSON의 데이터 타입으로 객체의 해당 field의 값을 도저히 만들어낼 수 없으면 변환 예외가 발생한다. 
(만들어낼 수 없는 경우: `{"A" : true}` ➜ `{public int A}` ) 

### d. 예제

다음 예제는 jackson 라이브러리를 이용하여 JSON 값을 java 객체로 변환하는 로직이다. Class는 위에서도 한 번 사용한 Person을 그대로 활용하였다. 

```java
public class JsonDeserializeExample {
    public static void main(String[] args) {
        String jsonString = "{\"name\": \"Alice\", \"age\": 30}";

        ObjectMapper objectMapper = new ObjectMapper();

        try {
            // JSON 문자열을 Java 객체로 역직렬화
            Person person = objectMapper.readValue(jsonString, Person.class);
            System.out.println("Deserialized Person: " + person);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```



## (5) Spring Boot에서 @RestController 나 @ResponseBody로 API 구현 시, API 응답을 객체로 반환 했는데 왜 반환 결과는 JSON으로 나오나요? 

![image-20241105224011355](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/JSON 직렬화/image-20241105224011355.png)

그건 바로 HttpMessageConverter 덕분이다. `@RestController` = `@Controller` + `@ResponseBody`이다. 이때 `@ResponseBody` 어노테이션을 쓰면 기존 MVC 패턴의 Sever Side Rendering이 아니라 Client Side Rendering을 하고 서버는 API 서버가 되겠다는 것을 선언하는 것과 같은 말임으로, 기존에 사용하던 `ViewResolver` 대신 `HttpMessageConverter`를 쓴다. 해당 인터페이스는 Http를 통해 메세지를 주고 받을 수 있도록 객체를 상황에 맞게 변환하는 규칙을 담은 인터페이스이다. 
  해당 인터페이스의 구현체는 크게 `StringHttpMessageConverter`와 `MappingJackson2HttpMessageConverter`가 있는데, 전자는 문자열 반환값을 JSON으로 변환하기 위한 용도이고, 후자는 객체형태의 반환값을 JSON으로 변환하기 위한 용도이다.
  **결론:** 여튼 `@RestController`를 활용하여 api로직을 작성하면 객체로 값을 반환해도, 해당 객체값이 HttpMessageConverter를 한번 거쳐서 JSON으로 자동 변환되어 클라이언트에게 간다는 것을 알면된다.   