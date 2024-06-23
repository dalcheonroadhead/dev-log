# 1. Moduler란? 

$$
A/B = Q + R
$$

이면 Q가 몫이고, R을 나머지라고 한다. 이를 modluer 연산자를 통해 표현하면 
$$
A mod B = R
$$
이다. moduler는 나누었을 때 나머지를 구하는 연산자이다. 

# 2. 모듈러 연산의 덧셈 뻴셈 법칙

$$
(A + B) mod C = (AmodC + BmodC)mod C
$$

- A = 14, B = 17, C = 5라고 해보자.
- LHS(식의 왼쪽 변) = (14+17)mod 5 = 31 mod 5 = 1
- RHS(식의 오른쪽 변) = (14mod5 + 17mod5 ) mod5 = (4+2)mod5 = 1

뺄셈도 마찬가지 이다. 

# 3. 모듈러 연산의 곱셈 법칙 

$$
(A*B)modC = ((AmodC)*BmodC)modC
$$

- A = 4, B = 7, C = 6 이라고 할 때, 
- LHS = (4*7)mod6 = 4
- RHS = ((4mod6)*(7mod6))mod6 = (4 * 1)mod 6 = 4