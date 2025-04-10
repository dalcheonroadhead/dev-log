# 1. 문제 설명 📌

## (1) 링크🔗

https://leetcode.com/problems/minimum-operations-to-make-array-values-equal-to-k/description/

## (2) 해설🕵

### a. 문제 해석

너에게 nums라는 정수 배열과 k라는 정수가 주어진다. 
만약 배열 내에 존재하면서 h보다 큰값들이 모두 서로 동일하다면, 정수h는 유효하다고 간주된다. 예를 들어 nums = [10, 8, 10, 8] 이라고 하자. `h = 9`는 유효한 정수이다. 왜냐하면 9보다 큰 nums 배열 내의 정수가 모두 10으로 동일하기 때문이다. 하지만 h = 5 인 경우는 유효하지 않은 정수이다.

너는 다음의 동작을 `nums`에 대해 수행하도록 허용 되어진다.

- `nums` 안의 현재 값들에 대해 유효한 h를 하나 선택해라
- `nums[i] > h`인 인덱스 i를 만날 때마다 `nums[i]`의 값을 h로 만들어라

`nums`내의 모든 요소들을 `k`와 동등하게 만드는데 필요한 최소 동작 횟수를 반환하라. 만약 모든 요소를 `k`와 동등하게 만드는 것이 불가능하다면 -1을 반환하라

### b. 모르는 단어와 표현 정리

- `is called ~`: ~ 라고 불리운다. ~라고 간주된다.
- `identical`: 동일한 (동의어: same)
- `set A to B`: A를 B로 설정한다. 맞추다. 

---

### c. 문제 설명

문제 이해 자체가 힘들었다. 내가 지금까지 이해한 바로는

1. 현재 배열내의 상태들에 대해서 유효한 h 하나를 선택한다.
2. 그 친구로 순회하면서, 유효한 정수 (h)보다 큰 값들은 모두 h로 바꾼다. 
3. 1에서 2번 과정을 하나의 "동작"이라고 쳤을 때, 배열 내의 모든 수가 인수로 주어진 k로 변하는데 드는 최소 동작 횟수를 출력하라.

이다.

# 2. 생각의 흐름: 코드가 나오기까지 🗃️

## (1) IDEA 도출💡

**`KEY WORD`: `TreeMap`, `boolean 단순 구현`**

문제를 보면 배열을 나누는 기준인 h가 유효하려면, h보다 큰 수의 종류가 하나로 고정되어야 한다. 이 말은 **어짜피 제일 큰 수를 하나씩 처내야 한다는 것이다.** 문제에서 중복된 수의 개수는 상관이 없다. 따라서 TreeMap에 모든 수를 넣어놓고, `map.lastKey()` 즉 제일 큰 수가 k가 될 때까지 제일 큰 수를 쳐낸다. 

근데 문제를 풀고 다른 사람들의 코드를 봤더니 그냥 boolean 배열에 index는 해당 숫자, value 는 배열 속에 등장했다면 true 아니면 false로 둬서 k보다 큰 수의 개수를 세어서 출력하였다. 이러한 풀이를 보니, 굳이 중복된 수의 개수도 treeMap의 value로 세고 있었던 내 풀이가 과하다는 생각이 들었다. 

## (2) SUDO CODE 📜

1. map에 모든 값 저장 (by `compute()`)
2. 예외 처리 - 만약 map에서 제일 작은 수가 k보다 작다면, 어짜피 유효한 h로 최고 큰 수를 지워나가더라도 배열 내의 모든 수가 k가 될 수 없다. 따라서 바로 -1을 출력한다.
3. 2번을 통과했다면 map 내의 최고 큰 수가 k가 될 때까지 다음 과정을 반복한다.
   1. 제일 큰 수 삭제
   2. 동작 횟수 + 1 추가
   3. map이 텅 비었는지 체크 (텅 비었다면, 이제 남은 경우의 수는 배열 내의 모든 수가 k가 되는 경우의 수 밖에 없다는 뜻이다.)
4. 그 동안 누적한 동작 횟수 출력

## (3) 시간복잡도 분석 🕓

이것도 easy한 문제라 따로 시간 복잡도를 분석할 필요가 없었다.

# 3. 구현 코드 🔎

```java
class Solution {
    public int minOperations(int[] nums, int k) {
        int ans = 0;
        TreeMap<Integer, Integer> map = new TreeMap<>();
        for(int i = 0; i < nums.length; i++) {
            map.compute(nums[i], (key, ov) -> (ov == null? 1 : ov+1));
        }

        if(map.firstKey() < k) return -1;

        while(map.lastKey() != k) {
            map.compute(map.lastKey(), (key, ov) -> null);
            ans++;
            System.out.println(map.toString());
            if(map.size() == 0) break;
        }
        if(map.size() == 0) ans++;

        return ans;
    }
}
```

# 4. 배운 것들 🎯

TreeMap을 사용하는 방도에 대해서 다시 공부할 수 있었다.



>  이모지 모음: 🤔, ➜ ✨ 0️⃣1️⃣2️⃣3️⃣4️⃣5️⃣6️⃣7️⃣8️⃣9️⃣🔟

