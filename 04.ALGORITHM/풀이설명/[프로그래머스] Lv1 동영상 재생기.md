# 1. 문제 설명 📌

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/340213?language=java)

동영상 재생기의 작동을 구현하는 간단한 문제이다. 
`skip`: 현재 user의 위치가 오프닝 구간 안이면, 오프닝이 끝나는 지점으로 JUMP 한다. 
`next`: 10초 뒤로 위치를 움직인다. 만약 남은 동영상 시간이 10초 미만이면 동영상의 마지막 위치로 간다.
`prev`: 10초 앞으로 위치를 움직인다. 만약에 현 위치가 10초 미만인 상태에서 prev를 누르면 동영상 맨 처음 위치로 움직인다. (첫 위치 0분 0초)

# 2. 접근 방식 🗃️

`KEY WORD`: `SIMULATION`

그냥 구현 문제이다. 한 가지 까다로운 것은 입력이 String으로 주어지고, 그것을 int로 변환해 작업을 수행해야 하는 점과 답변은 또 String 형태로 변환하여 해야하는 점이다. 

## (1) 첫 번째 접근 방식: Class 이용 

첫 번째는 Video라는 분(mm)과 초(ss)를 멤버 변수로 가진 클래스의 객체를 만들어서 문제를 풀었다. 하지만 이 방식은 문제를 어렵게 푼 방식이었다. 왜냐하면 **60초를 초과하거나, 0초 미만으로 떨어져 분이 바뀌는 것**을 일일히 생각해야 했기 때문이다. 또한 **오프닝 구간인지 계산**도 복잡했다. 따라서 해당 방식은 추천하지 않는다. 

## (2) 두 번째 접근 방식:  분을 초로 변환하여 계산 

해당 방식을 활용하면, `prev`, `next` ,`skip` 계산이 간단한 산수로 쉬워져서 문제 풀기 훨씬 수월하다. 필자는 1번 방식으로 문제를 푼 뒤, 다른 분들의 풀이에서 힌트를 얻어 2번 방식으로 문제를 다시 풀었다. 2번 방식으로 풀며, `문제를 쉽게 푸는 방식을 생각하는 사고`가 얼마나 중요한지 알게 되었다. 

이제 두 가지 코드를 소개 하겠다.

# 3. 코드 소개 🔎

## (0) 사전 지식 

`StringTokenizer`를 활용할 줄 알아야 한다. 백준에서 문제 풀다보면 자주 활용하는 클래스 이기에 따로 걱정은 안하지만, `Spring.split("delim")`처럼 delim으로 값을 구분하여 문자열을 자르는 부분을 잘 모르시는 분들이 많다. 다음은 필자가 아는 선에서 간략하게 **StringTokenizer**에 대해 설명하려고 한다. 더 자세히 알고 싶은 분은 검색해보시기 바란다. 

```java
// 1. 인수 1개 : 이때 delim은 자동으로 " "(공백)이 된다.
StringTokenizer st = new StringTokenizer(target);
StringTokenizer st = new StringTokenizer("월/화 수 목 금 토/일"); // "월화", "수", "목", "금", "토/일"

// 2. 인수 2개 : 설정한 delim을 기준으로 문자열을 자른다.
StringTokenizer st = new StringTokenizer(target, delim);
StringTokenizer st = new StringTokenizer("월/화 수 목 금 토/일", "/"); // "월", "화 수 목 금 토", "일"

// 3. 인수 3개 : 마지막에 boolean 값을 더해 자른 문자열 내에 delim 값을 포함할 것인지 여부를 정한다.
// 1,2번에서 생략되었는데, 3번째 인자의 기본값은 false(delim은 포함 안한다.) 이다.
StringTokenizer st = new StringTokenizer(target, delim, boolean); 
StringTokenizer st = new StringTokenizer("월/화 수 목 금 토/일", "/", true); // "월/", "화 수 목 금 토/", "일"
    

```



## (1) Class 이용

