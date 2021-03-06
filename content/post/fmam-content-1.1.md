---
title: "FMAM - 1.1：指数分布"
date: 2018-07-18T17:01:14+08:00
Description: ""
Tags: [fmam]
Categories: []

---

本章节定义了指数分布和与指数分布相关联的一些基本属性。

#### 定义1.1.1：

如果一个非负随机变量Ｘ的概率分布函数（即累积分布函数 CDF）满足如下形式：

$$
F(t)=P\\{X \leq t\\}=1-exp(-\lambda t) \equiv 1-\sum _ {n=0}^{\infty}\frac{(-\lambda)^n t^n}{n!}, t \geq 0
$$

则称非负随机变量Ｘ服从指数分布。其中 $\lambda$ 是正实数，将Ｘ称为参数为 $\lambda $ 的指数随机变量。

取分布函数 F(t) 的导数，即得到指数分布的概率密度函数：

$$
f(t)=\frac{dF(t)}{dt}=\lambda exp(- \lambda t), t \geq 0
$$

#### 练习1.1.1：

式子
$$
\int _ {0} ^ {\infty} \lambda e ^ {- \lambda t} dt=1
$$
使用了分部积分法，试着从概率上解释（不使用任何计算）为什么积分的结果是 1 。

图 1.1 绘制了 $\lambda = 1$ 和 $\lambda = 5$ 时的指数分布的概率密度函数，这两者表现出了类似“指数”的形状。

