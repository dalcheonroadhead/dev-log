# 1. 문제 설명 📌

## (1) 링크🔗

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/151138)

## (2) 해설🕵

**`KEY WORD`**: **`DATEDIFF()`, `CASE WHEN THEN END 문`**

`DATEDIFF(끝 시간, 시작 시간)`은 끝 시간에서 시작시간을 빼서, 둘의 날짜 차이를 INTEGER로 반환하는 함수이다. 이를 활용하여 문제를 풀어야 한다. 

또한 CASE-WHEN-THEN...END 문을 활용해 30일 이상은 장기 대여 아니면 단기 대여로 구분해야 한다. 
이때 주의해야 할 점이 **당일 대여, 당일 반납해도 하루로 친다**는 것이다. 그것을 토대로 계산해보면, 실질적으로 날짜는 29일 지난 시점이 +1 더해서 30일 동안 대여를 한 시점이 된다.

# 2. 구현 코드🔎

```sql
SELECT
HISTORY_ID,
CAR_ID,
DATE(START_DATE),
DATE_FORMAT(END_DATE, '%Y-%m-%d') AS END_DATE,
(CASE
 WHEN DATEDIFF(END_DATE, START_DATE) >= 29 THEN '장기 대여'
 ELSE '단기 대여'
 END
) AS 'RENT_TYPE'
FROM CAR_RENTAL_COMPANY_RENTAL_HISTORY
WHERE DATE_FORMAT(START_DATE, '%Y-%m') = '2022-09'
ORDER BY HISTORY_ID
DESC;
```



# 3. DRILING 🎯

## (1) DATE DIFF 관련 정리

[설명 링크]()

## (2) CASE - WHEN - THEN ... END 문에 대한 정리

[설명 링크]()

>  이모지 모음: 🤔, ➜ ✨ 0️⃣1️⃣2️⃣3️⃣4️⃣5️⃣6️⃣7️⃣8️⃣9️⃣🔟

