# 1. 문제 설명 📌

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/72413)

무지랑 어피치랑 야근을 끝내고, 집에 갈려고함. 
버스가 끊겨서 택시를 타야 하는데, 택시 요금이 걱정됨. 
마침 무지랑 어피치가 집 가는 방향이 같은 쪽이라, 최대한 합승해서 전체 택시 비용을 줄이려고 함. 다만 합승하지 않고 가는게 전체 택시 비용이 더 작을 경우, 그 경우의 수를 선택함.

회사 위치 : s, 무지 집: a, 어피치 집: b 의 위치가 주어진다고 치자.
어피치와 무지의 택시 요금을 전부 합쳤을 때, 최소 택시 요금을 반환하라.

`힌트`만 얻고 가실 분들은 접근 방식까지만 보고 가시길 바라고, `풀이법을 보실 분들`은 끝까지 읽어주시라

# 2. 접근 방식 🗃️

`KEY WORD`: `다익스트라`

해당 문제가 다익스트라인 것을 알아채기는 쉽지만 거기에 더해 아이디어가 필요하다. 왜냐하면 그저 최소 택시 비용을 반환하라 했기에, 합승 없이 처음부터 따로 택시를 탔을 때의 요금이 최소인 경우도 있기 때문이다. 

## (1) 처음 내가 떠올린 풀이

- `출발지 ➜ 무지집`, `출발지 ➜ 어피치집` 최소 비용을 각각 다익스트라로 구한다. 
- 그 둘 중 경로가 겹치는 것이 있는지 확인한다.
- 겹친 경로만큼의 비용을 빼서 중복을 줄인다.

하지만 해당 풀이는 다음과 같은 이유로 구현하지 못했고 맞는 풀이도 아니다. 
첫째, `다익스트라 2개 비용의 합` - `중복 경로의 비용`이 최소 비용이 맞는가? 아닐 수 있다. 왜냐하면 경유지를 최대한 많이 활용해서 최소 비용을 더 줄이는 경우의 수가 있을 수 있기 때문이다. 
둘째, 중복 경로 찾기가 너무 까다로웠다. 

그래서 다른 사람의 풀이를 봤고, 접근 방식은 다음과 같다. 

## (2) 제대로 된 풀이

- 정점 중에서 `경유지`를 하나 고른다.
- `경유지까지 같이 갈 때 최소 비용` + `경유지 ➜ 어피지집 최소 비용` + `경유지 ➜ 무지집 최소 비용` 중 최소 비용 반환



해당 풀이가 제대로 된 이유는 다음과 같다. 

- `경유지를 하나도 안 쓴 경우`부터 `경유지를 최대한 활용한 경우`의 비용을 모두 확인할 수 있다. 
  왜냐하면 경유지는 `출발지`, `무지집`,`어피치집` 모두 가능하기 떄문이다. 그래서 **무지집이 마침 어피치집 가는 방향이라 무지집에서 무지 내려주고 택시 그대로 타고 집까지 가는 경우**, **출발지부터 따로따로 택시 타고 집가는 경우** 모두 확인이 가능하다. 

# 3. 코드 소개 🔎

## (0) 사전 지식

**a. 다익스트라 구현 방법에 대한 이해**: 기본적인 다익스트라만 사용하므로 따로 다익스트라 개념 설명은 하지 않겠다. 개념이 궁금하신 분은 다음 설명 링크를 참조하시길 바란다. [다익스트라 개념 설명](https://dalcheonroadhead.tistory.com/492), [다익스트라 구현 설명](https://dalcheonroadhead.tistory.com/503)

## (1) 전체 코드

```java
import java.io.*;
import java.util.*;

class Solution {
    static ArrayList<Vertex> [] lists;

    public int solution(int n, int s, int a, int b, int[][] fares) {
        // 0. initialize
        lists = new ArrayList [n+1];
        for(int i = 1; i < lists.length; i++){
            lists[i] = new ArrayList<>();
        }
        
        for(int i = 0; i <fares.length; i++){
            int start = fares[i][0];
            int end = fares[i][1];
            int weight = fares[i][2];
            lists[start].add(new Vertex(end, weight));
            lists[end].add(new Vertex(start, weight));
        }
        // 1. actual calculation
        int [] togther = dijstra(s);
        int ans = 0;
        for(int i = 1; i <= n; i++){            
            int [] alone = dijstra(i);
            ans = Math.min(ans, togther[i]+alone[a]+alone[b]);
        }
        return ans;
        
    }
    
    public int [] dijstra(int start){
        PriorityQueue<Vertex> pq = new PriorityQueue<>((o1,o2) -> o1.cost - o2.cost);
        int [] dist = new int [lists.length];
        Arrays.fill(dist, Integer.MAX_VALUE);
        pq.add(new Vertex(start, 0));
        dist[start] = 0;
        
        while(!pq.isEmpty()){
            Vertex now = pq.poll();
            if(now.cost > dist[now.idx]) continue; 
          
            for(int i = 0; i < lists[now.idx].size(); i++){
                int nv = lists[now.idx].get(i).idx;
                int nDist = dist[now.idx] + lists[now.idx].get(i).cost; // (현재 정점까지의 최단거리 + 현 정점에서 nv까지의 간선 비용)
                
                if(dist[nv] > nDist){
                    dist[nv] = nDist;
                    pq.add(new Vertex(nv, nDist));
                }
            }
        }
        
        return dist;
    }
}

class Vertex {
    int idx;
    int cost;

    
    public Vertex (int idx, int cost){
        this.idx = idx;
        this.cost = cost;
    }
}
```

## (3) 해체 분석

`a. togther[] 배열`: 해당 배열은 `출발지➜ 경유지`까지의 최단 거리를 담은 배열이다. 각 배열의 index가 경유지가 될 정점의 번호이고, value는 해당 정점까지의 최단 거리이다.

`b. alone[] 배열`:  경유지 i를 정하고 거기서부터 모든 정점까지의 최단거리를 저장한 배열이다. 이는 **혼자서 택시타고 가는 비용**을 뜻한다. 

`c.ans`: `together[i] + alone[a] + alone[b]`이다. i란 경유지까지 간 비용 + 따로 간 비용들의 합 이다. i는 출발지는 물론이고 무지집, 어피치집 다 될 수 있어서, 따로 예외처리 안해도 모든 경우의 수를 다 들여다 볼 수 있다.

# 4. 배운 것들 🎯

다익스트라 활용법에 대한 시야가 트인 것 같다.