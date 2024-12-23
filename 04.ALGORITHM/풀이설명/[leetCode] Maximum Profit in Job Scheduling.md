# 1. 문제 설명

[문제 링크](https://leetcode.com/problems/maximum-profit-in-job-scheduling/submissions/1361367801/)

 (1) 일거리의 `시작 시간`, `끝 시간`, `일을 끝냈을 때의 이익` 이 주어진다. 

(2) 시작 시간과 끝 시간의 범위가 겹치는 일은 같이 하지 못한다. 반면 어떤 일이 끝나자마자 다른 일은 시작할 수 있다. 
예를 들어, job A의 끝 시간이 3시 이고 job B의 시작시간이 3시이면 두 일 거리는 연달아 할 수 있다. 반면 job C가 3~5시이고, job D가 4~6시이면 두 일은 일의 시간 범위가 겹치므로 같이하지 못한다. 

(3) 이때, 겹치지 않게 일을 해서, `최대 이익`을 얻으려고 한다. 주어진 일거리들 중에서 가질 수 있는 `최대 이익`은 몇인가?

# 2. 접근 방식

`KEY WORD`: `DP`

(1) 주어진 문제가 `시작시간`, `끝시간`, `이익`을 따로 따로 주기에 이를 하나의 `일(job) 단위`로 하나로 묶는다. 

(2) 묶인 job들을 하나의 배열로 저장한다. 이때 배열은 `시작 시간 기준`으로 정렬 한다.

![image-20240819233812255](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[leetCode] Maximum Profit in Job Scheduling/image-20240819233812255.png)

(3)  DP용 배열을 만든다. (여기서는 이름을 maxProfit 이라고 하자.)

![image-20240819233841366](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[leetCode] Maximum Profit in Job Scheduling/image-20240819233841366.png)

Top-down 방식(맨 끝에서 부터 시작점으로 역행) 을 쓰려고 한다. 이러면 maxProfit[0]에 모든 일거리의 최대이익이 저장될 것이다. 
DP를 진행하는 방식은 다음과  같다. 만약 현재가 maxProfit[k]의 값을 정해야 한다면,

1. 현재 조회 중인 `k`번째 일거리를 건너 뛰고, 그 전까지의 최대 이익을 답습한다. 
   (maxProfit[k] = maxProfix[k+1])
2. 현재 조회 중인 k 번째 일을 하고 이익을 얻는다. 이때 결과가 최대 이익이 되려면 그 전 값들 중 `시간대가 겹치지 않는 선`에서 최대 이익을 누적해야 한다.
   (maxProfit[k] = job[k] [2] (현 이익) + maxProfit[nonOverLappingIndex])
3. 위의 두 개 중 최대값을 구한다.

### ✨ 한번 해보기 

그럼 위의 예시로 maxProfit[4] -> maxProfit[0]까지 구해보겠다. 

(1) maxProfit[4]는 원래의 배열에 없는 값이다. 해당 위치는 배열을 벗어나 OutOfIndex가 되는 것을 방지하기 위해서 넣었다. 따라서 0을 입력한다.
![image-20240819234747764](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[leetCode] Maximum Profit in Job Scheduling/image-20240819234747764.png)

(2) maxProfit[3] 은 현재 자신의 일을 하지 않고, 이전 값 중 최대값 답습(value = 0) 혹은 현재 자신의 일을 하고 겹치지 않는 선에서 최대값 누적 하기(그 이전의 겹치지 않는 값이 없으므로 그냥 70 )이 나오는데, 70이 최대 값이다. 

![image-20240819234812957](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[leetCode] Maximum Profit in Job Scheduling/image-20240819234812957.png)

(3) maxProfit[2] = 이전 값 답습 시 70, 자기 자신의 일을 하고 겹치지 않는 선에서 최대값 더 할시 40+0 이 되어 70이 된다. 
Job[2]를 보면 일이 4시에 마치는데, 4시 이후에 할 수 있는 다른 일이 없기 때문에 겹치지 않는 선에서 더 할 일이 없다. 

![image-20240819235102989](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[leetCode] Maximum Profit in Job Scheduling/image-20240819235102989.png)

(4) maxProfit[1] = Math.max(70,10) 이다.

![image-20240819235314230](../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[leetCode] Maximum Profit in Job Scheduling/image-20240819235314230.png)

(5) 이제야 제대로 보이겠다. 
maxProfit[0] = (70, 50  + 70) = 120 이다. 
Job[0]을 보면, 3시에 일을 끝내는데, 3시 이후에 이익 중 최대 이익이 70이여서 그 값을 더하면 된다.

### ✨ nonOverLappingIndex는 어떻게 찾을까? 

겹치지 않는 index를 어떻게 찾을까? 이분 탐색을 활용하는 방법도 있지만, **TreeMap**을 사용하면 훨씬 쉽다. 
TreeMap의 Key는 시작시간, value 는 index로 둔다. 이러면 시작시간이 빠른 순으로 정렬될 것이다. 우리는 이때, TreeMap의 함수 중 하나인 `ceilingEntry(Key k)` 라는 함수를 사용한다. 해당 함수는 인수로 들어온 key 값 보다 크거나 같은(greater) 값 중에서 `가장 가까운 값` 을 반환하는 함수이다. 
  아까 TreeMap의 Key를 시작시간으로 했음으로, `ceilingEntry(현재 조회 중인 일의 끝시간)`을 넣으면 그것보다 늦게 시작 하는 일 중 가장 빠른 시간대의 일을 반환할 것이다. 이를 이용하여 nonOverLappingIndex를 찾는다. 
`maxProfit[nonOverLappingIndex]`를 하면, 겹치지 않는 시간대 중 가장 빠른 시간대의 최대 이익을 얻을 수 있다. 이렇게 하면 알차게 지금까지의 최대 이익을 누적 시킬 수 있는 것이다.

<details> <summary>TreeMap이란?</summary> <div markdown="1">  Key 값을 기준으로 정렬되는 형태의 Map 자료구조, 정렬기준을 따로 설정하지 않으면 오름차순으로 정렬된다.  </div> </details>

# 3. 코드 분석 

```java
import java.io.*;
import java.util.*;

class Solution {
    public int jobScheduling(int[] startTime, int[] endTime, int[] profit) {
        // 1. 값 하나로 모으기
        int merged[][] = new int [startTime.length][3];
        for(int i = 0; i < startTime.length; i++){
            merged[i] = new int []{startTime[i], endTime[i], profit[i]};
        }
        Arrays.sort(merged, (a,b) -> Integer.compare(a[0], b[0]));
        // 2. dp 배열 만들기
        // 배열의 끝보다 그 뒤에서부터 시작해야함으로 한 칸 더
        int [] maxProfit = new int [merged.length + 1]; 
		// 3. 겹치지 않는 가장 빠른 시간대 구하기 용
        TreeMap<Integer, Integer> startTimeToIndex = new TreeMap<>();
        // 4. top-down dp
        for(int i = startTime.length - 1; i >= 0; i--){
			// 3번의 값 구하기 
            Integer nextNonOverlappingIndex = java.util.Optional.ofNullable(
                startTimeToIndex.ceilingEntry(merged[i][1])
            ).map(e -> e.getValue()).orElse(startTime.length);

            maxProfit[i] = Math.max(
                maxProfit[i+1], // 이번 꺼 Skip하고, 이전 값 중 제일 큰 값 가져오기
                merged[i][2] + maxProfit[nextNonOverlappingIndex] // 현재 이익 + 겹치지 않는 제일 가까운 위치의 최대값 가져오기 
            );
            // 5. 현재 값 다 구했으면, 겹치지 않는 가장 가까운 값 구하기 위해 TreeMap에 넣기
            startTimeToIndex.put(merged[i][0], i);
        }
        return maxProfit[0];
    }
}

```

# 4. 성장 하기

넘 어려워서 다른 사람 풀이를 봤다. LeetCode가 한국 풀이가 잘 없어, 외국인 풀이를 보느라 해멨다.