# 1. 정의

`A = Bq + R일 때, A와 B의 최대공약수 G는 B와 R의 G와 같다.` 를 활용하여, `연쇄`적인 나눗셈으로 A,B의 최대공약수를 구하는 방법을 말한다. 

# 2. 원리 

정의에서 `연쇄`적인 나눗셈이라고 한 이유는 다음과 같다. 
```java
1. A = Bq + R 
2. B = Rq + R'
3. R = R`q + 0 
```

간단하게 3번안에 끝나는 예시를 보자. 

1. A,B의 G는 B,R의 G와 같음.
2. B,R의 G는 R,R'의 G와 같음.
3. 마지막 식에서 나머지가 없으므로, R'는 R의 약수임. R과 R'의 최대 공약수는 당연하게도 R'임. 
4. 이제 연어처럼 3 -> 1로 거슬러 올라가면 종국에 A,B의 최대공약수는 R'랑 같음.

# 3. 증명

## (1) A와 B와 R이 같은 G를 공유하는가? 

````java
A = Ga, B = Gb (G가 최대공약수임으로, a와b는 서로소)

A = Bq + R 임으로, 
R = G(a+bq)이다. 따라서 같은 G를 공유한다.
````

## (2) B와 R의 최대공약수도 G가 맞는가? 

B와 R의 최대공약수도 G려면, 위의 식에서 b와 (a+bq)도 서로소여야 한다. 
G가 최대공약수이면, B와 R을 동시에 나눌 수 있는 수는 모두 G에 합해져 있으므로 나머지인 b와 a+bq는 절대로 서로 나누는 수가 공유되어서는 안되기 때문이다. 

귀류법을 사용하겠다. 
귀류법은 결론을 부정했을 시 생기는 모순으로 원명제가 참임을 증명하는 방식이다. 
 G가 공약수가 아니라서 b와 a+bq 사이야 공약수가 m이 있다고 가정해보겠다. 

```java
b = mk
a+bq = mk'
```

그러면 다음과 같은 모순이 생긴다.

```java
a+bq = a+mkq = mk'
a = mk'-mkq 
a = m(k'-kq)
```

맨 위에서 a와 b는 서로소라고 했다. 
만약 m >=2 이면 해당 전제와 모순된다. m = 1 이면, b와 a+bq는 모순된다. 

따라서 b와 a+bq는 서로소이고, B와 R은 G를 최대공약수로 공유한다. 
# GCD와 LCM의 관계

A와 B의 최대공약수를 `G`, 최소공배수를 'L'이라 할 때, 다음 식이 성립한다. 

```java
(A*B)/G = L
```

## WHY? 

![image-20240924164612412](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/유클리드 호제법 증명/image-20240924164612412.png)![image-20240924164743480](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/유클리드 호제법 증명/image-20240924164743480.png)![image-20240924165049979](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/유클리드 호제법 증명/image-20240924165049979.png)