# Leetcode Note #

---

## Topic
- [**Array**](#Array)
- Hash Table
- Linked List
- Math
- Two Pointers
- String
- Binary Search
- Divide and Conquer
- Dynamic Programming
- Backtracking
- Stack
- Heap
- Greedy
- Sort
- Bit Manipulation
- DFS
- BFS
- Union Find
- Tree
- Graph
- Sliding Window
- Design
- Topological Sort
- Recursion

## Array
## 1. Two Sum
>Given an array of integers, return indices of the two numbers such that they add up to a specific target.

>You may assume that each input would have exactly one solution, and you may not use the same element twice.

>Example:

>Given nums = [2, 7, 11, 15], target = 9,

>Because nums[0] + nums[1] = 2 + 7 = 9,
>return [0, 1].
##### Discussion
>K-sum的題目基本上是送分題，幾乎都是靠sorting+two pointers搞定，3-sum的題目是算出是否有3個數的合加起來為target，方法為先把所有數字做sorting，再用two pointer降維

>舉例來說，[1,2,3,5,7] target為11，可以知道[1,3,7]是解

>所以目標變成先把1往左邊擺，再從[2,3,5,7]做2-sum


- 從left pointer = 2 right pinter = 7開始，發現2+7 < 11-1，代表left pointer可以更大，所以把left++，直到left == right即可確認沒有解
- 這時改用2為target ，left為3 right為7，3+7 > 11-2，代表right pointer可以更小，所以right--
- 套用上述的邏輯，4 sum其實就是一次選兩個數放右邊，左邊再利用two pinter的技巧可以降一維，所以可以以此類推，K-sum的題目基本上可以用O(N^k-1)解決
>
>這次的2-sum反而是特例，一般來說，2-sum在有sorting的前提下，應該是O(N)就能解決，但是本題並沒有說明是否為sorted，而sorting本身就需要O(NlogN)，反而不划算
- 因此這次用的方式為hash
##### Soution
```python
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        d = {}
        for i, n in enumerate(nums):
            remain = target - n
            if remain in d:  
                return [d[remain], i]
            d[n] = i
```

## 15. 3 Sum
>Given an array nums of n integers, are there elements a, b, c in nums such that a + b + c = 0? Find all unique triplets in the array which gives the sum of zero

>**Note:**

>The solution set must not contain duplicate triplets.

```
Given array nums = [-1, 0, 1, 2, -1, -4],

A solution set is:
[
  [-1, 0, 1],
  [-1, -1, 2]
]
```
##### Discussion
> 如剛剛說的，3 sum可以透過two pointers退化為2 sum，但是這題有個麻煩的點在於它不能重複


> 且測資明確的告訴你input array有可能重複


> 所以除了原先的降維外，需要多考慮
- 做為target的值如果一樣則重複
- 做為left的值如果一樣則重複
- 做為right的值如果一樣則重複
```python
class Solution(object):
    def threeSum(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        nums = sorted(nums)
        ans, length = [], len(nums)
        for i, n in enumerate(nums):
            if i != 0 and n == nums[i-1]:
                continue
            target = -n
            left, right = i+1, length-1
            while left < right:
                l, r = nums[left], nums[right]
                if l + r == target:
                    ans.append([n, l, r])
                    while left < right and nums[left] == l: left += 1
                    while left < right and nums[right] == r: right -= 1
                elif l + r < target:
                    left += 1
                else:
                    right -= 1
        return ans
```

> 檢討
- 這樣的寫法只贏過60%的人
- Pattern很常見要熟記 ```if i != 0 and n == nums[i-1]:```
- 可以只跑len(nums)-2次