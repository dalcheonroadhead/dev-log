# 1. 문제 설명 📌

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/60060)

# 2. 구현 방법 🗃️

**`KEY WORD`: `트라이`**

- 0️⃣ **`중요한 처음 세팅!`**: Tri 구조를 만드는데 구성요소는 다음과 같다.
  	(a) `기본적인 자식 노드`: HashMap<Character, Node>로 구현 (자식 알파벳 이름, 그것의 객체)
  	(b) `lenMap`: 현재 조회 중인 문자 뒤로 부터 전부 물음표라고 쳤을 때, 그 와일드카드를 만족하는 문자가 
  				몇 개 있는지 세는 자료구조, HashMap<Integer, Integer>로 구현 (문자열의 길이, 만족하는 문자 개수)
- 1️⃣ **`입력`**: 문자열 그대로 받는 Tri 하나, 문자열을 뒤집어서 받는 Tri 하나씩 만들어 각각 따로 따로 입력 받음
         입력받으면서, 매번 LenMap의 값을 갱신한다.
- 2️⃣ **`찾기`**: 와일드카드 `?`를 만나기 전까지, 트라이를 깊게 들어간다. 반환값이 될 ans에 현재 조회중인 문자의 lenMap에서 현 문자열 길이를 가지는 값의 개수가 몇개 있는 항상 찾아서 새로 저장시킨다.

이 문제는 lenMap에 대해 이해 못하면 한 없이 어려워진다. 지금부터 구현 방법을 하나씩 해설 하겠다.

## (0) LenMap이란

![image-20250115025210831](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스] Lv4 가사검색/image-20250115025210831.png)

기본 트라이를 구현하면 위와 같다. 여기서 LenMap을 붙여 보겠다. (+ 해당 노드가 단어의 끝인지 확인하는 boolean값은 해당 문제에서 필요없어서 삭제함)

![image-20250115025837428](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스] Lv4 가사검색/image-20250115025837428.png)

각 노드 별 lenMap을 나타내었다. 각각의 원소는 key=value 형태로 나타내었다. 무슨 뜻인지 예를 들어 이해해보자.

 **맨 좌상단의 `f`**에 붙은 lenMap의 의미를 볼까? 이것은 ***`f????`***를 만족시키는 단어의 개수가 4개이고, ***`f?????`***를 만족시키는 단어는 1개 있다는 것이다.  **중간의 `a`**의 lenMap은 ***`fra??`***를  만족시키는 단어가 1개 존재한다는 것이다. 

매 문자를 조회할 때마다 그 문자의 lenMap에 **`map.put(문자열의 길이, 해당 key의 이전 value + 1)`**을 계산 해주면,  이것은 **이후 쿼리 계산 때, 현 문자 이후로 ? 만 온다고 쳤을 때, 그 물음표의 길이 별로 만족시킬 수 있는 단어의 개수를 의미하게 된다.**

### root 노드의 LenMap에도 꼬박꼬박 값을 계산해주는 이유 ✨ 

내가 한시간 반을 헤멘 부분이다. 이것을 해결하지 않으면 효율성 테스트 3번은 계속 통과 못한다. 바로 **`모든 문자가 와일드 카드로 이루어진 쿼리문에 대한 계산`**을 위해서 이다.  Root의 lenMap은 문자 없이 모든게 와일드 카드로 이루어진 쿼리문에 대한 계산을 위한 것이다.

## (1) 문자열을 뒤집어서 저장하는 트라이가 필요한 이유

해당 문제에서는 후미만 와일드 카드인 경우에 더불어 **선두가 와일드 카드인 경우**도 구하라고 한다. 

만약 **리버스 트라이 없이 값을 구하면** 접두사의 와일드 카드 수만큼 한 DEPTH에 26개의 자식을 돌아봐야 한다. 이는 단어의 길이가 10000개 일 수 있음으로 최악의 경우 $26^{10000}$의 계산을 시행하게 하므로 필히 **`시간 초과`**를 일으킨다.

