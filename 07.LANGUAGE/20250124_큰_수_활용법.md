# 0. 들어가며

**개발을 하거나,** 코딩테스트에서 `정수론` 관련 문제를 만나면 한 번씩 주어지는 입력의 크기가 Long형을 초월할 수 있을 때가 간혹 있다. 먼저 코테에서 자주 쓰이는 자료형인 `int`와 `Long`형의 최대, 최소값을 알아보자.

| 정수형 타입 | 데이터 표현 범위                                             |
| ----------- | ------------------------------------------------------------ |
| **int**     | -2,147,483,648 ~ 2,147,483,647 ($10^{10}$)                   |
| **long**    | -9,223,372,036,854,775,808 ~ 9,223,372,036,854,775,807 ($10^{18}$) |

`long`이 정말 많은 수의 범위를 커버할 수 있음에도 불구하고 백준의 [거의 소수](https://www.acmicpc.net/problem/1456)같은 문제는 이러한 자료형을 뛰어넘는다. 이때의 대처법 2가지를 설명하겠다.

# 1. 실수 자료형인 `Double` 사용

 `Double`은 64bit 부동 소수점으로 표현된 자료형으로 대략 $±10^{308}$ 까지 표현이 가능하다. 하지만 `지수 표기법`을 활용해 표현하므로 **입력을 정확한 값 표현이 아니라 근사적으로 나타내는 것에 불과하다.**

만약 계산의 목적이 대소 비교라면 활용해볼만 하지만, 정확한 값의 표현이면 원하는 답과 오차가 있을 수 있다.

# 2. `BigInteger` 클래스 활용

 `BigInteger` 클래스는 **`임의 정밀도`** 방식을 활용해, **표현해야할 숫자의 크기나 정밀도 만큼 메모리를 동적으로 할당** 받기 때문에 아무리 큰 수라도 표현할 수 있다. (우리가 쓰는 원시 자료형들은 모두 `고정 정밀도`로 쓸 수 있는 메모리의 크기가 1~8byte 내로 정해져 있다.)

**주의해야할 점**은 `BigInteger` 객체로 표현한 수로 사칙 연산을 하려면 **기호 대신 `BigInteger` 내부의 함수를 사용**해야 한다.

## (1) `BigInteger` 객체 선언 및 초기화

```java
BigInteger bigNum = new BigInteger("표현할 수를 String으로 입력");
```

## (2) 덧셈(`.add()`)

```java
BigInteger num1 = new BigInteger("9223372036854775808");
BigInteger num2 = new BigInteger("12345678901234567890");
BigInteger sum = num1.add(num2);
System.out.println("덧셈 결과: " + sum);
```

## (3) 뺄셈(`.subtract()`)

```java
BigInteger difference = num2.subtract(num1);
System.out.println("뺄셈 결과: " + difference);
```

## (4) 곱셈(`.multiply()`)

```java
BigInteger product = num1.multiply(num2);
System.out.println("곱셈 결과: " + product);
```

## (5) 나눗셈(`.divide()`)

```java
BigInteger quotient = num2.divide(num1);
System.out.println("나눗셈 결과: " + quotient);
```

## (6) 나머지 연산 (`.remainder()`)

```java
BigInteger remainder = num2.remainder(num1);
System.out.println("나머지: " + remainder);
```

## (7) 비교 연산(`.compareTo(BigInteger Other)`)

- 반환값 `0`: 둘이 같음
- 반환값 `양수`: 현재 값이 더 큼
- 반환값 `음수`: 현재 값이 더 작음 (인수로 들어온 값이 더 큼) 

```java
int comparison = num1.compareTo(num2);
if (comparison == 0) 		System.out.println("num1과 num2는 같음");
else if (comparison > 0) 	System.out.println("num1이 num2보다 큼");
else 						System.out.println("num1이 num2보다 작음");
```

## (8) 주의점 ⚠️

- **Big Integer는 `불변 객체`**: 위 매소드들을 보면, 만약 `A + B`를 한다고 하면, `A.add(B)`로 표현하기에, *'계산한 값이 A에 저장되는 것 아닌가?'* 하는 생각이 들 수 있다. 하지만 **모든 사칙연산의 결과는 새로운 Big Integer 객체를 생성하여 반환**하고, 원본 값에 영향을 주지 않는다!
- **메모리 사용량이 큼**: 매 사칙연산마다 새로운 객체를 생성하며, BigInteger를 하나 표현하는데도 많은 메모리가 든다. 따라서 `BigInteger`는 적재적소의 상황에 필요할 때만 사용해야 한다.