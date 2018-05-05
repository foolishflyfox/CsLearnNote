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
|Image analysis|Fully|Single|Determinstic|Episodic|Semi|Continuous|
|Part-picking Robot|Partially|Single|Stochastic|Episodic|Dynamic|Continuous|
|Refinery Control|Partial|Multi|Stochastic|Sequential|Dynamic|Continuous|
|Interactive English tutor|Partial|Single|Stochastic|Sequential|Dynamic|Discrete|

Agent = Architecture + Program

4 中基本的 Agent 程序：
1. 简单反射Agent
1. 基于模型的反射Agent
1. 基于目标的Agent
1. 基于效用的Agent

