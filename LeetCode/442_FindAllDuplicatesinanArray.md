---
export_on_save:
 html: true
---
@import "/blog_head.md"

# 442. Find All Duplicates in an Array
> <https://leetcode.com/problems/find-all-duplicates-in-an-array/>

Given an array of integers, 1 ≤ a[i] ≤ n (n = size of array), some elements appear **twice** and others appear **once**.

Find all the elements that appear **twice** in this array.

Could you do it without extra space and in O(n) runtime?

**Example:**
> **Input:**
[4,3,2,7,8,2,3,1]
**Output:**
[2,3]

## Solution

```python {.line-numbers}
class Solution:
    def findDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: List[int]
        """
        nums.insert(0, 0)
        p = 1
        cnt = 0
        while p!= len(nums):
            while (nums[p]!=p and nums[nums[p]]!=nums[p] 
                and cnt<100):
                tmp = nums[p]
                nums[p] = nums[nums[p]]
                nums[tmp] = tmp
            p += 1
        duplicates = []
        for i in range(len(nums)):
            if i != nums[i]:
                duplicates.append(nums[i])
        return duplicates
```

本题的功能实现较为简单，关键要做到时间复杂度为`O(n)`以及空间复杂度为`O(1)`。代码的思路较为清晰，就不再分析了，关键讲一下遇到的两个情况：

**问题1：代码的第13~15行时数据交换的操作，能不能用 `a,b = b,a` 的方式进行？**

答：如果这里使用`a,b = b,a`的方式进行数据交换，那么上面3句语句就要写成: `nums[p],nums[nums[p]] = nums[nums[p]],nums[p]`，这样是不可以的。
其实 `a,b = b,a` 在逻辑上会扩展成：`t=a;a=b;b=t`3条语句。而在本例中，第二个被复制的变量用了第一个被复制的变量作为索引，所以在第一个变量修改后，第二个变量会根据第一个变量修改后的索引设置值。
例如，我们有一个数组`a`和一个变量`x`
```python {cmd id="setax"}
a = [1,2,3,4]
x = 1
```
我希望x指向的位置和我的x互换，最后结果为 `a=[1,1,3,4]`, `x=2`:
```python {cmd continue="setax"}
x, a[x] = a[x], x
print(a, x)
```
可以发现 `a[1]=2`没变，而改变的是`a[2]`，上面的互换操作等价于：
```python {cmd continue="setax"}
t = x;x = a[x];a[x] = t
print(a, x)
```
其实如果调整互换顺序就可以了：
```python {cmd continue="setax"}
t = a[x]; a[x] = x; x = t
print(a, x)
```
上面的代码等价于：
```python {cmd continue="setax"}
a[x], x = x, a[x]
print(a, x)
```
因此，该例中的确可以将 3 行变为 1 行，不过代码为 : `nums[nums[p]],nums[p]=nums[p],nums[nums[p]]`

**总结：如果在用`a,b=b,a`型进行变量互换时，一定要保证变量`a`的变化不会影响变量`b`。**

**问题2：在语句过长时使用 `\` 作为分隔符，会使得在出现语法错误后，错误消息中提示的行号不正确**

解决方法：使用括号`( )` 将长语句括起来就可以任意分行而不用加 `\` ，也不会出现错误提示不正确的情况。

## Elegent solution

讨论区中有一种更加优雅的实现：
```python
class Solution(object):
    def findDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: List[int]
        """
        res = []
        for x in nums:
            if nums[abs(x)-1] < 0:
                res.append(abs(x))
            else:
                nums[abs(x)-1] *= -1
        return res
```

一种实现方法是开辟一个长度为`len(n)`数组进行计数，初始化全部为0，不过这样空间复杂度就变为了$O(n)$。上面实现的方法奇妙的地方是使用 **正负号进行计数** 。
