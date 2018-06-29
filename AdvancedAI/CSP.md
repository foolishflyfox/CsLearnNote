---
export_on_save:
 html: true
---
@import "/blog_head.md"

# CSP 约束满足问题

## 将问题形式化为 CSP 问题的原因

- 用 CSP 表示问题很自然：如果你有 CSP 求解系统，使用它求解问题，比使用其他技术来求解要简单得多；
- CSP 求解要比状态空间搜索要快，因为 CSP 求解能够快速消除庞大的搜索空间；例如着色问题，若一共有3种颜色可以选择，若不考虑约束传播，在计算某个节点的5个邻居时，搜索空间为 $3^5=243$ ，而如果考虑约束传播，共有 $2^5=32$ 种；

许多不能用常规状态空间搜索求解的棘手问题可以很快地用 CSP 解决；

## 实例1：作业调度问题

工厂有很多日常作业调度问题，要满足各种约束。现实中，很多这样的问题是用 CSP 技术解决的。考虑汽车组装中的调度问题，整个作业由许多任务组成，可以把每个任务处理成变量，变量的值是任务开始的时间，用整数分钟表示。约束是诸如一个任务必须在另一个任务之前的断言——例如：要组装好车轱辘后才能装轮胎——还有一些任务可以立即执行，还可以定义任务执行时间的约束。

只考虑一部分汽车组装，包括 15 个任务：安装轮轴（前和后），安装 4 个车轮（前后左右），拧紧每个车轮的螺母，安装轮毂罩和检查完整安装，可用 15 个变量表示：

$$
\begin{aligned}
X = \{&Axle_F, Axle_B, Wheel_{RF}, Wheel_{LF}, Wheel_{RB}, Wheel_{LB}, Nuts_{RF}, Nuts_{LF}, \\
&Nuts_{LF}, Nuts_{RB}, Nuts_{LB}, Cap_{RF}, Cap_{LF}, Cap_{RB}, Cap_{LB}, Inspect\}
 \end{aligned}
 $$

变量的值是任务的开始时间。下面开始引入任务间的过程约束。任务 $T_1$ 必须在任务 $T_2$ 之前完成，$T_1$ 的完成需要 $d_1$ 时间，加入如下形式的算术约束：$$T_1+d_1\le T_2$$

在这个例子中，轮轴要先于车轮安装，安装需要 10 分钟，则约束如下：
$$
\begin {aligned}
Axle_F+10\le Wheel_{RF}; Axle_F+10\le Wheel_{LF} \\
Axle_B+10\le Wheel_{RB}; Axle_B+10\le Wheel_{LB}
\end{aligned}
$$
每个车轮的安装需要 1 minute， 拧螺母需要 2 minute，安装轮毂罩需要 1 minute：
$$
\begin {aligned}
Wheel_{RF}+1\le Nuts_{RF}; Nuts_{RF}+2\le Cap_{RF} \\
Wheel_{LF}+1\le Nuts_{LF}; Nuts_{LF}+2\le Cap_{LF} \\
Wheel_{RB}+1\le Nuts_{RB}; Nuts_{RB}+2\le Cap_{RB} \\
Wheel_{LB}+1\le Nuts_{LB}; Nuts_{LB}+2\le Cap_{LB} \\
\end {aligned}
$$
若 4 个工人安装车轮，但他们必须共享一个工具来帮助安装轮轴，需要析取约束表示 $Axle_F$ 和 $Axle_B$ 在时间上不能重合：
$Axle_F+10\le Axle_B$ 或 $Axle_B+10\le Axle_F$


