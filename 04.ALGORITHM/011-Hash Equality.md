# 1. 무엇에 대해 알아보나요? 🤔💭

이번에 알아볼 내용은 **Hash 형태의 자료 구조**에서 (ex - HashMap, HashSet 등) **저장된 자료간의 동등성을 어떻게 비교하는지**와 이것을 우리의 프로젝트나 알고리즘 풀이에 맞게 **재정의 하는 방법**에 대해 알아보겠다. 

# 2. 왜 이걸 알아야 하죠? 🤷🏻‍♂️

물론 HashMap의 key나 HashSet의 값으로 원시자료형만을 사용한다면, 해당 내용을 알아볼 필요가 없다. 하지만 만약 HashMap의 key로 우리가 새롭게 만든 클래스의 객체를 사용해야하는 순간이 온다면 해당 내용을 아는 것이 극히 도움이 되고, 문제 해결 시야를 넓혀준다. 
왜냐하면 개발자가 재정의 해주지 않는 이상 **Hash 자료구조는 객체형 자료에 대한 동등성 비교를 하지 못하기** 때문이다. 

## (1) 객체형 Key로는 동등성 비교가 되지 않는 이유

**결론: 객체와 같은 `참조형 변수`간의 동등성을 비교할 경우, Hash 자료구조는 기본적으로 메모리 주소를 이용해 비교한다.  이는 개발자가 원하는 `의미적으로 같은 객체를 동일하다 판단하기`라는 조건을 만족시키지 못한다.**

해당 글에서는 `HashMap`을 기준으로 내용을 설명하겠지만, HashSet과 같은 다른 Hash 자료구조에 통용되는 내용이다. 

### ※ HashMap에서 Key 값에 해당하는 value를 꺼내는 과정 

HashMap의 key-value Entry는 모두 `Hash Bucket` 이라는 저장소에 분류되어 저장된다. 여기서 분류되는 기준은 `HashCode`이다. 즉, 같은 hashCode를 가진 Key들은 같은 hash Bucket에 저장된다. 

![image-20241018180910569](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/011-Hash Equality/image-20241018180910569.png)

a. map.get(Key)를 하면 HashMap은 인수로 들어온 Key의 `HashCode`를 확인하고, 그 hashCode가 속한 Bucket을 조회한다.

![image-20241018180949098](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/011-Hash Equality/image-20241018180949098.png)

b. 만약 bucket안의 값이 2개 이상이면, Key.equals()를 이용하여 인수 Key와 `동등`한 Key값이 있는지 찾는다. 

![image-20241018181032913](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/011-Hash Equality/image-20241018181032913.png)

c. 만약 인수와 동등한 Key가 없다면 Null을 출력하고, 있다면 해당 key의 value를 반환한다.

### ※ 동등성 비교 안되는 이유 

위의 1,2,3 과정을 보면 `(1) hashCode를 활용해 크게 한 번 걸러내기`, `(2) equals()를 활용해 정밀하게 같은 값 찾기`의 순으로 진행되는 것을 알 수 있다. 
 이를 위해서 1차로 Key 값의 hashCode()를 먼저 활용하고 그 후 equals()를 활용한다. 원시 자료형이면 이 둘 다 성공적으로 이루어지지만, `Key로 참조형을 활용했다면`, hashCode는 객체의 `메모리 주소`로 만들어지고, equals()는 비교되는 두 대상이 참조하는  메모리 주소가 같은지로 동등성을 비교하기 때문에, Key마다 메모리 주소가 모두 달라 `동등성 비교 자체가 되지 않는다.` 
 따라서 멤버 변수가 같아서 의미적으로 같은 객체 A,B가 있고 이 둘을 HashMap의 Key로 사용한다면 Key는 A 따로 B 따로 만들어지고 관리되지, 서로 Value 값을 공유하며 갱신하지는 않는다는 소리이다. 이러면 메모리 주소를 따로 저장하여 두지 않는다면 객체 값을 가진 Hash 자료 구조는 활용하지 못한다는 결론이 나온다.

## (2) equals()와 hashCode()의 관계

아까 위에서 설명했듯이 `(1) hashCode를 활용해 크게 한 번 걸러내기`, `(2) equals()를 활용해 정밀하게 같은 값 찾기` 과정으로 간다. 
이 말 뜻은, 

![image-20241018182417255](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/011-Hash Equality/image-20241018182417255.png)

