# 1. 문제 설명 📌

[문제 링크](https://leetcode.com/problems/sort-an-array/description/?envType=problem-list-v2&envId=radix-sort)

해당 문제는 자신이 사용하는 언어의 내부 라이브러리 정렬을 사용하지 않고 O(nlog(n))에 문제를 푸는 것이 관건이다.
(문제에서도 진짜 라이브러리 정렬을 썼는지 안 썼는지는 양심에 맡겼다...)

# 2. 접근 방식 🗃️

`Key word`: `Merge sort`

병합 정렬의 최악의 시간 복잡도가 O(nlog(n))이기 때문에 쓰면 풀린다. 하지만 한 가지 백준과 다른 점이 있어 1시간 동안 헤멘 부분이 있다. 

 **리트코드는 알고리즘 자체의 시간 뿐만 아니라, 자료구조를 사용하며 생기는 메모리 오버 헤드, 가비지 컬랙션 부담에 따른 실행 시간 증가 또한 `실행시간`에 포함시키는 듯 하다.**

그렇게 느낀 이유는 내가 Merge_sort를 하면서 부분 정렬된 값들을 넣을 tmp 임시 배열을 매 재귀마다 새로 호출하였더니, TLE가 발생했기 때문이다. 해당 임시배열을 static으로 단 한번만 설정하게 해주니 문제가 풀렸다. 여러 외국인의 코드를 살펴보니, 다들 당연하다는 듯이 이런 식으로 **자주 활용되는 자료구조는 재활용**하는 식으로 문제를 풀었더라.

공간 복잡도 낭비를 그저 공간 복잡도에 한정시키지 않고 그 여파로 생기는 메모리 오버 헤드, 가비지 컬랙션 사용 횟수도 시간복잡도에 포함시킨 리트 코드에 놀랬다. 

# 3. 코드 소개 🔎

## (1) 성공한 코드

```java
class Solution {

    private static int [] tmp;

    public int[] sortArray(int[] nums) {
        tmp = new int [nums.length];
        merge_sort(nums, 0, nums.length-1);
        return nums;
    }

    public void merge_sort(int [] nums, int start, int end) {
        // 0. 기저 조건
        if(start >= end) return;
        int mid = start + (end-start)/2;
        merge_sort(nums, start, mid);
        merge_sort(nums, mid+1, end);

        for(int i = start; i <= end; i++){
            tmp[i] = nums[i];
        }

        int L = start; int R = mid+1; int E = start;
        while(L <= mid && R <= end){
            if(tmp[L] <= tmp[R]){
                nums[E++] = tmp[L++];
            }else {
                nums[E++] = tmp[R++];
            }
        }

        if(L <= mid) {
            while(L <= mid){
                nums[E++] = tmp[L++];
            }
        }
    }
}
```

## (2) TLE가 난 코드

```java
class Solution {
    public int[] sortArray(int[] nums) {
        merge_sort(nums, 0, nums.length-1);
        return nums;
    }

    public void merge_sort(int [] nums, int start, int end) {
        // 0. 기저 조건
        if(start >= end) return;
        int [] tmp = new int [nums.length];
        int mid = start + (end-start)/2;
        merge_sort(nums, start, mid);
        merge_sort(nums, mid+1, end);

        for(int i = start; i <= end; i++){
            tmp[i] = nums[i];
        }

        int L = start; int R = mid+1; int E = start;
        while(L <= mid && R <= end){
            if(tmp[L] <= tmp[R]){
                nums[E++] = tmp[L++];
            }else {
                nums[E++] = tmp[R++];
            }
        }

        if(L <= mid) {
            while(L <= mid){
                nums[E++] = tmp[L++];
            }
        }
    }
}
```

# 4. 배운 것들 🎯

배운 것을 2번 접근방식에 다 적어버렸다 ㅎ

