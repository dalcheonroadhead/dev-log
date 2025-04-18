# 1. 문제 설명 📌

## (1) 링크🔗

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/157343)

## (2) 해설🕵

**`KEY WORD`**: **`LIKE`, `REGEXP`** 

- OPTIONS란 카테고리를 또 정규화 안해서 한 컬럼에 여러 개의 값들이 한 줄의 문자열로 들어있는 상태
- 문자열 중에서 '네비게이션'이 포함되어 있는 녀석을 찾아야 함.
- 이걸 하는 방법에는 `LIKE` 활용과, `REGEXP` 활용 2가지로 나뉨

# 2. 구현 코드🔎

## A. LIKE 활용 쿼리문

```sql
SELECT *
FROM CAR_RENTAL_COMPANY_CAR
WHERE OPTIONS LIKE '%네비게이션%'
ORDER BY CAR_ID
DESC;
```

## B. `REGEXP` 활용 쿼리문

```sql
SELECT
    *
FROM 
    car_rental_company_car
WHERE
    options REGEXP '네비게이션'
ORDER BY
    car_id DESC;
```

# 3. DRILING 🎯

## (1) 문자열 패턴 매칭 연산자 `LIKE` vs `REGEXP`

[관련 정리 링크]()



>  이모지 모음: 🤔, ➜ ✨ 0️⃣1️⃣2️⃣3️⃣4️⃣5️⃣6️⃣7️⃣8️⃣9️⃣🔟

