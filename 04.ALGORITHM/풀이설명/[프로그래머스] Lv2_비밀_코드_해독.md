# 1. 문제 설명 📌

## (1) 링크🔗

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/388352)

## (2) 해설🕵

암호 분석 도구랑 스무고개 해서, 암호가 될 수 있는 경우의 수 구하기



# 2. 생각의 흐름: 코드가 나오기까지 🗃️

## (1) IDEA 도출💡

**`KEY WORD`: `조합`, `완탐(Brute Force)`**

1. 조합으로 5개의 번호를 뽑는다.
2. 유효성 검증: 5개의 번호가 암호 분석기를 전부 통과하는지 확인한다.
3. 전부 통과하면, 답의 경우의 수에 + 1 한다.

## (2) SUDO CODE 📜

```diff
+ 1. 1 ~ n 까지 중 5개의 수를 조합으로 뽑는다.
+ 2. q[0] ~ q[마지막] 까지 하나의 행 씩 다음을 확인한다.
-	q[0][0] ~ q[0][1] 까지 중에서 1번에서 뽑은 숫자와 일치하는 수가 몇 개인지 센다. (sum이라 하자)
-	만약 sum이 해당 암호 분석기에서 말한 답안 코드의 개수 (ex - q[i]일때의 ans[i])와 다르다면 여기서 종료한다.
+ 3. 2번을 전체 통과하면 answer + 1 올린다.
```

## (3) 시간복잡도 분석 🕓

$O({n}\_C\_{5} * (m*5))$

- 조합으로 n개 중 5개를 뽑는다.
- 하나 뽑을 때마다 2차원 배열 q를 전부 디벼 본다. (최악의 경우, 전체 다 뒤짐) - m(q의 길이)*5

# 3. 구현 코드 🔎

```java
import java.util.*;

class Solution {
    static int answer = 0;
    public int solution(int n, int[][] q, int[] ans) {
        combination(1,0,n,new int [5], q, ans);
        return answer;
    }
    
    public void combination (int start, int cnt, int n, int [] guess, int [][] q, int [] ans) {
        if(cnt == 5) {
            HashSet <Integer> set = new HashSet<>();
            for(int i = 0; i < 5; i++){
                set.add(guess[i]);
            }
            isPossible(q, ans, set);
            return;
        }
        
        for(int i = start; i <= n; i ++) {
            guess[cnt] = i;
            combination(i+1, cnt+1, n, guess, q, ans);
        }
    }
    
    public void isPossible(int [][]q, int [] ans, HashSet<Integer> set) {
        for(int i = 0; i < q.length; i ++) {
            int sum = 0;
            for(int j = 0; j < 5; j++){
                if(set.contains(q[i][j])) sum++;
            }
            if(sum != ans[i]) return;
        }
        answer ++;
        return;
    }
}
```

# 4. 배운 것들 🎯

## (1) 잘못된 생각의 방향 확인

![image-20250415175732793](https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-04/image-20250415175732793.png)

답을 찾는 재귀문 안에, 각 Q문에서 ans 개 만큼 뽑는 조합 재귀를 다시 넣었다. 이런 이유로 안 좋은 풀이였고, 답을 찾지 못헀다.

- ans 숫자는 매 재귀마다 달라지고, ans개 뽑은 배열 값 또한 달라진다. 

- 공용으로 쓰고 있는 SET은 재귀가 많아질수록 얽힐 수 있기에, 좋은 풀이가 아니었다. 

- 굳이 답을 찾는 재귀 내에 조합 재귀를 다시 넣을 필요가 없었는데, 오히려 어렵게 풀었다. 

  그래서 에러 찾기가 쉽지 않았다.

현재 풀이를 표시하면 다음과 같다.

![image-20250415180200016](https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-04/image-20250415180200016.png)

훨씬 보기 쉽고, 문제 풀이의 흐름이 직렬적이어졌다.



>  이모지 모음: 🤔, ➜ ✨ 0️⃣1️⃣2️⃣3️⃣4️⃣5️⃣6️⃣7️⃣8️⃣9️⃣🔟

