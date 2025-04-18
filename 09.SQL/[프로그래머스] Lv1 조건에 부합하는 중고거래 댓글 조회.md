# 1. 문제 설명 📌

## (1) 링크🔗

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/164673)

## (2) 해설🕵

**`KEY WORD`**: `DATE_FORMAT 활용`, `INNER JOIN`

- 게시판 글, 그와 연관된 댓글을 한 번에 조회해야 하므로, JOIN 필수
- 이때, 조건에 맞더라도, 양쪽 모두 값이 있는 경우만 가져와야 함으로, **INNER JOIN**을 써야 한다고 판단
- `YYYY-MM` 형태를 맞추기 위해 `DATE_FORMAT` 활용

# 2. 구현 코드 🔎

```sql
SELECT
gb.TITLE,
gb.BOARD_ID,
gr.REPLY_ID,
gr.WRITER_ID,
gr.CONTENTS,
DATE_FORMAT(gr.CREATED_DATE, '%Y-%m-%d') AS 'CREATED_DATE'
FROM USED_GOODS_BOARD gb
INNER JOIN USED_GOODS_REPLY gr
ON (gb.BOARD_ID = gr.BOARD_ID)
WHERE YEAR(gb.CREATED_DATE) = '2022' 
AND MONTH(gb.CREATED_DATE) = '10'
ORDER BY gr.CREATED_DATE ASC, 
gb.TITLE ASC;
```



# 3. DRILING 🎯

DATE_FORMAT의 경우, 와일드 카드 뒤에 영어가 대문자이냐, 소문자이냐에 따라 답이 달라진다. Remind를 위해서 다시 적어두고 넘어간다.

| 형식        | 의미          | 예시 (`2024-04-15 13:45:00`) |
| ----------- | ------------- | ---------------------------- |
| `%Y`        | 연도 (4자리)  | `2024`                       |
| `%y`        | 연도 (2자리)  | `24`                         |
| `%m`        | 월 (2자리)    | `04`                         |
| `%c`        | 월 (1~12)     | `4`                          |
| `%d`        | 일 (2자리)    | `15`                         |
| `%e`        | 일 (1~31)     | `15`                         |
| `%H`        | 시 (24시간제) | `13`                         |
| `%h` / `%I` | 시 (12시간제) | `01`                         |
| `%i`        | 분            | `45`                         |
| `%s`        | 초            | `00`                         |

참고로 `%M`은 월이 영어로 나온다. (ex  10 -> October)

>  이모지 모음: 🤔, ➜ ✨ 0️⃣1️⃣2️⃣3️⃣4️⃣5️⃣6️⃣7️⃣8️⃣9️⃣🔟

