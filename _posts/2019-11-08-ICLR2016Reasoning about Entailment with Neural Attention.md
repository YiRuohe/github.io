---
layout: post
title: ICLR2016用神经注意力推断蕴含关系
date: 2019-11-08
categories: blog
tags: [Attention,treeLSTM]
description: 论文笔记。
---

ICLR 2016 Reasoning about Entailment with Neural Attention

word-by-word neural attention mechanism 促进了对单词或短语对蕴含关系的推理

reads two sentences to determine entailment

设置了定性分析 attention 权重 证明推理能力

它是第一个可微的端到端可微系统实现SOTA在文本蕴含数据集上

Recognizing textual entailment (RTE)

之前缺乏大的高质量的RTE数据集

实际上可以不依赖part-of-speech tags 、parses

Bowman et al. [EMNLP2015]A large annotated corpus for learning natural language inference. 提出 SNLI

Bowman的lstm编码premise和hypothesis为一个定长的密集向量concat后经过multi-layer perceptron (MLP)分类

而这篇是用attention以premise为条件去处理hypothesis

contributions：

1.我们提出了一种基于LSTMs的神经模型，它可以一次读取两个句子来确定句子的含义，而不是将每个句子独立地映射到一个语义空间

2.我们通过一个神经word-by-word的注意机制来扩展这个模型，以提高对成对的单词和短语进行推理

2.我们提供了一个详细的定性分析神经注意的RTE能力

我们的基准LSTM在SNLI上的准确率达到了80.9%，比根据RTE定制的手工词汇特征分类器的准确率高出2.7个百分点。

加上word-by-word neural attention再提高2.6个百分点，83.5%。

cell存储长时间信息 三个门控制信息进出cell 公式：

<center>
	<p><img src="https://raw.githubusercontent.com/waaaaaag/waaaaaag.github.io/edit/master/_posts/img/clip_image002.png" align="center"></p>
</center>
input gates（2）Forget gates (Eq. 3) and output gates (Eq. 4)

LSTM可以学到语义丰富的句子表示，但我们想读取两个句子来确定蕴含关系，从而推理词语与短语的蕴含

High-level structure：

<center>
	<p><img src="https://raw.githubusercontent.com/waaaaaag/waaaaaag.github.io/edit/master/_posts/img/clip_image004.png" align="center"></p>
</center>

一个LSTM读premise 不同参数的LSTM读分隔符和hypothesis

但是它的cell是使用上一个LSTM的最后一个cell进行初始化 即c5-->c6 

用word2vec作词表示 训练的时候没有再优化了 

attention是基于最后一个output（h9），attention（word-by-word）是所有hypothesis的output (h7, h8 and h9)

vocab以外的词随机初始化（-0.5，0.5），随训练optimize 。那么它的vocab是用什么训练的呢？ 其他的语料？

验证集和测试集里遇到的vacab以外的词固定成随机vector

用线性层投影词向量到hidden size 的维数产生input Xi

为了分类，输出的最后一个output(h9)的非线性投影上使用一个softmax层，并将其放入三个类(ENTAILMENT, NEUTRAL or CONTRADICTION)的目标空间中，使用cross-entropy loss进行训练

Attetion:

<center>
	<p><img src="https://raw.githubusercontent.com/waaaaaag/waaaaaag.github.io/edit/master/_posts/img/clip_image006.png" align="center"></p>
</center>

 

<center>
	<p><img src="https://raw.githubusercontent.com/waaaaaag/waaaaaag.github.io/edit/master/_posts/img/clip_image008.png" align="center"></p>
</center>

Word-by-word attention:

similar to Bahdanau et al. [2014], Hermann et al. [2015] and Rush et al. [2015].但是不用attention生成词，而是通过句子里的短语、词的软对齐对句子对编码

<center>
	<p><img src="https://raw.githubusercontent.com/waaaaaag/waaaaaag.github.io/edit/master/_posts/img/clip_image010.png" align="center"></p>
</center>

Two-way attention

交换premise和hypothesis,产生两个句子表示再拼起来

Experiments

SNLI.2015比SICK.2014大两个数量级，而且SICK中的示例很多都是从其它示例里启发式生成的。SNLI全部是人工标注的！

ADAM 第一个动力参数 0.9 第二个0.99

每个模型执行一个初始学习率的网格搜索[1E-4, 3E-4, 1E-3],dropout [0.0, 0.1, 0.2] and l2-regularization strength [0.0, 1E-4, 3E-4, 1E-3]

我们认为这是由于信息能够从一个句子表现形式流向另一个。具体地说,该模型不浪费能力编码假设(事实上它不需要编码的假设),但是可以阅读这个假设在一个更集中的方式通过检查单词和短语的矛盾和悖论的基于语义表示的前提。一种解释是LSTM近似于RTE的有限状态自动机。

未来：迁移学习任务 大文本单元 段落、篇章 使用分层注意力机制 非自然语言序列蕴含问题

 

 

 
