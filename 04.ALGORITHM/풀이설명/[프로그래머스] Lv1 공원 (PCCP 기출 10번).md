# 1. 문제 설명 📌

[문제링크](https://school.programmers.co.kr/learn/courses/30/lessons/340198)

# 2. 접근 방식 🗃️

`KEYWORD`: `SIMULATION`

어렵게 생각할 것 없이, 지민이가 가진 돗자리 중 큰 것부터 공원의 격자에 일일히 대보면 된다. 
만약 겹치면 어짜피 둘 수 없기 때문에, 다음 가능한 자리부터 start 한다.

![image-20241203022546628](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스] Lv1 공원 (PCCP 기출 10번)/image-20241203022546628.png)

첫번째 자리부터 대보았는데, 전부 겹친다. 다음 start 가능한 곳으로 넘어간다.

![image-20241203022601257](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스] Lv1 공원 (PCCP 기출 10번)/image-20241203022601257.png)

![image-20241203022625311](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스] Lv1 공원 (PCCP 기출 10번)/image-20241203022625311.png)

![image-20241203022639967](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스] Lv1 공원 (PCCP 기출 10번)/image-20241203022639967.png)

이렇게 쭉 가다가, 
![image-20241203022704453](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스] Lv1 공원 (PCCP 기출 10번)/image-20241203022704453.png)

되는 여백을 만나면 바로 탈출하면 된다. 왜냐하면 우리는 돗자리 큰 거부터 대보고 있었기 때문이다. 지민이는 5x5부터 들고 있었으나, 공원에는 5x5를 둘 자리가 없다.

# 3. 코드 소개 🔎

```java
import java.util.*;

class Solution {

    
    public int solution(int[] mats, String[][] park) {
        int answer = -1;
 
        int row = park.length;
        int col = park[0].length;
        
        Arrays.sort(mats);
        
        universe:
        for(int i = mats.length-1; i>= 0; i--){
            
            int now = mats[i];
            
            for(int j = 0; j <= row - now; j++){
                for(int k = 0; k <= col - now; k++){
                    boolean isNowClear = true;
                    
                    earth:
                    for(int a = j; a < j + now; a++){
                        for(int b = k; b < k + now; b++){
                            if(!park[a][b].equals("-1")){
                                isNowClear = false;
                                break earth;
                            }
                        }
                    }
                    
                    if(isNowClear) {
                        answer = now;
                        break universe;
                    }
                }
            }
            
        }
        return answer;
    }
}
```

  for문이 5개가 들어간다. break는 가독성을 높이려고 `Label`을 활용했다. 위의 `earth`나 `universe`처럼 for나 while문에 이름을 명명해두고, `break earth`나 `break universe`를 하면, 해당 반복문까지 한 번에 탈출한다.  

# 4. 배운 것들 🎯

처음에 문제를 1차원 배열에 여백이 연속될 경우 누적해서 풀려고 했다... 이렇게 풀면, 상하좌우 여백이 연속되는지 계속 체크해나가야 하기 때문에 예외처리 해줘야할 것이 많고, 문제를 아주 어렵게 푸는 것이다. 예전에 SWEA 달팽이 문제를 풀 때만 해도 이러한 문제는 되게 쉬웠는데, 어떻게 풀어야할지 감이 안 잡히는게 참 힘들었다. 다시 열심히 문제를 풀어야겠다.