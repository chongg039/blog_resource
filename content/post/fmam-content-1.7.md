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

### 1.7.3 PH 分布的进一步推广

1. *双侧 PH 分布和双侧 ME 分布* 对于 PH 分布和 ME 分布二者都是在实线的非负半部分定义的，而 PH 分布和 ME 分布的一个概括 (generalizations，翻译不准确？) 要包含实线的负半部分，这种  generalizations 被称为双边分布 (bilateral distributions) 。See Shanthikumar (1985), Ahn and Ramaswami (2005), and Bladt et al. (2013) for more details.
2. Semi-Markovian PH 分布 (Shi et al. (1996)).
3. 多元 ME 分布 (Bladt and Nielsen (2010)).

### 1.7.4 PH 分布和 ME 分布的表征

什么样的概率分布是 PH 分布和 ME 分布？

O’Cinneide (1990a) 给出了 PH 分布的完整表征（见定理1.2.2）。其他参考文献包括 Aldous and Shepp (1987), Asmussen (1989, 2000), Maier and O’Cinneide (1992), Yao (2002), He and Zhang (2005), Fackrell et al. (2010), Sanguesa (2010) 等。

ME 分布的表征可以在 Asmussen and Bladt (1996), Fackrell (2003, 2005), He and Zhang (2007), Bean et al. (2008), Bodrog et al. (2008) 等中找到。

（注：Characterization 翻译成“表征”可能不准确？）

### 1.7.5 最小 PH 表示和其他 PH 分布相关问题

在所有等价 PH 表示中具有最小阶 (order) 的 PH 表示被称为最小 PH 表示 (minimal PH-representation) 。自 1975 年以来，找到一个 PH 分布的最小 PH 表示一直是一个悬而未决的问题。如 Commault and Mocanu (2003) 中所说的那样，这个问题相当于一个在控制理论中众所周知的最小表示问题。需要注意的是，He and Zhang (2008) 开发了一种算法，用来找到一个最小阶的等价 Coxian 表示。Pulungan and Hermanns (2009) 考虑了一个找到最小化表示的特殊结构。这两者都只解决了 PH 分布子集中的最小化表示问题。一般来说，找到最小的 PH 表示是一个基础的，但却能对理论研究和应用产生重大影响的课题，并且十分具有挑战性。

### 1.7.6 其他练习

Neuts (1995) 提供了大量与 PH 分布相关的练习和应用。Neuts (1995) 中的第四章和第五章对理解 PH 分布和他们的应用尤其有帮助。