```java
import java.io.*;
import java.util.*;

class Solution {
    
    static Video end;
    static Video open_s;
    static Video open_e;
    
    public String solution(String video_len, String pos, String op_start, String op_end, String[] commands) {
        // 0. Initialize
        StringTokenizer st = new StringTokenizer(video_len, ":");
        end = new Video(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken()));
        st = new StringTokenizer(op_start, ":");
        open_s = new Video(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken()));
        st = new StringTokenizer(op_end, ":");
        open_e = new Video(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken()));
        st = new StringTokenizer(pos, ":");
        Video now = new Video(Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken()));
        
        for(String order : commands){
            now.isInOpening(open_s, open_e);
            if(order.equals("next")) now.next(end);
            else now.prev();
        }
        
        now.isInOpening(open_s, open_e);
        
        return now.toString();
        
    }
}

class Video {
    
    int mm;
    int ss;
    
    public Video (int mm, int ss){
        this.mm = mm;
        this.ss = ss; 
    }
    
    public void next(Video end) {       
        this.ss += 10;
        if(this.ss >= 60) {
            this.ss = this.ss%10;
            this.mm += 1;
        }
        
        if(this.mm > end.mm || (this.mm == end.mm && this.ss > end.ss)){
            this.mm = end.mm;
            this.ss = end.ss;
        }
    }
    
    public void prev() {
        this.ss -= 10; 
        if(this.ss < 0){
            this.ss += 60;
            this.mm -=1;
        }
        if(this.mm < 0){
            this.mm = 0;
            this.ss = 0;
        }
    }
    
    public void isInOpening(Video open_s, Video open_e){
        if(open_s.mm > this.mm || (open_s.mm == this.mm && open_s.ss > this.ss)) return;
        if(open_e.mm < this.mm || (open_e.mm == this.mm && open_e.ss < this.ss)) return;
        
        this.mm = open_e.mm;
        this.ss = open_e.ss;
        return;
    }
    
    
    public String toString() {
        StringBuilder sb = new StringBuilder();
        if(this.mm < 10) sb.append("0").append(this.mm);
        else sb.append(this.mm);
        
        sb.append(":");
        
        if(this.ss < 10) sb.append("0").append(this.ss);
        else sb.append(this.ss);
        
        return sb.toString();
    }
    

}
```

## (2) 분을 초로 변환 풀이

```java
import java.io.*;
import java.util.*;

class Solution {
    private final String DELIM  = ":";
    private final String PREV   = "prev";
    private final String NEXT   = "next";
    
    static private int now;
    static private int end;
    static private int open_s;
    static private int open_e;
    
    public String solution(String video_len, String pos, String op_start, String op_end, String[] commands) {
        
        now = convert2Sec(pos);
        end = convert2Sec(video_len);
        open_s = convert2Sec(op_start);
        open_e = convert2Sec(op_end);
        
        skip();
        for(String order : commands){
            if(order.equals(PREV)) prev();
            else next();
            skip();
        }
        
        return convert2String(now);
        
    }
    
    
    public int convert2Sec (String mmss) {
        StringTokenizer st = new StringTokenizer(mmss, DELIM);
        return Integer.parseInt(st.nextToken())*60 + Integer.parseInt(st.nextToken());
    }
    
    public String convert2String (int time) {
        StringBuilder sb = new StringBuilder();
        int mm = time/60;
        int ss = time%60;
        
        if(mm < 10) sb.append("0");
        sb.append(mm).append(":");
        if(ss < 10) sb.append("0");
        sb.append(ss);
        
        return sb.toString();
    }
    
    public void skip() {
        if(now < open_s || now > open_e) return;
        else now = open_e;
    }
    
    public void next() {
        now += 10;
        if(now > end) now = end;
    }
    
    public void prev() {
        now -= 10;
        if(now < 0) now = 0;
    }
}
```



# 4. 배운 것들 🎯

이러한 시간 문제들을 항상 `Class`화 시켜서 풀곤 했는데, 특정 단위로 모두 변환 후 문제를 푸는 것이 더 쉽게 풀 수 있음을 깨달았다.