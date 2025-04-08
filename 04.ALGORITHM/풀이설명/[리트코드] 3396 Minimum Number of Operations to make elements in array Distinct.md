# 1. 문제 설명 📌

## (1) 링크🔗

https://leetcode.com/problems/minimum-number-of-operations-to-make-elements-in-array-distinct/description/

## (2) 해설🕵

당신에게 `integer` 형태의 숫자 배열이 주어집니다. 당신은 배열안의 요소들이 중복이 없다는 것을 보장할 필요가 있습니다. 이것을 달성하기 위해, 당신은 언제든 다음 동작을 수행할 수 있습니다. 

- 배열의 첫 3가지 요소를 삭제하세요. 만약 배열이 3개보다 더 적게 요소를 가지고 있다면 남아있는 모든 요소를 삭제하십쇼.

빈 배열은 **중복이 제거된 배열**이라고 여겨진다는 것을 주의하세요. 배열 안 요소의 중복이 제거되도록 만들기 위해 필요한 최소한의 동작 횟수를 반환하세요.





# 2. 생각의 흐름: 코드가 나오기까지 🗃️

## (1) IDEA 도출💡

**`KEY WORD`: `HashMap을 활용한 단순 구현`**

중복된 값의 개수 관리를 해야한다는 측면에서 `HashMap`이 생각났다.  숫자의 총 개수가 100을 넘지 않는다는 면에서 해당 문제는 자료 구조를 쓸 수 있는가를 묻는 문제이지, 시간복잡도에 맞는 알고리즘을 묻는 문제가 아님을 느꼈다.  
  문제에 대한 아이디어는 다음과 같다.

### a. HashMap 채우기

![image-20250408135234043](https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-04/image-20250408135234043.png)

HashMap의 key는 숫자, value는 그 숫자가 나온 횟수이다. 위의 `numsThatsMoreThan1`은 임의의 숫자가 2번 이상 등장하면 cnt를 +1 올린다. 여기서는 최종적으로 **`numsThatsMoreThan1` = 2**가 될 것이다. 보다시피 HashMap은 요소 삭제에 따른 숫자의 개수 **`추적용 테이블`**이다.

### b. 3개씩 지우며 배열이 중복 없는 배열인지 확인

![image-20250408135628126](https://raw.githubusercontent.com/dalcheonroadhead/img-cloud/main/2025-04/image-20250408135628126.png)

3개씩 지워가며, HashMap과 numsThatsMoreThan1을 줄여나간다. numsThatsMoreThan1은 임의의 숫자가 1개밖에 안 남았을 때 지우면 된다. 

### c. 사용한 함수

해당 과정을 HashMap의 **`GET` 이나 `PUT`**으로만 한다면 상당히 코드가 지저분해질 것이다. 따라서 이번 풀이에서는 **` HashMap.compute(key, (key, oldvalue) -> remmaping Function)`**을 활용했다. 
  `compute(k, (k,ov)-> {return nv})`는 함수의 이름답게  HashMap 내부의 값들을 조정하는 함수이다. 주요 특징은 다음과 같다.

- 구성요소는 다음과 같다. `k` = key 값, `ov` = 기존 value 값, `nv` = 람다식을 거친 새로운 value 값
- **만약 key와 value가 hashMap에 존재하지 않는다면?** : 걱정하지 마라. 이 경우는 ov = null로 값이 찍힌다. 따라서 ov = null일 경우, key가 기존에 HashMap에 들어있는 값이 아님으로, 그걸 감안해서 코드를 짜면 된다.
- **2번째 함수인 람다함수에서 return 하는 값이 해당 key의 new value 로써 HashMap에 덮어쓰기 된다.** 만약 key도 기존에 없는 값이였다면, 새로운 key-value 쌍이 HashMap에 추가 되는 것이다.
- **만약 람다함수의 return 이 null 로 만든다면?**: 이것은 해당 key를 HashMap에서 삭제하겠다는 뜻이다. 즉 `map.remove(k)`와 같은 효과를 낸다. 

## (2) SUDO CODE 📜

```bash
        // 1. make hash map that has number as key, counts that number is appear as value
        // 2. remove 3 elements from array
        // 2-1 when I remove element, I should substract value with 1 in hashmap values
        // 2-2 when all hashmap's key has only 1 value, finish minimumOperations function
        
        외국인들이 많이 쓰는 리트코드라 영어로 써봤다 ㅎ
```

## (3) 시간복잡도 분석 🕓

해당 문제는 주어진 인수의 최대 크기로 봤을  때, 시간복잡도를 신경 쓰지 않아도 되는 문제이다.

# 3. 구현 코드 🔎

```java
class Solution {
    public int minimumOperations(int[] nums) {
        // 1. make hash map that has number as key, counts that number is appear as value
        // 2. remove 3 elements from array
        // 2-1 when I remove element, I should substract value with 1 in hashmap values
        // 2-2 when all hashmap's key has only 1 value, finish minimumOperations function

        int numsThatsMoreThan1 = 0;
        HashMap<Integer, Integer> map = new HashMap<>();

        for(int i = 0; i < nums.length; i++) {
            int now =  
            map.compute(nums[i], (k,ov) -> {
                if(ov == null) return 1; 
                else return ov+1;
                });

            if(now == 2) numsThatsMoreThan1++;
        }

        if(numsThatsMoreThan1 == 0) return 0;

        int iter = 0;
        int ans = 0;
        while (iter < nums.length) {
            int left = nums.length - iter;
            for(int i = 0; i < Math.min(3, left); i++) {
                int now = nums[iter++];
                Integer flag = map.compute(now, (k,ov) -> {
                    if(ov == 1) return null;
                    else if(ov == 2) return 1;
                    else return ov-1;
                });

                if(flag != null && flag == 1) numsThatsMoreThan1--;
            }
            ans++;
            if(numsThatsMoreThan1 == 0) return ans;
        }
        return ans;
    }
}
```

# 4. 배운 것들 🎯

HashMap의 compute란 함수를 오랜만에 쓰며, 까먹고 잘못 쓰던 부분들을 고쳤다. 해당 부분에 대한 Log는 다시 남기도록 하겠다.



>  이모지 모음: 🤔, ➜ ✨ 0️⃣1️⃣2️⃣3️⃣4️⃣5️⃣6️⃣7️⃣8️⃣9️⃣🔟

