# 1. 문제 설명

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/12977?language=java)

주어진 숫자 중 3개의 수를 더했을 때 소수가 되는 경우의 개수를 구하려고 합니다. 숫자들이 들어있는 배열 nums가 매개변수로 주어질 때, nums에 있는 숫자들 중 서로 다른 3개를 골라 더했을 때 소수가 되는 경우의 개수를 return 하도록 solution 함수를 완성해주세요.

##### 제한사항

- nums에 들어있는 숫자의 개수는 3개 이상 50개 이하입니다.
- nums의 각 원소는 1 이상 1,000 이하의 자연수이며, 중복된 숫자가 들어있지 않습니다.

------

##### 입출력 예



| nums        | result |
| ----------- | ------ |
| [1,2,3,4]   | 1      |
| [1,2,7,6,4] | 4      |

##### 입출력 예 설명



입출력 예 #1
[1,2,4]를 이용해서 7을 만들 수 있습니다.

입출력 예 #2
[1,2,4]를 이용해서 7을 만들 수 있습니다.
[1,4,6]을 이용해서 11을 만들 수 있습니다.
[2,4,7]을 이용해서 13을 만들 수 있습니다.
[4,6,7]을 이용해서 17을 만들 수 있습니다.

> 출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges

# 2. 풀이 설명 

#### keyword

: 조합, 소수 판별 

1. 조합으로 n개의 수 중 3개를 뽑는 경우의 수를 하나 하나 순회한다. 
2. 각 경우의 수마다, 기저조건에서 3개를 더 했을 때, 소수인지 판별 한다. 

# 3. 제출 코드 

```java
class Solution {
    
    static int [] arr;
    static boolean [] isVisited;
    static int cnt = 0;
    
    public int solution(int[] nums) {
        arr = new int [nums.length];
        isVisited = new boolean[nums.length];
        
        for(int i = 0; i < arr.length; i++){
            arr[i] = nums[i];
        }
        
        combination(0, 0);
        
        return cnt;
    }
    
    public void combination(int depth, int start) {
        if(depth == 3){
            int v = 0;
            // 선택된 수들을 합해서 후보 값을 만든다.
            for(int i = 0; i < isVisited.length; i ++){
                if(isVisited[i]){
                    v += arr[i];
                }
            }
            // 만약 후보값이 전체의 절반의 수 중 하나라도 나누어 떨어지면 소수가 아니다.
            for(int i = 0; i <= v/2; i ++){
                
                if(i == 0 || i == 1) continue;
                
                if(v%i == 0) {
                    return;
                }
            }
            // 그렇지 않다면 소수임으로 소수 개수를 하나 카운트 한다.
            cnt++;
            return; 
        }
        
        for(int i = start; i < arr.length; i++){
            if(!isVisited[i]){
                isVisited[i] = true; 
                combination(depth+1, i+1);
                isVisited[i] = false;
            }
        }
    }
}
```

# 4. 좋아요를 많이 받은 풀이

 ```java
 
 import java.util.Arrays;
 
 class Solution {
 
 
 
     public int solution(int[] nums) {
         int ans = 0;
 
         for(int i = 0; i < nums.length - 2; i ++){
             for(int j = i + 1; j < nums.length - 1; j ++){
                 for(int k = j + 1; k < nums.length; k ++ ){
                     if(isPrime(nums[i] + nums[j] + nums[k])){
                         ans += 1;  
                     } 
                 }
             }
         }
         return ans;
     }
     public Boolean isPrime(int num){
         int cnt = 0;
         for(int i = 1; i <= (int)Math.sqrt(num); i ++){
             if(num % i == 0) cnt += 1; 
         }
         return cnt == 1;
     }
 }
 ```

- 3중 For 문으로 조합 구현  
  i가 선택한 수 다음부터 j가 고르도록 하고, j가 고른 것 다음부터 k가 고르도록 해서, N개 중 3개를 순서 없이 고르는 조합을 간단히 구현 
  ☞ n이 50개 이하여서, 시간 복잡도를 따질 필요가 없기 때문에 쓰인 것이라 생각한다. 오히려 가독성은 좋은 듯
- 소수 판별 
  더 해진 값에 대한 소수 판별로 그 수의 제곱근 보다 작은 수들로 해당 num이란 더해진 수가 나누어 지는가를 계산하고 있다. 나는 제곱근으로 되는지가 가물가물해서 num/2보다 작은 수들로 구했다. 에라토스테네스의 체 다시 공부해야겠다. 

