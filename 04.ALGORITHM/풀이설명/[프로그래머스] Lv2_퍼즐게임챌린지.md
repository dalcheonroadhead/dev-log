# 1. 문제 설명 📌

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/340212)

![image-20241014211301537](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[00] 풀이설명 템플릿/image-20241014211301537.png)

**1번 퍼즐**
사용자의 레벨이 1이라 했을 때, 첫 번째 퍼즐은 사용자의 레벨 >= 퍼즐의 난이도 조건에 부합함으로 시간을 `3분` 드려서 퍼즐을 완료한다.

**2번 퍼즐**
`사용자의 레벨` < `퍼즐의 난이도` 이다. 
이 경우 문제의 요구조건처럼 직전 퍼즐을 (`퍼즐의 난이도` - `사용자의 레벨`) 만큼 다시 풀어야 한다. 그래서 2번 퍼즐을 푸는데는 1*3+4 = `7분`의 시간이 든다. 

**3번 퍼즐**
이것도 마찬가지로 `사용자의 레벨` < `퍼즐의 난이도` 임으로 (`퍼즐의 난이도` - `사용자의 레벨`)*4 + 2 = `10분` 이 든다. 

따라서, 사용자의 레벨이 1이면 모든 문제를 푸는데 총 20분이 소요된다. 

문제에서는 `limit` 이라는 제한시간이 주어진다. 
제한 시간 안에 퍼즐을 다 풀 수 있는 최소 레벨이 몇인지 구하여라 

# 2. 접근 방식 🗃️

`KEY WORD`: `Binary_Search`

풀이자의 레벨이 오를수록 퍼즐 풀이에 실패하고 다시 이전 퍼즐을 푸는 시간이 사라져서 전체 걸리는 시간이 줄어들 것이다. 
해당 문제에서는 제한시간내로 풀지만, 레벨이 최소값인 경우를 구하라고 하고 있다.
그렇다면 `제한시간 내로 모든 퍼즐이 풀면서도 제한시간에 제일 가까운 소요시간이 걸리는 레벨`을 구하면 된다. 

### (1)풀이자의 숙련도를 1씩 올리면서 완전 탐색으로 구한다면 어떨까?  

`시간 초과`
레벨과 비교가 되는개념인 퍼즐 난이도가 10의 5승까지 있다. 그렇다면 제한 시간 내에 풀 수 있는 레벨의 최소값을 완전탐색으로 구하려면 1부터 10의 5승까지 하나씩 대조해봐야 한다. 
`레벨 당 걸리는 시간 계산`, `모든 레벨을 보기` 최대 10의 5승의 제곱의 시간이 들어간다. 대략 1초에 10억 번 계산할 수 있는 컴파일러의 능력을 10배 초과하게 된다. 따라서 다른 방식을 생각 해야 한다. 

### (2) 이분 탐색

a. `구하려는 값(ans)` = 제한시간 내 모든 문제를 풀 수 있는 레벨들 중 최소 레벨
b.  `범위`

- `min` : `100,000` (해당 수는 퍼즐 난이도 중 최상 난이도이다. 최상 난이도를 풀 수 있는 레벨이라면 모든 퍼즐을 최소한의 시간으로 다 해결할 수 있기 때문에, min으로 둔다.)
- `max`: `1` (퍼즐 난이도의 최소값이 1이므로 level의 최소도 1로 두었다. 이러면 퍼즐 난이도가 아무리 쉬워도 다른 level의 숙련자에 비해서는 해결 소요 시간이 오래 걸릴 것이다.)
- `mid`: `(min + max)/2`  (둘의 중간값)

c. `비교`

-  퍼즐 풀이자가 `mid` 레벨이라 칠 때 걸리는 시간을 구해서 `limit (제한시간)`과 비교 
- 걸리는 시간을 구하는 함수를 `spending_time(int level)` 이라고 칠 때
  - `spending_time(mid) > limit` : max = mid+1 (제한시간 내로 문제를 못 푸는 레벨이므로, 레벨을 상향 조정 한다.)
  - `spending_time(mid) <= limit`: min = mid-1 (제한시간 내로 문제가 풀리고 있으므로 최소레벨을 찾을 때까지 레벨을 하향 조정한다.)

![image-20241014221108423](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스]Lv2_퍼즐게임챌린지/image-20241014221108423.png)

이렇게하면 어느 순간, max는 제한시간 내에 문제를 풀 수 있는 최소 레벨에서 멈춰있을 것이고, min은 제한시간 내에 풀 수 있는 레벨이었어도, 문제가 다 풀린다면 계속 하향되므로 언젠가는 max와 엇갈리게 될 것이다. 엇갈린 순간 min이 가리키는 레벨로는 더 이상 모든 문제를 제한 시간 내에 풀 수 없고, max로는 풀 수 있게 된다. 이때 max가 모든 문제를 풀 수 있는 레벨 중 최소 값이다. 

# 3. 코드 소개 🔎

```java
import java.io.*;
import java.util.*;
// 1. 이분 탐색
// 2. max = level 1로 문제 푼 시간, min = level 10000으로 문제 푼 시간, target = limit 값

class Solution {    
    public int solution(int[] diffs, int[] times, long limit) {
        
        return binary_search(diffs, times, limit);
        
    }
    
    public int binary_search(int [] diffs, int [] times, long limit) {
        int max = 1; int min = 100000; 
        while (max <= min){
            int level = (max+min)/2; 
            long mid = calcul(diffs,times, level);
            // 제한 시간을 초과함 -> level 상승
            if(mid > limit) max = level + 1; 
            // 제한시간보다 빨리 끝내거나 딱 맞출 때 -> level 하락
            else min = level - 1; 
        }
        return max;
    }
    
    public long calcul(int [] diffs, int [] times, int level) {
        long ans = 0;
        for(int i = 0; i < diffs.length; i++){
            if(diffs[i] <= level) ans += times[i];
            else ans += (long)(times[i] + times[i-1])* (long)(diffs[i] - level) + times[i];
        }
        return ans;
    }
}
```

# 4. 추신 💖

min과 max를 레벨 자체의 크기에 따라 설정한 게 아니고, 해당 레벨로 풀었을 때 걸리는 시간을 기준으로 정하다보니, 해당 변수들의 의미가 직관적이지 못했다. 다음에 풀게 된다면 해당 의미를 좀 더 직관적으로 보완해야겠다.
