# Chapter 5 Beyond Classical Search

>- 对应于 "Artificial Intelligence - A Modern Approach (3rd Edition)" 中的 Chapter 4
>- BEYOND CLASSICAL SEARCH (4.1~4.6)

## 局部搜索算法和最优化问题

- 在很多优化问题中，路径是无关紧要的，重要的是到达目标
- 局部搜索算法从单个当前结点(而不是多条路径)出发，通常试图通过只移动到它的邻近状态从而提升它
- 优点1: 很少的内存
- 优点2: 常能在系统优化算法不适用的很大或无限的(连续的)状态空间中找到合理的解

局部搜索算法的目标是根据目标函数找到最佳状态。因此，对解决最优化问题十分有用

状态空间地形图：横坐标：状态，纵坐标：目标函数

- 标高 = 代价 à 全局最小值
- 标高 = 目标 à 全局最大值

### 爬山法（最陡上升）

#### 特点

- 简单的循环过程，不断向值增加的方向持续移动，即登高
- 不维护搜索树，当前结点的数据结构只记录当前状态和目标函数值
- 不考虑与当前状态不相邻的状态，“就像健忘的人在大雾中试图攀登珠穆朗玛峰一样”

#### 名词

- 局部极大值：比它的每个邻接结点都高的峰顶，但比全局最大值小。 在局部极大值附近会被拉向峰顶，并卡住。这里就会衍生出一个问题，如果直接陷入“非最大值的极大值”，就出不来了，我们会在下面介绍 __解决方案__
- 山肩（shoulder）：它有一系列状态是局部极大值，但有机会逃离开！！！
- 平的局部极大值（”flat” local maximum）：它是一系列局部极大值， 不存在向上的出口

#### 逃出困境的“随机重启爬山法”

随机重启爬山法可以克服局部极大值：尝试，再尝试

- 通过随机生成初始状态来导引爬山法搜索，直到找到目标
- 每次爬山法搜索成功的概率为 𝑝 
- 需要重新开始搜索的期望次数为1/ 𝑝 
- 所需步数 = 一次成功迭代的搜索步数 + 失败的搜索步数与 (1−p)/ p的乘积

局限性：

- 爬山法不会向值比当前结点低（或代价高的）方向搜索， 因此是不完备的，理由是会陷入局部极大值
- 随机游走是完备的，但效率极低，理由是从后继集合中等 概率随机选择后继

### 模拟退火（Simulated Annealing）

- 模拟退火 = 完备 + 效率
- 解决办法：开始使劲摇晃，然后慢慢降低强度
- 思想：通过允许一些“坏”动作来逃避局部极大值，但逐渐减小其大小和频率


例子：高地不平的平面上有个乒乓球想掉到最深的裂缝中

如果只允许乒乓球滚动，很可能停留在局部极小点；

如果平面晃动，可以是乒乓球弹出局部极小点；

技巧是晃动幅度要足够大使乒乓球弹出局部极小点，但不能太大 把它从全局最小点弹出来

_（我个人感觉这种东西就是公说公有理，婆说婆有理，谁知道应该按照什么标准测试呢...）_

### 局部束搜索（Local Beam Search）

思想：记录𝑘个状态，而不是只记录一个

- 它从 𝑘 个随机生成的状态开始
- 每一步生成 _全部 𝑘 个状态的所有后继_ 状态
- 如果 _有一个是目标_ 状态，则算法终止
- 否则，选择所有后继状态中的前𝑘个最佳状态，重复上述过程


与并行运行𝑘个随机重启搜索不同！

- 随机重启搜索：𝑘个搜索相互独立
- 局部束搜索：并行搜索线程之间会传递有用信息，产生最好后继的状态会通知其它状态加入它们的行列


局限性：很多时候，所有𝑘个状态最终都落在一小块区域

- 随机束搜索：随机选择𝑘个后继状态，偏向好的后继状态
- 类似于自然选择，状态（生物体）根据值（适应度）产生后继 （后代子孙）

### 遗传算法（Genetic algorithms）

随机束搜索的一个变形，通过把 _两个父状态结合_ 生成后继，而不是通过修改单一状态进行

遗传算法 = 随机束搜索 + 从 __状态对__ 生成后继

- 种群：随机生成的𝑘个初始状态
- 个体：每个状态，用一个有限长度的字符串表示
- 适应度函数：𝒇 𝒙 = ∑𝒊 𝒙𝒊 

## 使用不确定性动作的搜索

### 与或搜索树（AND-OR Search）

