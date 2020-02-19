---
title: "2019 年中总结"
date: 2019-05-12T19:10:25+08:00
Description: ""
Tags: [summary]
Categories: [summary]

---

今年上半年也快过完了，回顾一下半年来的一些工作，觉得也算是小有成长。

### 生物信息

说实话我不太想回忆起这部分经历，毕竟跨学科学习对于我一个毫无生信方面经验的计算机学生来说实在是太痛苦了，还是跨的生物。

最开始在和师兄一起做基因优化和密码子聚类的工作。说实话导师在密码子聚类方面的一些想法确实很不错，也曾让我激动过一阵。但经历过无数次实验验证后也渐渐熄灭了热情，剩下的只有接踵而来的失败和迷茫。师兄密码子优化方面的工作小有成果，并且能和川大国家重点实验室的同学们共事过一段时间，也是让我感到非常荣幸的一件事。

搁置了这块工作后，我的导师便让我转向了蛋白质结构预测的工作，并曾信心满满地说“我们一定要参加2020年的 CASP 大赛”，仿佛 Alpha-Fold 的成功明天就会发生在我们身上一般。没办法，赶鸭子上架也得学呀。在详细了解了这个比赛后，知道了大家使用的模型都是一个双层的残差网络，也顺利地在 Github 上找到了一个实现。模型训练出来后，老师比较高兴，但也没有什么后续的动作了。我也乐的清闲，能少跟生物沾边就少沾边。

上半年最后做的一个和生信相关的小项目是药物-靶点预测系统，这个的实现也是参考的清华的一篇 nature 的论文。从这个项目中第一次系统地接触到了推荐系统，原理部分结合上学期修的矩阵理论也没有什么理解上的难点。在实验室做的第一篇论文分享也是这个，总的来说收获较大，也许工作后会考虑去做推荐算法。不过项目本身按照我老师的脾性，仍然搁置了就是了。

上半年做的生信方面的工作也就这些，没有锻炼出生物专业方面的技能，杂的倒是学了不少。也很感谢我在海洋大学的同学和她的师兄们，给予了我很大的帮助。不过我还是想说一句，我对生信一丁点的兴趣都没得。

### 自然语言

很显然，和川大在生物方面项目进展的并不顺利，我就被发配去了自然语言组。我是十分高兴的，一方面是自己本身对 nlp 比较感兴趣，同时做 cv 人太多了，另一方面是原来生信方面的工作大部分也是在处理氨基酸序列和 DNA 序列，和自然语言语料有共通之处。

第一个做了一个实体关系抽取器，用在我们的项目中抽取实体关系三元组，但准确的来说实体抽取是师兄的工作，我这边只是做了一个关系分类器。大概的思路就是使用字向量+实体的位置向量编码，输入到一个双向的 LSTM 中训练，最后输出到 softmax 分类器中。因为是用的语料都是短句，所以最终模型表现也比较好。

在这个项目中我觉得收获还是很大的，因为从设计模型、处理语料、参数调整，到中间矩阵的维度确认，一行行敲出代码并做调试，前前后后耗费了一周多的时间。其中最复杂的地方我觉得是在数据预处理部分，模型也就是多个中间矩阵的维度计算比较麻烦（我是全部画出来并手动计算的，原谅我的菜）。说来惭愧，通过这个过程我才第一次才真正理解了网络中每一步在做什么。以前虽然调试过许多模型，看过许多相关的书和视频，你要是让我去说名每一层网络中的参数是多少，为什么是这样，我还真有可能答不上来。

师兄肯定了我的工作，然而当我拿去给导师看的时候，导师却认为统计模型和深度学习模型都不能达到我们项目中要求的接近 100% 的分类准确率，并认为依存句法分析就可以解决大部分问题，让我回去重新用句法分析器做。有了前几次的经历，这次我已经有了心理准备，但心里还是有点不爽。

那能怎么办呢，做呗，只能拿哈工大的 LTP 基于句法分析树尝试去抽取关系。但依存树都是从一个动词开始分析句子的依存结构，抽取出来的事实三元组并不是我们需要的（实体1，关系，实体2）这种。在尝试了许多方法后，发现依存句法只能解决一些非常简单的题目，对那种多个实体的复杂长句就非常难以设计分析路径了。抛开使用深度网络的方法，现在要想解决关系抽取问题，目前只能依靠句模和规则库来构建了。

总的来说，这一段时间在 nlp 方面也算入了门，最重要的是找到了一个比较喜欢的方向，有机会的话也希望将来能从事这方面相关的工作。

### 阅读

之前说过我买了一个 kindle ，也算汇报一下情况吧。

最近读完了吴军老师的两本书：数学之美和浪潮之巅。其实我主要想说一下这两本书的写作风格我非常喜欢，平铺直叙却又耐得住咀嚼。我本人是非常喜欢看纪录片的，而且也一直认为能把晦涩难懂的知识以一种简洁明快而不枯燥的方式向人们普及是一种非常了不起的能力，而吴军老师恰恰就拥有这种能力。

我其实不太喜欢写博文，觉得有这种时间不如用来学习一些新的知识。但是自从买了 kindle 后，静心阅读的时间变长了，利用手机等方式的碎片化阅读变少了，有了更多的时间去思考平时遇到的问题，整个人也变得不是那么的浮躁。

能把自己遇到的问题，自己的想法记录下来，既是温故，又能知新，还可以与他人碰撞思维的火花，何乐而不为？可能是受到吴军老师的影响，现在的我的确是这么想的。希望将来有一日，我也能以一种优美而简洁的语言写下自己的想法，分享给他人。