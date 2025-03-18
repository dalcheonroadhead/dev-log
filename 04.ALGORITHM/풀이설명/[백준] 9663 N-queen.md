# 1. 문제 설명 📌

## (1) 링크🔗

https://www.acmicpc.net/problem/9663



## (2) 해설🕵

체스에서 `Queen`은 8방향을 갈 수 있는 말이다. (사방 탐색 + 사방의 대각)
체스는 자신이 한 번의 움직임으로 갈 수 있는 경로에 있는 말을 죽일 수 있다.
이 Queen을 모든 행에 하나씩 둘 때, 서로가 서로를 죽일 수 없게 두는 경우의 수를 구하는 것이다. 

# 2. 생각의 흐름: 코드가 나오기까지 🗃️

## (1) IDEA 도출💡

**`KEY WORD`: `BACK TRACKING`**

`N`의 최대값이 15라서, 15*15 체스판에서 백트래킹을 해도 시간 초과가 나지 않는다. 따라서 **`백 트래킹`**을 활용한다. 

경로가 겹치는지 확인하기 위해서는 다음 4가지를 확인해야 한다.

- 1️⃣ 현재 Queen을 놓으려는 행에 이미 다른 Queen이 존재한다. 
- 2️⃣ 현재 Queen을 놓으려는 열에 이미 다른 Queen이 존재한다.
- 3️⃣ 현재 Queen을 놓으려는 곳의 오른쪽 사선 대각 경로에 Queen이 존재한다.
- 4️⃣ 현재 Queen을 놓으려는 곳의 왼쪽 사선 대각 경로에 Queen이 존재한다.

1️⃣번의 경우, 어짜피 행별로 하나의 Queen만을 놓을 것이기 때문에 따로 점검할 필요가 없다.2️⃣3️⃣4️⃣ 번의 경우, queen을 놓는 경로의 열, 좌 사선, 우 사선을 확인해야 한다. 

## (2) SUDO CODE 📜

- 0️⃣ `세팅`: N*N 체스판을 만든다. 우리는 행마다 하나의 queen을 놓으면서 다음 행으로 이동할 것이다. 만약 **모든 행에 queen을 놓았다면, 해당 경우의 수는 N-queen 문제의 답이 되는 경우의 수이므로 cnt + 1을 하고 넘어간다.**
- 1️⃣ 행을 하나 조회한다.
- 2️⃣ 해당 행의 열 중 하나의 queen을 놓는다.
- 3️⃣ 해당 좌표에 queen을 놓았을 시, 열, 좌 사선, 우 사선에 안되는 경우가 있는지 확인한다.
- 4️⃣ 그러한 경우가 있다면, 이전 행으로 재귀를 통해 되돌아간다. 만약에 안되는 경우가 없다면 다음 행으로 넘어간다.
- 5️⃣ 마지막 행까지 Queen을 놓는 경우의 수의 개수를 센다.

## (3) 시간복잡도 분석 🕓

`N`의 크기가 최대 15라서 시간복잡도를 분석하는 행위가 의미가 없다.

# 3. 구현 코드 🔎

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;


public class Main {
    static int N, sum = 0;
    static int [][] boards;
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        N = Integer.parseInt(br.readLine());
        boards = new int [N][N];
        garo(0);
        System.out.println(sum);
    }

    public static void garo (int depth) {

        if(depth == N) {
            sum++;
            return;
        }

        for (int i = 0; i < N; i++) {
            if(!sero(depth,i)) continue;
            if(!right_deGak(depth, i)) continue;
            if(!left_deGak(depth, i)) continue;
            boards[depth][i] = 1;
            garo(depth+1);
            boards[depth][i] = 0;
        }
    }

    public static boolean sero (int depth, int now) {
        for (int i = 0; i <= depth; i++) {
            if(boards[i][now] == 1) return false;
        }
        return true;
    }

    public static boolean right_deGak (int depth, int now) {
        int row = depth; int col = now;
        while (OOB(row,col) && row >= 0 && col >= 0) {
            if(boards[row][col] == 0) {
                row--; col--;
            }
            else return false;
        }
        row = depth; col = now;
        while (row < N && col < N){
            if(OOB(row,col) && boards[row][col] == 0) {
                row++; col++;
            }
            else return false;
        }
        return true;
    }

    public static boolean left_deGak (int depth, int now) {
        int row = depth; int col = now;
        while (OOB(row,col) && row >= 0 && col < N) {
            if(boards[row][col] == 0) {
                row--; col ++;
            }
            else return false;
        }
        row = depth; col = now;
        while (OOB(row,col) && row < N && col >= 0){
            if(boards[row][col] == 0) {
                row ++; col --;
            }
            else return false;
        }
        return true;
    }

    public static boolean OOB (int row, int col){
        return row >= 0 && col >= 0 && row < N && col < N;
    }
}

class Solution {
    static int N;
    static int [][] rate;
    static int [] nums;
    public void solution () throws  IOException {
        input();
        
    }
    public void input () throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        N = Integer.parseInt(br.readLine());
        rate = new int [N][4];
        for (int i = 0; i < N; i++) {
            StringTokenizer st = new StringTokenizer(br.readLine());
            rate[i][0] = Integer.parseInt(st.nextToken());
            rate[i][1] = Integer.parseInt(st.nextToken());
            rate[i][2] = Integer.parseInt(st.nextToken());
            rate[i][3] = Integer.parseInt(st.nextToken());
        }
    }
    
    public void cal_rate () {
        nums = new int [N];
        for (int i = 0; i < N; i++) {
            int A = rate[i][0];
            int B = rate[i][1];
            int a_rate = rate[i][2];
            int b_rate = rate[i][3];
            int GCD = GCD(a_rate, b_rate);
            if(nums[A] == 0 && nums[B] == 0) {
                nums[A] = a_rate/GCD;
                nums[B] = b_rate/GCD;
            }
        }
    }
    
    public int GCD(int a, int b) {
        int r = a%b;
        while (r != 0){
            a = b;
            b = r;
            r = a%b;
        }
        return b;
    }
}

```

# 4. 배운 것들 🎯

back-tracking 너무 하기 싫다!!!!!!!!!!!!

>  이모지 모음: 🤔, ➜ ✨ 0️⃣1️⃣2️⃣3️⃣4️⃣5️⃣6️⃣7️⃣8️⃣9️⃣🔟

