---
title: "FMAM - 1.4：PH 分布和 PH 表示"
date: 2018-07-26T10:30:01+08:00
Description: ""
Tags: [fmam]
Categories: []
---

```markdown
注：本书中 "order" 译作 “阶“，原因如下：

在 "Stat Trek" 上 "Matrix Order" 的释义为：
The number of rows and columns that a matrix has is called its order or its dimension. By convention, rows are listed first; and columns, second. Thus, we would say that the order (or dimension) of the matrix below is 3 x 4, meaning that it has 3 rows and 4 columns.
矩阵具有的行数和列数 称为其 顺序 或 维度 。按照惯例，首先列出行，再给出列。因此，我们可以说下面矩阵的顺序（或维度）是3 x 4，这意味着它有 3 行 4 列。

本文中主要研究方阵，因此将 "the order of the matrix is n" 译作 ”n 阶方阵”
```

1.3节说明了 PH 表示在 PH 分布应用中的重要性。它还显示给定 PH 分布的 PH 表示并不是唯一的。在本节中，我们将进一步研究PH表示及其相关属性。我们研究算法以找到 PH 分布的简单等价的表示（例如，Coxian 表示）。

#### 例子1.4.1：

下面的 PH 分布代表参数 $\lambda = 1$ 的指数分布，可以使用定义 1.2.1 得到证明

1. $\pmb{\alpha} = (1, 0), \quad T = \begin{pmatrix}-1 & 0 \\\ 1 & -5 \end{pmatrix}$ 因为第二阶段无法到达，结果如下
2. $\pmb{\alpha} = (0.2, 0.8), \quad T = \begin{pmatrix}-2 & 1 \\\ 2 & -3 \end{pmatrix}$ 通过 $T \pmb{e} = - \pmb{e}$ ，容易证明 $exp(Tt) \pmb{e} = e ^ {-t} \pmb{e}$ ，结果如下
3. $\pmb{\alpha} = (0.5, 0.5), \quad T = \begin{pmatrix}-1 & 1 \\\ 0 & -2 \end{pmatrix}$ 通过 $\pmb{\alpha} T = - \pmb{\alpha}$ ，容易证明 $\pmb{\alpha}exp(Tt) = \pmb{\alpha} e ^ {-t}$ ，结果如下

#### 练习1.4.1：

证明 $\pmb{\alpha} = (2/3, 1/3)$ 和 $T = \begin{pmatrix}-4 & 4 \\\ 4 & -10 \end{pmatrix}$ 代表参数 $\lambda = 2$ 的指数分布。

如果两个 PH 表示代表相同的概率分布，则称二者为等效的 (equivalent) 。等效的 PH 表示可以具有不同数量的阶段，且各自的 PH 生成矩阵可以具有不同的结构。一般来说，等效 PH 表示可以通过添加辅助阶段（例如示例 1.4.1）、重新排列阶段或者转移 (transformation) 来构建。容易看出任何给定的 PH 分布具有无限多个等效 PH 表示。
#### 练习1.4.2：

下面的方法构造任意一个 PH 分布 $(\pmb{\alpha}, T)$ 的无限多个等价 PH 表示

1. 当 $S$ 是一个 PH 生成矩阵，$diag(T, S)$ 是一个有 $T$ 和 $S$ 的块对角矩阵时，证明 $(\pmb{\alpha}, T)$ 和 $((\pmb{\alpha}, 0), diag(T, S))$ 是等效的 PH 表示
2. 对于任何正整数 $n$ ，证明 $(\pmb{\alpha}, T)$ 和 $((\pmb{\alpha}, \pmb{\alpha}, ..., \pmb{\alpha})/n, diag(T, T, ..., T))$ 是等效 PH 表示
3. 当 $\\{c _ {1}, c _ {2}, ..., c _ {n}\\}$ 是非负实数且和为 1 ，证明 $(\pmb{\alpha}, T)$ 和 $((c _ {1} \pmb{\alpha}, c _ {2} \pmb{\alpha}, ..., c _ {n} \pmb{\alpha})/n, diag(T, T, ..., T))$ 是等效 PH 表示