1. equals()가 true인 두 Key A,B는 무조건 같은 HashCode를 가진다.
2. 두 Key C,D가 같은 hashCode를 가진다고 해서 무조건 동등한 객체는 아니다! 

가 성립된다. 

# 3. 어떻게 동등성을 맞춰주죠? 🎯

먼저 `동일하다`와 `동등하다`의 차이를 구분할 필요가 있을 것 같다. 일상생활에서도 두 용어는 비슷한 부류이지만 다른 의미로 사용된다. 
먼저 `동일하다`는 `두 물체 A,B가 완전히 같다.`를 의미한다. 이를 코딩에 대입해보면, `두 객체 혹은 변수가 동일한 메모리 주소를 가진다.` 로 풀이할 수 있다. 둘은 아예 같은 것이란 뜻이다. A,B가 동일한 객체라면  `==`으로 비교해도 true가 나온다.

`동등하다`는 `두 물체 A,B가 같은 자격을 가지고 있다.`를 뜻한다. 이를 코딩에 대입해보면 `두 객체가 다른 메모리 주소를 가지더라도 내용물은 완전히 같다.`를 의미한다. A,B가 동등한 객체라면 `==`으로 비교 시 false가 나오겠지만, 내용물만 비교할 경우 (ex- String의 equals()) true가 나온다. 

## (1) 우리가 동등하다고 생각하는 기준

우리가 생각하는 `두 객체 A,B가 같다`의 의미도 A,B가 동일하다의 의미가 아닌 `두 객체 A,B가 동등하다`의 의미로 쓰인다. 
메모리 주소는 다르더라도, 내용물은 같다는 것이다. 

## (2) hashCode 맞추기

equals()로 두 객체 A,B의 동등성을 판단하려면 먼저 `두 객체 A,B가 같은 Bucket에 존재한다.`가 성립하여야한다. 왜냐하면,  Hash 자료구조는 내부적으로 동등성을 판단하기 전에 hashcode 일치 여부로 동등성 판단 대상을 대거 걸러내기 때문이다. (이것이 Hash 자료구조가 조회에 빠른 이유이기도 하다.)

따라서 아무리 A.equals(B) == true라고 하더라도 A와 B의 hashCode가 달라버리면 말짱 도루묵이다. 
따라서 객체의 hashCode를 만드는 `hashCode()`라는 함수를 `객체의 내용물이 같으면 같은 hash bucket`에 포함되도록 재정의 해줘야 한다. 재정의 예시는 다음과 같다. 

```java
Class Exam1 {
    int x;
    int y;
    
    @Override
    public int hashcode() {
        return Objects.hash(x,y);
    }
}
```

위에서 쓰인 Objects.hash() 함수는 인수로 들어온 값으로 hashCode를 만든다. 인수가 같으면 당연히 같은 hashcode가 나온다. (내용물이 같으면 같은 hashcode가 나온다.)  다음은 `Objects.hash(Object... values)`의 내부인데 궁금하신 분은 참고하라.

```java
public static int hash(Object... values) {	// 인수의 개수가 정해져있기 않기에 몇개든 상관 없다.
    return Arrays.hashCode(values);
}
```

```java

public static int hashCode(Object a[]) { // hashcode를 만드는 로직
    if (a == null)
        return 0;

    int result = 1;

    for (Object element : a)
        result = 31 * result + (element == null ? 0 : element.hashCode());

    return result;
}
```

## (3) equals() 맞추기 

이제 `내용물이 같은 값들을 모두 같은 hash Bucket에 집어넣기`까지는 성공했으므로 `내용물이 같다면 동등하다 판단하기`만 남았다. 
동등성 판단을 위해서는 equals()를 재정의 해줘야 한다. 예시는 다음과 같다

```java
Class Exam {
    int x;
    int y;
    
    @OVerride
    public boolean equals(Object obj) {
        if(this == obj) return true; // 인수와 비교되는 값이 `동일`하다면 true를 바로 반환하여 불필요한 계산 줄이기
        if(obj == null || this.getClass() != obj.getClass()) return false; // 인수가 null 이거나, 비교대상과 타입이 동일하지 않다면 false 반환 
        Exam opponent = (Exam) obj;
        return (this.x == opponent.x && this.y == opponent.y);
    }
}
```

# 4. 풀어보면 좋은 문제📝

https://school.programmers.co.kr/learn/courses/30/lessons/340211

해당문제는 위에서 배운 내용을 활용하면 십분 쉽게 풀 수 있는 문제이다. 