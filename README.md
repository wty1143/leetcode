# Leetcode Note #

---
## Tips
- Tip1: Two pointer for sorted array (#Array 1. Two Sum)
- Tip2: Sum[i:j] = Sum[0:j] - Sum[0:i] for continuous array (# Array 560. Subarray Sum Equals K)
- Tip3: Knapsack Problem (0/1, unbounded) (#DP 322. Coin Change)
## Topic
- [**Array**](#Array)
- Hash Table
- Linked List
- Math
- Two Pointers
- String
- Binary Search
- Divide and Conquer
- [**Dynamic Programming**](#dp)
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
## 1. Two Sum (Easy)
>Given an array of integers, return indices of the two numbers such that they add up to a specific target.
>
>You may assume that each input would have exactly one solution, and you may not use the same element twice.
>
>Example:
>
>Given nums = [2, 7, 11, 15], target = 9,
>
>Because nums[0] + nums[1] = 2 + 7 = 9,
>return [0, 1].
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

## 15. 3 Sum (Medium)
>Given an array nums of n integers, are there elements a, b, c in nums such that a + b + c = 0? Find all unique triplets in the array which gives the sum of zero
>
>**Note:**
>
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

## 16. 3Sum Closest (Medium)

> Given an array nums of n integers and an integer target, find three integers in nums such that the sum is closest to target. Return the sum of the three integers. 
>
> You may assume that each input would have exactly one solution.

```
Given array nums = [-1, 2, 1, -4], and target = 1.

The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).
```
##### Discussion
> 基本上3 sum closest是完全一樣的套路，差別只有需要記錄目前最佳值，用nums[:3]，比亂選個magic number還好

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

## 560. Subarray Sum Equals K (Medium)

>Given an array of integers and an integer k, you need to find the total number of continuous subarrays whose 
>
>sum equals to k.
>
```
Input:nums = [1,1,1], k = 2
Output: 2
```
##### Discussion
> 這類型的題目，如果暴力法很簡單，基本上要一定要先試試看暴力法，一來是讓面試官至少知道你懂題目，也讓他有機會救你
>
> 或是有機會基於最初的暴力法做出優化
>
> **這題使用的技巧必須熟記，Sum[i:j] = Sum[0:j] - Sum[0:i]**

##### Solution (暴力解 63 / 80 test cases passed.)
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
##### Solution (Runtime: 88 ms, faster than 90.01%)
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

## 523. Continuous Subarray Sum (Medium)

> Given a list of non-negative numbers and a target integer k, write a function to check if the array has a
> 
> continuous subarray of size at least 2 that sums up to a multiple of k, that is, sums up to n*k where n is 
> 
> also an integer.
```
Input: [23, 2, 4, 6, 7],  k=6
Output: True
Explanation: Because [2, 4] is a continuous subarray of size 2 and sums up to 6.
```
```
Input: [23, 2, 6, 4, 7],  k=6
Output: True
Explanation: Because [23, 2, 6, 4, 7] is an continuous subarray of size 5 and sums up to 42.
```
##### Discussion
> 剛剛的變化題型，一樣是找到n[x:i]是否為k的倍數，根據剛剛的秘訣
> 
> 也就是n[0:x]使得n[0:i] - n[0:x]為k的倍數，也就代表n[0:x] % k == n[0:i] % k
> 
> 由於total % k的值也只有k種，所以記錄下每種total % k的值最早的index以確保subarray size至少是2
> 
> 但是corner case太多，通常面試不會出

##### Solution (Runtime: 184 ms, faster than 96.04%)
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
## <a name="dp"></a>Dynamic Programming
## 322. Coin Change (Medium)
> You are given coins of different denominations and a total amount of money amount. Write a function to compute
>
> the fewest number of coins that you need to make up that amount. If that amount of money cannot be made up by
> 
> any combination of the coins, return -1.

```
Input: coins = [1, 2, 5], amount = 11
Output: 3 
Explanation: 11 = 5 + 5 + 1
```
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
## 983. Minimum Cost For Tickets (Medium)
> In a country popular for train travel, you have planned some train travelling one year in advance.  The days of
>
> the year that you will travel is given as an array days.  Each day is an integer from 1 to 365.

> Train tickets are sold in 3 different ways:

> a 1-day pass is sold for costs[0] dollars;
> a 7-day pass is sold for costs[1] dollars;
> a 30-day pass is sold for costs[2] dollars.
>
> The passes allow that many days of consecutive travel.  For example, if we get a 7-day pass on day 2, then we can
>  travel for 7 days: day 2, 3, 4, 5, 6, 7, and 8.

> Return the minimum number of dollars you need to travel every day in the given list of days.

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

##### Solution (Runtime: 104 ms, faster than 5.31%，真是個垃圾方法...)
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

##### Solution (Runtime: 24 ms, faster than 91.24%)

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