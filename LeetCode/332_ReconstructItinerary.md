---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 332. Reconstruct Itinerary

> <https://leetcode.com/problems/reconstruct-itinerary/description/>

Given a list of airline tickets represented by pairs of departure and arrival airports `[from, to]`, reconstruct the itinerary in order. All of the tickets belong to a man who departs from `JFK`. Thus, the itinerary must begin with `JFK`.

**Note**:

1. If there are multiple valid itineraries, you should return the itinerary that has the smallest lexical order when read as a single string. For example, the itinerary `["JFK", "LGA"]` has a smaller lexical order than `["JFK", "LGB"]`.
2. All airports are represented by three capital letters (IATA code).
3. You may assume all tickets form at least one valid itinerary.

**Example 1**:
> **Input**: tickets = `[["MUC", "LHR"], ["JFK", "MUC"], ["SFO", "SJC"], ["LHR", "SFO"]]`
**Output**: `["JFK", "MUC", "LHR", "SFO", "SJC"]`

**Example 2**:

>**Input**: tickets = `[["JFK","SFO"],["JFK","ATL"],["SFO","ATL"],["ATL","JFK"],["ATL","SFO"]]`
**Output**: `["JFK","ATL","JFK","SFO","ATL","SFO"]`
**Explanation**: 
Another possible reconstruction is `["JFK","SFO","ATL","JFK","ATL","SFO"]`. But it is larger in lexical order.

## Solution

### Solution-1

递归动态规划，时间较长：
```python
class Solution:
    @staticmethod
    def get_route(itin, cur_city, city_cnt):
        import copy
        route = [cur_city]
        city_cnt -= 1
        while cur_city in itin and len(itin[cur_city]):
            if len(itin[cur_city])==1:
                route.append(itin[cur_city].pop())
                cur_city = route[-1]
                city_cnt -= 1
            else:
                for i in range(len(itin[cur_city])):
                    tmp_itin = copy.deepcopy(itin)
                    tmp_city = itin[cur_city][i]
                    del tmp_itin[cur_city][i]
                    remain_route = Solution.get_route(tmp_itin, tmp_city, city_cnt)
                    if remain_route!=None:
                        route += remain_route
                        cur_city = None
                        city_cnt = 0
                        break
                else:
                    break
        if city_cnt!=0:
            return None
        else:
            return route
        
    def findItinerary(self, tickets):
        """
        :type tickets: List[List[str]]
        :rtype: List[str]
        """
        from collections import deque
        import bisect
        itinerary = dict()
        for t_from, t_to in tickets:
            if t_from not in itinerary:
                itinerary[t_from] = deque([t_to])
            else:
                bisect.insort(itinerary[t_from], t_to)
        cur_city = 'JFK'
        route = Solution.get_route(itinerary, cur_city, len(tickets)+1)
        return route
```

### Solution-2

递归+技巧

```python
from collections import deque
import bisect
class Solution:
    def dfs(self, itin, cur_city, route):
        while cur_city in itin and itin[cur_city]:
            next_city = itin[cur_city].popleft()
            self.dfs(itin, next_city, route)
        route.appendleft(cur_city)
    def findItinerary(self, tickets):
        """
        :type tickets: List[List[str]]
        :rtype: List[str]
        """
        itinerary = dict()
        for t_from, t_to in tickets:
            if t_from not in itinerary:
                itinerary[t_from] = deque([t_to])
            else:
                bisect.insort(itinerary[t_from], t_to)
        route = deque([])
        cur_city = 'JFK'
        self.dfs(itinerary, cur_city, route)
        return route
```

### Solution-3

递归+技巧+堆

```python
from collections import deque, defaultdict
import heapq

class Solution:
    def findItinerary(self, tickets):
        """
        :type tickets: List[List[str]]
        :rtype: List[str]
        """
        edges = defaultdict(list)
        for ticket in tickets:
            heapq.heappush(edges[ticket[0]], ticket[1])
        order = deque([])
        self.dfs(order, edges, 'JFK')
        return list(order)
    
    def dfs(self, order, edges, city):
        while edges[city]:
            next_city = heapq.heappop(edges[city])
            self.dfs(order, edges, next_city)
        order.appendleft(city)
```

