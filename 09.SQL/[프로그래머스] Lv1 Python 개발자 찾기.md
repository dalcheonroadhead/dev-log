# 1. 문제 설명 📌

## (1) 링크🔗

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/276013)

## (2) 해설🕵

**`KEY WORD`**: **`IN` **

Python을 쓸 수 있는 개발자를 전부 찾는 쿼리문이다. 근데 지금은 테이블 정규화를 안 해놔서, 하나의 테이블에 SKILL이 _1, _2, _3 형태로 여러 개 존재하고 있다. 여기서 Python이 든 사람들을 추려내야 한다. 처음에 구할 때는,

```sql
WHERE SKILL_1 = 'Python'
OR SKILL_2 = 'Python'
OR SKILL_3 = 'Python'
```

이런 식으로 구했지만, `IN`을 쓰면 

```sql
'Python' IN (SKILL_1, SKILL_2, SKILL_3)
```

으로 구하면 된다.

필자는 `COLUMN NAME IN (VALUE_1, VALUE_2, ...)` 이 형태만 가능한 줄 알았지만, 그 반대도 가능하다는 것을 처음 알았다.

# 2. 구현 코드 🔎

```sql
SELECT 
d.ID,
d.EMAIL,
d.FIRST_NAME,
d.LAST_NAME
FROM DEVELOPER_INFOS d
WHERE 'Python' IN (SKILL_1, SKILL_2, SKILL_3)
ORDER BY ID
ASC;
```



# 3. DRILING 🎯

## (1) `A IN (B,C)` 쿼리문의 작동원리 

**A IN (B,C)** 쿼리문은 내부적으로 다음과 같이 작동한다. A = B or A =C
처음 학습할 때, A는 무조건 컬럼의 이름, B,C는 필터링을 걸기 위한 상수 값이라 생각했지만, MySQL과 PostgreSQL 등 몇몇 DB에서는 반대도 가능함을 이번에 배웠다. (상수 IN 컬럼)

즉 

```sql
'Python' IN (SKILL_1, SKILL_2, SKILL_3)
```

쿼리문은

```sql
SKILL_1 = 'Python' OR SKILL_2 = 'Python' OR SKILL_3 = 'Python'
```

로 변환되어서 쿼리문이 진행 된다.



>  이모지 모음: 🤔, ➜ ✨ 0️⃣1️⃣2️⃣3️⃣4️⃣5️⃣6️⃣7️⃣8️⃣9️⃣🔟

