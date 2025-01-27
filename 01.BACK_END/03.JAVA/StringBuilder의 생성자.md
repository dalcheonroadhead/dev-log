# 0. 무엇을 정리하나요

필자는 문자열을 뒤집는 기술이 필요할 때, `StringBuilder`클래스의 `reverse()`를 자주 활용한다. 근데, 펠린드롬 수를 빠르게 구하기 위해, 다음과 같이 문제를 푸니 틀리는 것 아닌가! 

```java 
StringBuilder straight 	= new StringBuilder(num);
StringBuilder reverse 	= new StringBuilder(num).reverse();
```

콘솔에 찍어보니 **두 객체 모두에 어떠한 입력값도 들어가지 않았다!** 코테에서 이러한 에러를 겪었으면 적잖이 당황했을 것 같다. 오늘은 필자와 같이 당황할 사람들을 위해, **StringBuilder의 생성자에 `int i`를 넣는 것과 `String str`을 넣는 것의 차이**를 알아보려 한다.

# 1. `new StringBuider(int i)`

해당 생성자는 **StringBuilder 객체의 초기 버퍼 크기**를 설정하는 생성자이다. 

## (1) 버퍼란? 

**StringBuilder 내부에 문자열을 저장하기 위한 배열**을 의미한다. 물론 StringBuilder는 가변적으로 문자열을 저장하기 때문에 해당 배열은 입력이 늘어남에 따라 크기가 늘어난다. 하지만 여기서 *늘어난다* 라는 표현은 기존의 문자열 배열이 크기를 늘린다는 것이 아니라 **크기가 더 큰 배열을 선언하여 거기에 배열의 내용물을 옮기는 식으로** 크기를 늘린다.

  따라서 적절한 초기 버퍼 비용 설정은 **배열 복사 비용을 줄여준다.** 하지만 그렇다고 너무 크게 버퍼를 설정하면 메모리 낭비로 이어지므로, **적절한 크기**의 버퍼 설정이 중요하다.

# 2. `new StringBuilder(String str)`

문자열이 인수로 들어오는 해당 생성자는 말 그대로 **'StringBuilder의 내용물로 해당 문자열을 넣겠다'** 는 뜻이다. 

# 3. 결론

따라서 `int` 형을 생성자에 내용물로 넣고 싶다면, 그전에 **문자열로 바꾸는 작업이 필수**이다. 예) `new StringBuilder(Integer.toString(i))` (i = int)