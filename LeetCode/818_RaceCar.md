---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 818. Race Car

> <https://leetcode.com/problems/race-car/description/>

## Description

Your car starts at position 0 and speed +1 on an infinite number line.  (Your car can go into negative positions.)

Your car drives automatically according to a sequence of instructions A (accelerate) and R (reverse).

When you get an instruction "A", your car does the following: `position += speed, speed *= 2`.

When you get an instruction "R", your car does the following: if your speed is positive then `speed = -1` , otherwise `speed = 1`.  (Your position stays the same.)

For example, after commands "AAR", your car goes to positions 0->1->3->3, and your speed goes to 1->2->4->-1.

Now for some target position, say the **length** of the shortest sequence of instructions to get there.

## Solution

### Solution 1 -- 临界状态递归

使用递归求解：不断向目标靠近，到达快接近目标位置时停下来，接近目标位置时指值执行 “A” 操作将超越目标位置，而不执行 “A” 操作又没到达目标位置的状态。

```python
class Solution:
    def racecar(self, target):
        """
        :type target: int
        :rtype: int
        """
        def search_actions(aim, speed):
            if aim==0:
                return ""
            pos = 0
            actions = ""
            while abs(aim-pos) >= abs(speed):
                pos += speed
                actions += 'A'
                speed *= 2
            if pos == aim:
                return actions
            method1 = actions + 'RR' + search_actions(aim-pos, speed/abs(speed))
            pos += speed
            method2 = actions + 'AR' + search_actions(aim-pos, -speed/abs(speed))
            return method1 if len(method1)<len(method2) else method2
        actions = ""
        speed = 1
        if target < 0:
            actions += "R"
            speed = -1
        actions += search_actions(target, speed)
        return len(actions)
```
- 优点：具有完备性，时间复杂度和空间复杂度都非常小
- 缺点：得到的不一定为优解

**在实际的生产中很可能选择这种方法，找到的是次优解，但是算法的速度非常快，并且和最优解的差距也不大。**

### Solution 2 -- 迭代加长搜索

```python
class Solution:
    def racecar(self, target):
        """
        :type target: int
        :rtype: int
        """
        def search_actions(aim, max_depth):
            from collections import namedtuple
            if aim==0:
                return ""
            Node = namedtuple('Node', 'actions pos speed')
            frontier = []
            frontier.append(Node("", 0, 1))
            ret_actions = None
            while len(frontier) and ret_actions==None:
                cur = frontier.pop()
                actionA = Node(cur.actions+'A', cur.pos+cur.speed,
                              cur.speed*2)
                actionR = Node(cur.actions+'R', cur.pos,
                              -cur.speed//abs(cur.speed))
                if actionA.pos==aim:
                    ret_actions = actionA.actions
                else:
                    if max_depth > len(cur.actions)+1:
                        frontier.append(actionR)
                        frontier.append(actionA)
            return ret_actions
        max_depth = 1
        while True:
            actions = search_actions(target, max_depth)
            if actions!=None:
                break
            else:
                max_depth += 1
        return len(actions)
```
- 优点：完备性，最优性，空间复杂度较小(与BFS相比)
- 缺点：时间复杂度大

## Solution3 -- BFS

```python
class Solution:
    def racecar(self, target):
        """
        :type target: int
        :rtype: int
        """
        from collections import namedtuple
        from collections import deque
        if target==0:
            return ""
        Node = namedtuple('Node', 'actions pos speed')
        frontier = deque()
        frontier.append(Node("", 0, 1))
        ret_actions = None
        while len(frontier) and ret_actions==None:
            cur = frontier.popleft()
            actionA = Node(cur.actions+'A', cur.pos+cur.speed,
                          cur.speed*2)
            actionR = Node(cur.actions+'R', cur.pos,
                          -cur.speed//abs(cur.speed))
            if actionA.pos==target:
                ret_actions = actionA.actions
            else:
                frontier.append(actionR)
                frontier.append(actionA)
        return len(ret_actions)
```
- 优点：完备性，最优性
- 缺点：时间复杂度大（比迭代加长小），空间复杂度大

## Solution4 -- 去除重复状态BFS

上面的BFS算法还是会存在很多的重复状态，通过一个集合将重复状态记录下来，以避免产生重复状态。

```python
class Solution:
    def racecar(self, target):
        """
        :type target: int
        :rtype: int
        """
        from collections import namedtuple
        from collections import deque
        if target==0:
            return ""
        Node = namedtuple('Node', 'actions pos speed')
        frontier = deque()
        frontier.append(Node("", 0, 1))
        states = set((0, 1))
        ret_actions = None
        while len(frontier) and ret_actions==None:
            cur = frontier.popleft()
            actionA = Node(cur.actions+'A', cur.pos+cur.speed,
                          cur.speed*2)
            actionR = Node(cur.actions+'R', cur.pos,
                          -cur.speed//abs(cur.speed))
            if actionA.pos==target:
                ret_actions = actionA.actions
            else:
                for action in (actionA, actionR):
                    if (action.pos, action.speed) not in states:
                        states.add((action.pos, action.speed))
                        frontier.append(action)
        print(ret_actions)
        return len(ret_actions)
```
该算法比前面的快很多了，但是在 LeetCode 中测试的时候，在目标值为 5478 的时候还是超时了。

## Solution5 -- 剪枝

去除了位置`pos`与`target相反的状态`，以及 `speed` > `target*2` 的状态，最终通过测试的代码为：
```python
class Solution:
    def racecar(self, target):
        """
        :type target: int
        :rtype: int
        """
        from collections import namedtuple
        from collections import deque
        if target==0:
            return ""
        Node = namedtuple('Node', 'actions pos speed')
        frontier = deque()
        frontier.append(Node("", 0, 1))
        states = set((0, 1))
        ret_actions = None
        while len(frontier) and ret_actions==None:
            cur = frontier.popleft()
            actionA = Node(cur.actions+'A', cur.pos+cur.speed,
                          cur.speed*2)
            actionR = Node(cur.actions+'R', cur.pos,
                          -cur.speed//abs(cur.speed))
            if actionA.pos==target:
                ret_actions = actionA.actions
            else:
                for action in (actionA, actionR):
                    if ((action.pos, action.speed) not in states and
                       action.pos*target>0 and action.speed<=target*2):
                        states.add((action.pos, action.speed))
                        frontier.append(action)
        return len(ret_actions)
```
该方案的速度大大加快，并且通过了LeetCode测试。

