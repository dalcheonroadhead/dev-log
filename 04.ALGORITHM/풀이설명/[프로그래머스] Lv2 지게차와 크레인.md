# 1. 문제 설명 📌

## (1) 링크🔗

https://school.programmers.co.kr/learn/courses/30/lessons/388353

## (2) 해설🕵

해당 문제는 지게차와 크레인의 작동 원리를 이해하는 것이 중요하다.

- **`지게차`**: 박스의 4개의 면 중 한 면이라도 외부의 공기와 맞닿아 있으면 그것을 빼낼 수 있음. 만약 4면이 모두 다른 박스로 막혀 있다면 해당 박스는 지게차로 못 꺼낸다.

- `크레인`: 어디에 있든 명령 받은 박스와 같은 유형의 박스이면 꺼낼 수 있다.

# 2. 생각의 흐름: 코드가 나오기까지 🗃️

## (1) IDEA 도출💡

**`KEY WORD`: `BFS`, `BRUTE FORCE`**

### 1️⃣ 지게차 구현에 대하여,

지게차의 작동원리를 재현하는 알고리즘으로 **`BFS`**를 택했다. 

![image-20250408120308144](https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-04/image-20250408120308144.png)

외부에 한 면을 더 그려서 BFS를 타면 겉면이 외부의 공기에 맞닿는 면을 쉽게 찾을 수 있기 때문이다.



**주의해야 할 점!**

BFS를 돌면서 Target Box를 빈 공간을 뜻하는 '_'로 직접 바꿔준다면, BFS를 도는 와중에도 실시간으로 공기와 맞닿는 박스가 무엇인지가 달라지게 된다. 이렇게 되면, 지게차가 절대 접근할 수 없는 박스마저 처리하게 되므로, 답이 달라진다.  따라서 **필자는 ** `지게차가 Target box를 만났다.` ➜ `TargetBox 자리를 알파벳에서 '-'`로 바꿔준다. ➜ `BFS가 모두 끝난 마지막에 '-'를 '_'로 바꾼다.` 로 바꾸어, 같은 BFS 내에서는 같은 박스 면적까지만 접근할 수 있도록 하였다. 
  `'-'`는 **임시 표시**로 `현 BFS에 의해 지어졌지만, 빈공간은 아님! `을 나타내는 문자 기호 이다.



### 2️⃣ 크레인에 대하여

크레인은 그냥 O($ N^2 $)인 brute force를 그대로 이용했다. 왜냐하면 창고의 가로와 세로가 최대 50이라, 최악의 경우가 250개를 매번 들여다 보는 것이기 때문에 $N^2$ 의 시간복잡도를 쓴다고 해서, 시간초과가 나지 않을 것이라 판단했기 때문이다. 

## (2) SUDO CODE 📜

```plaintxt
초기화 함수 {
	0. 입력 받은 storage 크기 만큼 wareHouse라는 2차원 배열 생성.
	1. 입력으로 받은 1차원 배열을 한칸의 테두리가 존재하는 2차원 배열의 중앙에 집어넣기
	2. 빈 공간과 박스 공간의 구별을 위하여 빈 공간은 '_'로 채우기
	3. 방향 배열을 만들어 넣어놓기
}

지게차 함수 {
	1. wareHouse의 0.0부터 시작해서 BFS를 돈다. 
	2. 현재 들어온 명령어와 같은 박스를 만나면 해당 박스를 '_'로 만들고 전체 잔여 개수도 1 줄인다.
}

크레인 함수 {
	1. 2차원 배열을 일일히 돌면서 원하는 값을 찾는다.
	2. 현재 들어온 명령어와 같은 박스를 만나면 해당 박스를 '_'로 만들고 전체 잔여 개수도 1 줄인다.
}
```

## (3) 시간복잡도 분석 🕓

### 1️⃣ 지게차에 대한 분석