对于 $m$ 阶的 PH 表示 $(\pmb{\alpha}, T)$ ，整数 $m$ 称作该表示的阶（注：这句话两个 order 都翻译成了 “阶”，但应该不是这么翻译）。考虑 PH 表示
$$
\left(
\pmb{\alpha} = (0.2, 0.8, 0), \quad
T = 
\begin{pmatrix}
-2 & 1.5 & 0 \\\\\\
1 & -5 & 0 \\\\\\
1 & 1 & -3
\end{pmatrix}
\right)
\tag{1.73}
$$
容易验证该 PH 表示是否可简化（即 $T + \pmb{T ^ 0 \alpha}$ 是不可简化的）。通过删除 $\pmb{\alpha}$ 中的第三个元素，$T$ 中的第三行和第三列，一个二阶等效 PH 表示就得到了。

#### 命题1.4.1：

如果 PH 表示为 $\pmb{\alpha}, T$ 是不可约（翻译不准确？）的，那么可以找到一个更小阶数（order，意思分析在练习1.4.2中，后续补充）的 PH 表示。证明略。

#### 练习1.4.3：

证明任何一个给定的 PH 分布都可以有无限多个不可简化的 PH 表示。

显然若 PH 表示的阶更高，则可以构造数量更多的不同的概率分布。假设我们想要找到一个近似概率分布 $F(t)$ 的 PH 分布，为了找到一个令人满意的结果，PH 表示的阶可能必须很大。那么必须多大的阶才能产生令人满意的近似值呢？例如，我们如何才能找到近似常数 1 的 PH 分布（即 \(F(t) = 0, if t < 1; 1, if t \geq 1\)）？下面这个著名的结论给出了对应概率分布变异系数（见命题 1.1.1）的所需阶的下限。它找到了一个 \(m\) 阶 Erlang 分布在所有具有 \(m\) 阶或更小的 PH 表示的 PH 分布中具有最小的变异系数。

#### 定理1.4.1：

一个 m 阶 PH 分布的 PH 随机变量的变异系数至少是 $1/\sqrt{m}$，且 m 阶 Erlang 分布是唯一能达到下限的分布。

#### 练习1.4.4：

一个变异系数为 0.1 的 PH 表示需要多高的阶？像图 。110 那样重新绘制 5 阶，50 阶，100阶 Erlang 分布的概率密度函数，均值为 1 。绘制阶为 5 ，50，100 且均值为 1 的人以随机生成的 PH 表示的概率密度函数，计算他们的变异系数（见练习 1.2.14）。

定理 1.4.4 可作如下推广：两个概率分布 $F(x)$ 和 $G(x)$ 分别对应随机变量 $X$ 和 $Y$ ，若对每一个凸函数 (convex function) $h(x)$ 都有 $E[h(X)] \geq E[h(Y)]$ ，则 $F(x)$ majorizes（不知道怎么翻译）$G(x)$ 。在文献中，$Y$ 被认为具有比 $X$ 更小的凸序 (convex order ，翻译不准确) （表示为 $Y \leq _ {cx} X$）（见 Shaked and Shanthikumar (2006)）。因为 $h(x) = x$ 和 $g(x) = -x$ 都是凸函数， $Y \leq _ {cx} X$ 意味着 $E[Y] = E[X]$ 。凸序表示 $X$ 对所有 $x$ ，在 $E[max(0, X - x)] \geq E[max(0, Y-x)]$ 和 $E[max(0, x - X)] \geq E[max(0, x - Y)]$ 这个意义上 $X$ 要比 $Y$ “更多变 (more variable)” 。

#### 定理1.4.2：

一个 m 阶的 PH 分布的 PH 表示 majorizes 相同意义的 m 阶 Erlang 分布。

#### 练习1.4.5：

证明 m 阶均值为 $1/mu $ 的 PH 分布的标准差大于等于 $1/(\mu \sqrt{m})$ ，然后用这个结论证明定理 1.4.1 （提示：考虑变异系数，同样注意函数 $(x-c) ^ {2}$） 是关于 $x$ 的凸函数。

虽然必须添加更多阶段才能获得较小的变异系数，但是即使是 2 阶 PH 分布，其变异系数的值也可以为任意大。

#### 练习1.4.6：略