따라서 문자열을 뒤집어 저장하는 리버스 트라이가 필요하다. 가령 문제의 예제 처럼 `"????o"`를 구한다고 해보자. 입력을 리버스 트라이로 저장한 형태는 다음과 같다.

![image-20250115031445296](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스] Lv4 가사검색/image-20250115031445296.png)

위와 같이 `o`가 접두사가 되어 **단어 후미가 와일드카드인 경우와 똑같이** 계산해주면 된다. (그림은 귀찮아서 뒤집었을 때, o가 앞으로 오는 것만 그렸다. 실제로는 더 많은 노드가 있다.)

## (2) 찾기

찾기는 진짜 물음표를 만나기 전까지, 매 노드에서 현 노드가 마지막 단어일 것이라 생각하고 입력으로 들어온 str의 길이만큼의 lenMap value가 얼마인지 확인해주면 된다.

## (1) 시간복잡도 분석 🕓

만약 ?가 있을 경우, 자식 알파벳을 모두 확인했을 경우 $O(26^{h})$의 시간이 들것이다. (`h` = 트라이의 높이)

하지만 위와 같이 구하면 정석 트라이대로 $O(NM)$의 시간이 든다. (`N` = 쿼리의 개수, `M` = 문자열의 길이)

# 3. 코드 소개 🔎

```java
import java.io.*;
import java.util.*;


class Tri {
    Node root; 
    
    public Tri() {
        root = new Node();
    }
    
    public void insert(String str) {
        Node now = this.root;
        for(int i = 0; i < str.length(); i++){
            char c = str.charAt(i);
            now.children.putIfAbsent(c, new Node());
            now.lenMap.compute(str.length(), (k,ov) -> ov == null? 1 : ov + 1);
            now = now.children.get(c);
        }
    }
    
    public int find(String str) {
        int ans = 0;
        Node now = this.root;
        
        if(str.charAt(0) == '?') ans = now.lenMap.getOrDefault(str.length(), 0);
        
        for(int i = 0; i < str.length(); i++){
            char c = str.charAt(i);
            if(c =='?') break;
            if(!now.children.containsKey(c)) return 0; // 이런 문자 가지고 있지 않음.
            now = now.children.get(c);
            ans = now.lenMap.getOrDefault(str.length(),0);
        }
        return ans;
    }
}

class Node {
    HashMap<Character, Node> children;
    HashMap<Integer, Integer> lenMap;
    
    public Node() {
        children = new HashMap<>();
        lenMap = new HashMap<>();
    }
}

class Solution {
    public int[] solution(String[] words, String[] queries) {
        int[] answer = new int [queries.length];
        Tri tri = new Tri();
        Tri irt = new Tri();
        
        for(int i = 0; i < words.length; i++){
            tri.insert(words[i]);
            irt.insert(reverse(words[i]));
        }
        
        for(int i = 0; i < queries.length; i++){
            String now = queries[i];
            if(now.charAt(0) != '?') {
                answer[i] = tri.find(now);
            }else{
                answer[i] = irt.find(reverse(now));
            }
        }
        
        return answer;
    }
    
    public String reverse (String str) {
        return new StringBuilder(str).reverse().toString();
    }
}
```

## (1) insert 부분

```java
    public void insert(String str) {
        Node now = this.root;
        for(int i = 0; i < str.length(); i++){
            char c = str.charAt(i);
            now.children.putIfAbsent(c, new Node());
            now.lenMap.compute(str.length(), (k,ov) -> ov == null? 1 : ov + 1);
            now = now.children.get(c);
        }
    }
```

`compute` 함수를 활용해, null인 경우에 대한 계산도 다른 코드 없이 했다.

## (2) find 부분

