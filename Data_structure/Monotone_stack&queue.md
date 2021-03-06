## 单调栈 

**从数组中找到元素两侧大于或小于它的元素，也可用于寻找数组中的逆序部分。**

### 题目

#### 42. 接雨水

* 单调递减栈，逐层计算储水量。

```python
class Solution:
    def trap(self, height: List[int]) -> int:
        stack = []
        res = 0
        for i in range(len(height)):
            # 遇到大于栈顶的元素即可实现储水
            while stack and height[stack[-1]] < height[i]:
                cur = height[stack.pop()]   # 储水底部高度
                while stack and height[stack[-1]] == height[cur]:
                    stack.pop()
                if stack:
                    # 由左右两侧高度决定储水量
                    res += (min(height[stack[-1]],height[i])-cur)*(i-stack[-1]-1)
            stack.append(i)
        return res
```

#### 84. 柱状图中最大的矩形

* 单调递增栈，分别找到两侧第一个低于自己的矩形。

```python
class Solution:
    def largestRectangleArea(self, heights: List[int]) -> int:
        res = 0
        stack = [-1]	# 解决栈为空的情况
        heights += [0]	# 遍历结束时计算余下的矩形面积
        for i in range(len(heights)):
            while stack and heights[stack[-1]]>heights[i]:
                cur = heights[stack.pop()]
                while len(stack)>1 and heights[stack[-1]] == cur:
                    stack.pop()
                res = max(res,cur*(i-stack[-1]-1))
            stack.append(i)
        return res
```

#### 85. 最大矩形

* 单调递增栈，在矩阵的每一行计算柱状图中的最大矩形。

```python
class Solution:
    def maximalRectangle(self, matrix: List[List[str]]) -> int:
        if not matrix:
            return 0
        m, n = len(matrix), len(matrix[0])
        row = [0] * (n+1)
        res = 0
        for i in range(m):
            stack = [-1]
            for j in range(n+1):
                if j < n:
                    row[j] = row[j]+1 if matrix[i][j]=='1' else 0
                while stack and row[stack[-1]]>row[j]:
                    cur = row[stack.pop()]
                    while stack and row[stack[-1]]==cur:
                        stack.pop()
                    res = max(res,cur*(j-stack[-1]-1))
                stack.append(j)
        return res 
```

* 暴力法，求每个点所在边的左右边界和高r。

```python
class Solution:
    def maximalRectangle(self, matrix: List[List[str]]) -> int:
        if not matrix:
            return 0
        m, n = len(matrix), len(matrix[0])
        left, right, height = [0]*n, [n]*n, [0]*n
        area = 0
        for i in range(m):
            cur_left, cur_right = 0, n
            for j in range(n):
                if matrix[i][j] == '1':
                    left[j] = max(left[j], cur_left)
                    height[j] += 1
                else:
                    left[j] = 0
                    height[j] = 0
                    cur_left = j + 1
            for j in range(n-1,-1,-1):
                if matrix[i][j] == '1':
                    right[j] = min(right[j], cur_right)
                else:
                    right[j] = n
                    cur_right = j
                area = max(area, (right[j]-left[j])*height[j])
        return area
```

#### 255. 验证前序遍历二叉搜索树

* 单调递减栈，当出现局部递增时说明进入了右子树，右子树的节点必须大于父节点。

```python
class Solution:
    def verifyPreorder(self, preorder: List[int]) -> bool:
        stack = []
        max_num = float('-inf')
        for each in preorder:
            if each < max_num:
                return False
            while stack and each > stack[-1]:	# 进入右子树
                max_num = stack.pop()	# 找到当前节点的父节点，接下来的所有节点都应小于其值
            stack.append(each)
        return True
```

#### 316. 去除重复字母

* 单调递增栈，在保证之后还有字母存在的条件下，去除字典序较大的字母。

```python
class Solution:
    def removeDuplicateLetters(self, s: str) -> str:
        c = Counter(s)  # 未被使用的字母的数量
        stack = []
        memo = set() 
        for each in s:
            if each not in memo:
                while stack and stack[-1] > each and c[stack[-1]]>0:
                    memo.remove(stack.pop())
                memo.add(each)
                stack.append(each)
            c[each] -= 1
        return ''.join(stack)
```

#### 321. 拼接最大数

* 单调递减栈，遍历在两个数组中选取的数字数量，分别找到最大数字后合并。

```python
class Solution:
    def maxNumber(self, nums1: List[int], nums2: List[int], k: int) -> List[int]:
        def find(nums,i):
            # 获得数组中最大的i项
            if not i:
                return []
            stack, n = [], len(nums)-i  # 可以删除的元素数量
            for each in nums:
                while n and stack and stack[-1]<each:
                    n -= 1
                    stack.pop()
                stack.append(each)
            return stack[:i] 
        
        res = [0] * k
        for i in range(k+1):
            if i <= len(nums1) and k-i<=len(nums2):
                n1 = find(nums1,i)
                n2 = find(nums2,k-i)
                res = max(res, [max(n1,n2).pop(0) for _ in range(k)])   # 合并两个数组
        return res
```

