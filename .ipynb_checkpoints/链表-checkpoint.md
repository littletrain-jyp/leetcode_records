注意链表的定义，在python里

# 203 移除链表元素
```python
# 添加虚拟节点
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def removeElements(self, head: Optional[ListNode], val: int) -> Optional[ListNode]:
        dummy_node = ListNode(0, head)
        st = dummy_node
        while (st.next != None):
            if st.next.val == val: # 如果下一个正好是要删除的值
                st.next = st.next.next # 执行删除操作
            else:  # 注意这里是else， 因为删除了之后，下一个节点又重置了，所以要再一次判断，不能往后移动
                st = st.next
        return dummy_node.next

```
链表的两种操作方式：
- 直接使用原来的链表进行删除操作
- **设置一个虚拟头结点再进行删除操作** 这样不用专门针对头节点处理

# 707 设计链表

