# 1. 문제 설명 📌

## (1) 링크🔗

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/298515)

## (2) 해설🕵

**`KEY WORD`**: `문자열 합치기`, `집계 함수`

- `cm`를 길이에 합치기 위해 **`CONCAT()`** 함수 활용
- `max()` 집계 함수 활용

# 2. 구현 코드 🔎

```sql
SELECT CONCAT(max(f.LENGTH), 'cm') as MAX_LENGTH
FROM FISH_INFO f
```

# 3. DRILING 🎯

## (1) MAX() 집계함수의 내부 작동 원리

```c
// 초기화
int maxValue = NULL;

for (int i = 0; i < rowCount; i++) {
    // currentRowValue는 i번째 row의 length 값 (ex: f.LENGTH)
    int currentRowValue = getValueFromRow(i, "length");

    // NULL 무시
    if (currentRowValue == NULL) {
        continue;
    }

    // 첫 값은 무조건 세팅
    if (maxValue == NULL || currentRowValue > maxValue) {
        maxValue = currentRowValue;
    }
}

return maxValue;
```

이렇게 **행 하나씩 비교하며 MAX 값을 찾는 구조를 ** **1-pass linear scan**이라 부른다.

## (2) CONCAT(A,B,C...) 내부의 작동 원리

해당 함수는 코딩 언어에서와 마찬가지로 인수 내부의 문자열을 이어 붙이는 함수이다. 이는 다음과 같이 동작한다.

### 1. 파라미터 평가

파라미터로 들어온 인수들을 평가한다.

- 계산을 거쳐야하는 것이 있는지 ex( 집계함수 등)
- 형 변환 처리가 필요한 것이 있는지 

### 2. SQL 내부 메모리 버퍼에서 문자열을 연결한 새로운 문자열 객체 생성

- 문자열 버퍼 동적으로 확보
- max()문에서 나온 숫자(ex - 34)와 'cm' 연결해서 '34cm' 라는 새 문자열 객체 생성해서 넣어놓음

### 3. (예외처리) 만약 인수 중 하나라도 null 이라면?

걍 null을 반환한다. 

```sql
SELECT CONCAT('수', '민') → '수민'
SELECT CONCAT('수', NULL) → NULL
```



여기서는 <u>(1) 파라미터 평가</u>, <u>(2) MAX 함수 계산</u>, <u>(3) 형 변환</u>, <u>(4) 이어 붙여서 새로운 문자열 객체 생성</u> 절차로 이루어진다. 
여기서 max()는 하나의 행만 반환하므로, `CONCAT()`은 하나의 행에 대해서밖에 이루어지지 않는다.



>  이모지 모음: 🤔, ➜ ✨ 0️⃣1️⃣2️⃣3️⃣4️⃣5️⃣6️⃣7️⃣8️⃣9️⃣🔟

