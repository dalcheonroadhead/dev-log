# 1. 문제 설명 

[문제 설명](https://school.programmers.co.kr/learn/courses/30/lessons/12952#)

# 2. 접근 방식

`KEY WORD` : `BACK-TRACKING`

 (0) `사전 세팅`
 1차원 배열(arr)을 n의 크기만큼 만들고 배열의 `index` = 행 , 배열의 `value` = 열로 생각한다. 
예를 들어 배열이 다음과 같을 때, 그림으로 나타내면 이렇게 된다. 

| index(행) | 0    | 1    | 2    | 3    |
| --------- | ---- | ---- | ---- | ---- |
| value(열) | 1    | 3    | 0    | 2    |

![image-20240806175414995](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스] N-queen/image-20240806175414995.png)

(1) 만약에 arr[i] = j 라고 한다면 2차원 배열 [i] [j] 에 퀸을 두겠다는 소리이다. 이게 가능한지 체크한다. 체크하는 방법은 
0 ~ i-1 까지의 배열 값을 이용해, 이전에 놔둔 퀸의 공격 경로와 겹치는지 확인하면 된다. 확인법은 다음과 같다. 

**(1-1) 좌하단 확인**
![image-20240806175752946](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스] N-queen/image-20240806175752946.png)

대각선이 일치하는 값들은 모두 행+열의 합이 같다. 이를 이용한다. 우리의 경우는 index가 행이고 value가 열이니까 
`index + arr[index]` 가 같은지 확인하면 된다. 

**(2) 우하단 확인**
![image-20240806175955180](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스] N-queen/image-20240806175955180.png)

우하단의 경우, 행끼리의 차와 열끼리의 차가 같으면 같은 대각선 상에 놓여있는 것이다. 

**(3) 같은 열에 있는지 확인**
배열의 value 가 열이었음으로, 같은 값을 가진 배열의 원소가 있는지 확인하면 된다.   

# 3. 코드 분석 

```java
import java.io.*;
import java.util.*;

class Solution {
    // index = 행, value = 열  
    static int [] board;
    static int cnt = 0;
    public int solution(int n) {
        board = new int [n];
        backTracking(0);
        return cnt;
    }
    
    public void backTracking(int depth) {
        
        if(depth == board.length){
            cnt++;
            return;
        }
        
        
        for(int i = 0; i < board.length; i++){
            board[depth] = i;
            if(possibility(depth)){backTracking(depth+1);}
        }   
    }
    
    // 현재 행에 퀸이 들어간 값이 되는지 안되는지 체크 (이전 값들을 이용 좌하단, 우하단, 세로)
    public boolean possibility(int index) {
        for(int i = 0; i < index; i ++) {
            // 좌하단
            if(board[index] + index == board[i] + i) return false; 
            // 우하단
            int diff = index - i; 
            if(board[i] + diff == board[index]) return false; 
            // 세로 
            if(board[i] == board[index]) return false;
        }
        return true;
    }
}
```



# 4. 성장 하기

### A. 처음에 고려했던 잘못된 접근 방식 - 2차원 배열에서 불가능한 칸 일일히 지우기 

(1) 체스판을 2차원 배열로 생각하고, 0행 부터 n-1행까지 차례대로 탐색한다. (재귀 이용하여 다음 행 탐색)
(2) 각 행에 퀸을 두었을 떄, 그 퀸의 공격 경로를 모두 `X` 표시 해두는 식으로 불가능한 칸을 지워나간다. 
(3-1) 만약 행을 탐색했는데 모든 칸이 `X` 표시 이면 해당 경우의 수에서는 N-queen 성립이 불가능 하므로, 이전 재귀로 돌아간다.
(3-2) 마지막 행까지 퀸을 두는데 성공했다면, 가능한 경우의 수를 하나 올린다.

![image-20240806174115402](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스] N-queen/image-20240806174115402.png)

하지만 해당 방법은 제대로 구현하는데 생각해야할 부분이 많고, 불가능한 칸을 지우는 과정에서 반복문이 많이 필요하기 때문에 비효율적이다. 

*(1)제대로 구현하는데 걸림돌* : 재귀에서 되돌아 올 때, 불가능한 칸을 다시 가능한 칸으로 바꾸는 경우

![image-20240806174547783](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스] N-queen/image-20240806174547783.png)

다음과 같이 Queen이 있는 경우를 생각해보자. 이때, 공격 경로가 겹친다. 
![image-20240806174717822](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스] N-queen/image-20240806174717822.png)

1,3에 있는 queen을 제거한다고 했을 때, 이 queen의 공격 경로는 모두 다시 가능한 경로로 check 시킬 것이다. 이때 (2,2)도 queen을 둘 수 있는 가능한 경로로 돌아가버리게 된다. 이러한 포인트에서 계산이 잘못되게 된다. 이를 방지하기 위해서, 불가능한 경로에 stack point (겹칠 때마다 +1,  경로 제거 시에는 - 1, 0이 되면 가능한 경로)로 표시할수도 있지만,  코드량과 생각해야할 게 많아져서 비효율적이다. 

(2) 불가능한 경로 체크, 다시 가능한 경로로 만드는데, 반복문이 엄청 들어감. 

아래는 A 방법으로 구성한 내 코드이다. 
나는 중복되는 불가능한 경로에 대해서 생각하지 않아서, 불가능한 경로 체크가 제대로 되지 않아, 백트래킹의 가지치기가 제대로 되지 않았다. 그 결과 시간 초과도 나고, 답도 틀렸다. 

```java
import java.io.*;
import java.util.*;

class Solution {
    
    static int [][] board;
    static int cnt = 0;

    public int solution(int n) {
        board = new int[n][n];
        backTracking(0);
        return cnt;
    }
    
    public void backTracking(int depth) {
        
        // for(int [] temp : board){
        //     System.out.println(Arrays.toString(temp));
        // }
        // System.out.println("-----------------------");
        
        // 1. 기저 조건
        if(depth == board.length) {
            cnt++;
            return;
        }
        
        // 2. 계산 조건
        for(int i = 0; i < board.length; i++){
            if(board[depth][i] == 0){
                board[depth][i] = 1; 
                left(   depth, i, 1);
                right(  depth, i, 1);
                sero(   depth, i, 1);
                backTracking(depth+1);
                board[depth][i] = 0; 
                left(   depth, i, 0);
                right(  depth, i, 0);
                sero(   depth, i, 0);
            }
        }
    }
    // 좌 대각
    public void left(int x, int y, int value) {
        while(x >=0 && x < board.length && y >= 0 && y < board.length ) {
            board[x][y] = value;
            x++; y--;
        }
    }
    // 우 대각
    public void right(int x, int y, int value) {
        while(x >=0 && x < board.length && y >= 0 && y < board.length) {
            board[x][y] = value; 
            x++; y++;
        }
    }
    // 세로 
    public void sero(int x, int y, int value) {
        for(int i = x; i < board.length; i++) {
            board[i][y] = value; 
        }
    }
}
```



