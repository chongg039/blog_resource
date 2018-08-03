---
title: "FMAM - 1.4：PH 分布和 PH 表示"
date: 2018-07-26T10:30:01+08:00
Description: ""
Tags: [fmam]
Categories: []
---

1.4PH分布：PH表示

1.3节说明了 PH 表示在 PH 分布应用中的重要性。它还显示给定 PH 分布的 PH 表示并不是唯一的。在本节中，我们将进一步研究PH表示及其相关属性。我们研究算法以找到 PH 分布的简单等价的表示（例如，Coxian 表示）。

#### 例子1.4.1：略

#### 练习1.4.1：略

#### 练习1.4.2：略

#### 命题1.4.1：

如果 PH 表示为 \\(\pmb{\alpha}, T\\) 是不可约（翻译不准确？）的，那么可以找到一个更小阶数（order，意思分析在练习1.4.2中，后续补充）的 PH 表示。证明略。

#### 练习1.4.3：略

#### 定理1.4.1：

一个 m 阶 PH 分布的 PH 随机变量的变异系数至少是 \\(1/\sqrt{m}\\)，且 m 阶 Erlang 分布是唯一能达到下限的分布。

#### 练习1.4.4：略

#### 定理1.4.2：

一个 m 阶的 PH 分布的 PH 表示 majorizes （基本不知道啥意思）相同意义的 m 阶 Erlang 分布。

#### 练习1.4.5：略

#### 练习1.4.6：略

#### 定理1.4.3：

令 T 为一个 （上三角或下三角）PH 发生器，它的对角线元素为
$$
\\{-\lambda _ {1}, -\lambda _ {2}, ..., -\lambda _ {m}\\}
$$
且满足
$$
\lambda _ {1} \geq \lambda _ {2} \geq ... \geq \lambda _ {m}
$$
那么任何 PH 分布 \\(\pmb{\alpha}, T\\) 就具有等效的 m 阶 Coxian 表示 \\(\pmb{\beta}, S(\lambda)\\) ，即 T is PH-majorized by S(λ) 。

#### 命题1.4.2：

假定 T 是一个 m 阶 的 PH 发生器（PS：这个 PH-generator 放在 matrix-generator 中应该是生成矩阵，这里暂时直译了），且满足 \\(DT=T^{'}D\\) ，其中矩阵 D 是具有正对角线元素的对角矩阵，T' 是 T 的转置。那么任何 PH 分布 \\(\pmb{\alpha}, T\\) 就具有等效的 m 阶 Coxian 表示 \\(\pmb{\beta}, S(\lambda)\\) ，其中 \\(\lambda=(\lambda _ {1}, \lambda _ {2}, ..., \lambda _ {m})\\)。并且 \\(\{-\lambda _ {1}, -\lambda _ {2}, ... , -\lambda _ {m}\}\\) 都是 T 的特征值，且按照模的降序排列（计算多重性）。

#### 练习1.4.7：略

#### 练习1.4.8*：略

#### 定理1.4.4：

如果任何 PH 生成矩阵 T 都只有实数特征值，任何 PH 分布 \\((\pmb{\alpha}, T)\\) 都表示为 Coxian 分布。

#### 定理1.4.5：

矩阵表示 \\((\pmb{\alpha)}, T\\) 和 \\((\pmb{\alpha}P, S(\lambda))\\) 是等价的，即
$$
1-\pmb{\alpha}exp(Tt)\pmb{e}=1-\pmb{\alpha}Pexp(S(\lambda))\pmb{e}, for t \geq 0
$$
除此之外，我们还有 \\(TP=PS(\lambda)\\) 和 \\(P\pmb{e}=\pmb{e}\\)

#### 练习1.4.9：略

#### 练习1.4.10：略

#### 定理1.4.6：

假定 PH 生成矩阵 T 是三角矩阵，或者存在一个正对角矩阵 D ，有 DT=T'D。那么 T 的所有特征值都是实数，并且 SPA （SPA是什么？）中的矩阵 P 是非负的（非负矩阵？），这要求在向量 \\(\lambda \\) 中，特征值 \\(\{-\lambda _ {1}, -\lambda _ {2}, ... , -\lambda _ {m}\}\\) 按照它们模的降序排列。

#### 练习1.4.11：略

#### 练习1.4.12：略

#### 练习1.4.13：略

#### Commentary：略

#### Additional Exercises and Extensions：略