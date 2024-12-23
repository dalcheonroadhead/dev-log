# 1. 문제 설명 📌

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/340199)

# 2. 접근 방식 🗃️

`KEY WORD`: `SIMULATION`

# 3. 코드 소개 🔎

```java
import java.util.*;

class Solution {
    public int solution(int[] wallet, int[] bill) {
        int answer = 0;
        Arrays.sort(wallet); Arrays.sort(bill);
        
        while(wallet[0] < bill[0] || wallet[1] < bill[1]){
            answer += 1;
            bill[1] /=2;
            Arrays.sort(bill);
        }
        
        return answer;
    }
}
```

# 4. 배운 것들 🎯

없음. 꾸준함을 위한 재물