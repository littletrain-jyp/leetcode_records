题号：
- [704 二分查找](#704 二分查找)
    - [35 搜索插入位置](# 35 搜索插入位置)
    - [34 在排序数组中查找元素的第一个和最后一个位置](# 34 在排序数组中查找元素的第一个和最后一个位置)
- [27 移除元素](# 27移除元素)
    - [26 删除排序数组中的重复项](# 26 删除排序数组中的重复项)


# 704 二分查找
```python

from typing import List
# 解法1： 左闭右闭区间
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1

        # 左闭右闭区间
        while(left <= right): # 因为left==right 是有意义的
            mid = int((left + right)/2)
            if target < nums[mid] :
                right = mid - 1 # 因为当前的mid一定不是target了
            elif target > nums[mid]:
                left = mid + 1
            else:
                return mid
        
        return -1
    
# 解法2
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums)

        # 左闭右开区间
        while(left < right): # 因为left==right 无意义的
            mid = int((left + right)/2)
            if target < nums[mid] :
                right = mid #  下一个查询区间就不会比较当前的mid了
            elif target > nums[mid]:
                left = mid + 1
            else:
                return mid
        
        return -1
"""
关键点是理清区间的定义。
"""

```

## 35 搜索插入位置
```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1

        # 左闭右闭区间
        while(left <= right): # 因为left==right 是有意义的
            mid = int((left + right)/2)
            if target < nums[mid] :
                right = mid - 1 # 因为当前的mid一定不是target了
            elif target > nums[mid]:
                left = mid + 1
            else:
                return mid
        
        # 分四种情况
        # 1.目标在所有元素之前 此时left right 为[0, -1]
        # 2.目标值等于数组中的某个元素，return mid
        # 3.目标值介于数组中的两个元素之间。此时跳出循环的前一个条件一定是left=mid=right，接下来，若target > nums[mid] , 则 left = mid + 1，此时应该插入的位置是right + 1 = left = mid + 1， 若target < nums[mid] ，则 right = mid - 1, 但此时应该插入的位置是right + 1 = mid = left
        # 4.目标在所有元素之后，此时left right 为[n, n-1]
        # 所以最后return left 或者right + 1都可以，因为最后肯定是left>right,且left = right + 1
        return left
```

## 34 在排序数组中查找元素的第一个和最后一个位置
```python
# 解法1： 二分遍历找到值后再找左右界
class Solution:
    def searchRange(self, nums: List[int], target: int) -> List[int]:
        left = 0
        right = len(nums) - 1

        while (left <= right): 
            mid = left + ((right - left) / 2) 

            if target > nums[mid]:
                left = mid + 1
            elif target < nums[mid]:
                right = mid - 1
            else:
                s = mid - 1
                while(s >= 0):
                    if nums[s] == target:
                        s = s - 1
                    else:
                        break

                e = mid + 1
                while(e <= len(nums) - 1):
                    if nums[e] == target:
                        e = e + 1
                    else:
                        break
                
                return [s+1,e-1]
        
        return [-1, -1]
    
# 解法2： 采用两个二分查找分别寻找左边界和右边界
class Solution:
    def searchRange(self, nums: List[int], target: int) -> List[int]:
        
        def get_right_border(nums: List[int], target: int) -> int:
            left = 0
            right = len(nums) - 1
            
            right_border = -2

            while (left <= right): 
                mid = left + ((right - left) // 2) 

                if target < nums[mid]:
                    right = mid - 1
                else: #寻找右边界, 这里找到的右边界是不包含target的
                    left = mid + 1
                    right_border = left # 
            
            return right_border
        
        def get_left_border(nums: List[int], target: int) -> int:
            left = 0
            right = len(nums) - 1
            
            left_border = -2

            while (left <= right): 
                mid = left + ((right - left) // 2) 

                if target > nums[mid]:
                    left = mid + 1
                else: #寻找右边界, 这里找到的右边界是不包含target的
                    right = mid - 1
                    left_border = right # 
            
            return left_border


        left_border = get_left_border(nums, target)
        right_border = get_right_border(nums, target)
        if (left_border == -2 or right_border == -2): 
            return [-1,-1]
        if (right_border - left_border > 1):
            return [left_border + 1, right_border-1]
        return [-1,-1]

```


# 27 移除元素
采用双指针法（快慢指针）： 核心是**通过一个快指针和一个慢指针在一个for循环里完成两个for循环的工作**
```python
#  同向双指针解法， 不改变相对位置。
class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:
        n = len(nums)
        
        slow = 0
        fast = 0
        while(fast < n):
            # 用来收集不等于的值，如果fast对应值不等于val, 则把它和slow替换，并让slow前进。
            if (nums[fast] != val):
                nums[slow] = nums[fast]
                slow += 1
            fast += 1
        return slow


# 相向双指针，改变元素相对位置
class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:
        n = len(nums)
        
        left = 0
        right = n - 1
        
        while(left <= right):
            # left 用来记录边界
            if nums[left] == val: 
                if nums[right] != val:
                    nums[left], nums[right] = nums[right], nums[left]
                    left += 1
                right -= 1  # 如果nums[right] 不是val,那么要交换位置，然后right-1，如果是val,那正好，是要去掉的对象，也要right-1
            else:
                left += 1 # 没找到val,left 就一直前进
        return left

```

## 26 删除排序数组中的重复项
```python
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        n = len(nums)
        
        slow = 0
        fast = 0
        tmp = nums[0]
        while (fast < n):
            # fast 用来保留那些不同的值,与上一题不同的是，要动态更新tmp
            if (nums[fast] != tmp):
                slow += 1
                nums[slow] = nums[fast]
                tmp = nums[fast]
            fast += 1
        return slow + 1 # 返回的是数组的长度, 不是数组的边界
```
## 283 移动零
```python

# 先将0删掉，再补零
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        n = len(nums)
        
        slow = 0
        fast = 0
        while (fast < n):
            if (nums[fast] != 0):
                nums[slow] = nums[fast]
                slow += 1
            fast += 1
        while (slow < n):
            nums[slow] = 0
            slow += 1
        return
    
# 直接交换
# 慢指针指向当前已处理好的序列的尾部，右指针指向待处理序列的头部。
# 则有以下性质：
# 1. 左指针左边均为非零数。 2. 右指针左边直到左指针处均为0
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        n = len(nums)
        
        slow = 0
        fast = 0
        while (fast < n):
            if (nums[fast] != 0):
                nums[slow], nums[fast] = nums[fast], nums[slow] # 交换后，每一步都会停下来
                slow += 1
            fast += 1
        return
```
## 844 比较含退格的字符串
```python
class Solution:
    def backspaceCompare(self, s: str, t: str) -> bool:
        list_s, list_t = list(s), list(t)
        idx_a = 0
        
        for i in range(len(list_s)):
            if list_s[i] != '#':
                list_s[idx_a] = list_s[i]
                idx_a += 1
            else:
                idx_a -= 1 if idx_a > 0 else 0
                
        idx_b = 0
        for i in range(len(list_t)):
            if list_t[i] != '#':
                list_t[idx_b] = list_t[i]
                idx_b += 1
            else:
                idx_b -= 1 if idx_b > 0 else 0
        return list_s[:idx_a] == list_t[:idx_b]

```


# 977 有序数组的平方
因为数组有序，所以数组平方最大的就在数组的两端，不是左边就是右边，不可能是中间。所有采用左右指针
```python
class Solution:
    def sortedSquares(self, nums: List[int]) -> List[int]:
        n = len(nums)
        
        left = 0
        right = n-1
        
        res = [0] * n # 要从后往前修改元素
        i = n - 1
        while(left <= right):
            if (nums[left] ** 2 >= nums[right] ** 2):
                res[i] = nums[left] ** 2
                left += 1
            else:
                res[i] = nums[right] ** 2
                right -= 1
            i -= 1
        return res
```

# 209 长度最小的子数组
采用**滑动窗口**，即不断调整 子序列的起始位置和终止位置，从而得出想要的结果。
在暴力解法中，是一个for循环滑动窗口的起始位置，一个for循环为滑动窗口的终止位置，用两个for循环完成一个不断搜索区间的过程。
那么滑动窗口用一个for循环是如何完成这个操作呢：如果一个for循环，那么应该表示的是滑动窗口的起始还是终止位置，如果是起始位置，那么如何找终止位置，又会陷入for循环。所以**滑动窗口用一个for循环，其索引必然是窗口的终止位置**。

```python
class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        res = float("inf") # 定义一个无限大的数

        start = 0 # 滑动窗口的起始位置
        num_sum = 0 # 总和
        for end in range(len(nums)):
            num_sum += nums[end]

            while(num_sum >= target): # 这里循环的目的是找起始位置。如果窗口内的总值大于或者等于目标值，即满足条件，就要先更新res，因为题目的要求是大于等于。
                res = min(res, end - start + 1)
                num_sum -= nums[start]
                start += 1
        
        return 0 if res == float("inf") else res
# 时间复杂度O(n):不是for里面放个while就是O(n^2)，要看每个元素被操作的次数，这里每个元素被放进来一次，出去一次，时间复杂度是2n所以是O(n)
# 空间复杂度O(1)
```

## 904 水果成篮

上一题209是最小滑窗，这一题是最大滑窗。

**滑动窗口**：
- 滑动窗口是**右指针先出发**，左指针视情况追赶右指针。可类比男生暗恋女生，两人都往前走，但男生总是默默跟着女生但也不敢超过她。因此**右指针最多遍历一遍数组，左指针也最多遍历一遍数组，时间复杂度不超过O(2n)**。接下来的问题就是如何判断滑动窗口内是否满足题设条件：
    - (1) 要么遍历这个滑窗，通过遍历来判断滑窗是否满足，需要O(n), 总时间O(n^2)
    - (2) 要么选择字典，用空间换时间，那么判断滑窗需要O(1), 总时间O(n)

[参考题解](https://leetcode.cn/problems/fruit-into-baskets/solution/shen-du-jie-xi-zhe-dao-ti-he-by-linzeyin-6crr/)


**最小滑动窗口模板**
```python
for end in range(len(nums)):
    判断[start, end] 是否满足条件
    while 满足条件:
        不断更新结果（要在while内更新！）
        start += 1(最大程度压缩start, 使滑窗尽可能小)
```

**最大滑动窗口模板**
```python 
for end in range(len(nums)):
    判断[start, end]是否满足条件
    while 满足条件:
        start += 1（最保守的压缩start, 一旦满足条件就退出压缩的过程，使滑窗尽可能大）
    不断更新结果（要在while外更新）
```
所以关键区别在于，**最大滑窗是在迭代右移右边界的过程中更新结果，而最小滑窗则是在迭代右移左边界的过程中更新结果**，所以虽然都是滑窗，但是二者的模板和对应的贪心思路并不一样。


```python

class Solution:
    def totalFruit(self, fruits: List[int]) -> int:
        # 采用哈希表和一个中间变量来定义 当前条件
        buckets = defaultdict(int)
        class_cnt = 0

        res = 0
        start = 0

        for end in range(len(fruits)):
            #  判断当前是否满足条件
            if buckets[fruits[end]] == 0:
                class_cnt += 1
            buckets[fruits[end]] += 1

            # 开始更新左指针
            while class_cnt > 2:
                if buckets[fruits[start]] == 1:
                    class_cnt -= 1
                buckets[fruits[start]] -= 1
                start += 1

            # 满足条件了，要更新结果了
            res = max(res, end - start + 1)
        return res 

```