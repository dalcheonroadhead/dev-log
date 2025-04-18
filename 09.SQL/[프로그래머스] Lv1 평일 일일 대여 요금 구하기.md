# 1. 문제 설명 📌

## (1) 링크🔗

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/151136)

## (2) 해설🕵

**`KEY WORD`**: **`ROUND(A, num)`**

- `ROUND(A,num)`일 때, num은 반올림하여 살아있는 소수점 자릿수이다. ROUND(A,1)이라면, 2.x, 3.x 등 1의 자리까지 살아 남는다는 뜻이다. 
- SQL 쿼리문에서는 `=`이 JAVA와 달리 내용 비교이다. 따라서 문자열을 비교할 때도 써도 된다.

# 3. 구현 코드🔎

```sql
SELECT
ROUND(AVG(DAILY_FEE), 0) AS 'AVERAGE_FEE'
FROM CAR_RENTAL_COMPANY_CAR
WHERE CAR_TYPE = 'SUV'
```

# 4. DRILING 🎯

## (1) `=` 내용 비교에 대해

- JAVA와 달리 MySQL은 `A = B`일 시, 값 기반 비교가 수행됨
- MySQL에서는 메모리 주소를 활용한 객체 개념이 없으므로, `A = B`를 문자열에 썼을 때, 작동하지 않을까봐 노심초사 하지 않아도 됨.
- 단 COLLATION 설정에 영향을 받을 수 있다.

```sql
-- 대소문자 구분 없이 비교 (기본 collation)
SELECT 'abc' = 'ABC';  -- 결과: 1 (true)

-- 대소문자 구분 collation 사용
SELECT 'abc' COLLATE utf8mb4_bin = 'ABC';  -- 결과: 0 (false)
```

### A. COLLATION 이란?

**Collation**은 어떤 문자셋(CharSet)을 기준으로 문자를 정렬하고 비교할지에 대한 정의 이다. 이것에 따라 **`=` 동등 비교에 따른 결과 가 달라진다.** 예를 들면,

- default Collation인 `utf8_general_cl`의 경우 대소문자 비교를 하지 않기 때문에 다음과 같은 문장이 참이다.

```sql
SELECT `abc` = `ABC`;
```

- 반면 Collation이 `utf8-bin`이라면, 대소문자 구분이 존재한다. 따라서 위의 문장이 거짓이 된다. 

### B. 그럼 `=` 동등비교를 코딩 테스트에서 써도 되는가? 

코딩 테스트의 MySQL 실행기는 아마 Collation에 따로 설정을 가하지 않았을 것임으로, `utf8mb4_general_cl`이 적용되어 있을 것이다. 따라서 괜찮을 것 같다.





>  이모지 모음: 🤔, ➜ ✨ 0️⃣1️⃣2️⃣3️⃣4️⃣5️⃣6️⃣7️⃣8️⃣9️⃣🔟

