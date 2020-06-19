---
title: 将单词转换为矢量
titleSuffix: Azure Machine Learning
description: 了解如何使用提供的三个 Word2Vec 模型从文本语料库中提取词汇表及其对应的单词嵌入。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: e0e796b75690bcacc6be8ef29b8b490c7faa40af
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853649"
---
# <a name="convert-word-to-vector"></a>将单词转换为矢量

本文介绍如何使用 Azure 机器学习设计器（预览版）中的“将单词转换为矢量”模块，将各种不同的 Word2Vec 模型（Word2Vec、FastText、Glove 预训练模型）应用到指定为输入的文本语料库，并生成具有单词嵌入的词汇表。

此模块使用 Gensim 库。 有关 Gensim 的详细信息，请访问其[官方网站](https://radimrehurek.com/gensim/apiref.html)，网站上提供教程和算法说明。

### <a name="more-about-convert-word-to-vector"></a>有关将单词转换为矢量的详细信息

一般而言，将单词转换为矢量（又称单词矢量化）是一种自然语言处理过程，该过程使用语言模型或方法将单词映射到矢量空间，即通过实数的矢量表示每个单词，并同时允许具有相似含义的单词拥有相似的表示形式。

单词嵌入可用作 NLP 下游任务（例如文本分类、情绪分析等）的初始输入。

单词嵌入技术多种多样，在此模块中，我们实现三种广泛使用的方法，其中包括两种在线训练模型 Word2Vec 和 FastText，以及一种预训练模型 glove-wiki-gigaword-100。 在线训练模型根据输入数据进行训练，而预训练模型根据较大的文本语料库（例如维基百科、Google News）进行离线训练，后者通常包含大约 1000 亿个单词，因此，单词嵌入在单词矢量化期间保持不变。 预训练单词模型可带来减少训练时间、更好地编码单词矢量以及提高整体性能等好处。

+ Word2Vec 是使用浅层神经网络学习单词嵌入的最流行方法之一，以下论文讨论了相关理论，并以 PDF 格式提供下载：[Efficient Estimation of Word Representations in Vector Space, Mikolov, Tomas, et al](https://arxiv.org/pdf/1301.3781.pdf)（高效估算矢量空间中的单词表示形式）。此模块中的实现基于[适用于 Word2Vec 的 gensim 库](https://radimrehurek.com/gensim/models/word2vec.html)。

+ 以下论文对 FastText 理论进行了说明，并以 PDF 格式提供下载：[Enriching Word Vectors with Subword Information, Bojanowski, Piotr, et al](https://arxiv.org/pdf/1607.04606.pdf)（使用子词信息扩充单词矢量）。此模块中的实现基于[适用于 FastText 的 gensim 库](https://radimrehurek.com/gensim/models/fasttext.html)。

+ Glove 预训练模型 glove-wiki-gigaword-100 是基于维基百科文本语料库的预训练矢量的集合，其中包含 56 亿个令牌和 40 万个无大写的词汇，并以 PDF 格式提供下载：[GloVe:Global Vectors for Word Representation](https://nlp.stanford.edu/pubs/glove.pdf)（GloVe：单词表示形式的全局矢量）。

## <a name="how-to-configure-convert-word-to-vector"></a>如何配置将单词转换为矢量

此模块需要包含文本列的数据集，经过预处理的文本更合适。

1. 将“将单词转换为矢量”模块添加到管道中。

2. 请提供包含一个或多个文本列的数据集作为模块的输入。

3. 对于“目标列”，仅选择包含要处理的文本的一列。

    一般而言，因为此模块根据文本创建词汇表，所以不同列的内容会有所不同，这导致词汇表的内容也有所不同，因此，模块仅接受一个目标列。

4. 对于“Word2Vec 策略”，从 `GloVe pretrained English Model`、`Gensim Word2Vec` 和 `Gensim FastText` 中选择。

5. 如果“Word2Vec 策略”为 `Gensim Word2Vec` 或 `Gensim FastText`：

    + **Word2Vec 训练算法**。 从 `Skip_gram` 和 `CBOW` 中选择。 原始[论文](https://arxiv.org/pdf/1301.3781.pdf)中介绍了两者的差异。

        默认方法为 `Skip_gram`。

    + **单词嵌入长度**。 指定单词矢量的维数。 对应于 gensim 中的 `size` 参数。

        默认 embedding_size 为 100。

    + **上下文窗口大小**。 指定要预测的单词和当前单词之间的最大距离。 对应于 gensim 中的 `window` 参数。

        默认窗口大小为 5。

    + **Epoch 数量**。 指定语料库的 epoch（迭代）数量。 对应于 gensim 中的 `iter` 参数。

        默认 epoch 数量为 5。

6. 对于“最大词汇表大小”，指定生成的词汇表中的最大单词数。

    如果有超出此数量的唯一单词，则删除不常用的单词。

    默认词汇表大小为 10000。

7. 对于“最小单词计数”，请提供最小单词计数，这将使模块忽略所有频率低于此值的单词。

    默认值为 5。

8. 提交管道。

## <a name="examples"></a>示例

模块有一个输出：

+ **具有嵌入的词汇表**：包含生成的词汇表以及每个单词的嵌入，一个维度占用一列。

### <a name="result-examples"></a>结果示例

为了说明“将单词转换为矢量”模块的工作原理，以下示例将使用默认设置的此模块应用于 Azure 机器学习（预览版）中提供的经过预处理的维基百科 SP 500 数据集。

#### <a name="source-dataset"></a>源数据集

该数据集包含一个类别列，以及从维基百科提取的全文。 此表仅显示几个典型示例。

|text|
|----------|
|nasdaq 100 component s p 500 component foundation founder location city apple campus 1 infinite loop street infinite loop cupertino california cupertino california location country united states...|
|br nasdaq 100 nasdaq 100 component br s p 500 s p 500 component industry computer software foundation br founder charles geschke br john warnock location adobe systems...|
|s p 500 s p 500 component industry automotive industry automotive predecessor general motors corporation 1908 2009 successor...|
|s p 500 s p 500 component industry conglomerate company conglomerate foundation founder location city fairfield connecticut fairfield connecticut location country usa area...|
|br s p 500 s p 500 component foundation 1903 founder william s harley br arthur davidson harley davidson founder arthur davidson br walter davidson br william a davidson location...|

#### <a name="output-vocabulary-with-embeddings"></a>具有嵌入的输出词汇表

下表包含以维基百科 SP 500 数据集为输入的此模块的输出。 最左侧的列表示词汇表，其嵌入矢量由同一行中其余列的值表示。

|词汇|嵌入维度 0|嵌入维度 1|嵌入维度 2|嵌入维度 3|嵌入维度 4|嵌入维度 5|...|嵌入维度 99|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|nasdaq|-0.375865|0.609234|0.812797|-0.002236|0.319071|-0.591986|...|0.364276
|组件|0.081302|0.40001|0.121803|0.108181|0.043651|-0.091452|...|0.636587
|s|-0.34355|-0.037092|-0.012167|0.151542|0.601019|0.084501|...|0.149419
|p|-0.133407|0.073244|0.170396|0.326706|0.213463|-0.700355|...|0.530901
foundation|-0.166819|0.10883|-0.07933|-0.073753|0.262137|0.045725|...|0.27487
founder|-0.297408|0.493067|0.316709|-0.031651|0.455416|-0.284208|...|0.22798
location|-0.375213|0.461229|0.310698|0.213465|0.200092|0.314288|...|0.14228
city|-0.460828|0.505516|-0.074294|-0.00639|0.116545|0.494368|...|-0.2403
apple|0.05779|0.672657|0.597267|-0.898889|0.099901|0.11833|...|0.4636
campus|-0.281835|0.29312|0.106966|-0.031385|0.100777|-0.061452|...|0.05978
infinite|-0.263074|0.245753|0.07058|-0.164666|0.162857|-0.027345|...|-0.0525
loop|-0.391421|0.52366|0.141503|-0.105423|0.084503|-0.018424|...|-0.0521

在此示例中，我们使用默认的 `Gensim Word2Vec` 作为“Word2Vec 策略”，“训练算法”为 `Skip-gram`，“单词嵌入长度”为 100，因此，我们有 100 个嵌入列。

## <a name="technical-notes"></a>技术说明

本部分包含常见问题的提示和解答。

+ 在线训练和预训练模型之间的差异

    在此“将单词转换为矢量”模块中，我们提供了三种不同的策略：两种在线训练模型和一种预训练模型。 在线训练模型将输入数据集用作训练数据并在训练过程中生成词汇表和单词矢量，而预训练模型已根据更大的文本语料库（例如维基百科或 Twitter 文本）进行训练，因此，预训练模型实际上是单词/嵌入对的集合。  

    如果选择 Glove 预训练模型作为单词矢量化策略，它会从输入数据集中汇总词汇表，并为预训练模型中的每个单词生成嵌入矢量，而无需进行在线训练。使用预训练模型可以节省训练时间，并且具有更好的性能，尤其是在输入数据集大小相对较小的情况下。

+ 嵌入大小

    一般而言，将单词嵌入长度设置为几百（例如，100、200、300）可获得良好的性能，因为较小的嵌入大小意味着较小的矢量空间，而矢量空间可能会导致单词嵌入冲突。  

    对于预训练模型，单词嵌入长度是固定的，在此实现中，glove-wiki-gigaword-100 的嵌入大小为 100。


## <a name="next-steps"></a>后续步骤

请参阅[适用于 Azure 机器学习的模块集](module-reference.md)。 

有关特定于设计器（预览版）模块的错误列表，请参阅[机器学习错误代码](designer-error-codes.md)。