- 与结点：行动
- 或结点：状态

与或搜索问题的解是一颗子树：

1. 每个叶子上都有 目标结点
2. 在或结点上规范一个行动
3. 在与结点上包含所有可能后果

- 循环（Loop）：定义为跟自己上面的“祖宗节点”但凡有一个相同，说明重复，即：Loop，说明“失败”！

## 使用部分可观察信息的搜索

部分可观察问题：Agent可以感知，但是它的感知不足以精确描述状态

- 例如，Agent在可能的某个状态中，执行一个动作可能导致另一种可能结果（行动失败），即使环境是确定性的

解决部分可观察问题的关键概念：信念状态（belief states）

def(信念状态)：在给定 _行动序列和感知信息_ 的情况下用来表达Agent对它当前所在的物理世界的信念

### 无观察信息的搜索

在真空吸尘器世界中进行无观察（无传感）的搜索：

- 初始信念状态是 1 , 2 , 3 , 4 , 5 , 6 , 7 , 8 
- \[𝑟𝑖𝑔ℎ𝑡\] 之后，信念状态变成 2 , 4 , 6 , 8 
- \[𝑟𝑖𝑔ℎ𝑡 , 𝑠𝑢𝑐𝑘\] 之后，信念状态变成 4 , 8 
- \[𝑟𝑖𝑔ℎ𝑡 , 𝑠𝑢𝑐𝑘 , 𝑙𝑒𝑓𝑡\] 之后，信念状态变成 3 , 7 
- \[𝑟𝑖𝑔ℎ𝑡 , 𝑠𝑢𝑐𝑘 , 𝑙𝑒𝑓𝑡 , 𝑠𝑢𝑐𝑘\] 之后，信念状态变成 7 ，也就是目标状态


#### 方法理论

1. 求解无观察信息的问题，是在 _信念状态空间_ 中而 _不是实际状态空间_ 中搜索，解总是一个 __行动序列__
2. 在信念状态空间中，问题是完全可观察的，因为Agent总是知道自己的信念状态
3. 因此，可以通过构造信念状态求解搜索问题

#### 模型定义

- 转移模型：确定性的与不确定性的
- 预测：行动后生成新的状态的过程

- 信念状态数量通常在 _确定性行动_ 后 __不增加__
- 信念状态数量通常在 _不确定性行动_ 后 __增加__

#### 结果判定

1. 目标测试：一个信念状态满足目标，仅当其中所有物理状态都满足 $GOAL − TEST_P$
2. 路径消耗：理想情况下，我们可以假设所有状态下同一行动的开销是相同的

### 有观察信息的搜索

有观察信息指的是Agent可以感知到部分信息，但不是完整信息

#### 感知函数

与无观察信息搜索的问题形式化相比，有观察信息搜索的问题形式化：多了一个感知函数PERCEPT(s) ，它能返回给定状态𝑠的感知信息

- 如果这个感知本身是不确定的，它可以返回所有可能的感知信息的集合
- 完全可感知的问题中，每个状态 𝑠 下 PERCEPT(𝑠) = 𝑠
- 无感知信息问题中，PERCEPT(𝑠) = 𝑛𝑢𝑙𝑙

#### 状态转移三阶段

信念状态的转移的三个阶段:

1. 预测阶段，与无感知信息问题相同，给定信念状态𝑏的行动𝑎，预测的信念状态为：

>$b^* = PREDICT(b,a)$

2. 观察预测阶段，确定预测信念状态里可观察到的感知𝑜的集合：

> ${Possible}-{Percepts}-{Set}(b^*) = \{o: o = PERCEPT(S) \&\& s \in b\}$

3. 更新阶段，对于每个可能的感知信息，确定可能得到的 _新的信念状态_ 。新的信念状态 $b_0$ 是 $b^*$ 中可能产生该感知的状态的集合（子集）

> $b_0 = UPDATE(b^*, o) = \{s: o = PERCEPT(s) \&\& s \in b^*\}$


综上所述，对于给定的初始信念状态𝑏、行动𝑎、感知𝑜，则新的信念状态为：

>$b^* = UPDATE(PREDICT(b, a), o)$

### 部分可观察环境中的Agent

- 继续估计信念状态，并进行“与”或“或”搜索以达到目标状态的解

- 对于给定的初始信念状态𝑏、行动𝑎、感知𝑜，则新的信念状态为：$b^* = UPDATE(PREDICT(b, a), o)$

- 观察信息可以减少信念状态集合的大小，并可以帮助 Agent制定更短的计划

