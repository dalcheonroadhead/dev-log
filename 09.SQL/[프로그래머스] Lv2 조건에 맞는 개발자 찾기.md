# 1. 문제 설명 📌

## (1) 링크🔗

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/276034)

## (2) 해설🕵

**`KEY WORD`**: **`Bit 연산`**

개발자의 정보(`DEVELOPERS`)와 개발 언어의 스킬 코드(`SKILLCODES`)가 적힌 두 테이블이 존재한다.
`DEVELOPERS`에서는 개발자가 쓰는 언어에 대해 SKILL_CODE라는 컬럼으로 나타내는데, 이 코드의 INTEGER 값은 2진수 비트로 나타냈을 때, **2진수 자릿값 하나당 해당 개발자가 사용할 수 있는 언어 CODE의 값**이다.
  예를 들어, 어떤 개발자 B의 `SKILL_CODE` 컬럼의 값이 400이라 해보자. 그리고 개발언어의 SKILLCODES 테이블이 다음과 같다고 해보자.

| NAME       | CATEGORY  | CODE |
| ---------- | --------- | ---- |
| C++        | Back End  | 4    |
| JavaScript | Front End | 16   |
| Java       | Back End  | 128  |
| Python     | Back End  | 256  |

B의 `SKILL_CODE` 값을 2진수로 표현해보면, (b'110010000')이다. 즉 B는 코드가 256(=b'100000000')인 Python과 128(=b'10000000')인 Java, 16(=b'10000')인 JavaScript를 쓸 수 있다는 것이다. 

# 2. 생각의 흐름

이걸 어떻게 구해야 할까? 
MySQL에서 쓰는 것은 조금 어색하지만 **비트 연산자를 활용**해야 한다. 
**`&`** 연산자를 사용하면, 둘 다 특정 자릿값의 값이 1일 때만 1로 나온다.  

![image-20250417122226973](https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-04/image-20250417122226973.png)

문제 풀이는 다음과 같아 진다.

- Where문으로 문제에서 원하는 조건인 'C#'과 'Python'에 대한 코드로 `SKILLCODES` 테이블을 추린다.
- 모든 개발자에 대해 C#과 Python 코드와 비트 연산을 한다. (카티시안 곱 ($O(N*2)$))
- 비트 연산 결과가 0보다 큰 테이블만 살려둔다.
- 만약 한 명의 개발자가 'C#'과 'Python' 둘 다 쓸 수 있으면, 카티시안 곱을 했기 때문에 똑같은 개발자가 두 번 중복되어 출력될 것이다. 이를 방지하기 위해 `DISTINCT`를 사용한다.



# 3. 구현 코드 🔎

```sql
SELECT
DISTINCT
d.ID,
d.EMAIL,
d.FIRST_NAME,
d.LAST_NAME
FROM DEVELOPERS d, SKILLCODES s
WHERE s.NAME IN ('C#', 'Python')
AND d.SKILL_CODE & s.CODE > 0
ORDER BY d.ID
ASC;
```



# 4. DRILING 🎯



>  이모지 모음: 🤔, ➜ ✨ 0️⃣1️⃣2️⃣3️⃣4️⃣5️⃣6️⃣7️⃣8️⃣9️⃣🔟