考虑 PH 分布具有表示
$$
\pmb{\alpha} = (a, 1-a), \quad
T = 
\begin{pmatrix}
-a & 0 \\\\\\
0 & -1
\end{pmatrix}
\tag{1.76}
$$

其中 $0 < a < 1$ 。证明 $cv = \sqrt{2(1-a+1/a)(2-a) ^ {-2} - 1}$ ，并说明当 $a$ 接近 0 时 $cv$ 接近正无穷大。

如定理 1.4.1 和 1.4.2 所示，Erlang 分布在 PH 分布集中起着重要作用。公式 1.19 给出了一个 Erlang 分布的简单矩阵表示，在等式 1.19 中的矩阵 $T$ 具有双对角线结构，这既适用于数值计算也适用于概率解释。事实上，等式 1.20 和 1.21 显示像这样定义的 Coxian 分布具有简单的矩阵表示，我们称之为 Coxian 表示，同时也具有简单的概率解释。许多 PH 表示都具有相同或不同阶的等效 Coxian 表示，因为它们事实上代表了 Coxian 分布。为了找到一个 PH 表示的等效 Coxian 表示的条件，并由此产生用于查找等效 Coxian 表示的算法是非常有趣的。这类问题的第一个解决方案的是由 Cumani (1982) and Dehon and Latouche (1982) 提出的。令
$$
S(\pmb{\lambda}) = 
\begin{pmatrix}
\lambda _ {1} & & & \\\\\\
\lambda _ {2} & - \lambda _ {2} & & \\\\\\
 & \ddots & \ddots & \\\\\\
  & & \lambda _ {m} & - \lambda _ {m}
\end{pmatrix}
\tag{1.77}
$$

其中 $\pmb{\lambda} = (\lambda _ {1}, \lambda _ {2}, ..., \lambda _ {m})$ 。方便起见，$\pmb{\lambda}$ 中的元素根据它们的模的降序排序（即一个实数或者复数的绝对值），除非另有说明。

对于 PH 生成矩阵 $T$ 和 $S$ ，若对任意 PH 分布 $(\pmb{\alpha}, T)$ 都存在一个 PH 表示 $(\pmb{\beta}, S)$ 且这二者是等效的，则称 "T PH-majorized by S" 。

#### 定理1.4.3：

令 T 为一个 （上三角或下三角）PH 发生器，它的对角线元素为
$$
\\{-\lambda _ {1}, -\lambda _ {2}, ..., -\lambda _ {m}\\}
$$
且满足
$$
\lambda _ {1} \geq \lambda _ {2} \geq ... \geq \lambda _ {m}
$$
那么任何 PH 分布 $\pmb{\alpha}, T$ 就具有等效的 m 阶 Coxian 表示 $\pmb{\beta}, S(\lambda)$ ，即 T is PH-majorized by S(λ) 。

#### 命题1.4.2：

