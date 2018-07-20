---
title: "FMAM - 1.3：PH分布的闭包属性"
date: 2018-07-20T15:59:56+08:00
Description: ""
Tags: [fmam]
Categories: []

---

在本节中，我们展示了一组 PH 分布在一些操作下是封闭的情况（例如，“min”，“max”，“+”）。闭包属性证明了随机建模中 PH 分布在数学上的动态可操作性，这体现了他们在应用中的关键作用。首先，我们考虑采用马尔可夫链方法练习 1.1.5 , 1.1.6 和 1.1.9 。

#### 例子1.3.1：略

#### 例子1.3.2：略

#### 练习1.3.1：略

#### 练习1.3.2：略

#### 命题1.3.1：
假定 \\(X _ {1}\\) 具有 PH 表示为 \\((\vec{\alpha},T)\\) 的 PH 分布，\\(X _ {2}\\) 具有 PH 表示为 \\((\vec{\beta},T)\\) 的 PH 分布，且 \\(X _ {1}\\) 与 \\(X _ {2}\\) 相互独立，那么 \\(X=min\\{X _ {1}, X _ {2}\\}\\) 具有 PH 表示为 $$(\vec{\alpha} \otimes \vec{\beta}, T \otimes I + I \otimes S) \tag{1.38}$$ 的 PH 分布。证明略。

#### 练习1.3.3：略

#### 练习1.3.4：略

#### 命题1.3.2：
假定 \\(X _ {1}\\) 具有 PH 表示为 \\((\vec{\alpha},T)\\) 的 PH 分布，\\(X _ {2}\\) 具有 PH 表示为 \\((\vec{\beta},T)\\) 的PH分布，且 \\(X _ {1}\\) 与 \\(X _ {2}\\) 相互独立，那么 \\(X=X _ {1} + X _ {2}\\) 具有 PH 表示为 
$$
\Biggl(
(\vec{\alpha}, (1-\vec{\alpha}\vec{e})\vec{\beta}), \biggl(
\begin{smallmatrix} T & T^0\vec{\beta} \\\ 
0 & S 
\end{smallmatrix} 
\biggl) 
\Biggl)
$$
的 PH 分布。证明略（代数证明和概率证明）。

#### 练习1.3.5：略

#### 练习1.3.6：略

#### 命题1.3.3：
假定 \\(X _ {1}\\) 具有 PH 表示为 \\((\vec{\alpha},T)\\) 的 PH 分布，\\(X _ {2}\\) 具有 PH 表示为 \\((\vec{\beta},T)\\) 的PH分布，且 \\(X _ {1}\\) 与 \\(X _ {2}\\) 相互独立，那么 \\(X=max\\{X _ {1}, X _ {2}\\}\\) 具有 PH 表示为 
$$
\Biggl(
(\vec{\alpha} \otimes \vec{\beta}, (1-\vec{\alpha}\vec{e})\vec{\beta}, (1-\vec{\beta}\vec{e})\vec{\alpha}), 
\biggl(
\begin{smallmatrix} T \otimes I + I \otimes S & T^0 \otimes I & I \otimes S^0 \\\ 0 & S & 0 \\\ 0 & 0 & T \end{smallmatrix} 
\biggl) 
\Biggl)
$$
的 PH 分布。证明略。

#### 练习1.3.7：略

#### 练习1.3.8：略

#### 命题1.3.4：
假设 N 具有带参数 p 的几何分布 (0 < p < 1)，即 \\(P\\{N=n\\}=p^{n-1}(1-p), n=1, 2, ... \\) 假定 \\(\\{X _ {n}, n=1, 2, ... \\}\\)  服从同一个 PH 表示为 \\((\vec{\alpha}, T)\\) 的 PH 分布，其中 \\(\vec{\alpha}\vec{e}=1$\\)。所有随机变量是相互独立的，那么随机变量 \\(Y=\sum _ {n-1}^{N}X _ {n}\\) 服从 PH 表示为 $$(\vec{\alpha}, T + pT^0\vec{\alpha})$$ 的 PH 分布。证明略。

#### 练习1.3.9：略

#### 练习1.3.10：略

#### 引理1.3.1：
假定 X 具有不可还原的（irreducible 翻译不准确？不可约矩阵？）的 PH 表示为 \\((\vec{\alpha}, T)\\) 的 PH 分布，且 \\(\vec{\alpha}\vec{e}=1\\) ，令 \\(\vec{\theta}\\) 满足 \\(\vec{\theta} Q=0\\) 且 \\(\vec{\theta}\vec{e}=1\\) ，那么有 $$\vec{\theta}=-\vec{\alpha}T^{-1}/E[X]$$ 且有 $$E[X]=1/(\vec{\theta}T^0)$$ 证明略。

#### 命题1.3.5：
假定 X 具有不可还原的 PH 表示为 \\((\vec{\alpha}, T)\\) ，且 \\(\vec{\alpha}\vec{e}=1\\) 的 PH 分布，令 \\(\vec{\theta}\\) 满足 \\(\vec{\theta} Q=0\\) 且 \\(\vec{\theta}\vec{e}=1\\) ，那么 \\((\vec{\theta}, T)\\) 是均匀分布 X 的 PH 表示。证明略。

#### 练习1.3.11：略

#### 练习1.3.12：略

#### Commentary：略

#### Additional Exercises and Extensions：略