```java
    public int find(String str) {
        int ans = 0;
        Node now = this.root;
        
        if(str.charAt(0) == '?') ans = now.lenMap.getOrDefault(str.length(), 0);
        
        for(int i = 0; i < str.length(); i++){
            char c = str.charAt(i);
            if(c =='?') break;
            if(!now.children.containsKey(c)) return 0; // 이런 문자 가지고 있지 않음.
            now = now.children.get(c);
            ans = now.lenMap.getOrDefault(str.length(),0);
        }
        return ans;
    }
```

`getOrDefault()`로 null Exception을 피했다.

# 4. 배운 것들 🎯

![image-20250115032334327](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[프로그래머스] Lv4 가사검색/image-20250115032334327.png)

## (1) 발목을 잡는 효율성

맨 처음 코드는 와일드 카드 `?`만날 시 모든 자식 알파벳을 뒤져서 확인하는 것으로 했다. 정확도는 다 맞혔지만, 효율성 1,2,3을 돌파하지 못했다.

```java
import java.io.*;
import java.util.*;

class Solution {
    public int[] solution(String[] words, String[] queries) {
        int[] answer = new int [queries.length];
        Tri tri = new Tri();
        
        for(int i = 0; i < words.length; i++){
            tri.insert(words[i]);
        }
        
        for(int i = 0; i < queries.length; i++){
            String now = queries[i];
            if(now.charAt(0) != '?') {
                answer[i] = tri.cntPrefix(now);
            }else{
                answer[i] = tri.cntSuffix(now);
            }
        }
        
        return answer;
    }
}

class Tri {
    Node root; 
    
    public Tri() {
        root = new Node();
    }
    
    public void insert(String str) {
        Node now = this.root;
        
        for(int i = 0; i < str.length(); i++){
            char c = str.charAt(i);
            now.children.putIfAbsent(c, new Node());
            now = now.children.get(c);
        }
        now.isEnd = true;
    }
    
    public int cntPrefix(String query) {
        int ans = 0;
        Node now = this.root;
        // 필수적인 문자 다 들어갔는지 확인
        int i = 0;
        for(; i < query.length(); i++){
            char c = query.charAt(i);
            if(c == '?') break;
            if(!now.children.containsKey(c)) return 0;  // 쿼리의 문자부분을 만족하지 못한다는 뜻
            now = now.children.get(c);
        }
        ans += cntPrefix(query.length() - i, now);
        return ans;
    }
    
    private int cntPrefix(int leftover, Node now){
        
        if(leftover == 0 && now.isEnd) return 1;
        
        int cnt = 0;
        for(Character key : now.children.keySet()){
            Node child = now.children.get(key);
            cnt += cntPrefix(leftover-1, child);
        }
        
        return cnt;
    }
    
    public int cntSuffix(String str) {
        Node now = this.root;
        int wild_card_cnt = 0;
        String left = "";
        for(int i = 0; i < str.length(); i++){
            if(str.charAt(i) == '?') wild_card_cnt++;
            else left += str.charAt(i);
        }
        return cntSuffix(now, wild_card_cnt, left);
    }
    
    private int cntSuffix ( Node now, int leftover, String left) {
        // 기저 조건
        if(leftover == 0 && left.length() == 0 && now.isEnd) return 1;
        if(leftover == 0 && left.length() != 0){
            for(int i = 0; i < left.length(); i++){
                Character c = left.charAt(i);
                if(now.children.containsKey(c)) {
                    now = now.children.get(c);
                }else {
                    return 0;
                }
            }
            if(now.isEnd) return 1;
        }
        // 계산
        int ans = 0;
        for(Character key : now.children.keySet()) {
            Node child = now.children.get(key);
            ans += cntSuffix(child, leftover-1, left);            
        }
        return ans;
    }
}

class Node {
    HashMap<Character, Node> children; 
    boolean isEnd;
    
    public Node() {
        children = new HashMap<>();
        isEnd = false;
    }
}
```

lenMap 자료구조 활용을 보며, 시야를 넓혔다.