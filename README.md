
# Leetcode Note #

---
## Tips
- Tip1: Two pointer for sorted array (#Array 1. Two Sum)
- Tip2: Sum[i:j] = Sum[0:j] - Sum[0:i] for continuous array (# Array 560. Subarray Sum Equals K)
- Tip3: Knapsack Problem (0/1, unbounded) (#DP 322. Coin Change)
- Tip4: backtrace or K sum remove duplicates
    - ```if i != 0 and n == nums[i-1]:```(#15. 3Sum)
    - ```if idx > start and nums[idx] == nums[idx-1]: continue```(#40. Combination Sum II)
- Tip5: 鴿籠原理要記得，如果題目說要constant extra space，八成就是用input array + swap(#41. First Missing Positive)
- Tip6: 題目說要組合的個數時，不能用暴力解，勿忘dp (#377 Combination Sum IV)
- Tip7: 螺旋題，通常要寫得蠻冗長的，關鍵在於要定義上下左右界，往某個方向走到底後，更新上下左右界即可
- Tip8: 遇到interval需要被merge時，先檢查是否交集，交集後的left左界中較小的，right則為右界中較大的 (#56. Merge Intervals)
## Trick
- Trick1: When accessing minus index, but you want to get a default value in 0, you can use **dp[max(day-7, 0)]+costs[1]**

## Topic
- [**Array**](#Array)
- Hash Table
- Linked List
- Math
- [**Two Pointers**](#double_pointers)
- String
- [**Binary Search**](#binary_search)
- Divide and Conquer
- Dynamic Programming(#dp)
- Backtracking
- Stack
- [**Dynamic Programming**](#dp)
- [**Backtracking**](#backtracking)
- [**Stack**](#stack)
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

### [1\. Two Sum](https://leetcode.com/problems/two-sum/)

Difficulty: **Easy**


Given an array of integers, return **indices** of the two numbers such that they add up to a specific target.

You may assume that each input would have **_exactly_** one solution, and you may not use the _same_ element twice.

**Example:**

```
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```

##### Discussion
>K-sum的題目基本上是送分題，幾乎都是靠sorting+two pointers搞定，3-sum的題目是算出是否有3個數的合加起來為target，方法為先把所有數字做sorting，再用two pointer降維
>
>舉例來說，[1,2,3,5,7] target為11，可以知道[1,3,7]是解
>
>所以目標變成先把1往左邊擺，再從[2,3,5,7]做2-sum


- 從left pointer = 2 right pinter = 7開始，發現2+7 < 11-1，代表left pointer可以更大，所以把left++，直到left == right即可確認沒有解
- 這時改用2為target ，left為3 right為7，3+7 > 11-2，代表right pointer可以更小，所以right--
- 套用上述的邏輯，4 sum其實就是一次選兩個數放右邊，左邊再利用two pinter的技巧可以降一維，所以可以以此類推，K-sum的題目基本上可以用O(N^k-1)解決
>
>這次的2-sum反而是特例，一般來說，2-sum在有sorting的前提下，應該是O(N)就能解決，但是本題並沒有說明是否為sorted，而sorting本身就需要O(NlogN)，反而不划算
- 因此這次用的方式為hash
##### Solution
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

### [15\. 3Sum](https://leetcode.com/problems/3sum/)

Difficulty: **Medium**


Given an array `nums` of _n_ integers, are there elements _a_, _b_, _c_ in `nums` such that _a_ + _b_ + _c_ = 0? Find all unique triplets in the array which gives the sum of zero.

**Note:**

The solution set must not contain duplicate triplets.

**Example:**

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
> 
> 且測資明確的告訴你input array有可能重複
> 
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

### [16\. 3Sum Closest](https://leetcode.com/problems/3sum-closest/)

Difficulty: **Medium**


Given an array `nums` of _n_ integers and an integer `target`, find three integers in `nums` such that the sum is closest to `target`. Return the sum of the three integers. You may assume that each input would have exactly one solution.

**Example:**

```
Given array nums = [-1, 2, 1, -4], and target = 1.

The sum that is closest to the target is 2\. (-1 + 2 + 1 = 2).
```
##### Discussion
> 基本上3 sum closest是完全一樣的套路，差別只有需要記錄目前最佳值，用nums[:3]，比亂選個magic number還好

#### Solution

Language: **Python**

```python
class Solution(object):
    def threeSumClosest(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        nums = sorted(nums)
        length, best = len(nums), sum(nums[:3])
        for i, n in enumerate(nums[:-2]):
            j, k = i+1, length-1
            while j < k:
                total = nums[i] + nums[j] + nums[k]
                
                if total == target:
                    return target
                elif total < target:
                    j += 1
                else:
                    k -= 1
                if abs(target - total) < abs(target - best):
                    best = total
                    
        return best
```
>高手表示
```
I think the insight is something like this - Given an array and a brute force algorithm that seems waaay too slow (n^3), try to think of ways that we could get it to n^2, nlogn, n. If the given array problem is the type of a problem where order/index doesn't matter, always consider sorting the array. Once you've got it sorted, you have a great heuristic to use to iterate over the array.

If you've gotten to that point, and are wondering how to traverse the array, 1, 2, 3+ pointers is always something that should be at the top of your list of things to consider when tackling an unfamiliar problem.
```

### [18\. 4Sum](https://leetcode.com/problems/4sum/)

Difficulty: **Medium**


Given an array `nums` of _n_ integers and an integer `target`, are there elements _a_, _b_, _c_, and _d_ in `nums` such that _a_ + _b_ + _c_ + _d_ = `target`? Find all unique quadruplets in the array which gives the sum of `target`.

**Note:**

The solution set must not contain duplicate quadruplets.

**Example:**

```
Given array nums = [1, 0, -1, 0, -2, 2], and target = 0.

A solution set is:
[
  [-1,  0, 0, 1],
  [-2, -1, 1, 2],
  [-2,  0, 0, 2]
]
```

##### Discussion
這三行去除重複
1. if i != 0 and nums[i] == nums[i-1]: continue
2. while left < right and nums[left] == c: left += 1
3. while left < right and nums[right] == d: right -= 1


#### Solution

Language: **Python**

```python
class Solution(object):
    def fourSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[List[int]]
        """
        ans = []
        
        # a + b + c + d = target
        nums = sorted(nums)
        length = len(nums)
        
        for i in xrange(length-2):
            if i != 0 and nums[i] == nums[i-1]: continue
            for j in xrange(i+1, length):
                if j != i + 1 and nums[j] == nums[j-1]:
                    continue
                    
                a = nums[i]
                b = nums[j]
                
                left, right = j+1, length-1
                while left < right:
                    c = nums[left]
                    d = nums[right]
                    if a + b + c + d == target:
                        while left < right and nums[left] == c: left += 1
                        while left < right and nums[right] == d: right -= 1
                        ans.append([a, b, c, d])
                    elif a + b + c + d < target:
                        left += 1
                    else:
                        right -= 1
        return ans
```

### [560\. Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/)

Difficulty: **Medium**


Given an array of integers and an integer **k**, you need to find the total number of continuous subarrays whose sum equals to **k**.

**Example 1:**  

```
Input:nums = [1,1,1], k = 2
Output: 2
```

**Note:**  

1.  The length of the array is in range [1, 20,000].
2.  The range of numbers in the array is [-1000, 1000] and the range of the integer **k** is [-1e7, 1e7].

##### Discussion
> 這類型的題目，如果暴力法很簡單，基本上要一定要先試試看暴力法，一來是讓面試官至少知道你懂題目，也讓他有機會救你
>
> 或是有機會基於最初的暴力法做出優化
>
> **這題使用的技巧必須熟記，Sum[i:j] = Sum[0:j] - Sum[0:i]**

#### Solution (暴力解 63 / 80 test cases passed.)
```python
class Solution(object):
    def subarraySum(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: int
        """
        count = 0
        length = len(nums)
        for i in xrange(length):
            total = 0
            for j in xrange(i, length):
                total += nums[j]
                if total == k:
                    count += 1       
        return count
```
> 但O(N^2)明顯的不夠好，有機會降到O(N)嗎?
> 
> Double pointer看起來行不通，因為不是sorted，而且解答要求連續
>
> 所以看到連續array，需要降低複雜度，請一定想到```Sum[i:j] = Sum[0:j] - Sum[0:i]```
> 
> 以這題來說，nums[0:1], nums[1:2]都是我們要的答案，nums[0:1]簡單，掃過一次就能算出
> 
> 但是nums[1:2]我們原本只能用雙層for loop從i=1累加，但是我們知道nums[1:2] = nums[0:2] - nums[0:1]
> 
> 換句話說只要我們可以找到任何nums[0:x]剛好等於nums[0:i]-k，那就代表nums[x:i]必為k
> 
> 則我們的答案次數即可+1
> 
> 因此我用了dictionary，把nums[0:i]的值與出線的次數都記錄下來，之後當到nums[0:i]時，只要看之前有幾次nums[0:i]-k
> 
> 累加上去即可，但是要注意這個可能做法會漏了nums[0:i] = k的機會，所以在中間補上
#### Solution (Runtime: 88 ms, faster than 90.01%)
```
class Solution(object):
    def subarraySum(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: int
        """
        count, total = 0, 0
        length = len(nums)
        d = {}
        for i in xrange(length):
            total += nums[i]
            if total-k in d:
                count += d[total-k]
            if total == k:
                count += 1
            d[total] = d[total] + 1 if total in d else 1
        return count
```
>高手表示，只要把d[0]的數量先給1，這樣就解決了 (Runtime: 80 ms, faster than 99.33%)
```
class Solution(object):
    def subarraySum(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: int
        """
        count, total = 0, 0
        length = len(nums)
        d = {0:1}
        for n in nums:
            total += n
            if total-k in d:
                count += d[total-k]
            d[total] = d[total] + 1 if total in d else 1
        return count
```

### [523\. Continuous Subarray Sum](https://leetcode.com/problems/continuous-subarray-sum/)

Difficulty: **Medium**


Given a list of **non-negative** numbers and a target **integer** k, write a function to check if the array has a continuous subarray of size at least 2 that sums up to a multiple of **k**, that is, sums up to n*k where n is also an **integer**.

**Example 1:**

```
Input: [23, 2, 4, 6, 7],  k=6
Output: True
Explanation: Because [2, 4] is a continuous subarray of size 2 and sums up to 6.
```

**Example 2:**

```
Input: [23, 2, 6, 4, 7],  k=6
Output: True
Explanation: Because [23, 2, 6, 4, 7] is an continuous subarray of size 5 and sums up to 42.
```

**Note:**

1.  The length of the array won't exceed 10,000.
2.  You may assume the sum of all the numbers is in the range of a signed 32-bit integer.

##### Discussion
> 剛剛的變化題型，一樣是找到n[x:i]是否為k的倍數，根據剛剛的秘訣
> 
> 也就是n[0:x]使得n[0:i] - n[0:x]為k的倍數，也就代表n[0:x] % k == n[0:i] % k
> 
> 由於total % k的值也只有k種，所以記錄下每種total % k的值最早的index以確保subarray size至少是2
> 
> 但是corner case太多，通常面試不會出

#### Solution (Runtime: 184 ms, faster than 96.04%)
```python
class Solution(object):
    def checkSubarraySum(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: bool
        """
        # 00 always correct
        if len(nums) > 1 and any(nums[i] == 0 and nums[i+1] == 0 for i in xrange(len(nums)-1)):
            return True
        
        if not nums or k == 0: 
            return False
            
        d, total = {nums[0]%k:0}, nums[0]
        for i, n in enumerate(nums[1:], 1):
            total += n
            pivot = total % k
            # Need to prevent n itself is k's multiple
            if (pivot in d and i-d[pivot]>1) or pivot == 0 :
                return True
            # But KK is valid answer
            if pivot not in d:
                d[pivot] = i
               
        return False
```

<a name="dp">## Dynamic Programming

### [4\. Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/)

Difficulty: **Hard**


There are two sorted arrays **nums1** and **nums2** of size m and n respectively.

Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).

You may assume **nums1** and **nums2** cannot be both empty.

**Example 1:**

```
nums1 = [1, 3]
nums2 = [2]

The median is 2.0
```

**Example 2:**

```
nums1 = [1, 2]
nums2 = [3, 4]

The median is (2 + 3)/2 = 2.5
```
##### Discussion
> 這題相當單純，2個sorted array找中位數，最笨的方法是把兩個array相加，sorting後找中間
> 數，這作法需要O(NlogN)，但題目是two sorted array，所以可以改用O(N)變成一個sorted array，這樣明顯都到太慢，應該是O(logN)就可以解決
> 
> 也就是需要O(logN)找到第K大的數
> 
> 方法如下，
> [1, 2, 7]
> [5, 6, 9] 
> 接下來針對上面的array，可以切分為4種:
> 
> [{} | 1, 2, 7], [1 | 2, 7], [1, 2 | 7], [1, 2, 7 | {}]
> 
> 因為我們要第4小的數，當我們的一刀切在idx的位置，那也代表下面的array必須切到k-idx
> 舉例來說
> 
> [1       | 2, 7]
> [5, 6, 9 |     ]  但是9 > 2，代表這刀太左邊了應該往右
>
> [1, 2 | 7]
> [5, 6 | 9]  <----- 2 < 9 and 6 < 7符合題意
> 自此binary search完成
```python
class Solution(object):
    def get_k_smallest_by_binary_search(self, nums1, nums2, k):
            
        if len(nums1) > len(nums2):
            nums1, nums2 = nums2, nums1
        if len(nums1) == 0:
            return nums2[k-1]
             
        left, right = max(0, k-len(nums2)), min(len(nums1), k)
        
        while True:
            idx1 = (left+right)/2
            idx2 = k-idx1
            #print idx1, idx2, k
            cond1 = (idx1 == len(nums1) or idx2 == 0 or nums1[idx1] >= nums2[idx2-1])
            cond2 = (idx2 == len(nums2) or idx1 == 0 or nums1[idx1-1] <= nums2[idx2])
            
            if cond1 and cond2:
                if idx1 == 0:
                    return nums2[k-1]
                if idx2 == 0:
                    return nums1[k-1]
                return max(nums2[idx2-1], nums1[idx1-1])
            elif cond1:
                right = idx1 - 1
            elif cond2:
                left = idx1 + 1
            else:
                assert 0
        assert 0
        
    def findMedianSortedArrays(self, nums1, nums2):
        """
        :type nums1: List[int]
        :type nums2: List[int]
        :rtype: float
        """
        total = len(nums1) + len(nums2)
        if total % 2 == 0:
            median1 = float(self.get_k_smallest_by_binary_search(nums1, nums2, total/2))
            median2 = float(self.get_k_smallest_by_binary_search(nums1, nums2, total/2+1))
            return (median1+median2)/2
        else:
            return float(self.get_k_smallest_by_binary_search(nums1, nums2, total/2+1))
```

### [26\. Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)

Difficulty: **Easy**


Given a sorted array _nums_, remove the duplicates such that each element appear only _once_ and return the new length.

Do not allocate extra space for another array, you must do this by **modifying the input array** with O(1) extra memory.

**Example 1:**

```
Given nums = [1,1,2],

Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively.

It doesn't matter what you leave beyond the returned length.```

**Example 2:**

```
Given nums = [0,0,1,1,1,2,2,3,3,4],

Your function should return length = 5, with the first five elements of nums being modified to 0, 1, 2, 3, and 4 respectively.

It doesn't matter what values are set beyond the returned length.
```

**Clarification:**

Confused why the returned value is an integer but your answer is an array?

Note that the input array is passed in by **reference**, which means modification to the input array will be known to the caller as well.

Internally you can think of this:

```
// nums is passed in by reference. (i.e., without making a copy)
int len = removeDuplicates(nums);

// any modification to nums in your function would be known by the caller.
// using the length returned by your function, it prints the first len elements.
for (int i = 0; i < len; i++) {
    print(nums[i]);
}```

##### Discussion
> 原本的想法是留個一個slow pointer，只要發現新的就往回overwrite，
> 但是這個做法在大多數都沒重複的時候，會有很多次value assign，因此
> 參考了其他人的做法，用while loop，不失為一個好選擇

#### Solution 1

Language: **Python**
```python
class Solution(object):
    def removeDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if not nums:
            return
        
        current = 1
        
        for i in xrange(1, len(nums)):
            if nums[i] != nums[i-1]:
                nums[current] = nums[i]
                current += 1
        for i in xrange(len(nums)-current):
            del nums[current]
```

#### Solution 2

Language: **Python**

```python
class Solution(object):
    def removeDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        i = 0
        while i < len(nums):
            if i >= 1 and nums[i] == nums[i-1]:
                del nums[i]
            else:
                i+=1
```

### [80\. Remove Duplicates from Sorted Array II](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/)

Difficulty: **Medium**


Given a sorted array _nums_, remove the duplicates such that duplicates appeared at most _twice_ and return the new length.

Do not allocate extra space for another array, you must do this by **modifying the input array** with O(1) extra memory.

**Example 1:**

```
Given nums = [1,1,1,2,2,3],

Your function should return length = 5, with the first five elements of nums being 1, 1, 2, 2 and 3 respectively.

It doesn't matter what you leave beyond the returned length.
```

**Example 2:**

```
Given nums = [0,0,1,1,1,1,2,3,3],

Your function should return length = 7, with the first seven elements of nums being modified to 0, 0, 1, 1, 2, 3 and 3 respectively.

It doesn't matter what values are set beyond the returned length.
```

**Clarification:**

Confused why the returned value is an integer but your answer is an array?

Note that the input array is passed in by **reference**, which means modification to the input array will be known to the caller as well.

Internally you can think of this:

```
// nums is passed in by reference. (i.e., without making a copy)
int len = removeDuplicates(nums);

// any modification to nums in your function would be known by the caller.
// using the length returned by your function, it prints the first len elements.
for (int i = 0; i < len; i++) {
    print(nums[i]);
}
```
##### Discussion
這題跟上一題一了無新意，把條件從前一個變成前兩個而已

#### Solution

Language: **Python**

```python
class Solution(object):
    def removeDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        i = 0
        while i < len(nums):
            if i >= 2 and nums[i] == nums[i-1] and nums[i] == nums[i-2]:
                del nums[i]
            else:
                i+=1
                
```

### [27\. Remove Element](https://leetcode.com/problems/remove-element/)

Difficulty: **Easy**


Given an array _nums_ and a value _val_, remove all instances of that value and return the new length.

Do not allocate extra space for another array, you must do this by **modifying the input array** with O(1) extra memory.

The order of elements can be changed. It doesn't matter what you leave beyond the new length.

**Example 1:**

```
Given nums = [3,2,2,3], val = 3,

Your function should return length = 2, with the first two elements of nums being 2.

It doesn't matter what you leave beyond the returned length.
```

**Example 2:**

```
Given nums = [0,1,2,2,3,0,4,2], val = 2,

Your function should return length = 5, with the first five elements of nums containing 0, 1, 3, 0, and 4.

Note that the order of those five elements can be arbitrary.

It doesn't matter what values are set beyond the returned length.
```

**Clarification:**

Confused why the returned value is an integer but your answer is an array?

Note that the input array is passed in by **reference**, which means modification to the input array will be known to the caller as well.

Internally you can think of this:

```
// nums is passed in by reference. (i.e., without making a copy)
int len = removeElement(nums, val);

// any modification to nums in your function would be known by the caller.
// using the length returned by your function, it prints the first len elements.
for (int i = 0; i < len; i++) {
    print(nums[i]);
}
```

##### Discussion
這題可以用上一題的思路，把所有不是val的刪光光像是上面這樣
也可以用two pointer，不停的往後換

#### Solution

Language: **Python**

```python
class Solution(object):
    def removeElement(self, nums, val):
        """
        :type nums: List[int]
        :type val: int
        :rtype: int
        """
        #i = 0
        #while i < len(nums):
        #    if nums[i] == val:
        #        del nums[i]
        #    else:
        #        i += 1
        #return len(nums)
        
        i, n = 0, len(nums)-1
        
        while i <= n:
            if nums[i] == val:
                nums[i] = nums[n]
                n -= 1
            else:
                i += 1
        return i
```

### [31\. Next Permutation](https://leetcode.com/problems/next-permutation/)

Difficulty: **Medium**


Implement **next permutation**, which rearranges numbers into the lexicographically next greater permutation of numbers.

If such arrangement is not possible, it must rearrange it as the lowest possible order (ie, sorted in ascending order).

The replacement must be and use only constant extra memory.

Here are some examples. Inputs are in the left-hand column and its corresponding outputs are in the right-hand column.

`1,2,3` → `1,3,2`  
`3,2,1` → `1,2,3`  
`1,1,5` → `1,5,1`

##### Discussion
要解這題要先觀察一下1, 3, 2, 4, 5 -> 1, 3, 2, 5, 4 -> 1, 3, 4, 2, 5
為什麼 1, 3, 2, 5, 4的下一個是 1, 3, 4, 2, 5呢? 因為從右邊往回推2遇到5了，也就是第一個開始遞減的數是關鍵
這個數以左不需要動
右邊則是要找下一個數，關鍵是右邊數列中最小的，也就是[5, 4]較小的，所以選擇4
其餘的則按照大小sorting
所以程式分兩部份
1. 找到pivot
2. 找到右邊的所有大於pivot的數中最小的當下一個pivot
3. 將pivot與此數交換
4. pivot右邊sorting排好

#### Solution

Language: **Python**

```python
class Solution(object):
    def nextPermutation(self, nums):
        """
        :type nums: List[int]
        :rtype: None Do not return anything, modify nums in-place instead.
        """
        # 1, 3, 2, 4, 5 -> 1, 3, 2, 5, 4 -> 1, 3, 4, 2, 5
        
        if nums == sorted(nums, reverse=True):
            nums.sort()
            return
        
        i = len(nums)-1
        while i > 0:
            if nums[i-1] < nums[i]:
                break
            i -= 1
        # i-1 is the pivot
        nums[i:] = sorted(nums[i:])
        
        # find the one that larger then and closest pivot
        for n in xrange(i, len(nums)):
            if nums[n] > nums[i-1]:
                nums[n], nums[i-1] = nums[i-1], nums[n]
                break
        
```

### [41\. First Missing Positive](https://leetcode.com/problems/first-missing-positive/)

Difficulty: **Hard**


Given an unsorted integer array, find the smallest missing positive integer.

**Example 1:**

```
Input: [1,2,0]
Output: 3
```

**Example 2:**

```
Input: [3,4,-1,1]
Output: 2
```

**Example 3:**

```
Input: [7,8,9,11,12]
Output: 1
```

**Note:**

Your algorithm should run in _O_(_n_) time and uses constant extra space.


##### Discussion
這題有3個重點，如何O(N) + constant extra space + 鴿籠原理
假設今天array當中的數，大於array本身的size，無論如何都放不近來才對
所以不用考慮他們
那接下來就是要把小於array本身size的數放到對的地方記錄
可以開另一個array，只要數小於array本身size，就把那個array對應的index設定為1
接下來只要掃那個array找第一個不是1的就是答案
但是這樣就不是constant extra space，**通常題目寫這句話就是要你用原本的array**
但是如果那個index本身就有小於array本身size的數怎麼辦
所以就用swap的，只有在條件符合i才會++

舉例來說\
[3,4,7,2,1,2]
size為4
```
i = 0: 3 -> swap(3,7), [7, 4, 3, 2, 1, 2]
i = 0: 7 -> (7 > 5), i++
i = 1: 4 -> swap(4,2), [7, 2, 3, 4, 1, 2]
i = 1: 2 -> nums[2] == 2, i++
i = 2: 3 -> nums[3] == 3, i++
i = 3: 4 -> nums[4] == 4, i++
i = 4: 1 -> swap(7,1), [1, 2, 3, 4, 7, 2]
i = 4: 7 -> (7 > 5), i++
i = 5: 2 -> nums[2] == 2, i++

[1,2,3,4,7,2] -> ans = 5
```

#### Solution

Language: **Python**

```python
class Solution(object):
    def firstMissingPositive(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if not nums: return 1
        N = len(nums)
        i = 0
        
        while i < N:
            val = nums[i]
            if 0 < val <= N and nums[val-1] != val:
                nums[val-1], nums[i] = nums[i], nums[val-1]
            else:
                i += 1
        
        for i, n in enumerate(nums):
            if n != i+1:
                return i+1
            
        return n+1
```
### [55\. Jump Game](https://leetcode.com/problems/jump-game/)

Difficulty: **Medium**


Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Determine if you are able to reach the last index.

**Example 1:**

```
Input: [2,3,1,1,4]
Output: true
Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.
```

**Example 2:**

```
Input: [3,2,1,0,4]
Output: false
Explanation: You will always arrive at index 3 no matter what. Its maximum
             jump length is 0, which makes it impossible to reach the last index.
```
##### Discussion
題目是給一個list，希望我們看看能不能到最後
首先，只要數字不為0，就有機會往後
所以就不斷的更新current max index，
但只要目前的值為0，index卻等於目前最大能到的index，就代表無法再往後了
因此return False
如果成功到達最後，則return True

#### Solution

Language: **Python**

```python
class Solution(object):
    def canJump(self, nums):
        """
        :type nums: List[int]
        :rtype: bool
        """
        limit = 0
        for i, n in enumerate(nums):
            if i+n >= len(nums)-1:
                return True
            if n == 0 and i == limit:
                return False
            limit = max(i+n, limit)
        return True
```
### [45\. Jump Game II](https://leetcode.com/problems/jump-game-ii/)

Difficulty: **Hard**


Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Your goal is to reach the last index in the minimum number of jumps.

**Example:**

```
Input: [2,3,1,1,4]
Output: 2
Explanation: The minimum number of jumps to reach the last index is 2.
    Jump 1 step from index 0 to 1, then 3 steps to the last index.
```

**Note:**

You can assume that you can always reach the last index.

##### Discussion
這題貌似背包客問題，希望用最少的jump走到終點
Solution 1, 不停地往後面更新最少步數，所以可以得到每個index的最小步數
但這個做法會吃TLE
如何簡化呢?
**我想到的是假設假設第一個數能到達index 100，那就算第二個數能夠到100，也於事無補，畢竟step就是多了1**
Solution 2，我們只更新100以後的，也就是說假設第二個數最多能到104，那我們就只更新101~104，直到index到100為止
這做法就不會吃TLE了
但是能再怎麼簡化呢?
事實上，在我們更新了第一步之後，不需要101~104每個都更新，因為能到104代表一定能到101，只要記得到104就好
也就是說第1步內會到的1~100，只要往後更新step的最大值
以剛剛的例子假設第三個index能到110代表我們step2最大是110，所以直到第一個數能到的100為止
我們就知道step2能到的最大範圍，同理在step1到step2中間可以更新step3
這也就是所謂的**BFS**

#### Solution

Language: **Python**

```python
class Solution(object):
    def jump(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        #Solution 1
        #dp = [len(nums)] * len(nums)
        #dp[0] = 0
        #
        #for idx, step in enumerate(nums):
        #    for i in range(1, step+1):
        #        if idx+i < len(nums):
        #            dp[idx+i] = min(dp[idx]+1, dp[idx+i])
        #return dp[-1]
        
        # Solution 2
        #dp = [len(nums)] * len(nums)
        #dp[0] = 0
        #max_step = [0] * (len(nums)+1)
        #for idx, step in enumerate(nums):
        #    start_step = max_step[dp[idx]]
        #    for i in range(start_step-idx, step+1):
        #        if idx+i < len(nums):
        #            dp[idx+i] = min(dp[idx]+1, dp[idx+i])
        #    max_step[dp[idx]+1] = max(max_step[dp[idx]+1], idx+step)
        #return dp[-1]
        
        #dp = [len(nums)] * len(nums)
        #dp[0] = 0
        #max_step = 0
        #for idx, step in enumerate(nums):
        #    for i in xrange(max_step-idx, step+1):
        #        if idx+i < len(nums):
        #            dp[idx+i] = min(dp[idx]+1, dp[idx+i])
        #    if idx == max_step:
        #        max_step = idx+step
        #return dp[-1]
        
        if len(nums) == 1: return 0
        step, current_range, next_range = 0, 0, nums[0]
        for i, n in enumerate(nums):
            next_range = max(i+n, next_range)
            if i == current_range:
                step += 1
                if next_range >= len(nums)-1:
                    break
                current_range = next_range
        return step   
        
```

### [48\. Rotate Image](https://leetcode.com/problems/rotate-image/)

Difficulty: **Medium**


You are given an _n_ x _n_ 2D matrix representing an image.

Rotate the image by 90 degrees (clockwise).

**Note:**

You have to rotate the image , which means you have to modify the input 2D matrix directly. **DO NOT** allocate another 2D matrix and do the rotation.

**Example 1:**

```
Given input matrix = 
[
  [1,2,3],
  [4,5,6],
  [7,8,9]
],

rotate the input matrix in-place such that it becomes:
[
  [7,4,1],
  [8,5,2],
  [9,6,3]
]
```

**Example 2:**

```
Given input matrix =
[
  [ 5, 1, 9,11],
  [ 2, 4, 8,10],
  [13, 3, 6, 7],
  [15,14,12,16]
], 

rotate the input matrix in-place such that it becomes:
[
  [15,13, 2, 5],
  [14, 3, 4, 1],
  [12, 6, 8, 9],
  [16, 7,10,11]
]
```

#####
[a][b] move to [b][N-a]連續四次

#### Solution

Language: **Python**

```python
class Solution(object):
    def rotate(self, matrix):
        """
        :type matrix: List[List[int]]
        :rtype: None Do not return anything, modify matrix in-place instead.
        """
        # When N == 3
        # [0][0] move to [0][3]
        # [0][1] move to [1][3]
        # [0][2] move to [2][3]
        # [2][2] move to [2][1]
        
        # [a][b] move to [b][N-a]
        length = len(matrix)-1
        
        # [15,13, 2, 5, 1],  4->2
        # [14, 3, 4, 1, 1],
        # [12, 6, 8, 9, 1],
        # [16, 7,10,11, 1],
        # [16, 7,10,11, 1],
        
        for i in xrange(0, length):
            for j in xrange(length-i*2):
                a, b = i, i+j
                val1 = matrix[a][b]
                val2 = matrix[b][length-a]
                val3 = matrix[length-a][length-b]
                val4 = matrix[length-b][a]
                matrix[b][length-a] = val1
                matrix[length-a][length-b] = val2
                matrix[length-b][a] = val3
                matrix[a][b] = val4             
```

### [53\. Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)

Difficulty: **Easy**


Given an integer array `nums`, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

**Example:**

```
Input: [-2,1,-3,4,-1,2,1,-5,4],
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.
```

**Follow up:**

If you have figured out the O(_n_) solution, try coding another solution using the divide and conquer approach, which is more subtle.

##### Discussion
詳見Tip2: Sum[i:j] = Sum[0:j] - Sum[0:i] for continuous array (# Array 560. Subarray Sum Equals K)

#### Solution

Language: **Python**

```python
class Solution(object):
    def maxSubArray(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        min_sub = 0
        total = 0
        ans = nums[0]
        for n in nums:
            total += n
            ans = max(ans, total - min_sub)
            min_sub = min(min_sub, total)
        return ans
```

### [54\. Spiral Matrix](https://leetcode.com/problems/spiral-matrix/)

Difficulty: **Medium**


Given a matrix of _m_ x _n_ elements (_m_ rows, _n_ columns), return all elements of the matrix in spiral order.

**Example 1:**

```
Input:
[
 [ 1, 2, 3 ],
 [ 4, 5, 6 ],
 [ 7, 8, 9 ]
]
Output: [1,2,3,6,9,8,7,4,5]
```

**Example 2:**

```
Input:
[
  [1, 2, 3, 4],
  [5, 6, 7, 8],
  [9,10,11,12]
]
Output: [1,2,3,4,8,12,11,10,9,5,6,7]
```

##### Discussion
**螺旋題，通常要寫得蠻冗長的，關鍵在於要定義上下左右界，往某個方向走到底後，更新上下左右界即可**

#### Solution

Language: **Python**

```python
class Solution(object):
    def spiralOrder(self, matrix):
        """
        :type matrix: List[List[int]]
        :rtype: List[int]
        """
        if not matrix:
            return []
        if not matrix[0]:
            return []
        
        height, width, left_boader, up_boader = len(matrix), len(matrix[0]), 0, 0
        ans = []
        left, right, down, up = 0, 1, 2, 3
        i, j, direction = 0, 0, right
        total = height * width
        for _ in xrange(total):
            ans.append(matrix[i][j])
            if direction == right:
                if j != width-1:
                    j += 1
                else:
                    i += 1
                    direction = down
                    up_boader += 1
                    
            elif direction == down:
                if i != height-1:
                    i += 1
                else:
                    direction = left
                    j -= 1
                    width -= 1
            
            elif direction == left:
                if j != left_boader:
                    j -= 1
                else:
                    i -= 1
                    direction = up
                    height -= 1
            else:
                if i > up_boader:
                    i -= 1
                else:
                    direction = right
                    j += 1
                    left_boader += 1
        return ans
```

### [56\. Merge Intervals](https://leetcode.com/problems/merge-intervals/)

Difficulty: **Medium**


Given a collection of intervals, merge all overlapping intervals.

**Example 1:**

```
Input: [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
Explanation: Since intervals [1,3] and [2,6] overlaps, merge them into [1,6].
```

**Example 2:**

```
Input: [[1,4],[4,5]]
Output: [[1,5]]
Explanation: Intervals [1,4] and [4,5] are considered overlapping.
```

**NOTE:** input types have been changed on April 15, 2019\. Please reset to default code definition to get new method signature.

##### Discussion
**遇到interval需要被merge時，先檢查是否交集，交集後的left左界中較小的，right則為右界中較大的**
```python
if interval[0] <= right:
    right = max(right, interval[1])
```

#### Solution

Language: **Python**

```python
class Solution(object):
    def merge(self, intervals):
        """
        :type intervals: List[List[int]]
        :rtype: List[List[int]]
        """
        intervals.sort(key=lambda v: v[0])
        
        if not intervals:
            return []
        
        ans = []
        left, right = intervals[0]
        for interval in intervals:
            if interval[0] <= right:
                right = max(right, interval[1])
            else:
                ans.append([left, right])
                left, right = interval
        ans.append([left, right])
        return ans
```

### [57\. Insert Interval](https://leetcode.com/problems/insert-interval/)

Difficulty: **Hard**


Given a set of _non-overlapping_ intervals, insert a new interval into the intervals (merge if necessary).

You may assume that the intervals were initially sorted according to their start times.

**Example 1:**

```
Input: intervals = [[1,3],[6,9]], newInterval = [2,5]
Output: [[1,5],[6,9]]
```

**Example 2:**

```
Input: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
Output: [[1,2],[3,10],[12,16]]
Explanation: Because the new interval [4,8] overlaps with [3,5],[6,7],[8,10].
```

**NOTE:** input types have been changed on April 15, 2019\. Please reset to default code definition to get new method signature.

##### Discusion
由Tip8 遇到interval需要被merge時，先檢查是否交集，交集後的left左界中較小的，right則為右界中較大的

我們將新的interval做merge，只要有交集，就擴大目前的這個集合，左右界則是follow tip8
即可以找到我們要的全部overlap區間

#### Solution

Language: **Python**

```python
class Solution(object):
    def insert(self, intervals, newInterval):
        """
        :type intervals: List[List[int]]
        :type newInterval: List[int]
        :rtype: List[List[int]]
        """
        a, b = newInterval
        left, right = a, b
        
        ans_left, ans_right = [], []
        
        for interval in intervals:
            x, y = interval
            # this interval will not be merged
            if y < a:
                ans_left.append(interval)
                continue
            if x > b:
                ans_right.append(interval)
                continue
             
            #    a ------- b
            # x------y
            #          x ----- y
            #      x --- y
            # x -------------- y
            # otherwise, start to merge
            if a <= y or x <= b:
                left = min(left, x)
                right = max(right, y)
        
        return ans_left + [[left, right]] + ans_right
```

### [59\. Spiral Matrix II](https://leetcode.com/problems/spiral-matrix-ii/)

Difficulty: **Medium**


Given a positive integer _n_, generate a square matrix filled with elements from 1 to _n_<sup>2</sup> in spiral order.

**Example:**

```
Input: 3
Output:
[
 [ 1, 2, 3 ],
 [ 8, 9, 4 ],
 [ 7, 6, 5 ]
]
```
##### Discussion
用Tip7即可結案

#### Solution

Language: **Python**

```python
class Solution(object):
    def generateMatrix(self, n):
        """
        :type n: int
        :rtype: List[List[int]]
        """
        ans = [[None for j in range(n)] for i in range(n)]
        left, right, up, down = 1, 2, 3, 4
        x, y, direction = 0, 0, right
        left_boader, up_boader, down_boader, right_boader = 0, 0, n-1, n-1
        for num in range(n*n):
            ans[x][y] = num+1
            if direction == right: 
                if y < right_boader:
                    y += 1
                else:
                    direction = down
                    up_boader += 1
                    x += 1
            elif direction == down:
                if x < down_boader:
                    x += 1
                else:
                    direction = left
                    right_boader -= 1
                    y -= 1
            elif direction == left:
                if y > left_boader:
                    y -= 1
                else:
                    direction = up
                    down_boader -= 1
                    x -= 1
            else:
                if x > up_boader:
```

### [62\. Unique Paths](https://leetcode.com/problems/unique-paths/)

Difficulty: **Medium**


A robot is located at the top-left corner of a _m_ x _n_ grid (marked 'Start' in the diagram below).

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).

How many possible unique paths are there?

![](https://assets.leetcode.com/uploads/2018/10/22/robot_maze.png)  
<small style="display: inline;">Above is a 7 x 3 grid. How many possible unique paths are there?</small>

**Note:** _m_ and _n_ will be at most 100.

**Example 1:**

```
Input: m = 3, n = 2
Output: 3
Explanation:
From the top-left corner, there are a total of 3 ways to reach the bottom-right corner:
1\. Right -> Right -> Down
2\. Right -> Down -> Right
3\. Down -> Right -> Right
```

**Example 2:**

```
Input: m = 7, n = 3
Output: 28
```
##### Discussion
數學題，m!/((n!)*(m-n)!)

#### Solution

Language: **Python**

```python
class Solution(object):
    def uniquePaths(self, m, n):
        """
        :type m: int
        :type n: int
        :rtype: int
        """
        def factorial(n):
            if n <= 1:
                return 1
            else:
                return n * factorial(n-1)
        
        return factorial(n+m-2)/factorial(n-1)/factorial(m-1)
```

### [63\. Unique Paths II](https://leetcode.com/problems/unique-paths-ii/)

Difficulty: **Medium**


A robot is located at the top-left corner of a _m_ x _n_ grid (marked 'Start' in the diagram below).

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).

Now consider if some obstacles are added to the grids. How many unique paths would there be?

![](https://assets.leetcode.com/uploads/2018/10/22/robot_maze.png)

An obstacle and empty space is marked as `1` and `0` respectively in the grid.

**Note:** _m_ and _n_ will be at most 100.

**Example 1:**

```
Input:
[
  [0,0,0],
  [0,1,0],
  [0,0,0]
]
Output: 2
Explanation:
There is one obstacle in the middle of the 3x3 grid above.
There are two ways to reach the bottom-right corner:
1\. Right -> Right -> Down -> Down
2\. Down -> Down -> Right -> Right
```

##### Discussion
這題就不能賴皮了，乖乖dp，```dp[i][j] = dp[i-1][j]+dp[i][j-1]```
障礙物直接跳過就好

#### Solution

Language: **Python**

```python
class Solution(object):
    def uniquePathsWithObstacles(self, obstacleGrid):
        """
        :type obstacleGrid: List[List[int]]
        :rtype: int
        """
        
        height = len(obstacleGrid)
        if not height:
            return 0
        width = len(obstacleGrid[0])
        if not width:
            return 0
        
        dp = [[0] * width for _ in range(height)]
        dp[0][0] = 1 if not obstacleGrid[0][0] else 0
        
        for i in xrange(height):
            for j in xrange(width):
                if obstacleGrid[i][j]:
                    continue
                    
                if i == 0 and j == 0:
                    continue
                elif i == 0:
                    dp[i][j] = dp[i][j-1]
                elif j == 0:
                    dp[i][j] = dp[i-1][j]
                else:
                    dp[i][j] = dp[i][j-1] + dp[i-1][j]
        
        return dp[-1][-1]
                             
```

### [64\. Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/)

Difficulty: **Medium**


Given a _m_ x _n_ grid filled with non-negative numbers, find a path from top left to bottom right which _minimizes_ the sum of all numbers along its path.

**Note:** You can only move either down or right at any point in time.

**Example:**

```
Input:
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
Output: 7
Explanation: Because the path 1→3→1→1→1 minimizes the sum.
```
##### Discussion
跟上一題一樣dp，```dp[i][j] = min(dp[i-1][j], d[i][j-1])+nums[i][j]```

#### Solution

Language: **Python**

```python
class Solution(object):
    def minPathSum(self, grid):
        """
        :type grid: List[List[int]]
        :rtype: int
        """
        if not grid:
            return 0
        if not grid[0]:
            return 0
        
        height = len(grid)
        width = len(grid[0])
        
        dp = [[0]*width]*height
        
        for i in xrange(height):
            for j in xrange(width):
                if i == 0 and j == 0:
                    dp[0][0] = grid[0][0]
                elif i == 0:
                    dp[i][j] = dp[i][j-1]+grid[i][j]
                elif j == 0:
                    dp[i][j] = dp[i-1][j]+grid[i][j]
                else:
                    dp[i][j] = min(dp[i][j-1], dp[i-1][j])+grid[i][j]
                    
        return dp[-1][-1]
        
```

### [66\. Plus One](https://leetcode.com/problems/plus-one/)

Difficulty: **Easy**


Given a **non-empty** array of digits representing a non-negative integer, plus one to the integer.

The digits are stored such that the most significant digit is at the head of the list, and each element in the array contain a single digit.

You may assume the integer does not contain any leading zero, except the number 0 itself.

**Example 1:**

```
Input: [1,2,3]
Output: [1,2,4]
Explanation: The array represents the integer 123.
```

**Example 2:**

```
Input: [4,3,2,1]
Output: [4,3,2,2]
Explanation: The array represents the integer 4321.
```

##### Discussion
cout初始值給1即可以
記得最後離開loop要考慮cout為1的情況

#### Solution

Language: **Python**

```python
class Solution(object):
    def plusOne(self, digits):
        """
        :type digits: List[int]
        :rtype: List[int]
        """
        cout, ans = 1, []
        for i in digits[::-1]:
            digit = (i+cout)%10
            cout = (i+cout)/10
            ans.append(digit)
        if cout:
            ans.append(1)
        
        return ans[::-1]
```
### [73\. Set Matrix Zeroes](https://leetcode.com/problems/set-matrix-zeroes/)

Difficulty: **Medium**


Given a _m_ x _n_ matrix, if an element is 0, set its entire row and column to 0\. Do it .

**Example 1:**

```
Input: 
[
  [1,1,1],
  [1,0,1],
  [1,1,1]
]
Output: 
[
  [1,0,1],
  [0,0,0],
  [1,0,1]
]
```

**Example 2:**

```
Input: 
[
  [0,1,2,0],
  [3,4,5,2],
  [1,3,1,5]
]
Output: 
[
  [0,0,0,0],
  [0,4,5,0],
  [0,3,1,0]
]
```

**Follow up:**

*   A straight forward solution using O(_m__n_) space is probably a bad idea.
*   A simple improvement uses O(_m_ + _n_) space, but still not the best solution.
*   Could you devise a constant space solution?

##### Dicussion
題目要把所有0的row, column都設為0
所以需要統計出那些row, column有包含0
這邊的解法是用set, space complexity(O(mn))
能不能更省呢?

**可以用1st row跟1st column當作flag**

#### Solution

Language: **Python**

```python
class Solution(object):
    def setZeroes(self, matrix):
        """
        :type matrix: List[List[int]]
        :rtype: None Do not return anything, modify matrix in-place instead.
        """
        # set it to another value then set all the other value to 0 after scan all
        row = set()
        column = set()
        
        if not matrix:
            return
        if not matrix[0]:
            return
        
        height, width = len(matrix), len(matrix[0])
        
        for i in xrange(height):
            for j in xrange(width):
                if matrix[i][j] == 0:
                    row.add(i)
                    column.add(j)
        
        for r in row:
            for j in xrange(width):
                matrix[r][j] = 0
        for c in column:
            for i in xrange(height):
                matrix[i][c] = 0
```

### [74\. Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/)

Difficulty: **Medium**


Write an efficient algorithm that searches for a value in an _m_ x _n_ matrix. This matrix has the following properties:

*   Integers in each row are sorted from left to right.
*   The first integer of each row is greater than the last integer of the previous row.

**Example 1:**

```
Input:
matrix = [
  [1,   3,  5,  7],
  [10, 11, 16, 20],
  [23, 30, 34, 50]
]
target = 3
Output: true
```

**Example 2:**

```
Input:
matrix = [
  [1,   3,  5,  7],
  [10, 11, 16, 20],
  [23, 30, 34, 50]
]
target = 13
Output: false
```
##### Discussion
這題基本上可以用兩個binary search就解決
但是如果無法用bianry search，O(m+n)也可以解
魔法就是先從7開始
如果要找的target較小就往左
不然就往下

#### Solution

Language: **Python**

```python
class Solution(object):
    def searchMatrix(self, matrix, target):
        """
        :type matrix: List[List[int]]
        :type target: int
        :rtype: bool
        """
        # two binary serach should work
        # first for the col[0] element
        # second for the row
        if not matrix:
            return False
        if not matrix[0]:
            return False
        
        left, right = 0, len(matrix)-1
        
        while left <= right:
            mid = (left+right)/2
            if matrix[mid][0] <= target:
                left = mid + 1
            else:
                right = mid - 1
        
        target_row = right
        left, right = 0, len(matrix[0])-1
        
        while left <= right:
            mid = (left + right)/2
            if matrix[target_row][mid] == target:
                return True
            elif matrix[target_row][mid] < target:
                left = mid + 1
            else:
                right = mid - 1
        return False
```

### [75\. Sort Colors](https://leetcode.com/problems/sort-colors/)

Difficulty: **Medium**


Given an array with _n_ objects colored red, white or blue, sort them so that objects of the same color are adjacent, with the colors in the order red, white and blue.

Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.

**Note:** You are not suppose to use the library's sort function for this problem.

**Example:**

```
Input: [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]
```

**Follow up:**

*   A rather straight forward solution is a two-pass algorithm using counting sort.  
    First, iterate the array counting number of 0's, 1's, and 2's, then overwrite array with total number of 0's, then 1's and followed by 2's.
*   Could you come up with a one-pass algorithm using only constant space?

##### Discussion
這題希望我們可以把[2,0,2,1,1,0]變成[0,0,1,1,2,2]
其實sorting就好
但是如果要求O(n)
就要想怎麼優化
第一個可以想的是它最多就三種0, 1, 2
所以感覺就可以用two pointer去試試看
0一個，2一個，分別代表第一個不是0跟2的index
一路往兩個pointer swap並內縮

sorting的法則
- 當只有2種element，用two pointer
- 當只有3種element，一樣two pointer
- 當只有N種element，用counting sort，缺點是需要O(N) space complexity
- 當element的個數不詳，用qsort

#### Solution

Language: **Python**

```python
class Solution(object):
    def sortColors(self, nums):
        """
        :type nums: List[int]
        :rtype: None Do not return anything, modify nums in-place instead.
        """
        two = len(nums)-1
        i, zero = 0, 0
        while i < len(nums):
            if nums[i] == 2 and i < two:
                nums[i], nums[two] = nums[two], nums[i]
                two -= 1
            elif nums[i] == 0 and zero != i:
                nums[i], nums[zero] = nums[zero], nums[i]
                zero += 1
            else:
                i += 1
            
```python
class Solution(object):
    def sortColors(self, nums):
        """
        :type nums: List[int]
        :rtype: None Do not return anything, modify nums in-place instead.
        """
        def swap(a, b): 
            a, b = b, a
            
        zero, two = 0, len(nums)-1
        i = 0
        while i <= two:
            if nums[i] == 0 and i != zero:
                nums[i], nums[zero] = nums[zero], nums[i]
                zero += 1
            elif nums[i] == 2:
                nums[i], nums[two] = nums[two], nums[i]
                two -= 1
            else:
                i += 1
		'''
        [2,0,2,1,1,0]
        [0,0,2,1,1,2]
        [0,0,1,1,2,2]
		'''
```

### [80\. Remove Duplicates from Sorted Array II](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/)

Difficulty: **Medium**


Given a sorted array _nums_, remove the duplicates such that duplicates appeared at most _twice_ and return the new length.

Do not allocate extra space for another array, you must do this by **modifying the input array** with O(1) extra memory.

**Example 1:**

```
Given nums = [1,1,1,2,2,3],

Your function should return length = 5, with the first five elements of nums being 1, 1, 2, 2 and 3 respectively.

It doesn't matter what you leave beyond the returned length.
```

**Example 2:**

```
Given nums = [0,0,1,1,1,1,2,3,3],

Your function should return length = 7, with the first seven elements of nums being modified to 0, 0, 1, 1, 2, 3 and 3 respectively.

It doesn't matter what values are set beyond the returned length.
```

**Clarification:**

Confused why the returned value is an integer but your answer is an array?

Note that the input array is passed in by **reference**, which means modification to the input array will be known to the caller as well.

Internally you can think of this:

```
// nums is passed in by reference. (i.e., without making a copy)
int len = removeDuplicates(nums);

// any modification to nums in your function would be known by the caller.
// using the length returned by your function, it prints the first len elements.
for (int i = 0; i < len; i++) {
    print(nums[i]);
}
```

##### Discussion
看著當初的自己竟然大言不慚的用了del
甚感抱歉
這類型的題目
高手會使用slow pointer的技巧
從index 2開始掃，因為index小於2一定合法
接下來就去看目前這個位置的前兩個跟nums[idx]有沒有一樣
一樣的話代表放滿了  就continue
不一樣代表可以繼續塞
把nums[slow_pointer]的值更新為nums[idx]
slow_pointer += 1
之所以只要檢查nums[slow_pointer-2]是因為他一定遞增
所以只要nums[slow_pointer-2] == nums[idx]
因為遞增的特性
nums[slow pointer-1]必定為相同值

#### Solution

Language: **Python**

```python
class Solution(object):
    def removeDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        i = 0
        while i < len(nums):
            if i >= 2 and nums[i] == nums[i-1] and nums[i] == nums[i-2]:
                del nums[i]
            else:
                i+=1
        
                
```
#### Solution

Language: **Python**

```python
class Solution(object):
    def removeDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        # 1 1 2 -> OK
        # 1 2 2 -> OK
        # 2 2 2 -> Not OK
        slow_pointer, idx = 2, 2
        for idx in xrange(2, len(nums)):
            if nums[idx] != nums[slow_pointer-2]:
                nums[slow_pointer] = nums[idx]
                slow_pointer += 1
        return slow_pointer
        
                
```

## <a name="double_pointers"></a>Double Pointers
### [11\. Container With Most Water](https://leetcode.com/problems/container-with-most-water/)

Difficulty: **Medium**


Given _n_ non-negative integers _a<sub style="display: inline;">1</sub>_, _a<sub style="display: inline;">2</sub>_, ..., _a<sub style="display: inline;">n </sub>_, where each represents a point at coordinate (_i_, _a<sub style="display: inline;">i</sub>_). _n_ vertical lines are drawn such that the two endpoints of line _i_ is at (_i_, _a<sub style="display: inline;">i</sub>_) and (_i_, 0). Find two lines, which together with x-axis forms a container, such that the container contains the most water.

**Note: **You may not slant the container and _n_ is at least 2.

![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/17/question_11.jpg)

<small style="display: inline;">The above vertical lines are represented by array [1,8,6,2,5,4,8,3,7]. In this case, the max area of water (blue section) the container can contain is 49\.</small>

**Example:**

```
Input: [1,8,6,2,5,4,8,3,7]
Output: 49```

##### Discussion
> 經典的Double pointer，一般來說這類型的題目會用while loop，讓左邊右邊一起往中間逼近，舉例來說
```python
while l < r:
	if xxx:
		return 
	elif nums[l]:
		l += 1
	else
		r -= 1
```
> 這題是要算最大存水空間，我們假設左邊這時選到了10右邊選到了3，這時我們的總蓄水為(10-3) * min(10, 3)
> 這時如果移動10，一定不會有更好的結果，因為高度最多為還是3，但是(10-3)變成了(9-3)
> 所以我們取而代之的從將小的那邊往中間推進
> 這題的關鍵在於要想到中間的蓄水量是由 min(nums[l], nums[r]) * (r-l)

#### Solution 
```pythnon
class Solution(object):
    def maxArea(self, height):
        """
        :type height: List[int]
        :rtype: int
        """
        ans = 0
        l, r = 0, len(height)-1
        
        while l < r:
            left, right = height[l], height[r]
            
            ans = max(ans, min(left, right) * (r-l))
            if left < right:
                l += 1
            else:
                r -= 1
                
        return ans
```

### [42\. Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)

Difficulty: **Hard**


Given _n_ non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.

![](https://assets.leetcode.com/uploads/2018/10/22/rainwatertrap.png)  
<small style="display: inline;">The above elevation map is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. In this case, 6 units of rain water (blue section) are being trapped. **Thanks Marcos** for contributing this image!</small>

**Example:**

```
Input: [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6```

##### Discussion

> 要解這題首先要像上一題一樣，了解任意取兩個邊界時，水容量是怎麼算的
每個積水，都由右邊最大的值以及左邊最大的值兩者取較小的當作水位，並且要扣掉這一格本身的高度，舉例來說，這格本身的高度是2，左邊最高5，右邊最高7，那針對這一格就是min(5, 7) - 2 = 3個單位的積水，要得到左右的最大值，可以先用O(N)掃過一輪。

##### Solution

Language: **Python**

```python
class Solution(object):
    def trap(self, height):
        """
        :type height: List[int]
        :rtype: int
        """
        ans = 0
        
        right, r_max = [], 0
        left, l_max = [], 0
        for i in xrange(len(height)):
            right.append(r_max)
            r_max = max(height[len(height)-i-1], r_max)
            left.append(l_max)
            l_max = max(height[i], l_max)
            
        right = right[::-1]
        
        for i in xrange(0, len(height)):
            val = min(left[i], right[i])
            if val > height[i]:
                ans += val - height[i]
        
        return ans
```

## <a name="binary_search"></a>Binary Search
### [33\. Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)

Difficulty: **Medium**


Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e., `[0,1,2,4,5,6,7]` might become `[4,5,6,7,0,1,2]`).

You are given a target value to search. If found in the array return its index, otherwise return `-1`.

You may assume no duplicate exists in the array.

Your algorithm's runtime complexity must be in the order of _O_(log _n_).

**Example 1:**

```
Input: nums = [4,5,6,7,0,1,2], target = 0
Output: 4
```

**Example 2:**

```
Input: nums = [4,5,6,7,0,1,2], target = 3
Output: -1
```

##### Discussion
Binary search的變形題，關鍵有兩個
1. rotated sorted array從任何一方切一刀後，左右兩邊都還是rotated sorted
2. 怎麼定義子問題

也就是說我們只要知道這個數會出現在右邊還是左邊
接下來就可以把大問題變成子問題，反正子問題還是符合題意


#### Solution

Language: **Python**

```python
class Solution(object):
    def search(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        left, right = 0, len(nums)-1
        
        # 3 4 5 6 7 1 2
        # 7 1 2 3 4 5 6
        # 4 5 6 7 1 2 3 
        
        while left <= right:
            mid = (left) + (right-left)/2
            
            if nums[mid] == target:
                return mid
            if nums[left] == target:
                return left
            if nums[right] == target:
                return right
            
            # left target mid -> left side
            # target mid left -> left side
            # target left mid -> right side
            
            if target < nums[mid]:
                
                if nums[left] < target:
                    right = mid-1
                else:
                    if nums[left] < nums[mid]:
                        left = mid + 1
                    else:
                        right = mid -1
            else:
                if nums[right] > target:
                    left = mid + 1
                else:
                    if nums[mid] > nums[right]:
                        left = mid + 1
                    else:
                        right = mid - 1
            
        
        return -1
    
```

### [81\. Search in Rotated Sorted Array II](https://leetcode.com/problems/search-in-rotated-sorted-array-ii/)

Difficulty: **Medium**


Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e., `[0,0,1,2,2,5,6]` might become `[2,5,6,0,0,1,2]`).

You are given a target value to search. If found in the array return `true`, otherwise return `false`.

**Example 1:**

```
Input: nums = [2,5,6,0,0,1,2], target = 0
Output: true
```

**Example 2:**

```
Input: nums = [2,5,6,0,0,1,2], target = 3
Output: false
```

**Follow up:**

*   This is a follow up problem to , where `nums` may contain duplicates.
*   Would this affect the run-time complexity? How and why?


##### Discussion
這題跟33一樣，差別只在於數列可以重複
剛開始覺得複雜，其實只要遇到大小一樣的邊界就跳掉就好
這樣會讓Time complexity飆高至O(N)
不如return target in nums，也是O(N)

#### Solution

Language: **Python**

```python
class Solution(object):
    def search(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: bool
        """
        left, right = 0, len(nums)-1
        
        while left <= right:
            
            mid = (left+right)/2
            
            if nums[left] == target:
                return True
            if nums[mid] == target:
                return True
            if nums[right] == target:
                return True
            
            if nums[left] == nums[right]:
                left += 1
                continue
            if nums[left] == nums[mid]:
                left = mid + 1
                continue
            if nums[mid] == nums[right]:
                right = mid - 1
                continue
            
            if nums[left] < target < nums[mid]:
                right = mid - 1
            elif target < nums[left] < nums[mid]:
                left = mid + 1
            elif target < nums[mid] < nums[left]:
                right = mid -1
            elif nums[mid] < target < nums[right]:
                left = mid + 1
            elif nums[mid] < nums[right] < target:
                right = mid - 1
            elif nums[right] < nums[mid] < target:
                left = mid + 1
            
        return False
```

### [153\. Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)

Difficulty: **Medium**


Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e.,  `[0,1,2,4,5,6,7]` might become  `[4,5,6,7,0,1,2]`).

Find the minimum element.

You may assume no duplicate exists in the array.

**Example 1:**

```
Input: [3,4,5,1,2] 
Output: 1
```

**Example 2:**

```
Input: [4,5,6,7,0,1,2]
Output: 0
```
##### Discussion
這題也是sorted array的纇題
最小值其實也就是pivot，這題想到的應該想要切一刀
看看是不是pivot，不是就是往左或往右
所以就拆成兩個部分
1. 怎麼知道是不是pivot
    - 可以用跟左邊的數比較得到，左邊的數一般都要小於自己除非你是pivot，這邊奸詐的利用了python -1是最右邊的特性
2. 如果不是pivot要往左還是往右
    - 如果最右邊的數比目前mid還大的話代表不在右側，應該往左
    - 反之應該往右，舉例來說: 4 5 6 7 1 2 3，3 < 代表中間必定有pivot
3. 這邊要很注意的是如果不幸的nums[mid] == nums[mid-1]，也就是當array size為1時，要額外判斷或是改成nums[mid] <= nums[mid-1]
4. Binary search 請愛用while left <= right:

#### Solution

Language: **Python**

```python
class Solution(object):
    def findMin(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        left, right = 0, len(nums)-1
        while left <= right:
            mid = (left+right)/2
            if nums[mid] <= nums[mid-1]:
                return  nums[mid]
            elif nums[mid] > nums[right]:
                left = mid + 1
            else:
                right = mid - 1
        
        assert 0
        
```

### [154\. Find Minimum in Rotated Sorted Array II](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array-ii/)

Difficulty: **Hard**


Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e.,  `[0,1,2,4,5,6,7]` might become  `[4,5,6,7,0,1,2]`).

Find the minimum element.

The array may contain duplicates.

**Example 1:**

```
Input: [1,3,5]
Output: 1
```

**Example 2:**

```
Input: [2,2,2,0,1]
Output: 0
```

**Note:**

*   This is a follow up problem to .
*   Would allow duplicates affect the run-time complexity? How and why?

##### Discussion
這題是153的follow up
這邊要記得的是binary search在遇到element有可能重複時
這題就無法用O(logN)解
舉例來說: 
**11110111這種情況無論看mid left right都沒有辦法把某一邊除掉**
所以是O(N)
解法也相當作弊，勇敢的無恥的把重複的消掉即可

#### Solution

Language: **Python**

```python
class Solution(object):
    def findMin(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        left, right = 0, len(nums)-1
        
        while left <= right:
                
            mid = (left+right)/2
            
            if nums[mid] == nums[right]:
                right -= 1
                continue
            
            if nums[mid] < nums[mid-1]:
                return nums[mid]
            
            if nums[mid] < nums[right]:
                right = mid-1
            else:
                left = mid+1
                
        return nums[mid]
```

### [34\. Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

Difficulty: **Medium**


Given an array of integers `nums` sorted in ascending order, find the starting and ending position of a given `target` value.

Your algorithm's runtime complexity must be in the order of _O_(log _n_).

If the target is not found in the array, return `[-1, -1]`.

**Example 1:**

```
Input: nums = [5,7,7,8,8,10], target = 8
Output: [3,4]
```

**Example 2:**

```
Input: nums = [5,7,7,8,8,10], target = 6
Output: [-1,-1]
```

##### Discussion
經典的binary search有重複element尋找左右界的題目
用了兩個binary search去找
針對左界，就算找到了，也往左邊看看有沒有更左的解
針對右界，就算找到了，也往右邊看看有沒有更右的解
看起來比較醜但直觀

Solution 2參考了高手的做法
不需要一直更新index1, index2
概念如下
針對左界，如果發現target大於nums[mid]
這很明顯的我們要的數在右邊
但是針對等於或是小於，則是都往左邊繼續搜
右界以此類推
\
最終如果這個數有找到的話
index1, index2分別會是左右界
如果只有一個數被找到則index1 == index2
但是如果完全找不到
理論上index1會是離target最近但是大於target的數
index2則是離target最近但小於target的數
舉例來說
[5,7,7,8,8,10]
6
這樣的測資下idx1 = 1, idx2 = 0
按照題意 return [-1, -1]

#### Solution

Language: **Python**

```python
class Solution(object):
    def searchRange(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        left, right = 0, len(nums)-1
        index1, index2 = -1, -1
        while left <= right:
            mid = (left + right)/2
            
            if nums[mid] == target:
                index1 = mid
                right = mid - 1
            elif nums[mid] > target:
                right = mid - 1
            else:
                left = mid + 1
        
        left, right = 0, len(nums)-1
        
        while left <= right:
            mid = (left + right)/2
            
            if nums[mid] == target:
                index2 = mid
                left = mid + 1
            elif nums[mid] > target:
                right = mid - 1
            else:
                left = mid + 1
        return [index1, index2]
```

#### Solution 2

Language: **Python**

```python
class Solution(object):
    def searchRange(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        left, right = 0, len(nums)-1
        
        # 1 1 1 1 1
        while left <= right:
            mid = (left+right)/2
            if nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1
        idx1 = left
        
        left, right = 0, len(nums)-1
        while left <= right:
            mid = (left+right)/2
            if nums[mid] > target:
                right = mid - 1
            else:
                left = mid + 1
        idx2 = right
        
        if idx1 <= idx2:
            return [idx1, idx2]
        else:
            return [-1,-1]
            
            
            
```

### [278\. First Bad Version](https://leetcode.com/problems/first-bad-version/)

Difficulty: **Easy**


You are a product manager and currently leading a team to develop a new product. Unfortunately, the latest version of your product fails the quality check. Since each version is developed based on the previous version, all the versions after a bad version are also bad.

Suppose you have `n` versions `[1, 2, ..., n]` and you want to find out the first bad one, which causes all the following ones to be bad.

You are given an API `bool isBadVersion(version)` which will return whether `version` is bad. Implement a function to find the first bad version. You should minimize the number of calls to the API.

**Example:**

```
Given n = 5, and version = 4 is the first bad version.

call isBadVersion(3) -> false
call isBadVersion(5) -> true
call isBadVersion(4) -> true

Then 4 is the first bad version. 
```
##### Discussion
標準的binary search，公式
```python
left, right = 0, len(nums)-1

while left <= right:
    mid = (left+right)/2
    if nums[mid] == target:
        break
    elif nums[mid] < target:
        left = mid + 1
    else:
        right = mid - 1
```
如果是要找最右邊的，小改即可
- 如果target存在且僅有一個，最終right會是答案
- 如果target存在且有多個，最終right會是最右邊的target
- 如果target不存在，最終right會是小於target的數中最大的

```python
left, right = 0, len(nums)-1

while left <= right:
    mid = (left+right)/2
    if nums[mid] <= target:
        left = mid + 1
    else:
        right = mid - 1
return right
```


#### Solution

Language: **Python**

```python
# The isBadVersion API is already defined for you.
# @param version, an integer
# @return a bool
# def isBadVersion(version):
​
class Solution(object):
    def firstBadVersion(self, n):
        """
        :type n: int
        :rtype: int
        """
        left, right = 0, n-1
        
        # false false true
        
        while left <= right:
            mid = (left+right)/2
            if not isBadVersion(mid):
                left = mid + 1
            else:
                right = mid - 1
        
        return left
```

### [35\. Search Insert Position](https://leetcode.com/problems/search-insert-position/)

Difficulty: **Easy**


Given a sorted array and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.

You may assume no duplicates in the array.

**Example 1:**

```
Input: [1,3,5,6], 5
Output: 2
```

**Example 2:**

```
Input: [1,3,5,6], 2
Output: 1
```

**Example 3:**

```
Input: [1,3,5,6], 7
Output: 4
```

**Example 4:**

```
Input: [1,3,5,6], 0
Output: 0
```

##### Discussion
如上題，套公式即可

#### Solution

Language: **Python**

```python
class Solution(object):
    def searchInsert(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        left, right = 0, len(nums)-1
        
        while left <= right:
            mid = (left+right)/2
            
            if nums[mid] == target:
                return mid
            elif nums[mid] < target:
                left = mid+1
            else:
                right = mid-1
        
        return left
```

## <a name="dp"></a>Dynamic Programming
### [322\. Coin Change](https://leetcode.com/problems/coin-change/)

Difficulty: **Medium**


You are given coins of different denominations and a total amount of money _amount_. Write a function to compute the fewest number of coins that you need to make up that amount. If that amount of money cannot be made up by any combination of the coins, return `-1`.

**Example 1:**

```
Input: coins = [1, 2, 5], amount = 11
Output: 3 
Explanation: 11 = 5 + 5 + 1```

**Example 2:**

```
Input: coins = [2], amount = 3
Output: -1
```

**Note**:  
You may assume that you have an infinite number of each kind of coin.

##### Discussion
> 題意相當清楚，就是給你幾個面額，試著用最少的coin去湊出target，這應該很直覺得會想用最大的面額先去湊，剩下的再用小面額試試看，
> 
> 所以剛開始用的是DFS，以上面的例子也就是先試試看5*2，剩下的1變成了子問題，但這個方法最慘的狀況需要O(S^N)，也果然如預料的吃了
> 
> TLE，leetcode通常這類型的題目有兩個做法，要嘛就是DFS+Prune，不然就是換做法，而這做法八成是DP
> 
> 因此不死心的我還是用了直覺的DFS開始

##### Solution: (TLE in 31/182，也就是說是個超爛的做法)
```python
class Solution(object):
    
    def dfs(self, index, coins, amount, current_count):
        
        if amount == 0:
            self.current_best = min(self.current_best, current_count)
            return
        
        if  index == len(coins):
            return
        
        coin = coins[index]
        
        max_coin = amount/coin
        for i in xrange(max_coin, -1, -1): 
            self.dfs(index+1, coins, amount-coin*i, current_count+i)

        return
        
    def coinChange(self, coins, amount):
        """
        :type coins: List[int]
        :type amount: int
        :rtype: int
        """
        if amount == 0:
            return 0
        coins = filter(lambda c: c<=amount, coins)
        if not coins:
            return -1
            
        coins = sorted(coins, reverse=True)
        self.current_best = amount+1
        self.dfs(0, coins, amount, 0)
        
        if self.current_best != amount+1:
            return self.current_best 
        return -1
```
> 於是開始想著，如果今天這個amount沒有很大呢(雖然題目沒說)，是不是有機會像是爬格子這樣，每個數字看看有沒有辦法用最小的次數湊
> 
> 到，這樣時間複雜度就變成O(S*N)了，偷偷去看解答發現還真的這麼爛，所以這題應該是要補上例如S <= 10000之類的比較好

##### Solution: (Runtime: 784 ms, faster than 86.82%)
```python
class Solution(object):
    
    def coinChange(self, coins, amount):
        """
        :type coins: List[int]
        :type amount: int
        :rtype: int
        """
        
        
        if amount == 0:
            return 0
        
        coins = filter(lambda c: c <= amount, coins)
        if not coins:
            return -1
        
        start = max(coins)
        dp = [-1] * (amount+1)
        dp[0] = 0
        
        for i in xrange(amount+1):
            min_count = -1
            for coin in coins:
                if dp[i-coin] != -1:
                    if dp[i] == -1 or dp[i] > dp[i-coin]+1:
                        dp[i] = dp[i-coin]+1 
        
        return dp[amount] if dp[amount] != -1 else -1
```

> 不死心的想看看能不能Prune更多，用DFS解
>
> 於是...想到了，如果目前最佳解是3次，而目前已經用掉1次，也就是只剩下2個coin要湊滿剩下的amount
> 
> 我們假設剩下的coin是2，也就是說它最多只能剩amount為4
> 
> 也就是說如果剩餘amount > 4的部份可以直接省略，可以注意到for loop是由大到小，所以條件達到後就可以break了
> 
> 因此補上後得到了爆炸性的成長，所以嚴格說起來這並不能說是DP，應該也要算是DFS
> 
> 但是這個一個一個湊的做法，是經典的[Unbounded 背包客問題](https://www.geeksforgeeks.org/unbounded-knapsack-repetition-items-allowed)的**簡化版**，要記下
> 
> 如果今天題目給出的target有限制，那八成就是用這招了

##### Solution: (Runtime: 180 ms, faster than 97.60%)
```python
class Solution(object):
    
    def dfs(self, index, coins, amount, current_count):
        
        if amount == 0:
            self.current_best = min(self.current_best, current_count)
            return
        
        if index == len(coins):
            return
        
        coin = coins[index]
        
        max_coin = amount/coin
        for i in xrange(max_coin, -1, -1):
            
            # No hope since even if you use all the coins, you can not reach target amount
            if amount > coin * (self.current_best - current_count):
                break
                
            self.dfs(index+1, coins, amount-coin*i, current_count+i)

        return
        
    def coinChange(self, coins, amount):
        """
        :type coins: List[int]
        :type amount: int
        :rtype: int
        """
        if amount == 0:
            return 0
        coins = filter(lambda c: c<=amount, coins)
        if not coins:
            return -1
            
        coins = sorted(coins, reverse=True)
        self.current_best = amount+1
        self.dfs(0, coins, amount, 0)
        
        if self.current_best != amount+1:
            return self.current_best 
        return -1
```
### [983\. Minimum Cost For Tickets](https://leetcode.com/problems/minimum-cost-for-tickets/)

Difficulty: **Medium**


In a country popular for train travel, you have planned some train travelling one year in advance.  The days of the year that you will travel is given as an array `days`.  Each day is an integer from `1` to `365`.

Train tickets are sold in 3 different ways:

*   a 1-day pass is sold for `costs[0]` dollars;
*   a 7-day pass is sold for `costs[1]` dollars;
*   a 30-day pass is sold for `costs[2]` dollars.

The passes allow that many days of consecutive travel.  For example, if we get a 7-day pass on day 2, then we can travel for 7 days: day 2, 3, 4, 5, 6, 7, and 8.

Return the minimum number of dollars you need to travel every day in the given list of `days`.

**Example 1:**

```
Input: days = [1,4,6,7,8,20], costs = [2,7,15]
Output: 11
Explanation: 
For example, here is one way to buy passes that lets you travel your travel plan:
On day 1, you bought a 1-day pass for costs[0] = $2, which covered day 1.
On day 3, you bought a 7-day pass for costs[1] = $7, which covered days 3, 4, ..., 9.
On day 20, you bought a 1-day pass for costs[0] = $2, which covered day 20.
In total you spent $11 and covered all the days of your travel.
```


**Example 2:**

```
Input: days = [1,2,3,4,5,6,7,8,9,10,30,31], costs = [2,7,15]
Output: 17
Explanation: 
For example, here is one way to buy passes that lets you travel your travel plan:
On day 1, you bought a 30-day pass for costs[2] = $15 which covered days 1, 2, ..., 30.
On day 31, you bought a 1-day pass for costs[0] = $2 which covered day 31.
In total you spent $17 and covered all the days of your travel.
```


**Note:**

1.  `1 <= days.length <= 365`
2.  `1 <= days[i] <= 365`
3.  `days` is in strictly increasing order.
4.  `costs.length == 3`
5.  `1 <= costs[i] <= 1000`

##### Discussion
> 這題應該也算是背包客問題的纇題，但除了要最少的價錢外，他還有幾天是不需要付錢的，這讓題目多了點難度
> 
> 於是我套用了背包客公式，基本上應該是天數在第一層 for loop，cost在第二層，**試著往回用過去的答案來更新現在**
> 
> 但如果沒有列入days的那幾天呢
> 
> 想像中這幾天就是不用付錢，所以理論上可以拿前一天花的錢當作這一天的，所以dp[n] = dp[n-1] if 不需要搭車
> 
> **除此之外針對每一個需要搭火車的日子，都是著往後面更新，讓後面的天數花的錢更少** <= 如果這樣做就會浪費超多時間
> 
> 應該是每次只要往回看N天前來更新今天就好，這才是背包客公式
> 
> Trick: dp[max(day-7, 0)]+costs[1] <- 相當pythonic的寫法，可以記下來

#### Solution (Runtime: 104 ms, faster than 5.31%，真是個垃圾方法...)
```python
class Solution(object):
    def mincostTickets(self, days, costs):
        """
        :type days: List[int]
        :type costs: List[int]
        :rtype: int
        """
        if not days:
            return 0

        last_day = days[-1]
        durations = [1,7,30]
        active_days = {d:True for d in days}
        
        dp = [costs[0] * len(days)] * (last_day+31)
        dp[0] = 0

        for day in xrange(1, last_day+1):
            if day not in active_days:
                dp[day] = dp[day-1]
            
            dp[day] = min(dp[day], dp[day-1]+costs[0])
            for n in range(7):
                dp[day+n] = min(dp[day+n], dp[day-1]+costs[1])
                
            for n in range(30):
                dp[day+n] = min(dp[day+n], dp[day-1]+costs[2])
                
            #for i, cost in enumerate(costs):
            #    duration = durations[i]
            #    for n in xrange(duration):
            #        dp[day+n] = min(dp[day+n], dp[day-1]+cost)
        
        return dp[last_day]
```

> 用了正統的方式後

#### Solution (Runtime: 24 ms, faster than 91.24%)

```python
class Solution(object):
    def mincostTickets(self, days, costs):
        """
        :type days: List[int]
        :type costs: List[int]
        :rtype: int
        """
        if not days:
            return 0
        
        last_day = days[-1]
        durations = [1,7,30]
        active_days = {d:True for d in days}
        
        dp = [costs[0] * len(days)] * (last_day+31)
        dp[0] = 0
        
        for day in xrange(1, last_day+1):
            if day not in active_days:
                dp[day] = dp[day-1]
            
            dp[day] = min(dp[day], dp[day-1]+costs[0], dp[max(day-7, 0)]+costs[1], dp[max(day-30, 0)]+costs[2])
        
        return dp[last_day]
```

## <a name="backtracking"></a>Backtracking
### [39\. Combination Sum](https://leetcode.com/problems/combination-sum/)

Difficulty: **Medium**


Given a **set** of candidate numbers (`candidates`) **(without duplicates)** and a target number (`target`), find all unique combinations in `candidates` where the candidate numbers sums to `target`.

The **same** repeated number may be chosen from `candidates` unlimited number of times.

**Note:**

*   All numbers (including `target`) will be positive integers.
*   The solution set must not contain duplicate combinations.

**Example 1:**

```
Input: candidates = [2,3,6,7], target = 7,
A solution set is:
[
  [7],
  [2,2,3]
]
```

**Example 2:**

```
Input: candidates = [2,3,5], target = 8,
A solution set is:
[
  [2,2,2,2],
  [2,3,3],
  [3,5]
]
```
##### Discussion
Backtrace 經典題，公式如下方，這次題目說不會有重複的input，但是每個element可以重複\
也就是self.backtrace(**idx**, nums, remain-nums[idx], current+[nums[idx]], ans) \
下層recursive可以從自己開始

#### Solution

Language: **Python**

```python
class Solution(object):
    def backtrace(self, start, nums, remain, current, ans):
        if remain == 0:
            ans.append(current)
            return
        if remain < 0:
            return
        # Prune
        for idx in xrange(start, len(nums)):
            if nums[idx] > remain:
                break
            self.backtrace(idx, nums, remain-nums[idx], current+[nums[idx]], ans)
            
    
    def combinationSum(self, candidates, target):
        """
        :type candidates: List[int]
        :type target: int
        :rtype: List[List[int]]
        """
        candidates.sort()
        ans = []
        self.backtrace(0, candidates, target, [], ans)
        return ans
```

### [40\. Combination Sum II](https://leetcode.com/problems/combination-sum-ii/)

Difficulty: **Medium**


Given a collection of candidate numbers (`candidates`) and a target number (`target`), find all unique combinations in `candidates` where the candidate numbers sums to `target`.

Each number in `candidates` may only be used **once** in the combination.

**Note:**

*   All numbers (including `target`) will be positive integers.
*   The solution set must not contain duplicate combinations.

**Example 1:**

```
Input: candidates = [10,1,2,7,6,1,5], target = 8,
A solution set is:
[
  [1, 7],
  [1, 2, 5],
  [2, 6],
  [1, 1, 6]
]
```

**Example 2:**

```
Input: candidates = [2,5,2,1,2], target = 5,
A solution set is:
[
  [1,2,2],
  [5]
]
```
##### Discussion
照著公式寫，但是每個element變成只能用一次\
self.helper(idx+1, nums, current+[nums[idx]], remain-nums[idx], ans)\
第二個麻煩是重複的不能列出\
最笨的做法就是用set把重複的全部濾掉，但這也太笨\
假設我們有個candidates = [1,1,1,2,3,4,4,4,4] target = 5\
這樣答案應該為[1,1,1,2], [1,1,3], [1,4], [2,3]\
濾掉的方法為**if idx > start and nums[idx] == nums[idx-1]: continue**\
照裡來說只要nums[idx] == nums[idx-1]就應該skip，舉例來說[1,4], [1,4], [1,4], [1,4]，後面三個都是重複的\
但是唯一的例外是，假設今天target是9，我們反而需要[1,4,4]，這個case會在第一次跑到\
所以後面的都可以濾掉，也就是[1, 4] -> 這邊的4是第2個4，它的所有情況都已經包含在第1個4，所以可以全部忽略


#### Solution

Language: **Python**

```python
class Solution(object):
    def helper(self, start, nums, current, remain, ans):
        
        if remain == 0:
            ans.append(current)
            return
        if remain < 0:
            return
        
        length = len(nums)
        for idx in xrange(start, length):
            if idx > start and nums[idx] == nums[idx-1]: continue
            self.helper(idx+1, nums, current+[nums[idx]], remain-nums[idx], ans)
    
    def combinationSum2(self, candidates, target):
        """
        :type candidates: List[int]
        :type target: int
        :rtype: List[List[int]]
        """
        candidates.sort()
        ans = []
        self.helper(0, candidates, [], target, ans)
        return ans
```

### [216\. Combination Sum III](https://leetcode.com/problems/combination-sum-iii/)

Difficulty: **Medium**


Find all possible combinations of _**k**_ numbers that add up to a number _**n**_, given that only numbers from 1 to 9 can be used and each combination should be a unique set of numbers.

**Note:**

*   All numbers will be positive integers.
*   The solution set must not contain duplicate combinations.

**Example 1:**

```
Input: k = 3, n = 7
Output: [[1,2,4]]
```

**Example 2:**

```
Input: k = 3, n = 9
Output: [[1,2,6], [1,3,5], [2,3,4]]
```

##### Discussion
幾乎跟所有combination一樣，唯一多的是限制了combination中，element的個數
```if remain == 0 and k == 0```
補上後已無難度

#### Solution

Language: **Python**

```python
class Solution(object):
    def helper(self, start, remain, current, ans, k):
        if k < 0:
            return
        if remain == 0 and k == 0:
            ans.append(current)
            return
        
        for idx in xrange(start, 10):
            self.helper(idx+1, remain-idx, current+[idx], ans, k-1)
            
    def combinationSum3(self, k, n):
        """
        :type k: int
        :type n: int
        :rtype: List[List[int]]
        """
        ans = []
        self.helper(1, n, [], ans, k)
        return ans
```

### [377\. Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/)

Difficulty: **Medium**


Given an integer array with all positive numbers and no duplicates, find the number of possible combinations that add up to a positive integer target.

**Example:**

```
nums = [1, 2, 3]
target = 4

The possible combination ways are:
(1, 1, 1, 1)
(1, 1, 2)
(1, 2, 1)
(1, 3)
(2, 1, 1)
(2, 2)
(3, 1)

Note that different sequences are counted as different combinations.

Therefore the output is 7.
```

**Follow up:**  
What if negative numbers are allowed in the given array?  
How does it change the problem?  
What limitation we need to add to the question to allow negative numbers?

##### Discussion
當題目不需要列出所有組數時，那就代表我們原本的backtrace一定會爆炸
畢竟backtrace是暴力解
果然實作完成馬上吃了一個TLE
**題目說要組合的個數時，不能用暴力解**
**寫在這邊是希望自己記得不要每次都傻傻的backtrace**

#### Solution

Language: **Python**

```python
class Solution(object):
    
    def helper(self, start, nums, remain):
        if remain == 0:
            self.ans += 1
        if remain < 0:
            return
        for idx in range(0, len(nums)):
            self.helper(idx+1, nums, remain-nums[idx])
    
    def combinationSum4(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        #self.ans = 0
        #self.helper(0, nums, target)
        #return self.ans
        dp = [0] * (target+1)
        for n in nums:
            if n <= target:
                dp[n] = 1
​
        for i in xrange(1, target+1):
            for n in nums:
                dp[i] += dp[max(i-n, 0)]
                
        return dp[target]
        
```

### [78\. Subsets](https://leetcode.com/problems/subsets/)

Difficulty: **Medium**


Given a set of **distinct** integers, _nums_, return all possible subsets (the power set).

**Note:** The solution set must not contain duplicate subsets.

**Example:**

```
Input: nums = [1,2,3]
Output:
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]
```

##### Discussion
直接代公式
結案，我愛backtracking

#### Solution

Language: **Python**

```python
class Solution(object):
    
    def helper(self, start, nums, current, ans):
        
        ans.append(current)
        
        for idx in xrange(start, len(nums)):
            self.helper(idx+1, nums, current+[nums[idx]], ans)
        
        
    def subsets(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        ans = []
        
        self.helper(0, nums, [], ans)
        return ans
        
```

### [90\. Subsets II](https://leetcode.com/problems/subsets-ii/)

Difficulty: **Medium**


Given a collection of integers that might contain duplicates, **_nums_**, return all possible subsets (the power set).

**Note:** The solution set must not contain duplicate subsets.

**Example:**

```
Input: [1,2,2]
Output:
[
  [2],
  [1],
  [1,2,2],
  [2,2],
  [1,2],
  []
]
```

##### Discussion
帶公式+Tip4，人生是美好的

#### Solution

Language: **Python**

```python
class Solution(object):
    def helper(self, start, nums, current, ans):
        ans.append(current)
        
        for idx in xrange(start, len(nums)):
            if idx > start and nums[idx] == nums[idx-1]: continue
            self.helper(idx+1, nums, current+[nums[idx]], ans)
    
    def subsetsWithDup(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        nums.sort()
        ans = []
        self.helper(0, nums, [], ans)
        return ans
    
```

## <a name="stack"></a>Stack
### [739\. Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)

Difficulty: **Medium**


Given a list of daily temperatures `T`, return a list such that, for each day in the input, tells you how many days you would have to wait until a warmer temperature. If there is no future day for which this is possible, put `0` instead.

For example, given the list of temperatures `T = [73, 74, 75, 71, 69, 72, 76, 73]`, your output should be `[1, 1, 4, 2, 1, 1, 0, 0]`.

**Note:** The length of `temperatures` will be in the range `[1, 30000]`. Each temperature will be an integer in the range `[30, 100]`.

##### Discussion
stack標準題，只要過去的資訊，需要等未來的某個事件發生才需要被處理，可以試試看stack
以這題來說，他需要找到遞減的天數，但是在值變大的時候，不知道距離多少
所以先推進stack，直到值變小的那天，再看看是否要pop，如果原本的值更小，那就不pop
讓天數繼續累積

#### Solution

Language: **Python**

```python
class Solution(object):
    def dailyTemperatures(self, T):
        """
        :type T: List[int]
        :rtype: List[int]
        """
        ans = [None] * len(T)
        stack = []
        for i, n in enumerate(T):      
            while stack:
                top = stack[-1]
                if top[1] < n:
                    ans[top[0]] = i - top[0]
                    del stack[-1]
                else:
                    break
            stack.append([i, n])
        for v in stack:
            ans[v[0]] = 0
        
        return ans
```
### [155\. Min Stack](https://leetcode.com/problems/min-stack/)

Difficulty: **Easy**


Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

*   push(x) -- Push element x onto stack.
*   pop() -- Removes the element on top of the stack.
*   top() -- Get the top element.
*   getMin() -- Retrieve the minimum element in the stack.

**Example:**

```
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> Returns -3.
minStack.pop();
minStack.top();      --> Returns 0.
minStack.getMin();   --> Returns -2.
```
##### Discussion
需要在O(1)得到stack最小的值，只要把推入stack的時候的min一起推就可以

#### Solution

Language: **Python**

```python
class MinStack(object):
​
    def __init__(self):
        """
        initialize your data structure here.
        """
        self.stack = []
​
    def push(self, x):
        """
        :type x: int
        :rtype: None
        """
        if not self.stack:
            self.stack.append((x, x))
        else:
            self.stack.append((x, min(x, self.stack[-1][1])))
​
    def pop(self):
        """
        :rtype: None
        """
        del self.stack[-1]
​
    def top(self):
        """
        :rtype: int
        """
        if not self.stack: 
            return None
        return self.stack[-1][0]
​
    def getMin(self):
        """
        :rtype: int
        """
        if not self.stack:
            return None
        return self.stack[-1][1]
​
    def top(self):
        """
        :rtype: int
        """
        if not self.stack: 
            return None
        return self.stack[-1][0]
​
    def getMin(self):
        """
        :rtype: int
```

### [20\. Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)

Difficulty: **Easy**


Given a string containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid.

An input string is valid if:

1.  Open brackets must be closed by the same type of brackets.
2.  Open brackets must be closed in the correct order.

Note that an empty string is also considered valid.

**Example 1:**

```
Input: "()"
Output: true
```

**Example 2:**

```
Input: "()[]{}"
Output: true
```

**Example 3:**

```
Input: "(]"
Output: false
```

**Example 4:**

```
Input: "([)]"
Output: false
```

**Example 5:**

```
Input: "{[]}"
Output: true
```
##### Discussion
經典的stack題，當右括號出現時，stack的頂如果不是對應的左括號，就不是valid
最後記得要檢查stack是不是空的

#### Solution

Language: **Python**

```python
class Solution(object):
    def isValid(self, s):
        """
        :type s: str
        :rtype: bool
        """
        stack, mapping = [], {}
        mapping['('] = ')'
        mapping['['] = ']'
        mapping['{'] = '}'
        for c in s:
            if c in ['(', '[', '{']:
                stack.append(c)
            else:
                if not stack or mapping[stack[-1]] != c:
                    return False
                else:
                    stack.pop()
        return len(stack) == 0
```

### [150\. Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/)

Difficulty: **Medium**


Evaluate the value of an arithmetic expression in .

Valid operators are `+`, `-`, `*`, `/`. Each operand may be an integer or another expression.

**Note:**

*   Division between two integers should truncate toward zero.
*   The given RPN expression is always valid. That means the expression would always evaluate to a result and there won't be any divide by zero operation.

**Example 1:**

```
Input: ["2", "1", "+", "3", "*"]
Output: 9
Explanation: ((2 + 1) * 3) = 9
```

**Example 2:**

```
Input: ["4", "13", "5", "/", "+"]
Output: 6
Explanation: (4 + (13 / 5)) = 6
```

**Example 3:**

```
Input: ["10", "6", "9", "3", "+", "-11", "*", "/", "*", "17", "+", "5", "+"]
Output: 22
Explanation: 
  ((10 * (6 / ((9 + 3) * -11))) + 17) + 5
= ((10 * (6 / (12 * -11))) + 17) + 5
= ((10 * (6 / -132)) + 17) + 5
= ((10 * 0) + 17) + 5
= (0 + 17) + 5
= 17 + 5
= 22
```

##### Discussion
**Division between two integers should truncate toward zero.**
寫法為int(float(x)/y)

#### Solution

Language: **Python**

```python
class Solution(object):
    def evalRPN(self, tokens):
        """
        :type tokens: List[str]
        :rtype: int
        """
        stack = []
        for token in tokens:
            if token in ['+', '-', '*', '/']:
                x, y = stack[-2], stack[-1]
                stack.pop()
                stack.pop()
                if token == '+':
                    stack.append(x+y)
                elif token == '-':
                    stack.append(x-y)
                elif token == '*':
                    stack.append(x*y)
                else:
                    stack.append(int(float(x)/y))
            else:
                stack.append(int(token))
            
        return stack[0]
```
### [622\. Design Circular Queue](https://leetcode.com/problems/design-circular-queue/)

Difficulty: **Medium**


Design your implementation of the circular queue. The circular queue is a linear data structure in which the operations are performed based on FIFO (First In First Out) principle and the last position is connected back to the first position to make a circle. It is also called "Ring Buffer".

One of the benefits of the circular queue is that we can make use of the spaces in front of the queue. In a normal queue, once the queue becomes full, we cannot insert the next element even if there is a space in front of the queue. But using the circular queue, we can use the space to store new values.

Your implementation should support following operations:

*   `MyCircularQueue(k)`: Constructor, set the size of the queue to be k.
*   `Front`: Get the front item from the queue. If the queue is empty, return -1.
*   `Rear`: Get the last item from the queue. If the queue is empty, return -1.
*   `enQueue(value)`: Insert an element into the circular queue. Return true if the operation is successful.
*   `deQueue()`: Delete an element from the circular queue. Return true if the operation is successful.
*   `isEmpty()`: Checks whether the circular queue is empty or not.
*   `isFull()`: Checks whether the circular queue is full or not.

**Example:**

```
MyCircularQueue circularQueue = new MyCircularQueue(3); // set the size to be 3
circularQueue.enQueue(1);  // return true
circularQueue.enQueue(2);  // return true
circularQueue.enQueue(3);  // return true
circularQueue.enQueue(4);  // return false, the queue is full
circularQueue.Rear();  // return 3
circularQueue.isFull();  // return true
circularQueue.deQueue();  // return true
circularQueue.enQueue(4);  // return true
circularQueue.Rear();  // return 4
```

**Note:**

*   All values will be in the range of [0, 1000].
*   The number of operations will be in the range of [1, 1000].
*   Please do not use the built-in Queue library.

##### Discussion
有個count會更好用

#### Solution

Language: **Python**

```python
class MyCircularQueue(object):
​
    def __init__(self, k):
        """
        Initialize your data structure here. Set the size of the queue to be k.
        :type k: int
        """
        self.queue = [None] * k
        self.head = 0
        self.tail = 0
        self.count = 0
        self.k = k
​
    def enQueue(self, value):
        """
        Insert an element into the circular queue. Return true if the operation is successful.
        :type value: int
        :rtype: bool
        """
        if self.count != self.k:
            self.queue[self.tail] = value
            self.tail = (self.tail + 1) % self.k
```
