# 1. 문제 설명 📌

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/250136)

문제 내용이 직관적이기 때문에 부가 설명은 생략하겠다.

# 2. 접근 방식 🗃️

`KEY WORD`: `BFS`

- `oils`라는 1차원 배열을 만든다. 해당 배열의 index 는 land의 열이고, value는 열 당 얻을 수 있는 석유의 양이다.
- land 전체에 대해서 이중 반복문으로 석유(`1`)이 있는 위치를 찾는다
- 만약 석유를 찾는다면 해당 위치부터해서 연결된 석유 덩어리를 `BFS`로 찾는다.
- BFS로 해당 위치에서 시작해 석유 덩어리를 모두 찾았으면, 지금까지 거친 적 있는 열에 지금까지 찾은 석유량을 더한다. 
  (예를 들어, 열을 1,2,3 거쳤고, 찾은 석유량이 7이면 oils[1] += 7, oils[2] += 7, oils[3] += 7 이 된다.) 

# 3. 코드 소개 🔎

먼저 전체 코드를 보여주고, 하나 하나 세세하게 분석하겠다. 

## (0) 전체 코드 소개

```java
import java.io.*;
import java.util.*;

class Solution {
    // 1. make one dimension array which represent land array's column to store amount oil in each column; 
    // 2. return Max amount 
    static int [] oils;
    static int [][] dir = {{0,1},{0,-1},{1,0},{-1,0}};
    public int solution(int[][] land) {
        oils = new int[land[0].length];
        for(int i = 0; i < land.length; i++){
            for(int j = 0; j < land[i].length; j++){
                if(land[i][j] == 1) bfs(i,j,land);
            }
        }
        Arrays.sort(oils);
        return oils[oils.length-1];
    }
    

    
    
    public void bfs (int r, int c, int [][] land){
        // 0 = soil, 1 = oil, 2 = visited already
        ArrayDeque<Coordinate> aq1 = new ArrayDeque<>();
        HashSet<Integer> set = new HashSet<>();
        aq1.add(new Coordinate(r,c));
        set.add(c);
        land[r][c] = 2;
        int acc = 1;
        while(!aq1.isEmpty()){
            Coordinate now = aq1.poll();
            
            for(int i = 0; i < 4; i++){
                int nr = now.r + dir[i][0];
                int nc = now.c + dir[i][1];
                
                if(nr >= land.length || nr < 0 || nc >= land[0].length || nc < 0) continue;
                if(land[nr][nc] == 0 || land[nr][nc] == 2) continue;
                acc++;
                land[nr][nc] = 2;
                set.add(nc);
                aq1.add(new Coordinate(nr,nc));
            }
        }
        
        for(int column : set){
            oils[column] += acc;
        }
    }
}

class Coordinate {
    int r; 
    int c; 
    
    public Coordinate (int r, int c){
        this.r = r;
        this.c = c;
    }
}
```

## (1) BFS 부분

```java
public void bfs (int r, int c, int [][] land){
    // 0 = soil, 1 = oil, 2 = visited already
    ArrayDeque<Coordinate> aq1 = new ArrayDeque<>();
    HashSet<Integer> set = new HashSet<>();
    aq1.add(new Coordinate(r,c));
    set.add(c);
    land[r][c] = 2;
    int acc = 1;
    while(!aq1.isEmpty()){
        Coordinate now = aq1.poll();
        
        for(int i = 0; i < 4; i++){
            int nr = now.r + dir[i][0];
            int nc = now.c + dir[i][1];
            
            if(nr >= land.length || nr < 0 || nc >= land[0].length || nc < 0) continue;
            if(land[nr][nc] == 0 || land[nr][nc] == 2) continue;
            acc++;
            land[nr][nc] = 2;
            set.add(nc);
            aq1.add(new Coordinate(nr,nc));
        }
    }
    
    for(int column : set){
        oils[column] += acc;
    }
}
```

일반 BFS랑 똑같지만 한가지 다른 점은 이제까지 거쳤던 열을 HashSet에 저장해놨다가 마지막에 해당 열에 해당하는 oils 원소에 찾은 석유량을 더한다는 점이다. 
 Coordinate 클래스는 그저 좌표를 저장한 클래스이고, 마지막에 최대량을 구하기 위해 oils 배열을 오름차순으로 정렬하고 마지막 값을 반환했다. 이제껏 풀었던 Lv2에 비해서 쉬운 문제였다.

# 4. 배운 것들 🎯

시간 복잡도에 대해서 공부를 했다고 생각했는데, 아직 부족한 듯 하다. 첫 풀이는 정확성 테스트에서 만점이었으나, 효율성 테스트를 전부 틀리며 반타작했다. 이제껏 풀었던 코딩 테스트에서 이러한 이유로 불합격을 많이 받지 않았을까? 나와 같이 시간복잡도 때문에 골머리를 썩는 분들이 계실거라 생각해 내 첫 풀이와 안 되었던 이유, 시간 복잡도 계산에서 했던 착각을 공유한다. 

## (1) 첫 풀이 소개

접근 방식은 다음과 같다.