![Fig.1.1](https://farm2.staticflickr.com/1820/43795309612_219af12fec_z.jpg#center)

#### 练习1.1.2：

分别绘制 $\lambda = 0.2$ 和 $\lambda = 10$ 时的指数分布的概率密度函数。

#### 练习1.1.3：

指数分布的 n 阶原点矩 (moments) 由下式给出，参数为 $\lambda$
$$
E[X ^ {n}] \equiv \int _ {i} ^ {\infty} t ^ {n} dF(t) = n!/ \lambda ^ {n},  \quad n=0, 1, 2, ...
$$
提示：使用分部积分法和归纳法 (induction method)，同样有
$$
d(exp(- \lambda t))/dt = - \lambda exp(-\lambda t)
$$

$$
d(t ^ {n})/dt = nt ^ {n-1}
$$

在练习 1.1.3 中，指数随机变量的 **均值 (mean)** ，**方差 (variance, Var)** 和**变异系数 (coefficient of variation, cv, 即标准差与均值之比)** 都可以获得。

#### 命题1.1.1：

对参数为 $\lambda $ 的指数随机变量X有：

$$
E[X]=\frac{1}{\lambda}
$$

$$
Var(X)\equiv E[(X-E[X])^2]=E[X^2]-(E[X])^2=\frac{1}{\lambda^2}
$$

$$
cv(X)\equiv \frac{\sqrt {Var(X)}}{E[X]}=\sqrt{Var(\frac{X}{E[X]})}=1
$$

我们注意到：

1. X 均值是由 $1/\lambda $ 给出的，这提供了一个简单的估算 $\lambda $的方法；
2. 所有指数分布均服从 $cv(X)=1$ ，这限制了指数分布的适用范围。

指数分布的唯一参数参数 $\lambda $ 可以明确解释不同的情况。例如，在可靠性原理中 $\lambda $ 被解释为失败率（见练习1.1.10），而它代表泊松过程中的到达率（参见第2章第2.1节）。

**注意**：在文献中，随机变量的数学期望也称为平均值，期望值。在本书中，我们将主要使用“均值”和“期望”，使用哪个取决于上下文和感兴趣的领域。

从指数分布和条件概率的定义来看，很容易发现当 $t \geq 0,s \geq 0 $ 时有：
$$
P\\{X>t+s|X>s\\}=P\\{X>t\\}
$$
这被称作无记忆性。假设 $X$ 表示时间，直观来说，无记忆性是指在给定 $X>s $ 条件下，剩余时间 $X-s$ 与已经经过的时间 $s$ 无关（译者注：原文这里有些啰嗦，无记忆性可以看预备知识）。

#### 练习1.1.4：显示指数分布的无记忆属性

有许多指数分布的现实例子。电子元件（例如电阻器）的寿命，灯泡的寿命，顾客到商店的时间间隔，这些都是指数分布的典型例子。以灯泡为例，使用过的灯泡（但不是太旧）只要能工作，也许就和新的一样好，这就意味着灯泡的寿命也许具有无记忆属性。因此，灯泡的寿命分布也许就能用指数分布来近似。

在随机建模中，由于其在所有连续随机变量之间的指数分布上的实用性和独特性，无记忆属性成为了指数分布最重要的属性。

#### 定理1.1.1：

指数随机变量是唯一的非负、非零、具有无记忆性的有限连续随机变量。证明略。

#### 练习1.1.5：

假定 $X _ {1}$ 和 $X _ {2}$ 是相互独立的指数型随机变量，并分别具有参数 $\lambda _ {1}$ 和 $\lambda _ {2}$ ，定义
$$
X = min\\{X _ {1}, X _ {2}\\}
$$
即二者之间的最小值，那么 $X$ 服从参数为 $\lambda _ {1} + \lambda _ {2}$ 的指数分布 (提示：$p\\{min\\{X _ {1}, X _ {2}\\}\\} = P\\{X _ {1} > t\\}P{X _ {2} > t}$ 。

![Fig.1.2](https://farm1.staticflickr.com/930/29973814068_6365c5e4ae_z.jpg#center)

无记忆属性可用于解释练习 1.1.5 中定义的随机变量 X 服从指数分布。因为 $X _ {1}$ 和 $X _ {2}$ 均具有无记忆性，若 $t < min\\{X _ {1}, X _ {2}\\}$ ，剩余时间 $X _ {1} - t | X _ {1} > t$ 和 $X _ {2} - t | X _ {2} > t$ 分别具有与 $X _ {1}$ 和 $X _ {2}$ 相同的指数分布。又因
$$
X - t | X > t = min\\{X _ {1} - t | X _ {1} > t, X _ {2} - t | X _ {2} > t\\}
$$
则随机变量 $X - t | X > t$ 具有和 $X$ 相同的分布。因此，随机变量 $X$ 具有无记忆属性（见图 1.2）。根据定理1.1.1，$X$ 必须是指数分布。直观上，我们可以考虑不同时期的 $X$ 的预测。$X$ 在零时刻的预测（即从零时刻开始）在概率上等于 $t$ 时刻对 $X$ 的预测 ($X > t$) 。在零时刻，$X$ 是两个相互独立的指数随机变量的最小值；在 $t$ 时刻，$X - t | X > t$ 仍是两个相互独立的指数随机变量的最小值（与前两个具有相同的参数）。因此，$X$ 具有无记忆性。 

我们注意到这里使用直观的方法来判断无记忆性，后面还将反复用来观察马尔可夫链的马尔可夫性质。

#### 练习1.1.6：

假定 $X _ {1}$ 和 $X _ {2}$ 是相互独立的指数型随机变量，并分别具有参数 $\lambda _ {1}$ 和 $\lambda _ {2}$ ，找出
$$
Y = max\\{X _ {1}, X _ {2}\\}
$$
和
$$
Z = X _ {1} + X _ {2}
$$
的分布函数和概率密度函数。那么随机变量 $Y$ 和 $Z$ 具有无记忆属性吗？直观地解释你的结论（不用任何计算，提示：在时间 $t$ 和 $s$ 时分别比较剩余时间，即 $Y - t | Y > t$, $Y - s | Y > s$, $Z - t | Z > t$, $Z - s | Z > s$, 见图 1.3）。

![Fig.1.3](https://farm2.staticflickr.com/1820/42035235420_fec620289e_z.jpg#center)

尽管练习 1.1.5 显示在 "min" 操作下指数随机变量的集合是闭合的 (closed，翻译不准确？)，但练习 1.1.6 则显示在 "max" 和 "+" 操作下集合不是闭合的。

练习 1.1.5 可以归纳为如下命题。

#### 命题1.1.2：

设 $\\{X _ j，j = 1，...，n\\} $ 是相互独立的指数随机变量，分别具有参数 $\\{λ _ j，j = 1，...，n\\} $ 。然后 
$$
X = min\\{X _ 1，...，X _ n\\}
$$
服从参数为
$$
\lambda _ 1+...+\lambda _ n
$$
的指数分布。（证明？使用归纳法）

#### 练习1.1.7：

令 $X _ {1}$ 和 $X _ {2}$ 是相互独立的指数型随机变量，并分别具有参数 $\lambda _ {1}$ 和 $\lambda _ {2}$ ，有
$$
P\\{X _ {1} \leq X _ {2}\\} = \lambda _ {1} / (\lambda _ {1} + \lambda _ {2})
$$
提示：使用条件概率
$$
P\\{X _ {1} \leq X _ {2}\\} = \int _ {0} ^ {\infty} P\\{X _ {1} \leq X _ {2} | X _ {1} = s\\} dP\\{X _ {1} \leq s\\} = \int _ {0} ^ {\infty} P\\{X _ {2} \geq s\\} dP\\{X _ {1} \leq s\\}
$$
练习 1.1.7 可以归纳为如下命题。

#### 命题1.1.3：

设 $\\{X _ j，j = 1，...，n\\}$ 是相互独立的指数随机变量，参数分别为 $\\{λ _ j，j = 1，...，n\\}$，那么 
$$
P\\{X \\}= min\\{X1...，Xn\\}=λ1/(λ1+ ... +λn)
$$

#### 练习1.1.8：使用命题 1.1.2 和练习 1.1.7 来证明命题 1.1.3

命题 1.1.2 和 1.1.3 在为包含指数分布的随机系统引入连续时间马尔可夫链起着关键性作用，我们将在本书中反复使用到这两个命题。

拉普拉斯变换 (Laplace-Stieltjes transform, LST) 是研究和应用概率分布中重要的工具，概率分布函数的 LSTs 和随机变量之间存在一对一的关系（**译注**：这里译者认为应该是“随机变量的 LSTs 和概率分布函数之间存在一一对应关系”，原文是这么写的与上下文表示不符合，有待纠正）。对于一个参数为 $\lambda $ 指数随机变量 $X$ ，当 $s \geq 0$ 时有

$$
\begin{align}
f^{*}(s) &\equiv E[e ^ {-sX}] 
\\\\ &\equiv \int _ {0} ^ {\infty} e ^ {-st} dP\\{X<t\\} 
\\\\ &= \int _ {0} ^ {\infty} e ^ {-st} \lambda e ^ {- \lambda t} dt 
\\\\ &= \lambda \int _ {0} ^ {\infty} e ^ {-(\lambda + s)t} dt \\\\ &= \frac{\lambda}{\lambda + s}
\end{align}
$$

函数 $f ^ {*} (s)$ 被称为随机变量 $X$ 的 LST 变换，那么任何随机变量经由上式的 LST 变换后都能找到一个参数为 $lambda$ 的指数分布。

指数分布可用于构造更复杂的概率分布，练习 1.1.9 提供了一个简单而着名的例子。具有参数 $(n, \lambda)$ 的 **Erlang 分布**被定义为一种概率分布，其概率密度函数
$$
f _ {(n, \lambda)}(t) = \frac{t ^ {n-1} \lambda ^ {n}}{(n-1)!} exp(- \lambda t), \quad t \geq 0
$$

#### 练习1.1.9：

假设 $\\{X _ {j}, j = 1, ..., n\\}$ 是相互独立的，具有相同参数 $\lambda $ 的指数随机变量，使用两种方法来说明 $X = X _ {1} + ... + X _ {n}$ 具有参数为 $(n, \lambda)$ 的 Erlang 分布：

1. 归纳法
2. 随机变量 $X$ 的 LSTs 和上面练习中定义的 Erlang 分布的公式 (即：$LST = \int _ {0} ^ {\infty} e ^ {-st} f _ {(n, \lambda)}(t)dt$) 。

（提示：从上面练习中的公式找出 Erlang 分布的概率分布函数。对于相互独立的随机变量，它们和的 LST 等于它们各自 LST 的结果之和 （可能不对？）。）

根据练习 1.1.9，可以将 Erlang 随机变量定义为有限数量的独立同分布的指数随机变量之和，这种关系为 Erlang 分布的使用提供了极大的灵活性。例如，这是我们立即可以得到 Erlang 分布的均值和方差，分别是 $n/ \lambda$ 和 $n/ \lambda ^ {2}$ 。

#### 练习1.1.10：

对于参数为 $\lambda $ 的指数分布，t 时刻的**失败率 (failure rate)** 被定义为 $f(t) / (1-F(t))$，由 $\lambda $ 决定。通过将其与无记忆属性联系起来，直观地解释结果。

注意
$$
P\\{t < X < t + \delta t | X > t\\} \approx \delta t f(t) / (1-F(t))
$$
在可靠性理论 ( reliability theory) 中被解释为 $(t, t + \delta t)$ 中的预期故障数。当 $\delta t \to 0$ 时
$$
P\\{t < X < t + \delta t | X > t\\} / \delta t \to f(t) / (1-F(t))
$$
被称为 t 时刻的失败率（即 t 时刻每单位时间的故障数）。

#### 命题1.1.4：

设 $\\{X _ n，n = 1,2，...\\}$ 是相互独立的指数随机变量，而且具有相同参数 $\lambda $ 。现假设一个随机变量 N ，和 $\\{X _ n，n = 1,2，...\\} $ 均两两相互独立，具有几何分布，且参数 p 为正整数 {1,2，...} 。即满足：

$$
P\\{N=n\\}=(1-p)p^{n-1},n=1,2,...
$$

定义
$$
Y=\sum _ {n=1}^{N}X _ n
$$
则 Y 服从参数为 $(1-p)\lambda $ 的指数分布。证明略。

#### Commentary：

由于指数分布在科学和工程学中被广泛使用，许多教科书也对它进行了研究。我们参考 Ross (2010) 来寻找指数分布的详细信息和 LST 等基本工具，以及随机模型中的概率生成函数。

#### Additional Exercises and Extensions：略


