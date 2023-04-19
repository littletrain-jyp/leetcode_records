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