#### 456. 132模式

* 单调递减栈。逆序维护一个单调栈，当前元素大于栈顶元素时即为可能的中间元素，由于单调栈的特性可以通过出栈找到该元素后尽可能大的元素，只要前面存在小于其的元素即满足132模式。

```python
class Solution:
    def find132pattern(self, nums: List[int]) -> bool:
        stack = []
        max_num = float('-inf')    
        for each in nums[::-1]:		# 逆序遍历，找到32的模式。
            if each < max_num:    
                return True
            while stack and stack[-1]<each:
                max_num = stack.pop()  # each作为中间元素，找到最大的元素作为3。
            stack.append(each)        
        return False
```

#### 503. 下一个更大元素 II

* 单调递减栈，遍历两次数组。

```python
class Solution:
    def nextGreaterElements(self, nums: List[int]) -> List[int]:
        n = len(nums)
        res = [-1] * n
        stack = []
        for i in range(2*n-1):
            while stack and nums[stack[-1]]<nums[i%n]:
                res[stack.pop()] = nums[i%n]
            stack.append(i%n)
        return res
```

#### 739. 每日温度

* 单调递减序列。

```python
class Solution:
    def dailyTemperatures(self, temperatures: List[int]) -> List[int]:
        n = len(temperatures)
        res = [0] * n
        stack = []
        for i in range(len(temperatures)):
            while stack and temperatures[i] > temperatures[stack[-1]]:
                res[stack[-1]] = i-stack[-1]
                stack.pop()
            stack.append(i)
        return res
```

#### 1504. 统计全1子矩形

* 单调递增栈，类似于85，统计以每一行为底的矩形的数量。

```python
class Solution:
    def numSubmat(self, mat: List[List[int]]) -> int:
        m, n = len(mat), len(mat[0])
        row = [0] * (n+1)
        res = 0
        for i in range(m):
            stack = [-1]
            for j in range(n+1):
                if j < n:
                    row[j] = row[j]+1 if mat[i][j]==1 else 0
                while stack and row[stack[-1]]>row[j]:
                    cur = row[stack.pop()]
                    while stack and row[stack[-1]]==cur:
                        stack.pop()
                    length = j-stack[-1]-1	# 矩形底边长度
                    # 子矩形数量由高度和底边长度决定。为避免重复，减去两侧最大高度。
                    res +=  (cur-max(row[stack[-1]],row[j]))*length*(length+1)//2
                stack.append(j)
        return res  
```

#### JZ33. 二叉搜索树的后序遍历序列

* 单调递增栈，类似于456。但出现局部递减时说明进入了左子树，将父节点出栈，接下来的元素必须小于父节点。

```python
class Solution:
    def verifyPostorder(self, postorder: List[int]) -> bool:
        if not postorder:
            return True
        max_num = float('inf')
        stack = []
        for each in postorder[::-1]:
            if each > max_num:
                return False
            while stack and each < stack[-1]:
                max_num = stack.pop()
            stack.append(each)
        return True
```



## 单调队列

**主要用于处理滑动窗口的最值问题。**

### 题目

#### 239. 滑动窗口的最大值

* 单调递减队列，排除无关数字。

```python
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        n = len(nums)
        q = deque([])
        for i in range(k):
            while q and q[-1]<nums[i]:
                q.pop()		# 中间的数对结果无影响
            q.append(nums[i])
        res = [q[0]]
        for i in range(n-k):
            if nums[i] == q[0]:
                q.popleft()	# 最大值离开滑动窗口
            while q and q[-1]<nums[i+k]:
                q.pop()
            q.append(nums[i+k])
            res.append(q[0])
        return res
```

#### 862. 和至少为 K 的最短子数组

* 前缀和+单调递增队列。利用单调递增队列存储前缀和，利用单调特性排除过大的前缀和，从队首开始判断和能否为K。

```python
class Solution:
    def shortestSubarray(self, nums: List[int], k: int) -> int:
        p, q = [0], deque([0])
        res = float('inf')
        for i in range(len(nums)):
            p.append(p[-1]+nums[i])
            while q and p[q[-1]]>=p[-1]:
                q.pop()	
            while q and p[-1]-p[q[0]]>=k:
                res = min(res,i-q.popleft()+1)
            q.append(i+1)
        return res if res!=float('inf') else -1
```

#### 918. 环形子数组的最大和

* 前缀和+单调递增队列

```python
class Solution:
    def maxSubarraySumCircular(self, nums: List[int]) -> int:
        n = len(nums)
        prefix = [0]
        for each in (nums+nums):
            prefix.append(prefix[-1]+each)
        res = nums[0]
        q = deque([0])
        for i in range(1,len(prefix)):
            if i-q[0]>n:
                q.popleft()
            res = max(res,prefix[i]-prefix[q[0]])
            while q and prefix[q[-1]]>=prefix[i]:
                q.pop()
            q.append(i)
        return res           
```
