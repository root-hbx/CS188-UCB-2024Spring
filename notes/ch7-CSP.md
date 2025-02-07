# Chapter 7 CSP

>- 对应于 "Artificial Intelligence - A Modern Approach (3rd Edition)" 中的 Chapter 6
>- CONSTRAINT SATISFACTION PROBLEM(6.1~6.6)

## 约束满足问题（CSP）

### 状态空间和解

求解约束满足问题CSP，需要定义状态空间和解的概念

1. 问题的状态：对部分或全部变量的一个赋值
2. 相容的（合法的）赋值：一个不违反任何约束条件的赋值
3. CSP的解：相容的、完整的赋值
4. 部分赋值：只有部分变量赋值

### CSP的形式化

- 离散变量
	- 有限值域
	- 无限值域
- 连续变量


约束图：

- 二元CSP：每个约束与两个变量相关
- 约束图：结点是变量，弧是约束

1. 一元约束与单个变量相关
2. 二元约束与两个变量相关
3. 高阶约束与三个或多个变量相关
4. 全局约束与任意个数的变量相关
5. 偏好约束（软约束），例如红色胜于绿色

## CSP的回溯搜索

### 标准搜索形式化（递增）

1. 状态：到目前为止的赋值
2. 初始状态：空赋值 { } 
3. 后继函数：赋值给一个与当前赋值不冲突的未赋值变量 → 如果没有合法赋值，将失败（无法修复）
4. 目标测试：当前赋值是完整的

- 对所有CSP都是一样的
- 路径无关紧要，因此也可以使用完整状态形式化

### 回溯搜索

- CSP的变量赋值是可交换的，即 [ 𝑊𝐴 𝑟𝑒𝑑 then 𝑁𝑇 𝑔𝑟𝑒𝑒𝑛 ] 与 [ 𝑁𝑇 𝑔𝑟𝑒𝑒𝑛 then 𝑊𝐴 𝑟𝑒𝑑 ] 相同
	- 因此，给变量赋值的时候不需要考虑顺序，只需要考虑在搜索树的每个结点上赋值给单个变量
- 具有单变量赋值的CSP的深度优先搜索称为回溯搜索
	- 每次为一个变量选择一个赋值，当没有合法的赋值时就回溯
	- 不断选择未赋值变量，轮流尝试变量值域中的每个值
	- 检测到不相容（不合法），回溯失败
- 回溯搜索是CSP的基本的无信息算法

### 最少剩余值启发式

>选择“合法”取值最少的变量

### 度启发式

最少剩余值启发式对选择第一个着色区域没有帮助，因为 初始的时候都有三种着色可能

>选择与未赋值变量约束最多的变量以降低未来分支因子

### 最少约束值启发式

- 给定一个变量，选择最少约束值
	- 排除剩余变量中最少的值的那个， 试图为剩余变量赋值留下最大空间

## 前向检验

思想

- 跟踪未赋值变量的剩余合法值
- 过滤：清除错误的选项（违反约束）
- 当任何变量没有合法值时终止搜索

前向检验将信息从赋值变量传播到未赋值变量，但不能为所有失败提供早期检验

### 弧相容

如果CSP中某变量值域中的所有取值满足该变量的所有二元约束，则称此变量是 __弧相容的__

更形式化的：对于变量xi, yi，若对于Di中的每个数值在Dj中都存在一些数值满足弧(Xi,Xj)的二元约束，则称Xi相当于Xj是弧相容的

比如，考虑约束$Y=X^2$

可以显式地写出约束是: <(X,Y), {(0,0), (1,1), (2,4), (3,9)}>

将弧相容概念扩展到处理n元约束而不仅仅是二元约束也是可能的，称之为“通用弧相容”


## CSP局部搜索

爬山法和模拟退火通常在“完整”状态下工作，即赋值 所有变量：初始状态是给每个变量都赋一个值，搜索过 程是一次改变一个变量的取值。可能会违反一些约束

局部搜索的目的：消除冲突

- 变量选择：随机选择任何有冲突的变量
- 通过最少冲突启发式选择值：
	- 选择违反约束最少的值
	- 即ℎ ( 𝑛 ) = 违反约束的总数”的爬山法

### 问题的结构与分解

以约束图表示的问题结构可用于快速找到解，而处理实际世界问题的唯一方法是将其分解为很多独立的子问题

- 独立性可以简单地通过在约束图中寻找连通子图来确定
- 每个连通子图对应于一个子问题$CSP_i$; 如果赋值$S_i$ 是$CSP_i$的一个解，那么$\bigcup_i$$S_i$ 是 $\bigcup_i$$S_i$ 的一个解

### 树结构CSP

如果约束图没有循环（树结构），则CSP可以在时间𝑂( 𝑛𝑑 ) 内求解 (线性时间)

### 近树结构CSP

条件：对一个变量赋值，删除这个变量、约束及与其邻接的值域

割集条件：对一组变量赋值，使剩下的变量形成一棵树

割集的大小为𝑐 ⟹ 𝑐小则非常快

