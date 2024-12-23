# 1. 문제 설명 

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/92335)

# 2. 접근 방식 

해당 문제는 문제에서 하라는 대로만 하면 된다. 

**(1) 받은 숫자를 N진법으로 변환한다.** 
문제를 풀던 당시에는 `Integer.toString(n, radix)` 라는 문법을 알지 못했다. 해당 문법은 n을 2번째 인자인 radix진법으로 변환해서 String으로 반환한다. `Integer.toString(n,2)`이면 n을 2진법으로 반환해서 String 값으로 반환하는 것이다. 
이 문법을 몰라서, 직접 반환했다. 
  반환 방법은 다음과 같다. 

- 바꾸려는 수를 n, 진법을 radix라고 할 때, `n%radix == 0` 이 될 때까지 n을 radix로 나눈다. 
  이때 나머지 값을 저장하고 있는다. 
- 드디어 `n%radix == 0` 이 되면 지금까지 나왔던 나머지들을 역순으로 줄 세운다. 
  자세한 변환 방법은 다음 링크에 나와있다. 해당 링크에서 10진수를 2진수 변환하는 방법에 2를 다른 수로 바꿔주면 모두 통한다. 
  `링크`: [2진수 10진수 변환](https://dalcheonroadhead.tistory.com/6)

**(2) 반환한 수를 0을 기준으로 자른다. ** 
여기에도 2가지 방법이 있다. `String.split("0")` 라는 split Method를 이용한 방식과, `StringTokenizer("값", "delim", delim 포함 여부 boolean)` 을 이용하는 방식이다.  
 첫 번째 방식은 반환 값이 String[] 배열이다. 잘린 값들이 배열에 차곡차곡 들어간다. 자르는 기준이 되는 delim은 포함되지 않는다. 
 두 번째 방식은 st에 buffer 형식으로 잘린 값들이 차례대로 저장된다. 이때 3번째 인자가 true인지, false인지 여부에 따라 delim 값도 포함할 수 있다. `st.hasMoreTokens()` 를 이용해 Loop문을 돌려서 `st.nextToken()`으로 잘려진 값들 중 현재 조회중인 값을 가져와 작업하면 된다. 
`st.hasMoreTokens()`: st에 저장된 값들이 남아있을때 `true`, 없으면 `false`
`st.nextToken()`: StringTokenizer로 자른 값들 중 하나를 반환, 반환하면, 그 다음 자른 값을 바라본다.

**(3) 소수 판별 한다.**
낱개의 값에 대한 소수 판별임으로, 에라토스테네스의 채를 사용하지 않고, `n의 제곱근까지 수 중 n을 나눌 수 있는 값이 있는가?` 를 이용해 소수 판별을 했다. 소수 판별에 대한 자세한 이론은 다음 링크를 확인하기를 바란다. 
`링크`: [쉬운 소수 판별법](https://dalcheonroadhead.tistory.com/438)

# 3. 코드 분석 

```java
import java.io.*;
import java.util.*;
class Solution {
    // 1. n진법으로 변환한다.
    // 2. 변환한 수를 0을 기준으로 자른다. 
    // 3. 각 숫자별로 소수 판별 한다.
    
    public int solution(int n, int k) {
        
        
        StringTokenizer st = new StringTokenizer(baseN(n,k),"0", false); // 진법 변환 및 0마다 자르기
        int cnt = 0; // 소수가 나온 횟수
        while(st.hasMoreTokens()){
            if(isPrime(Long.parseLong(st.nextToken()))) cnt++;
        }
        return cnt;
    }
    
    public String baseN(int n, int k) {
        StringBuilder sb = new StringBuilder();
        
        while(n/k != 0) {
            sb.insert(0, n%k);
            n = n/k;
        }
        sb.insert(0, n%k);
        
        return sb.toString();
    }
    
    public boolean isPrime(long value) {
        
        if(value == 1) return false;
        
        for(long i = 2; i <= Math.sqrt(value); i++){
            if(value%i == 0) return false;
        }
        return true;
    }
}
```



# 4. 성장 하기 

다른 사람의 풀이 중 깔끔한 것이 있어서 소개하려 한다. 
```java
class Solution {
    public int solution(int n, int k) {

        int ans = 0;
        // 1.Integer.toString 이용 바로 k진법으로 변환, 2.split으로 나눠서 배열로 받음
        String temp[] = Integer.toString(n, k).split("0");

        Loop : for(String t : temp) {
            // 0이 연속된 경우, 비어있음
            if(t.length() == 0) continue;
            long a = Long.parseLong(t);
            // 1은 소수가 아니라서 패스 
            if(a == 1) continue;
            // 나눠지는 것이 있으면 외곽의 Loop로 돌아감.
            for(int i=2; i<=Math.sqrt(a); i++)
                if(a%i == 0) continue Loop;

            ans++;
        }
        return ans;
    }
}
```

