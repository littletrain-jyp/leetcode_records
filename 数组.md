题号：
- [704 二分查找](#704 二分查找)
    - [35 搜索插入位置](# 35 搜索插入位置)
    - [34 在排序数组中查找元素的第一个和最后一个位置](# 34 在排序数组中查找元素的第一个和最后一个位置)





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