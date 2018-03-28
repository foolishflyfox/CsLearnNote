---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 27. Remove Element

> https://leetcode.com/problems/remove-element/description/

Given an array and a value, remove all instances of that value *in-place* and return the new length.

Do not allocate extra space for another array, you must do this by **modifying the input array** in-place with O(1) extra memory.

The order of elements can be changed. It doesn't matter what you leave beyond the new length.

**Example:**

> Given **nums** = [3,2,2,3], **val** = 3,<br>
Your function should return length = 2, with the first two elements of nums being 2.

原地算法(in-place algorithm)：是指算法在运行过程中只需要开辟非常少量的空间，而且该空间大小与输入数据的规模无关，其空间复杂度为$O(1)$的算法。

该题较为简单，也没有涉及什么数据结构，用 Python 可以用一条语句搞定：
```python {cmd}
nums = [3,2,2,3]; val=3
print([i for i in nums if i!=val])
```

如果用C语言写，代码如下：
```C
int removeElement(int* nums, int numsSize, int val) {
    int pos = 0;
    while(pos < numsSize){
        if(val==nums[pos]){
            nums[pos] = nums[numsSize-1];
            numsSize -= 1;
        }else{
            pos += 1;
        }
    }
    return numsSize;
}
```
