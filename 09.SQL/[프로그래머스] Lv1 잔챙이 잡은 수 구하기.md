# 1. 문제 설명 📌

## (1) 링크🔗

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/293258)

## (2) 해설🕵

**`KEY WORD`**: **IS NULL**

문제 조건이 10cm 이하인 물고기의 `LENGTH`는 무조건 NULL로 표시한다고 하였음으로, NULL 인 레코드의 개수만 세서 출력하면 된다.

# 2. 구현 코드 🔎

```sql
SELECT 
COUNT(*) AS 'FISH_COUNT'
FROM FISH_INFO f
WHERE f.LENGTH IS NULL;
```



# 3. DRILING 🎯

**`KEY WORD`: `IS NULL vs = NULL`**

처음에 문제를 풀 때, `= NULL` 로 문제를 풀어서 틀렸다. `= NULL`이 안되는 이유는 다음과 같다. 
`=` 은 비교 연산이다. 근데 `NULL` 자체가 **모르는 값**을 의미하므로, `A = NULL`은 곧 **'A가 뭔지 모르는 값과 같은가?'**를 묻는 비교 연산 이 된다. 따라서 어법적으로 맞지 않다. 
  반면, `A IS B`는 **'A의 상태가 B인가?'**이다. 즉 **논리나 상태가 특정 상태인지 판별하는 문장이다.** 그러면 'A의 상태가 NULL인가?'는 어법적으로 맞는 문장이 된다.



>  이모지 모음: 🤔, ➜ ✨ 0️⃣1️⃣2️⃣3️⃣4️⃣5️⃣6️⃣7️⃣8️⃣9️⃣🔟

