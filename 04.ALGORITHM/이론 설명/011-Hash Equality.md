# 0. 알아볼 것

![image-20241231135521574](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/011-Hash Equality/image-20241231135521574.png)

# 1. Hash Map 이란?

`<KEY, VALUE>` 형태의 데이터 쌍으로 이루어진 자료구조

KEY를 활용해 HashMap에 VALUE을 저장, 삭제, 조회 하는데 평균 `O(1)`의 시간이 든다.

<KEY, VALUE> 쌍을 `ENTRY`라고 부른다.

KEY 값은 중복될 수 없고, VALUE 값은 KEY 값이 다르다면 중복저장이 가능하다.

# 2. HashMap 내부 구조

![image-20241231140104752](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/011-Hash Equality/image-20241231140104752.png)

HashMap은 크게 `HASH 함수`와 `Hash Bucket`으로 이루어져 있다. 

- **Hash Bucket**은 값을 저장하는 장소로 Array로 구현되어 있다. 
- **Hash 함수**는 자료구조에 저장될 입력 값 마다의 `Hash 값`을 반환한다. 해당 Hash 값은 **Hash Bucket의 Index**이다. 따라서 Hash 함수는 입력 값이 저장될 위치를 알려주는 **방향키 역할**을 한다고 생각하면 되겠다.

이제 실제 입력값이 들어왔다고 해보자.

# 3. Put의 작동원리

![image-20241231140615504](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/011-Hash Equality/image-20241231140615504.png)

### a. Hash 함수로 Entry가 저장될 위치 찾기 

**Hash함수**에는 뭐가 들어갈까? 위에서 살펴본 HashMap의 정의를 보면 항상 KEY를 활용해, VALUE에 관한 조치를 하였다. 마찬가지로, 값이 저장될 위치를 찾는 것도 **KEY를 활용**한다. 그렇다면 Hash 함수에 바로 KEY를 입력으로 넣으면 될까? 아니다. Hash 함수에 입력값은 `hashCode`만 가능하다. 따라서 **KEY를 Hash Code로 변환하는 작업**이 전처리로 필요하다.

![image-20241231141417141](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/011-Hash Equality/image-20241231141417141.png)

이를 가능하게 해주는 함수가 HashMap의 내부 함수인 **hashcode()**이다. 해당 함수는 재정의 하지 않으면 **KEY값의 주소값**을 입력으로 받아, 내부 계산을 거쳐 정수로 변환시킨다. 
(HashMAP의 KEY, VALUE는 참조 자료형만 쓸 수 있기 때문에, 주소값이 무조건 있음) 

이제 hashcode() 함수의 반환값들은 Hash 함수를 거쳐, 해당 입력값이 저장될 bucket의 index(hash 값)로 변환될 것이다. 

### b. Hash 값이 일치하는 Hash Bucket에 값 저장

![image-20241231141726591](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/011-Hash Equality/image-20241231141726591.png)

만약 **서로 다른 입력값이 같은 Hash 값을 반환하는 경우** Bucket에 어떻게 저장할 것인가? 위에서 살펴봤다시피, Hash Bucket은 Array로 구현되어 있어, 값을 하나밖에 저장하지 못한다. 이렇게, 서로 다른 Entry값이 동일한 Hash 값을 반환하는 경우를 **`HASH 충돌`**이라고 한다. Hash 충돌을 다루는 방법에는 2가지가 있다.

- **방법 1. Open Addressing**: 해시 충돌 발생시 해시값 새로 구해서 해시 충돌을 우회
- **방법 2. Separate Chaining**: 해시 충돌 발생 시 HashBucket의 Value 부분을 **Linked List** 형태로 변환시켜, 동일한 해시값을 갖는 입력값은 동일한 Bucket index에 모두 저장

java에서는 방법2를 활용하고 있다. java8 이후 부터는 LinkedList 길이가 특정 임계값을 초과하면 이진트리로 변환하여, 해시값이 중복된 데이터들 사이에서 원하는 값을 찾아내는 시간을 O(n)에서 O(logN)으로 단축시켰다. 

