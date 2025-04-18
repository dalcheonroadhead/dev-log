# 1. 문제 설명 📌

## (1) 링크🔗

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/293259)

## (2) 해설🕵

**`KEY WORD`**: `CASE-WHEN 분기문`, `AVG 집계함수`, `ROUND() 원하는 지점에서 소수점 반올림`

- NULL 인 녀석들에 대한 예외 처리가 필요하다. -> AVG는 null 값을 제외하고 평균 내기 때문에, Null을 전부 10으로 바꿔주지 않으면, 계산이 제대로 되지 않는다.
- AVG()는 DEFAULT로 자릿수를 6개 까지 가져온다. 여기서 ROUND를 이용해 원하는 곳에서 소수점 반올림을 해야한다.

# 2. 구현 코드 🔎

```sql
SELECT 
ROUND( AVG(
    CASE 
        WHEN f.LENGTH IS NULL THEN 10
        ELSE f.LENGTH
    END
),2
    ) AS AVERAGE_LENGTH
FROM FISH_INFO f;
```

# 3. DRILING 🎯

## (1) CASE WHEN THEN 문에 대하여

```sql
SELECT
    CASE
        WHEN(조건A) THEN A
        WHEN(조건B) THEN B
		ELSE C
END AS 원하는 컬럼명
FROM TABLE
;
```

### A. THEN에 적힌 반환값은 WHEN에서 제시한 컬럼의 내용물로 바로 쓰인다?

 NO!

SELECT 문 안에 적힌 CASE WHEN THEN문은 그 값으로 고유의 열이 생성되어 반환된다. 만약 Alias 설정을 해두지 않으면, 

![image-20250415152925791](https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-04/image-20250415152925791.png)

이와 같이 CASE 문 전체로 하나의 열이 생성되어 반환되는 것이다. 따라서 CASE 문 안의 내용물이 컬럼의 내용으로 쓰이지는 않는다!

### B. ELSE와 END는 필수일까?

**둘 다 필수!**

ELSE 문이 없으면 `WHEN, THEN` 분기문을 빠져나간 모든 문장은 NULL로 표기된다.

END는 MySQL에서 필수 이다. 없으면 Syntax 오류가 난다.

## (2) ROUND(A, 숫자) 에 관하여

두 번째 인수 숫자는 **살아남을 소수점의 개수**이다. 해당 문제에서는 3번째 자리에서 반올림 하라고 했으므로 실제 살아남는 소수점 자리수는 2개가 된다. 따라서 `ROUND(A,2)`로 설정했다.



>  이모지 모음: 🤔, ➜ ✨ 0️⃣1️⃣2️⃣3️⃣4️⃣5️⃣6️⃣7️⃣8️⃣9️⃣🔟

