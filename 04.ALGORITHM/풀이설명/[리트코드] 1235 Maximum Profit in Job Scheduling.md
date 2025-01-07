# 1. 문제 설명 📌

[문제 링크](https://leetcode.com/problems/maximum-profit-in-job-scheduling/description/)

한 개 이상의 일이 존재하고, 일마다 `시작시간`, `끝시간`, `완료했을 때 이익` 이 주어질 때, 주어진 일련의 일들에서 나올 수 있는 **`최대 이익`**을 반환하라.

### 조건

- 선택한 일들은 서로 일의 진행 시간이 겹치면 안된다. 
- A라는 일의 endTime = X 이고, B라는 일의 startTime = X이면 A와 B를 연달아 일할 수 있다. 이는 겹치는 것으로 간주하지 않는다.

# 2. 접근 방식 🗃️

`KEY WORD`: `DP`

## (1) 초기화

(가) 각각 산재되어 있는 startTime, endTime, profit을 같은 일 단위로 묶어서 나열 ➜ class `Job` + ArrayList

(나) ArrayList를 시작 시간이 이른 순으로 정렬

(다) `DP`용 배열 maxProfit[] 만들기 (maxProfit[i] = ArrayList의 i번째 일을 선택할 수 있는 시점에서 누적 최대 이익)

(라) TreeMap<Integer, Integer> 생성: `Key`: startTime, `Value`: 해당 시작시간에 시작했을 때 누적 최대 이익의 index

![image-20250104130453887](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[리트코드] 1235 Maximum Profit in Job Scheduling/image-20250104130453887.png)

`maxProfit`의 배열 크기가 ArrayList보다 1 큰 것을 볼 수 있는데, 이는 MaxProfit의 계산이 이전 원소를 활용함으로, `OutOfArrayError`가 발생하지 않도록 하기 위해 한 칸 더 만든 것이다.

## (2) 계산

시간 상 제일 나중인 순번부터 역순으로 계산하는 **`TOP-DOWN`** 형식의 DP를 사용할 예정.

**`점화식`: maxProfit[i] = Math.max(maxProfit[i+1], cur_profit[i] + maxProfit[nonOverLappingFirstIndex])**

해석:
i번째 Job을 고를 수 있는 시점의 최대이익은 둘 중 하나이다.

- *a. i 번째 일을 고르지 않고, 이전까지 한 일로 이룬 최대값을 답습하거나*
- *b. i번째 일을 고르고, i번째 일과 겹치지 않는 시작 시점 중에서의 최대 이익을 더하거나*

그렇다면 b의 **일과 겹치지 않는 시작 시점 중 최대이익**은 어떻게 알 수 있을까?

> **답변**: 현재 시점 일과 겹치지 않는 시점에서의 maxProfit 값을 찾으면 된다. 

**이유**: maxProfit은 이익이 누적되어 있는 **`누적합 배열`**이다. 따라서 `maxProfit[i]`와 겹치지 않으면서도 계산이 제일 최근에 끝난 배열의 원소가 고를 수 있는 값 중 최대 이익이 될 것이기 때문이다. 따라서 계산은 다음과 같이 진행된다.

(마) `maxProfit[maxProfit.length - 1]` 부터 `maxProfit[0]` 까지 역순으로 채운다. (따라서 maxProfit[0]에는 문제의 답이 기록될 것이다.)

(바) 현재 시점에서 할 수 있는 일을 ArrayList에서 꺼낸다. (Job now = arrayList.get(i));

(사) 해당 시점과 안 겹치면서 가장 최근에 했던 일이 언제인지 알아낸다.
(`treeMap.ceilingEntry(now.e)` = ceilingEntry(Key)는 Key 이상인 Key값을 가진 Entry 중 Key에 가장 가까운 Entry를 반환, 이를 활용해서 현재 하는 일이 끝나는 시점에서 가장 가까운 시일에 시작하는 일의 `index`를 가져올 수 있음)

(아) 점화식을 진행한다.

(자) treeMap에 현재 시점의 일의 시작 시간과 그 index를 적어넣는다. ( 다음 7,8번 계산을 위해)

## (3) 직접 해보기✨

![image-20250104130733007](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[리트코드] 1235 Maximum Profit in Job Scheduling/image-20250104130733007.png)

a. 제일 먼저 사용하는 것들을 check 해두었다.

![image-20250104131807350](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[리트코드] 1235 Maximum Profit in Job Scheduling/image-20250104131807350.png)

b, MaxProfit[4]번 시점에서는 현 시점에서 할 수 있는 일을 하는 것이 최대이익을 내는 것임으로, 그대로 일을한다. 
maxProfit을 채워넣은 이후, 현 시점의 시작시간과 그 index를 TreeMap에 채워넣는다. (이후 계산에 사용하기 위함)

![image-20250104131839878](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[리트코드] 1235 Maximum Profit in Job Scheduling/image-20250104131839878.png)

c. maxProfit[3]번 시점에서는 이전 답 답습(maxProfit[4]) : `60`, 현 시점 일 하고, 그것과 시간이 겹치지 않는 선에서 최대 이익 일하기(cur_profit(3) + maxProfit[4]): `130` 임으로 둘 중 최대값인 130을 선택한다. MaxProfit[4]가 **겹치지 않는 선에서 최대 이익**인 이유는 cur_Job의 끝나는 시간이 6인데, maxProfit[4]의 시작 시간이 6이므로, 고를 수 있는 최대 이익 중 가장 최근 값이기 때문이다.
  위에서도 설명했듯이 maxProfit[]이 누적합 배열이라 최근 값을 가져오는 것이 최대 이익이다.

![image-20250104132418899](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[리트코드] 1235 Maximum Profit in Job Scheduling/image-20250104132418899.png)

d. 이전 값 답습: `130`, 현 시점 일하고, 겹치지 않는 최대 이익 가져오기: `100`임으로 130을 택한다. 

![image-20250104132524619](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[리트코드] 1235 Maximum Profit in Job Scheduling/image-20250104132524619.png)

e. 이전 값 답습: `130`, 현 시점 일하고, 겹치지 않는 최대 이익 가져오기: `20 + 60 = 80`이므로, 130을 답습한다.

![image-20250104132654752](../../../../../Documents/GitHub/dalcheonroadhead-github-blog/dalcheonroadhead.github.io/images/[리트코드] 1235 Maximum Profit in Job Scheduling/image-20250104132654752.png)

f. 이전 값 답습: `130`, 현 시점 일하기 + 현 시점 일과 겹치지 않는 시점에서 최대 이익: `20 + 130 = 150` 이므로 150을 택한다.

따라서 답은 150이 된다.

# 3. 코드 소개 🔎

```java
class Solution {
    public int jobScheduling(int[] startTime, int[] endTime, int[] profit) {
        // 0. 변수 모음
        ArrayList<Job> schedules = new ArrayList<>();           // 값 모으기 (시작 시간 순으로 정렬됨)
        TreeMap<Integer, Integer> findNonOverLappingIndex = new TreeMap<>(); // <startTime, max_profit_index>: key부터 시작할 때 최대 이익 
        int [] maxProfits = new int[profit.length + 1]; // index = 현재 해야하는 일

        // 1. 초기화
        for(int i = 0; i < startTime.length; i ++){
            schedules.add(new Job(startTime[i], endTime[i], profit[i]));
        }

        // 2. 계산
        // (2-1) start 기준으로 Job_schedules 정리 (오름차순)
        Collections.sort(schedules, (o1,o2)-> o1.s - o2.s);
        // 2. DP 시작, i = startTime
        for(int i = startTime.length-1; i>= 0; i--){
            Job now = schedules.get(i);
            // 현재 조회중인 일이 끝나는 시간보다 늦게 시작하면서 가장 가까운 index를 찾는다. 없다면, MaxProfit[]의 제일 마지막 index를 반환한다.
            int nonOverLappingIndex = Optional.ofNullable(findNonOverLappingIndex.ceilingEntry(now.e)).map(e -> e.getValue()).orElse(maxProfits.length-1);
            maxProfits[i] = Math.max(maxProfits[i+1], now.p + maxProfits[nonOverLappingIndex]);
            // 계산이 끝나면, 현재 계산이 끝난 maxProfit의 index를 TreeMap에 넣는다.
            findNonOverLappingIndex.put(now.s, i);
        }
        return maxProfits[0];
    }
}

class Job {
    int s;
    int e;
    int p;
    public Job(int s, int e, int p) {
        this.s = s;
        this.e = e;
        this.p = p;
    }

    public String toString() {
        return s+"/"+e +"/" +p;
    }
}
```



# 4. 배운 것들 🎯

## (1) 왜 TreeMap을 ArrayList 초기화 할 때 같이 초기화 시키지 않나요? 

> `결론`: 계산이 끝난 maxProfit의 index만 저장해야 한다. 그렇지 않으면 누적 계산이 끝나지 않은 maxProfit[]의 값이 시작 시간이 더 빠르다는 이유로  선택되어 계산 오류를 야기 시키기 때문이다.