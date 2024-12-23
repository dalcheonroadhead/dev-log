# 1. 문제 설명

[문제 링크](https://www.acmicpc.net/problem/1260)

# 2. 접근 방식

DFS, BFS 기본 문제이다. 

번호가 작은 노드 우선 방문이라서, DFS와 BFS 접근 전에, 각 정점 별로, 인접 정점 목록을 오름차순으로 정렬할 필요가 있었다. 

내가 헷갈렸던 부분은 DFS에서 언제 현 노드를 기록해야 하냐는 것이다. 현 노드를 출력하는 시점은 재귀를 통해 다음 노드로 가기 전이다. 왜냐하면, DFS 경로를 따라가며 정점을 방문한 흔적을 남겨야 하기 때문이다. 재귀 이후에 출력을 하면, 이미 지나갔다는 표시가 남지 않기에, 출력 순서가 제대로 되지 않는다. 

# 3. 코드 분석 

```java
import java.io.*;
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Collections;
import java.util.StringTokenizer;

public class Main {
    static ArrayList<Integer> [] list;
    static boolean [] isVisited;
    static int N,M,V;
    static StringBuilder sb = new StringBuilder();
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        V = Integer.parseInt(st.nextToken());
        list = new ArrayList[N + 1];

        for (int i = 1; i <= N; i++) {
            list[i] = new ArrayList<>();
        }

        for (int i = 0; i < M; i++) {
            st = new StringTokenizer(br.readLine());
            int start   = Integer.parseInt(st.nextToken());
            int end     = Integer.parseInt(st.nextToken());

            if(!list[start].contains(end)){
                list[start].add(end);
                list[end].add(start);
            }
        }
        for (int i = 1; i <= N; i++) {
            Collections.sort(list[i]);
        }

        isVisited = new boolean[N+1];
        dfs(V);
        sb.append("\n");
        isVisited = new boolean[N+1];
        bfs(V);

        System.out.println(sb);
    }

    public static void dfs (int now) {
        isVisited[now] = true;
        sb.append(now).append(" ");
        for (int i = 0; i < list[now].size(); i++) {
            int next = list[now].get(i);
            if(!isVisited[next]){
                dfs(next);
            }
        }
    }

    public static void bfs(int v) {
        ArrayDeque<Integer> aq1 = new ArrayDeque<>();
        aq1.add(v);
        isVisited[v] = true;
        while (!aq1.isEmpty()){
            int now = aq1.poll();
            sb.append(now).append(" ");
            for (int i = 0; i < list[now].size(); i++) {
                int next = list[now].get(i);
                if(!isVisited[next]){
                    isVisited[next] = true;
                    aq1.add(next);
                }
            }
        }
    }
}
```