假定 T 是一个 m 阶 的 PH 发生器（PS：这个 PH-generator 放在 matrix-generator 中应该是生成矩阵，这里暂时直译了），且满足 $DT=T^{'}D$ ，其中矩阵 D 是具有正对角线元素的对角矩阵，T' 是 T 的转置。那么任何 PH 分布 $\pmb{\alpha}, T$ 就具有等效的 m 阶 Coxian 表示 $\pmb{\beta}, S(\lambda)$ ，其中 $\lambda=(\lambda _ {1}, \lambda _ {2}, ..., \lambda _ {m})$。并且 $\{-\lambda _ {1}, -\lambda _ {2}, ... , -\lambda _ {m}\}$ 都是 T 的特征值，且按照模的降序排列（计算多重性）。

#### 练习1.4.7：

证明若 $T$ 是一个对称的 (symmetric) 生成矩阵，那么任意一个 PH 表示 $(\pmb{\alpha}, T)$ 都具有一个同阶的等效 Coxian 表示（提示：使用 Micchelli and Willoughby (1979) 中的结论和命题 1.4.2）。

#### 练习1.4.8*：略

证明若 $T$ 是一个三对角 (tri-diagonal) 生成矩阵，那么任意一个 PH 表示 $(\pmb{\alpha}, T)$ 都具有一个同阶的等效 Coxian 表示（提示：验证条件 $DT = T ^ {'} D$）。

更普遍的是，下一个结论显示若 $T$ 的所有特征值都是实数，那么所有 PH 表示 $(\pmb{\alpha}, T)$ 都代表 Coxian 分布。

#### 定理1.4.4：

如果任何 PH 生成矩阵 T 都只有实数特征值，任何 PH 表示 $(\pmb{\alpha}, T)$ 都表示为 Coxian 分布。

#### 定理1.4.5：

矩阵表示 $(\pmb{\alpha)}, T$ 和 $(\pmb{\alpha}P, S(\lambda))$ 是等价的，即
$$
1-\pmb{\alpha}exp(Tt)\pmb{e}=1-\pmb{\alpha}Pexp(S(\lambda))\pmb{e}, for t \geq 0
$$
除此之外，我们还有 $TP=PS(\lambda)$ 和 $P\pmb{e}=\pmb{e}$

#### 练习1.4.9：

考虑阶为 $m=  10$ 的下三角 PH 生成矩阵 $T = (t _ {i, j})$ ，由如下定义
$$
t _ {i, i} = -i, i = 1, ..., m \\\\
t _ {i, j} = 1, j < i \quad and \quad i, j = 1, ..., m \\\\
t _ {i, j} = 0, i < j \quad and \quad i, j = 1, ..., m
$$

找到矩阵 $P$ 和 $S(\pmb{\lambda})$ ，其中 $\pmb{\lambda} = (m, m-1, ..., 1)$ 。

#### 练习1.4.10：

（练习 1.2.21 的继续）考虑如下 PH 表示
$$
\pmb{\alpha} = (0.6, 0.2), \quad
T = 
\begin{pmatrix}
-4.5 & 2 \\\\\\
0.625 & -2.5
\end{pmatrix}
\tag{1.84}
$$

$T$ 的特征值为 $\\{-2, -5\\}$ ，通过设置 $\lambda _ {1} = 5$ 和 $\lambda _ {2} = 2$ 来找到它的等效矩阵表示，设置 $\lambda _ {1} = 2$ 和 $\lambda _ {2} = 5$ 来找到它的等效矩阵表示（注意：在练习 1.2.21 中将解决方案和矩阵表示进行比较）。

等式 1.81 和定理 1.4.5 中定义的频谱多项式算法 (the spectral polynomial algorithm) 能简单而有效得找到 PH 表示的等效双对角矩阵表示。但是，等效双对角矩阵表示 $(\pmb{\alpha} P, S(\pmb{\lambda}))$ 也许不是一个 PH 表示，因为 $\pmb{\alpha} P$ 也许不是非负的，或者 $S(\pmb{\lambda})$ 也许不是一个 PH 生成矩阵。显然，新的矩阵表示是一个 PH 表示当：

1. $T$ 的所有特征值都是实数
2. 矩阵 $P$ 是非负的（充分条件）

许多情况已经被这两个要求所验证。

#### 定理1.4.6：

假定 PH 生成矩阵 T 是三角矩阵，或者存在一个正对角矩阵 D ，有 DT=T'D。那么 T 的所有特征值都是实数，并且 SPA （SPA是什么？）中的矩阵 P 是非负的（非负矩阵？），这要求在向量 $\lambda $ 中，特征值 $\{-\lambda _ {1}, -\lambda _ {2}, ... , -\lambda _ {m}\}$ 按照它们模的降序排列。

#### 练习1.4.11：

考虑下面的 3 阶 PH 生成矩阵
$$
T = 
\begin{pmatrix}
-5 & 0 & 1.5 \\\\\\
1 & -2 & 0 \\\\\\
0 & 2 & -2
\end{pmatrix}
\tag{1.85}
$$

找到 $((0, 1, 0), T)$ 和 $((1, 0, 0), T)$ 的等效矩阵表示。

练习 1.4.11 表明 $\pmb{\alpha} P$ 可能不是非负的。因此， $(\pmb{\alpha} P, S(\pmb{\lambda}))$ 也许不是一个 Coxian 表示。那么什么时候  $(\pmb{\alpha} P, S(\pmb{\lambda}))$  才是一个 Coxian 表示？令 $\pmb{\beta} = \pmb{\alpha} P$ ，若 $P$ 可逆，那么有 $\pmb{\alpha} = \pmb{\beta} P ^ {-1}$ ，这表明当 $\pmb{\alpha})\\ 是矩阵 $P ^ {-1}$ 中的行的凸组合 (convex combination) ，$\pmb{\beta}$ 是非负的。对于练习 1.4.11 ，我们可以得到
$$
P = 
\begin{pmatrix}
0.77 & -0.11 & 0.33 \\\\\\
0.22 & 0.39 & 0.38 \\\\\\
0 & 0.13 & 0.86
\end{pmatrix} \quad and 
P ^ {-1} = 
\begin{pmatrix}
1.14 & 0.53 & -0.67 \\\\\\
-0.74 & 2.60 & -0.85 \\\\\\
0.11 & -0.40 & 1.28
\end{pmatrix}
\tag{1.86}
$$

矩阵 $P ^ {-1}$ 的行是 $m$ 阶向量，而且它们可以形成一个 convex hull (Rockafellar(1970)) 。因此，若 $P$ 是可逆的，$(\pmb{\alpha} P, S(\pmb{\lambda}))$ 是一个 Coxian 表示当且仅当 $\pmb{\alpha}$ 在 " convex hull " 中且 $P ^ {-1}$ 的行作为它的极值点 (extreme points)（见图 1.11 其中 " convex hull " 具有三个极值点：(1.14, 0.53, 0.67), (0.11, 0.40, 1.28) 和 (0.74, 2.60, 0.85)） 。

#### 练习1.4.12：

对于式子 1.85 中定义的 PH 生成矩阵：计算矩阵 $P ^ {-1}$ ；绘制出由它的三行组成的三角形；绘制出由 $\\{(1, 0, 0), (0, 1, 0), (0, 0, 1)\\}$ 组成的三角形；验证对所有 $\pmb{\alpha} s$ ，$(\pmb{\alpha} P, S(\pmb{\lambda}))$ 是一个 Coxian 表示（提示：因为所涉及的所有向量中元素的和都是 1 ，它们可以在二维空间中绘制，见图 1.11）。

图 1.11 中的“冰激凌蛋筒”覆盖了所有的 $\pmb{\alpha}$ ，使得 $(\pmb{\alpha}, T)$ 是一个概率分布，这是基于 Dehen and Latouche (1982) 的一个定理产生的结论。显然，对于较大三角形区域中的任意 $\pmb{\alpha}$ ，$(\pmb{\alpha}, T)$ 都有一个等效的 Coxian 表示 $(\pmb{\alpha} P, S(\pmb{\lambda}))$ ；对于较大和较小三角形交叉区域中的任意 $\pmb{\alpha}$ ，$(\pmb{\alpha}, T)$ 是一个 PH 表示，且 $(\pmb{\alpha} P, S(\pmb{\lambda}))$ 是一个 Coxian 表示。对于“冰激凌蛋筒”内部的其他 $\pmb{\alpha}$ ，$(\pmb{\alpha}, T)$ 代表一个 PH 分布，但它可能不是一个 PH 表示，因为 $\pmb{\alpha}$ 可能不是非负的。尽管如此，对于这样的 $\pmb{\alpha}$ ，$(\pmb{\alpha}, T)$ 的一个等效 PH 表示，尤其是更高阶的还是能找到。

#### 练习1.4.13：

为以下 PH 生成矩阵计算 SPA 中的矩阵 $P$
$$
T _ {1} = 
\begin{pmatrix}
-5 & 0 & 1 \\\\\\
0 & -3 & 1.5 \\\\\\
1 & 1.5 & -4
\end{pmatrix},
T _ {2} = 
\begin{pmatrix}
-5 & 0 & 0.1 \\\\\\
4 & -4 & 0 \\\\\\
0 & 1 & -1
\end{pmatrix},
T _ {3} = 
\begin{pmatrix}
-5 & 0 & 2 \\\\\\
3 & -3 & 0 \\\\\\
0 & 1 & -1
\end{pmatrix}
\tag{1.87}
$$

对于任何随机向量 $\pmb{\alpha}$ ，探讨 $(\pmb{\alpha}, T)$ 的等效双对角表示。

#### Commentary：略

#### Additional Exercises and Extensions：略