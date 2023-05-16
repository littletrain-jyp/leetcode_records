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

```python
class Node(object):
    def __init__(self, val=0):
        self.val = val
        self.next = None


class MyLinkedList:

    def __init__(self):
        self.head = Node() # 是一个虚结点，不作数的
        self.size = 0  # 设置一个链表，用于记录链表的长度，增加和删除时都要记得更新
    
    # index 是从0开始
    def get(self, index: int) -> int:
        if index < 0 or index >= self.size: # 如果给的超过范围返回-1
            return -1
        cur = self.head.next # 第0个节点
        while(index): # 遍历到第index个
            cur = cur.next
            index -= 1
        return cur.val

    def addAtHead(self, val: int) -> None:
        head = Node(val)
        head.next = self.head.next
        self.head.next = head
        self.size += 1

    def addAtTail(self, val: int) -> None:
        Tail = Node(val)
        cur = self.head
        while(cur.next):
            cur = cur.next
        cur.next = Tail
        self.size += 1

    def addAtIndex(self, index: int, val: int) -> None:
        if index < 0:
            self.addAtHead(val) # 函数里面size就+1了
            return
        elif index == self.size:
            self.addAtTail(val)
            return
        elif index > self.size:
            return

        IndexNode = Node(val)
        pre = self.head
        while(index):
            pre = pre.next
            index -= 1
        IndexNode.next = pre.next
        pre.next = IndexNode
        self.size += 1

    def deleteAtIndex(self, index: int) -> None:
        if index < 0 or index >= self.size:
            return
        pre = self.head
        while(index):
            pre = pre.next
            index -= 1
        pre.next = pre.next.next
        self.size -= 1

```


# 206 反转链表
一共两种解法：迭代解法 和 递归解法。重要的是记得存储前节点和后节点

```python

# Definition for singly-linked list.
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

# 双指针解法
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        cur = head
        pre = None
        while(cur): # 这里一定要判断的是cur，不是cur.next，如果是cur.next则最后的一个节点还未被反转
            tmp = cur.next # 存储下一个节点
            cur.next = pre # 反转
            pre = cur # 更新pre
            cur = tmp #更新cur
        return pre # 此时最后一个节点被处理，cur已经往前变为None了，所以需要返回pre
    
# 递归解法
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        def reverse(pre, cur):
            if not cur: # 定义终止条件
                return pre
            tmp = cur.next # 记录下一个节点
            cur.next = pre # 反转
            return reverse(cur, tmp) # 更新pre与cur
        return reverse(None, head)
    
```
# 24 两两交换链表中的节点
正常模拟即可，不过需要定义一个头节点
```python
# Definition for singly-linked list.
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
        
class Solution:
    def swapPairs(self, head: Optional[ListNode]) -> Optional[ListNode]:
        res = ListNode(next=head)# 定义头节点，这样返回的时候只返回res.next即可
        pre = res

        # 必须要有pre的下一个和下下一个才可以交换
        while (pre.next and pre.next.next):
            cur = pre.next  # 第一个
            post = pre.next.next  # 第二个

            cur.next = post.next
            pre.next = post
            post.next = cur

            pre = cur
        return res.next
```

# 19 删除链表的倒数第N个结点
双指针。不过需要注意快指针要比慢指针快n+1个，这样慢指针才能到第倒数n+1个节点。

```python
# Definition for singly-linked list.
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
        
class Solution:
    def removeNthFromEnd(self, head: Optional[ListNode], n: int) -> Optional[ListNode]:
        # 双指针，间隔n个即可
        dummy = ListNode()
        dummy.next = head

        slow, fast = dummy, dummy
        #设置n+1步主要为了让slow到达倒数n+1个节点
        while (n >= 0): # fast先往前走n+1步, 条件设置为 n>=0， 则前进n+1步。如果设置为while(n)，则只有n步            fast = fast.next
            fast = fast.next
            n -= 1
        while (fast != None):
            fast = fast.next
            slow = slow.next

        slow.next = slow.next.next
        return dummy.next
```

# 面试题02 07.链表相交
```python
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None

class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
        lenA = 0
        lenB = 0
        cur = headA
        while (cur):
            cur = cur.next
            lenA += 1
        cur = headB
        while (cur):
            cur = cur.next
            lenB += 1
        # 这里要重新赋值下
        curA, curB = headA, headB
        # 让A成为最长链表，lenA为其长度
        if lenA < lenB:
            curA, curB = curB, curA
            lenA, lenB = lenB, lenA
        # 将其尾部对齐
        diff = lenA - lenB
        while (diff):
            curA = curA.next
            diff -= 1
        while (curA):
            if curA == curB:
                return curB
            curA = curA.next
            curB = curB.next
        return None
```

# 142 环形链表
两个问题：检测是否有环以及环的入口在哪里
1.检测有环：快慢两个指针，快指针走两个，慢指针走1个，快指针一定先进入环内，则相遇一定是在环内。
2.环的入口在哪里： 两个指针分别从相遇的地方以及起始位置运行，重合的位置一定是环的入口
快慢指针相遇时，慢指针一定是第一次进入该圈，快指针一定是第二次进入该圈。因为快指针相对慢指针是每次多走一个，所以遍历一圈一定能追上。
x为环之前的部分，y为环开始到相遇位置的部分，z为相遇位置的部分到环结束的部分，也就是x+y+z为整个链表的长度。
已知相遇的位置时慢指针走了x+y，快指针走了x+2y+z，且快指针走的步数时慢指针的两倍,所以2x+2y = x+2y+z， 所以z = x,即两个指针分别从相遇的地方以及起始位置运行，重合的位置一定是环的入口 
```python

class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None

class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        slow, fast = head, head
        while fast and fast.next:
            fast = fast.next.next
            slow = slow.next
            # 如果相遇，则分别从相遇的地方以及起始位置运行，重合的位置一定是环的入口
            if fast == slow:
                p = head
                q = slow
                while p != q:
                    p = p.next
                    q = q.next
                return p
        return None
```

总结：
链表的一大问题就是操作当前节点必须要找到前一个节点才能操作，这就造成了头借点比较尴尬，所以**使用虚拟头结点**可以避免每次对头结点都要单独处理。
