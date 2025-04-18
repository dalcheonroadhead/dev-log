# 1. 문제 설명 📌

## (1) 링크🔗

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/144853)

## (2) 해설🕵

**`KEY WORD`**: `DATE 함수`, `= 동등 비교`

원래 DATE()함수 안에 DATE TIME을 넣으면 DATE로 변환 되어지는 것으로 알고 있는데, 여기서는 그게 안 통해서 뭔가 찜찜하다. 그래서 DBEAVER에서 돌려보니, 또 된다.

![image-20250418123716954](https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-04/image-20250418123716954.png)

일단 프로그래머스에서는 저 DATE 함수가 먹히지 않아서, `DATE_FORMAT(A, '와일드 카드 문장')` 형태로 문제를 풀었다.

# 2. 구현 코드🔎

```sql
SELECT
BOOK_ID,
DATE_FORMAT(PUBLISHED_DATE, '%Y-%m-%d') AS PUBLISHED_DATE
FROM BOOK
WHERE YEAR(PUBLISHED_DATE) = '2021'
AND CATEGORY = '인문';
```

# 3. DRILING 🎯

없음

>  이모지 모음: 🤔, ➜ ✨ 0️⃣1️⃣2️⃣3️⃣4️⃣5️⃣6️⃣7️⃣8️⃣9️⃣🔟