- 노드의 개수 : $N*M$ (N은 2차원 배열의 행이며, M은 열d이다.), 
- 간선의 개수: $NM 당 평균 4개 $ 하지만 한 번 방문했던 곳은 다음에 방문하지 않으므로, 실제 BFS를 돌리면 이거보다 더 적은 간선만 만날 것이다. 
- 최종: $O(NM * 2)$

### 2️⃣ 크레인에 대한 분석

- 최종: $O(NM)$

 따라서 최종적으로 **$O(NM*2 + NM)$**

# 3. 구현 코드 🔎

```java
import java.io.*;
import java.util.*;

class Solution {
    
    int cnt;
    char [][] wareHouse;
    int  [][] dir = new int [][] {{0,1},{0,-1},{1,0},{-1,0}};;
    
    public int solution(String[] storage, String[] requests) {
        init(storage);
        
        for(int i = 0; i < requests.length; i++){
            if(requests[i].length() == 1) fork_lift(requests[i].charAt(0));
            if(requests[i].length() == 2) craine(requests[i].charAt(0));
        }
        
        return cnt;
    }
    
    private void init(String [] storage) {
        
        cnt = storage.length * storage[0].length();
        wareHouse = new char[storage.length + 2][storage[0].length() + 2];
        
        for(int i = 0; i < storage.length + 2; i ++) {
            Arrays.fill(wareHouse[i], '_');
        }
        
        for(int i = 1; i <= storage.length; i++) {
            for(int j = 1; j <= storage[0].length(); j++) {
                wareHouse[i][j] = storage[i-1].charAt(j-1);
            }
        }
    }
    
    public void fork_lift(char order) {
        boolean [][] isVisited = new boolean [wareHouse.length][wareHouse[0].length];
        ArrayDeque<Coordinate> aq1 = new ArrayDeque<>();
        aq1.add(new Coordinate(0,0));
        isVisited[0][0] = true;
        
        while(!aq1.isEmpty()) {
            Coordinate now = aq1.poll();
            
            for(int i = 0; i < 4; i++){
                int nextR = now.row + dir[i][0];
                int nextC = now.col + dir[i][1];
                
                if(!OOB(nextR, nextC) && !isVisited[nextR][nextC]){
                    if(wareHouse[nextR][nextC] == order){
                        wareHouse[nextR][nextC] = '-';
                        cnt--;
                        isVisited[nextR][nextC] = true;
                    }
                    if(wareHouse[nextR][nextC] == '_'){
                        isVisited[nextR][nextC] = true;
                        aq1.add(new Coordinate(nextR, nextC));                   
                    }
                }
            }
        }
        for(int i = 1; i < wareHouse.length-1; i++){
            for(int j = 1; j < wareHouse[0].length-1; j++){
                if(wareHouse[i][j] == '-') wareHouse[i][j] = '_';
            }
        }
        
        return;
    }
    
    public void craine (char order) {
        for(int i = 1; i < wareHouse.length-1; i++){
            for(int j = 1; j < wareHouse[0].length-1; j++){
                if(wareHouse[i][j] == order){
                    cnt --;
                    wareHouse[i][j] = '_';
                }
            }
        }
    }
    
    public boolean OOB(int r, int c) {
        return r < 0 || c < 0 || r >= wareHouse.length || c >= wareHouse[0].length;
    }
}

class Coordinate {
    int row;
    int col;
    
    Coordinate (int row, int col) {
        this.row = row;
        this.col = col;
    }
}
```

# 4. 배운 것들 🎯

다른 사람들의 풀이를 보니, `Target Box 확인` ➜ `안 지우고 List나 queue 자료 구조에 좌표 표시` ➜ `BFS 끝난 뒤 지욱` 형식으로 구현한 것이 많았다. 

이렇게 하면 나처럼 나중에 `'-'`인 곳을 `'_'`로 바꾸기 위해 다시 전체 2차원 배열을 돌 필요가 없기 때문에 더 최적화 될 것 같다.

>  이모지 모음: 🤔, ➜ ✨ 0️⃣1️⃣2️⃣3️⃣4️⃣5️⃣6️⃣7️⃣8️⃣9️⃣🔟