- 진짜 석유 시추하듯이 모든 열을 0 ~N까지 내려가면서 탐색한다. 이때 구한 석유량을 모두 더한다. 
- land를 쓰면, 열마다 계산이 중첩되므로 copy라는 복사 배열을 만들어 매번 BFS를 새롭게 했다.
- 열마다 구했을 때, 석유량이 가장 큰 값을 찾는다. 

```java 
import java.io.*;
import java.util.*;

class Solution {
    // 1. choose a column;
    // 2. use BFS which contact with a column we choose;
    
    static int [][] dir = {{0,1},{0,-1},{1,0},{-1,0}};
    
    public int solution(int[][] land) {
        int ans = 0;
        for(int i = 0; i < land[0].length; i++){
            int [][] copy = new int[land.length][land[0].length];
            doCopy(land, copy);
            ans = Math.max(ans, bfs(i, copy));
        }
        return ans;
    }
    
    public void doCopy(int [][] original, int [][] copy){
        for(int i = 0; i < original.length; i++){
            System.arraycopy(original[i], 0, copy[i], 0, original[i].length);
        }
    }
    
    
    // BFS for each column;
    public int bfs (int start, int [][] land){
        // 0 = soil, 1 = oil, 2 = visited already
        ArrayDeque<Coordinate> aq1 = new ArrayDeque<>();
        int acc = 0;
        for(int i = 0; i < land.length; i++){
            if(land[i][start] == 1){
                land[i][start] = 2;
                aq1.add(new Coordinate(i,start));
                acc++;
                while(!aq1.isEmpty()){
                    Coordinate now = aq1.poll();
                    for(int j = 0; j < 4; j++){
                        int nr = now.r + dir[j][0];
                        int nc = now.c + dir[j][1];
                        
                        if(nr >= land.length || nr < 0 || nc >= land[0].length || nc < 0) continue; 
                        if(land[nr][nc] == 0 || land[nr][nc] == 2) continue;
                        
                        land[nr][nc] = 2;
                        aq1.add(new Coordinate(nr,nc));
                        acc++;
                    }
                }
            }
        }
        return acc; 
    }
}

class Coordinate {
    int r; 
    int c; 
    
    public Coordinate (int r, int c){
        this.r = r;
        this.c = c;
    }
}
```

## (2) 내 풀이가 시간초과가 났던 이유 

### a. 배열을 복사했던 것

문제의 행과 열의 최댓값이 500이다. 일단 배열 복사에서부터 시간초과가 난다. 나는 열마다 매번 새로운 2차원 배열을 생성해야 했으므로, 500 * 500 * 500 을 해야해서 1.25억번의 계산을 한다. 이는 1초 1억번 계산 이내로 해야하는 코딩 테스트의 계산 횟수 범위를 훨씬 뛰어넘는다.

### b. BFS를 매번 초기화해서 새롭게 했던 것

만약 land가 모두 1로 이루어진 땅이라 생각해보자. 그러면 행렬이 각각 500이라면 열(500)마다 BFS(500*500)을 매번 다시 해야한다. 이것도 1.25억번의 연산이 이루어져 시간 초과가 난다.

따라서 시간 초과가 날 수 밖에 없던 것이다. 

## (3) 시간 복잡도 계산과 관하여

### a. 현재 계산도 2차원 배열로 원소 탐색 및 석유면 BFS 진행인데, 시간복잡도가 괜찮나요? 🤔💭

언뜻 보면 현재 풀이도 2차원 배열로 석유의 시작 위치를 탐색하고, 거기서 부터 다시 BFS를 진행해서 연속된 것으로 보여 O(n * m * n * m)이지 않은지 의문이 들 수 있다. 하지만 내 정답 풀이는 **이중 반복문과 BFS가 연속(중첩) 되지 않는다.** 왜냐하면 만약 모든 땅이 석유라서 land[1] [1] 에서 BFS를 돈 경우, 해당 경우 한 번만 BFS가 활용되고 이후, BFS를 사용하지 않기 때문이다. 즉 **2차원 배열 원소마다 BFS를 실행하지 않기 때문에** 두 계산은 상호 독립적이다. 
 반면 내가 시간초과가 났던 풀이는 모든 열마다 BFS를 다시 한다. 즉 정답 풀이는 BFS와 **이중반복문이 독립적**이라 둘 중 제일 시간이 많이 드는 이중 반복문이 최악의 시간복잡도인 반면, 시간초과가 난 풀이는 **BFS와 이중 반복문이 연속적(중첩)**이어서 둘의 시간복잡도를 곱해야 최종 시간복잡도가 나온다. 

### b. 연속적이다. vs 독립적이다.

연속적이라 하면 위처럼 계산과 계산의 인과관계가 필연적이라는 것이다. 시간초과가 난 풀이에서 이중반복문 후에는 무조건 BFS가 다시 실행되어야 한다. 반면 계산과 계산이 독립적이라면, 둘 사이의 인과관계가 필연적이지 않다. 정답 풀이에서는 이중 반복문 후에 BFS를 실행하지 않는 경우도 있고, 실행하는 경우도 있는데, 이 주기가 규칙적이지도 않고 때에 따라 다르다.