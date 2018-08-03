---
title: "FMAM - 1.7：Additional Topics"
date: 2018-08-03T08:31:59+08:00
Description: ""
Tags: []
Categories: []
---

第 1.2, 1.3, 1.4, 1.5 和 1.6 节涵盖了 PH 分布和多元 PH 分布的基本理论。在本节中，我们将讨论概括和一些概括 PH 分布的理论问题。

### 1.7.1 离散的 PH 分布

考虑具有 m + 1 个状态的离散时间马尔可夫链，其概率矩阵为
$$
P=
\biggl(
	\begin{matrix}
		T & \pmb{T ^ {0}} \\
		0 & 1	
	\end{matrix}
\biggl)
$$
吸收到状态 \\(m+1\\) 的时间表示为 \\(X\\) ，我们将其定义为离散时间的 PH 随机变量。给出初始概率向量 \\((\pmb{\alpha}, 1-\pmb(\alpha e})\\) ，很容易得到
$$
P\\{X=n\\}= 
\left\{
	\begin{array}{**lr**}
	1 - \pmb{\alpha e}, \qquad if \quad n=0; \\
	\pmb{\alpha} T ^ {n-1} \pmb{T ^ 0},\qquad if \quad n=1, 2, ...
	\end{array}
\right.
$$

#### 练习1.7.1：略

#### 练习1.7.2：略

#### 练习1.7.3：略

#### 练习1.7.4：略

### 1.7.2 Matrix-Exponential (ME，矩阵-指数) 分布

(Lipsky and Ramaswami (1985), Lipsky (1992), Asmussen and Bladt (1996), and Fackrell (2003)). ME 分布是 PH 分布通过删除所有 \\(\pmb{\alpha}, T, \pmb{e}\\) 的元素的约束来得到的一般化（概括）结果，只需要方程 (1.9) 是概率分布即可。ME 分布集比 PH 分布集要大。例如，PH 分布的密度函数可以是在某些正数点处（翻译不准确？）为零，PH 分布的最小等价 ME 表示通常有更小的阶 (order) ，比其最小的 PH 表示还要显著更小。另一方面，随机建模中不方便使用 ME 表示，因为 PH 分布所享有的概率性解释已经丢失。

#### 例子1.7.1：略

#### 例子1.7.2：略

#### 练习1.7.5：略

#### 练习1.7.6：略

