---
export_on_save:
 html: true
---

## 第2章 智能Agent

环境的特性：

- 完全可观察和部分可观察
- 单Agent和多Agent
- 确定的和随机的：如果环境的下一个状态完全取决于当前状态和Agent执行的动作，就说环境是确定的，否则就是随机的
- 片段式和延续式：下一个片段不依赖于眼前的片段称为片段式，如装配汽车，否则就是延续式，如下棋
- 静态、动态、半动态：环境在Agent计算过程中会变化，称为环境是动态的，环境、对计算结果的评价不随计算过程变化即为静态，环境不随计算过程变化，但评价随时间变化称为半静态
- 离散的与连续的
- 已知的与未知的：对默认规则是否已知，例如Agent如果不知道下棋规则，则环境是未知的

|Task Environment|Observable|Agents|Deterministic|Episodic|Static|Discrete|
|---|---|---|---|---|---|---|
|Crossword Puzzle|Fully|Single|Deterministic|Sequential|Static|Discrete|
|Chess with a clock|Fully|Multi|Deterministic|Sequential|Semi|Discrete|
|Poker|Partial|Multi|Stochastic|Sequential|Static|Discrete|
|Backgammon|Fully|Multi|Stochastic|Sequential|Static|Discrete|
|Taxi Driving|Partially|Multi|Stochastic|Sequential|Dynamic|Continuous|
|Medical diagnosis|Partially|Single|Sotchastic|Sequential|Dynamic|Continuous|
|Image analysis|Fully|Single|Deterministic|Episodic|Semi|Continuous|
|Part-picking Robot|Partially|Single|Stochastic|Episodic|Dynamic|Continuous|
|Refinery Control|Partial|Multi|Stochastic|Sequential|Dynamic|Continuous|
|Interactive English tutor|Partial|Single|Stochastic|Sequential|Dynamic|Discrete|

Agent = Architecture + Program

4 中基本的 Agent 程序：
1. 简单反射Agent
1. 基于模型的反射Agent
1. 基于目标的Agent
1. 基于效用的Agent

学习 Agent 被划分为 4 个概念：Critic、Learning Element、Performance element、Problem generator 。

学习元件否则改进提高，而性能元件否则选择外部行动。性能元件接受感知信息并决策，学习元件利用来自评判元件的反馈评价 Agent 做得如何，并确定应该如何修改性能元件以便将来做得更好。**当设计学习特定技能的Agent时，第一个问题不是“我该如何让 Agent 学到这个技能”，而是“Agent一旦学会了这个技能，需要何种性能元件来行使该能力？”** 评判元件根据固定的性能标准告诉学习元件Agent的运转情况。问题发生器的任务是：建议探索性任务。

问题求解算法的性能：

在设计搜索算法之前，我们需要一些标准来对算法性能进行评价：
- 完备性：当问题有解时，这个算法是否能保证找到解？
- 最优性：当存在多个解时，这个算法得到的解是否最好？
- 时间复杂度
- 空间复杂度

在 AI 领域，状态空间图大多由初始状态、行动和转移模型隐式表示，并且大多是无限的，因此，复杂度通常由下列3个量来表达：b，**分支因子**，任何节点的最多后继数；d，目标节点所在的最浅深度；m，状态空间中任何路径的最大长度。时间常常由搜索过程中产生的节点数目来度量，而空间则由在内存中存储的最多节点数来度量。大多数情况下，我们描述搜索树的时间和空间复杂度；对于图，这个答案依赖于状态空间中的路径有多冗余。

无信息搜索（盲搜索）策略：除了问题定义中提供的状态信息外没有任何附加信息。搜索算法要做的生成后继并区分目标状态与非目标状态。这些搜索策略是以节点扩展的次序来分类的。

知道一个目标状态是否比其他状态“更有希望”接近目标的策略称为**有信息搜索**或者**启发式搜索**策略。

一般来说，指数级别复杂度的搜索问题不能用无信息的搜索算法求解，除非是规模很小的实例。

一致性代价搜索（uniform-cost search）：对于任何单步代价都最优的算法，而不是像 Breadth First Search 扩展深度最浅的结果。可以通过将边缘节点集按g值排序的队列来实现。

深度受限搜索：可以将深度设置为状态空间的直径，这是一个更好的深度受限搜索。然而对于大多数问题，不到问题找到，我们是无法找到一个好的深度界限的。

深度受限搜索可以通过修改一般的树搜索算法或图搜索算法来实现。或者它可以作为简单递归算法来实现。

迭代加深的深度优先搜索（iterative deepening search）是一种常用策略，它常用于和深度优先搜索结合使用来确定最好的深度界限。

一般来说，当搜索空间较大并且不知道解所在深度时，迭代加深的深度优先搜索是首选的无信息搜索方法。

**无信息搜索策略对比**
|Criterion|BFS|Uniform Cost|DFS|Depth Limited|Iterative Deepening|Bidirectional|
|---|---|---|---|---|---|---|---|
|Complete?|Yes|Yes|No|No|Yes|Yes|
|Time|$O(b^d)$||$O(b^m)$|$O(b^l)$|$O(b^d)$|$O(b^{d/2})$|
|Space|$O(b^d)$||$O(mb)$|$O(bl)$|$O(bd)$|$O(b^{d/2})$|
|Optimal?|Yes|Yes|No|No|Yes|Yes|

$b$ 表示分支因子；$d$ 表示最浅解的深度；$m$ 表示搜索树的最大深度；$l$ 表示深度界限；右上角的含义：$^a$ 当b有限时是完备的；$^b$若对正数 $\epsilon$ 有单步代价 $\ge\epsilon$，则是完备的；$c$ 当单步代价相同时算法最优；$^d$ 当双向都使用宽度优先搜索；

**贪婪最佳优先搜索(greedy best-first search)**：试图扩展离目标最近的节点，理由是这样可能可以很快找到解，因此它只用启发式信息：$f(n)=h(n)$。

贪婪最佳优先搜索与深度优先搜索类似，即使是有限状态空间，它也是不完备的。

**A\* 搜索**是最为人所知的最佳优先搜索。它对节点的评估结合了 g(n)，即 $f(n) = g(n) + h(n)$

A\* 搜索既是完备的也是最优的。

保证最优的条件：可采纳性和一致性。可采纳启发式是指它才不会过高估计到达目标的代价。一致性（有时也称为单调性）是指设有三个点A、B、C，它们满足：$cost(A\rightarrow C) \le cost(A\rightarrow B)+cost(B\rightarrow C)$，这是一般的三角不等式。

可采纳的启发式自然是乐观的，因为它们认为的解决问题所花代价比实际代价小。

剪枝：无需检验就直接把它们从考虑中排除——在AI的很多领域中都是很重要的。

元状态空间中的每个状态都要捕捉一个程序的内部（计算状态），程序是在目标层状态空间中搜索。
