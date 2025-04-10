# 1. 문제 설명 📌

## (1) 링크🔗

[2999. Count the Number of Powerful Integers](https://leetcode.com/problems/count-the-number-of-powerful-integers/)

## (2) 해설🕵

### 원문

You are given three integers `start`, `finish`, and `limit`. You are also given a **0-indexed** string `s` representing a **positive** integer.

A **positive** integer `x` is called **powerful** if it ends with `s` (in other words, `s` is a **suffix** of `x`) and each digit in `x` is at most `limit`.

Return *the **total** number of powerful integers in the range* `[start..finish]`.

A string `x` is a suffix of a string `y` if and only if `x` is a substring of `y` that starts from some index (**including** `0`) in `y` and extends to the index `y.length - 1`. For example, `25` is a suffix of `5125` whereas `512` is not.

### 해석

당신에게 `start`와 `finish` 그리고 `limit`이 주어진다. 그리고 또한 0부터 인덱스를 시작하는 `s`라는 String도 주어집니다. s는 양의 정수를 나타내고 있다.

`x`라는 양의 정수는 만약 그것이 s로 끝난다면 (다른 말로는 s가 x의 접미사라면) 그리고 x내의 모든 자릿수가 limit 이하의 숫자라면 x를 **강력하다**고 간주한다.

*start ~ finish* 의 범위안에 **"강력한"** 정수들의 총 개수를 반환하라 

문자열`x`는 문자열 ` y`의 접미사이다. 만약 `x`가 `y`의 임의의 인덱스(0을 포함)에서 시작하여, `y.length - 1`까지 확장될 경우 말이다. 예를 들어 25는 5125의 접미사이지만, 512는 아니다.



# 2. 생각의 흐름: 코드가 나오기까지 🗃️

## (1) IDEA 도출💡

**`KEY WORD`: `Digit DP`**

도저히 어떻게 풀지 모르겠어서 답지를 찾았다. 근데 왠걸... 우리나라 사람 SOVLED 풀이는 없고 죄다 중국권, 영어권 풀이였다. 그 중 하나를 잡고 해석해가며 문제를 풀었다. 여기엔 해당 풀이를 이해하면서 헤맸던 부분들에 대한 고찰과 전체 풀이 이해를 적어보려고 한다.

### a. 전체적인 맥락

1. DP로 1 ~ 시작점-1까지 유효한 숫자를 구한다
2. DP로 1 ~ 끝점까지 유효한 숫자를 구한다
3. 2번에서 1번을 빼서 답을 구한다.

### b. DP의 세부적인 방식

해당 문제의 풀이는 **DFS를 활용한 BOTTOM-UP 방식의 DP 이다.**

![image-20250410154853098](https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-04/image-20250410154853098.png)

- 1번: 맨 왼쪽부터 시작해서 접미사가 존재하는 위치까지 나아간다. 여기서는 접미사의 위치가 1의 자리수라고 가정해보겠다. 나아가는 기준은 다음과 같다.
  - 현재 자릿수의 값이 구간 내 최대값의 해당 자릿수의 값과 같은가 아닌가만 계속 확인한다.
  - 맨 왼쪽부터 현 자릿수까지 모든 자릿수가 최대값의 자릿수와 모두 일치했다면 boolean 값을 true, 아니면 false로 준다. (이렇게 둘을 나눈 이유는 후술하겠다.)
- 2번: 접미사 시작점부터, 인수로 주어진 문자열 `s`가 넣었을 때,  전체 값이 조건 상 유효한지를 확인한다.
  - 만약 맨 왼쪽에서부터 접미사 시작점까지 모두 한계치의 값을 써왔다면, 뒤의 접미사가 온다고 해도 한계치의 값을 넘을 수 있다! (예시 - 한계치 값: 123456, 접미사: 789 - 1000의 자리 올때까지 왼쪽의 값이 123이였다하더라도 접미사 붙이면 한계 값을 넘는다.)
  - 자리수 중 하나라도 한계치에 못 미쳤다면, 뒤에 접미사가 붙으면 무조건 유효하다.
- 3번: 이제 유효한 값 결과를 계속 누적한다. 자릿수엔 항상 0이 포함되어 있어서, 자릿수 별 유효한 값도 모두 계산에 상정되어 있다. (예 한계치 값: 123456, 접미사: 789 일 경우, 789, 1789, 21789 등 모두 계산 결과에 넣어져 있음)
- 4번: 현재까지 봐왔던 자릿수가 전부 한계 값의 자릿수와 같지 않다면, memo[현 자릿수] 배열의 원소에 현재 값을 캐싱한다.

### 왜 한계치가 아닌 경우만 캐싱하는가?

문제를 보면 두 종류를 나누어 계산하고 있음을 알 수 있다. 

1. 모든 자릿수가 한계치의 자릿수와 같은 경우
2. 1번이 아닌 경우

이 `1번이 아닌 경우`일 경우 다음 자릿수에 올 수 있는 값은 0 ~ digitLimit(인수로 주어진 값)으로 한정되어 있다. 따라서 결과값인 경우의 수 또한 매번 고정된 값이다. 이를 매 번 계산하는 것은 리소스 낭비이자, 시간 낭비이다. 이것을 피하기 위해 캐싱한다. 

또한 이 캐싱된 결과는 다음 번에 써먹어야 한다. 만약 memo[현 자릿수]에 0이 아닌 제대로 된 값이 들어가 있다면, 그걸 바로 사용하고 더 이상의 재귀를 진행하지 않고 탈출 해야한다.



## (2) SUDO CODE 📜



```diff
import java.util.*;

class Solution {

    public 본함수 {
+       // 1. 0 ~ start-1 까지 모든 조건을 만족하는 값의 개수 구하기
+       // 2. 0 ~ finish까지 모든 조건을 만족하는 값의 개수 구하기
+		// 3. 2번에서 1번을 뺀다. 이러면 구간 내의 모든 유효한 숫자의 개수를 알 수 있다. 이것이 답이다.
    }

    // pos = 현재 자릿수, isLimit = 최고 높은 자릿수부터 직전 자릿수까지 모두 조건에서 제안한 한계치를 찍었는가?
-    private long dfs(int pos, boolean isLimit) {
-        // 1-1) (예외 처리) 최대치의 자릿수보다 접미사의 자릿수가 크다면, 유효한 숫자는 아무것도 없다. 
-        // 1-2) (예외 처리) 현재까지 한계치를 갔다 오지 않았다면, 어짜피 뒤는 중복된 계산이 쭉 이어짐 (0 ~ limit까지 계산한 내용들).
        //      따라서 최초 한 번 계산한 것을 돌려쓴다.
        
        
-        // 2) (기저 조건 BASE CASE) 만약 접미사가 시작하는 부분에 현재 도달했다면, 다음을 통해 현재까지 만든 숫자의 유효성을 확인
            // a. 지금까지 정한 모든 자릿수가 최대크기가 아니라면 그냥 1 반환 (답의 총 개수에 누적 +1 되는 거임)
            // b. 만약 지금까지 모든 자릿수가 최대크기를 따라왔다면? 접미사가 한계치의 접미사 영역 부분 값보다 작으면 유효(+1), 아니면 무효(+0)
            
-        // 3) 다음 자릿수의 한계를 정하기
            // a. 현재까지 한계점을 계속 밟아왔다면, 다음 값도 한계치가 최대
            // b. 현재까지 한계점을 계속 밟아온 게 아니라면, 다음 값은 0~9중 어떤 것이어도 된다.
            // c. a,b 중 뭐가 되었든 값이 한계치를 벗어나서는 안된다.
            // d. 다음 dfs로 간다. , 현재 자리까지 한계점의 값을 활용했는지 아닌지를 isLimit boolean 값으로 표시한다.


-		// 4) 만약 현재 한계점을 매번 갱신하는 루트가 아니라면 memo에 캐싱한다.

-		// 5) 현재 위치에서 유효한 값의 개수가 dfs 재귀를 끝내며 모두 넘어왔을 것이다. 이것을 누적해서 다시 반환한다.
    }
}
```

## (3) 시간복잡도 분석 🕓

finish(최대 값)의 길이: `d`, 각 자리수의 한계치: `limit`이라고 할때, 캐싱을 사용함으로 각 자릿수마다 오직 limit 개 (즉 0부터 한계치까지) 재귀를 타면 된다. + 모든 자릿수가 최대치인 경우는 따로 계산해야 한다. 따라서 근사치를 내면

**$O(d * (limt + 1))$** 이 된다.

# 3. 구현 코드 🔎

```java
import java.util.*;

class Solution {
    private String suffix;          // 접미사
    private String threshold;       // 한계점
    private long [] memo;           // 한계점에 다다르지 않는 경우의 계산을 위한 캐싱 
    private int digitLimit;         // 자릿수 한계

    public long numberOfPowerfulInt(long start, long finish, int limit, String s) {
        this.suffix = s;
        this.digitLimit = limit;
        // 1. 0 ~ start-1 까지 모든 조건을 만족하는 값의 개수 구하기
        threshold = String.valueOf(start - 1);
        memo = new long [20];
        long count2Start = dfs(0, true);

        // 2. 0 ~ finish까지 모든 조건을 만족하는 값의 개수 구하기
        threshold = String.valueOf(finish);
        memo = new long [20];
        long count2Finish = dfs(0, true);

        return count2Finish - count2Start;
    }

    // pos = 현재 자릿수, isLimit = 최고 높은 자릿수부터 직전 자릿수까지 모두 조건에서 제안한 한계치를 찍었는가?
    private long dfs(int pos, boolean isLimit) {
        // 1-1) (예외 처리) 최대치의 자릿수보다 접미사의 자릿수가 크다면, 유효한 숫자는 아무것도 없다. 
        if(threshold.length() < suffix.length()) return 0;
        // 1-2) (예외 처리) 현재까지 한계치를 갔다 오지 않았다면, 어짜피 뒤는 중복된 계산이 쭉 이어짐 (0 ~ limit까지 계산한 내용들).
        //      따라서 최초 한 번 계산한 것을 돌려쓴다.
        if(!isLimit && memo[pos] != 0L) return memo[pos];

        // 2) (기저 조건 BASE CASE) 만약 접미사가 시작하는 부분에 현재 도달했다면, 다음을 통해 현재까지 만든 숫자의 유효성을 확인
        if(threshold.length() - pos == suffix.length()) {
            // a. 지금까지 정한 모든 자릿수가 최대크기가 아니라면 그냥 1 반환 (답의 총 개수에 누적 +1 되는 거임)
            // b. 만약 지금까지 모든 자릿수가 최대크기를 따라왔다면? 접미사가 한계치의 접미사 영역 부분 값보다 작으면 유효(+1), 아니면 무효(+0)
            return isLimit? (suffix.compareTo(threshold.substring(pos)) <= 0? 1 : 0) : 1;
        }
        // 3) 다음 자릿수의 한계를 정하기
            // a. 현재까지 한계점을 계속 밟아왔다면, 다음 값도 한계치가 최대
            // b. 현재까지 한계점을 계속 밟아온 게 아니라면, 다음 값은 0~9중 어떤 것이어도 된다.
            // c. a,b 중 뭐가 되었든 값이 한계치를 벗어나서는 안된다.
        int nextLimit = Math.min(digitLimit, (isLimit? threshold.charAt(pos) - '0' : 9));
        long validCnt = 0;

        for(int i = 0; i <= nextLimit; i++){
            // 다음 자리로 간다. + 지금까지 한계점을 사용했으며, 이번 자릿수도 한계 자릿수를 사용했다는 상태표시
            validCnt += dfs(pos + 1, isLimit && (threshold.charAt(pos) - '0') == i);
        }

        if(!isLimit) memo[pos] = validCnt;

        return validCnt;
    }
}
```

# 4. 배운 것들 🎯

알고리즘 문제에서 캐싱을 어떻게 사용하는지 한번 확인해볼 수 있는 좋은 기회였다.