따라서 예시가 만약 다음 그림처럼 해시 충돌이 났다면 Value 부분이 Linked List로 변환되어 같은 위치에 값들이 저장될 것이다.

![image-20241231142546061](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/011-Hash Equality/image-20241231142546061.png)

# 4. GET의 작동원리

이제는 KEY값을 통해 그에 대응되는 VALUE 값을 어떻게 찾아내는지 알아보자. 

### a. KEY 값을 Hash 값으로 바꾸기

PUT에서와 마찬가지로 `hashcode()`를 활용해 Key ➜ hashcode로 변환하고, 다시 해시함수를 활용해 hashcode ➜ hash 값으로 변환한다.

### b. Hash Bucket에서 hash 값에 대응하는 value 얻기

해시값은 Hash Bucket의 index임으로, 그에 대응하는 value를 찾아내서 반환하면 된다. 하지만 Hash 충돌이 일어난 경우라면 어떻게 할 것인가? 이때는 HashMap 내부의 **equals()** 함수를 활용한다. equals를 통해 입력으로 들어온 KEY 값과 BUCKET에 저장된 데이터 쌍 중 KEY 값 간의 동등성을 비교하는 것이다.

만약 equals()에 대한 재정의를 하지 않았다면, Object의 equals()를 그대로 사용하여 참조 자료형의 주소값끼리 서로 일치하는지를 확인하게 될 것이다.

# 5. Custom 객체를 KEY로 사용하지 못하는 이유?

> HashMap이 **의미적으로 동등한 KEY** 값을 동등하다고 인식하지 못한다.

동등하다고 인식하지 못하는 이유는 PUT, GET 설명에서 다 나왔다.

다시 살펴보면

- **PUT을 할 때**, 값이 저장될 위치가 되는 Hash값을 만드는 재료인 `hashcode()`를 참조자료형의 주소로 만든다. 그러면 의미적으로 동등하든 말든, 모든 입력값의 HashCode가 달라질 것이다. 
- **GET을 할 때** 동일한 Hash값에 저장된 값들 사이에서 현재 KEY와 **동등한** 값을 찾기 위해서 `equals()`를 활용한다. 하지만 비교하는 두 KEY의 주소값이 일치하는지 확인한다면, 의미적으로 동등하든 말든 모든 equals()가 false를 반환할 것이다.



첫 번째 문제 때문에, 의미적으로 동등한 KEY 값이 HashMap에 중복 저장된다. 이는 HashMap 자료구조의 정의를 해친다.
두 번째 문제 때문에, 의미적으로 동등한 KEY 값을 입력으로 받아도 원하는 VALUE를 조회하지 못하게 된다.

위와 같은 이유로 **밑 빠진 독에 물 붓기**가 된다.

# 6. 그래도 Custom 객체를 KEY로 사용하려면?

문제가 되었던 `hashcode()`와 `equals()`를 **동등한 값은 동등하다고 인식할 수 있게** 바꿔주면 된다.

일례로 우리가 HashMap에 자주 사용하는 `Wrapper Class`는 hashcode()와 equals()를 다음과 같이 재정의하고 있다. 대표적으로 `Integer Class`를 보면

```java
@Override
public int hashCode() {
    return value; // value는 Integer 객체가 저장하고 있는 int 값
}
```

`hashcode()`는 그냥 Integer 객체에 저장된 값을 그대로 반환하도록 해서, 같은 값은 같은 Hash Bucket 위치에 저장되도록 한 것이다. 이 때문에 우리는 HashMap 사용 시 의미적으로 동등한 Key값이 중복 저장 되지 않는 것이다.

```java
@Override
public boolean equals(Object obj) {
    if (this == obj) {
        return true; // 같은 객체를 참조하는 경우
    }
    if (obj instanceof Integer) {
        return value == ((Integer) obj).value; // 내부 값을 비교
    }
    return false; // 타입이 다르거나 값이 다르면 false
}

```

`equals()`는 조금 복잡한데, 입력으로 Object 객체가 들어와서 비교하는 대상끼리 동일 타입인지 체크하는 코드가 필요해서 그렇다. 그 다음은 **의미적으로 같은지**를 체크하는 코드이다.