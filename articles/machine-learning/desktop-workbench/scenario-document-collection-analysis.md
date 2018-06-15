---
title: 文档集分析 - Azure | Microsoft Docs
description: 如何使用 Azure ML Workbench 总结和分析大型文档集，包括短语学习、主题建模和主题模型分析等技术。
services: machine-learning
author: kehuan
ms.author: kehuan
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, MicrosoftDocs/mlreview, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 60d65b17d4cbe8a45ff3fb62b06852d7b945e8f1
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832853"
---
# <a name="document-collection-analysis"></a>文档集分析

该方案演示如何使用 Azure ML Workbench 总结和分析大型文档集，包括短语学习、主题建模和主题模型分析等技术。 Azure Machine Learning Workbench 可轻松扩展非常大的文档集，并提供了在各种计算环境中定型和调整模型的机制，从本地计算到数据科学虚拟机到 Spark 群集均包含在内。 通过 Jupyter 笔记本在 Azure Machine Learning Workbench 中提供了轻松开发。

## <a name="link-to-the-gallery-github-repository"></a>链接到库 GitHub 存储库

此实际方案的公共 GitHub 存储库包含此示例需要的所有材料，其中包括代码示例：

[https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis](https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis)

## <a name="overview"></a>概述

对于每天收集的大量数据（尤其是非结构化的文本数据），一项重大的挑战就是组织、搜索和了解这些海量的文本。 此文档集分析方案演示了一种高效自动化端到端工作流，用于分析大型文档集和完成下游 NLP 任务。

此方案提供的关键要素是：

1. 从文档中学习关键的多词短语。

1. 了解文档集中提供的基础主题。

1. 按主题分布呈现文档。

1. 提供基于主题内容组织、搜索和总结文档的方法。

此方案中提出的方法可以实现各种关键的行业工作负载，例如发现主题趋势异常情况、文档集摘要和类似的文档搜索。 它可以应用于许多不同类型的文档分析，如政府立法、新闻实事、产品评论、客户反馈和科学研究文章。

此方案中使用的机器学习技术/算法包括：

1. 文本处理和清理

1. 短语学习

1. 主题建模

1. 语料库摘要

1. 主题趋势和异常检测

## <a name="prerequisites"></a>先决条件

运行此示例的先决条件如下所示：

* 确保你已经按照[安装和创建快速入门](../service/quickstart-installation.md)中的说明正确安装 [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md)。

* 本示例可以在任何计算环境中运行。 但是，建议在内存至少为 16 GB 且磁盘空间至少为 5 GB 的多核计算机中运行本示例。

## <a name="create-a-new-workbench-project"></a>创建新的 Workbench 项目

使用本示例作为模板，创建一个新项目：
1.  打开 Azure Machine Learning Workbench
2.  在“项目”页上单击 + 号，然后选择“新建项目”
3.  在“新建项目”窗格中，填写新项目的信息
4.  在“搜索项目模板”搜索框中，键入“Document Collection Analysis”并选择模板
5.  单击“创建” 

## <a name="data-description"></a>数据说明

此方案中使用的数据集包含美国国会采取的每项立法行动的文本摘要和相关元数据。 数据是从 [GovTrack.us](https://www.govtrack.us/) 收集的，它跟踪美国国会的活动并帮助美国人参与其国家立法过程。 可以通过[此链接](https://www.govtrack.us/data/congress/)使用手动脚本下载批量数据，该脚本不包括在此方案中。 有关如何下载数据的详细信息，可以在 [GovTrack API 文档](https://www.govtrack.us/developers/api)中找到。

### <a name="data-source"></a>数据源

在此方案中，收集的原始数据是 1973 年至 2017 年 6 月（第 93 至第 115 次会议）的美国国会提出的一系列立法行动（提出的法案和决议）。 收集的数据采用 JSON 格式，包含有关立法行动的丰富信息。 有关数据字段的详细说明，请参阅[此 GitHub 链接](https://github.com/unitedstates/congress/wiki/bills)。 为了在此方案中实现演示目的，仅从 JSON 文件中提取一些数据字段。 此方案提供了包含这些立法行动记录的预编译 TSV 文件 `CongressionalDataAll_Jun_2017.tsv`。 可以通过笔记本文件夹下的笔记本 `1_Preprocess_Text.ipynb` 或 Python 包中的 `preprocessText.py` 自动下载 TSV 文件。

### <a name="data-structure"></a>数据结构

数据文件中有 9 个数据字段。 数据字段名称和说明如下所示。

| 字段名称 | Type | 说明 | 包含缺失值 |
|------------|------|-------------|---------------|
| `ID` | String | 法案和决议的 ID。 此字段的格式是 [bill_type][number]-[congress]。 例如，“hconres1-93”表示法案类型为“hconres”（代表众议院共同决议，请参阅[本文档](https://github.com/unitedstates/congress/wiki/bills#basic-information)），法案号为“1”，会议号为“93”。 | 否 |
| `Text` | String | 法案/决议的内容。 | 否 |
| `Date` | String | 最初提出法案/决议的日期。 采用“yyyy-mm-dd”的格式。 | 否 |
| `SponsorName` | String | 提出法案/决议的主要发起人的名字。 | 是 |
| `Type` | String | 主要发起人的标题类型是“rep”（代表）或“sen”（参议员）。 | 是 |
| `State` | String | 主要发起人的状态。 | 是 |
| `District` | Integer | 如果发起人的标题是代表，这是主要发起人的区号。 | 是 |
| `Party` | String | 主要发起人的参与方。 | 是 |
| `Subjects` | String | 国会图书馆累积添加到法案的主题术语。 这些术语用逗号分隔。 这些术语由国会图书馆的人员撰写，首次发布法案信息时通常不提供。 可以随时添加它们。 因此在法案生命周期终结之前，一些主题可能不再相关了。 | 是 |

## <a name="scenario-structure"></a>方案结构

文档集分析示例分为两种类型的可交付结果。 第一种类型是一系列 iPython Notebook，显示整个工作流的分步说明。 第二种类型是 Python 包以及如何使用该包的一些代码示例。 Python 包是通用的，足以供许多用例使用。

本示例中的文件按如下方式组织。

| 文件名 | Type | 说明 |
|-----------|------|-------------|
| `aml_config` | 文件夹 | Azure Machine Learning Workbench 配置文件夹，请参阅[此文档](./experimentation-service-configuration-reference.md)了解详细的试验执行配置 |
| `Code` | 文件夹 | 用来保存 Python 脚本和 Python 包的代码文件夹 |
| `Data` | 文件夹 | 用来保存中间文件的数据文件夹 |
| `notebooks` | 文件夹 | Jupyter 笔记本文件夹 |
| `Code/documentAnalysis/__init__.py` | Python 文件 | Python 包 init 文件 |
| `Code/documentAnalysis/configs.py` | Python 文件 | 文件分析 Python 包使用的配置文件，包括预定义的常数 |
| `Code/documentAnalysis/preprocessText.py` | Python 文件 | 用于对下游任务的数据进行预处理的 Python 文件。 |
| `Code/documentAnalysis/phraseLearning.py` | Python 文件 | 用于从数据中学习短语并转换原始数据的 Python 文件 |
| `Code/documentAnalysis/topicModeling.py` | Python 文件 | 用于定型 Latent Dirichlet Allocation (LDA) 主题模型的 Python 文件 |
| `Code/step1.py` | Python 文件 | 文档集分析步骤 1：预处理文本 |
| `Code/step2.py` | Python 文件 | 文档集分析步骤 2：短语学习 |
| `Code/step3.py` | Python 文件 | 文档集分析步骤 3：定型和评估 LDA 主题模型 |
| `Code/runme.py` | Python 文件 | 在一个文件中运行所有步骤的示例 |
| `notebooks/1_Preprocess_Text.ipynb` | iPython Notebook | 预处理文本并转换原始数据 |
| `notebooks/2_Phrase_Learning.ipynb` | iPython Notebook | 从文本数据中学习短语（数据转换后） |
| `notebooks/3_Topic_Model_Training.ipynb` | iPython Notebook | 定型 LDA 主题模型 |
| `notebooks/4_Topic_Model_Summarization.ipynb` | iPython Notebook | 基于定型的 LDA 主题模型总结文档集的内容 |
| `notebooks/5_Topic_Model_Analysis.ipynb` | iPython Notebook | 分析文本文档集的主题内容，并针对与文档集相关联的其他元数据关联主题信息 |
| `notebooks/6_Interactive_Visualization.ipynb` | iPython Notebook | 学习的主题模型的交互式可视化效果 |
| `notebooks/winprocess.py` | Python 文件 | 笔记本进行多处理所用的 python 脚本 |
| `README.md` | Markdown 文件 | 自述 Markdown 文件 |

### <a name="data-ingestion-and-transformation"></a>数据引入和转换

编译的数据集 `CongressionalDataAll_Jun_2017.tsv` 保存在 Blob 存储中，可同时从笔记本和 Python 脚本中访问。 数据引入和转换有两个步骤：预处理文本数据和短语学习。

#### <a name="preprocess-text-data"></a>预处理文本数据

预处理步骤应用自然语言处理技术来清理和准备原始文本数据。 它相当于非监督式短语学习和潜在主题建模的前兆。 详细的分步说明可以在笔记本 `1_Preprocess_Text.ipynb` 中找到。 还有一个 Python 脚本 `step1.py` 对应于该笔记本。

在该步骤中，TSV 数据文件是从 Azure Blob 存储下载的并作为 Pandas DataFrame 导入。 DataFrame 中的每个行元素都是紧密结合的一长串文本或“文档”。 然后，每个文档被拆分为大量文本，可能是句子、短语或子短语。 拆分旨在禁止短语学习过程在学习短语时使用交叉句子或交叉词组字串。

笔记本和 Python 包中都有为预处理步骤定义的多个功能。 大部分工作都可以通过调用这两行代码来完成。

```python
# Read raw data into a Pandas DataFrame
textDF = getData()

# Write data frame with preprocessed text out to TSV file
cleanedDataFrame = CleanAndSplitText(textDF, saveDF=True)
```

#### <a name="phrase-learning"></a>短语学习

短语学习步骤实现了在大型文档集中学习关键短语的基本框架。 它在题为“[Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf)”的论文中进行了介绍，这最初是在 2012 年 IEEE 口语技术研讨会上提出的。 短语学习步骤的详细实现情况显示在 iPython Notebook `2_Phrase_Learning.ipynb` 和 Python 脚本 `step2.py` 中。

此步骤将清理的文本作为输入，并学习大型文档集内存在的最关键的短语。 短语学习是一个迭代过程，可以分为三个任务：计数 n-gram，通过其组成词的加权点互信息对潜在短语进行排列以及将短语重写为文本。 这三个任务在多次迭代中按顺序执行，直到学习了指定的短语为止。

在文档分析 Python 包中，Python 类 `PhraseLearner` 是在 `phraseLearning.py` 文件中定义的。 下面是用于学习短语的代码段。

```python
# Instantiate a PhraseLearner and run a configuration
phraseLearner = PhraseLearner(cleanedDataFrame, "CleanedText", numPhrase,
                        maxPhrasePerIter, maxPhraseLength, minInstanceCount)

# The chunks of text in a list
textData = list(phraseLearner.textFrame['LowercaseText'])

# Learn most salient phrases present in a large collection of documents
phraseLearner.RunConfiguration(textData,
            phraseLearner.learnedPhrases,
            addSpace=True,
            writeFile=True,
            num_workers=cpu_count()-1)
```

> [!NOTE]
> 短语学习步骤是通过多处理实现的。 但是，更多的 CPU 内核**并不**意味着执行速度更快。 在我们的测试中，由于多处理的开销，内核超过 8 个的情况下性能并没有得到改善。 在一个有 8 个内核（3.6 GHz）的机器上学习 25,000 个短语大约需要两个半小时。
>

### <a name="topic-modeling"></a>主题建模

学习潜在主题模型用 LDA 是此方案的第三步。 要学习 [LDA 主题模型](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation)，此步骤需要 [gensim](https://radimrehurek.com/gensim/) Python 包。 此步骤的相应笔记本是 `3_Topic_Model_Training.ipynb`。 还可以参考 `step3.py`，了解如何使用文档分析包。

在此步骤中，主要任务是了解 LDA 主题模型并调整超参数。 定型 LDA 模型时需要调整多个参数，但最重要的参数是主题数。 主题太少可能对文件集没有洞察力；而选择太多会导致语料库“过度聚集”成许多小且高度相似的主题。 此方案的目的是为了演示如何优化主题数。 Azure Machine Learning Workbench 可以让你自由地在不同计算环境中运行具有不同参数配置的试验。

在文档分析 Python 包中，定义了一些功能来帮助用户找出最佳主题数。 第一种方法是评估主题模型的一致性。 支持四个评估矩阵：`u_mass`、`c_v`、`c_uci` 和 `c_npmi`。 [本白皮书](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf)讨论了这四个指标的详细信息。 第二种方法是评估留存语料库的困惑度。

对于困惑度评估，U 形曲线会找出最佳主题数。 弯头位置是最佳选择。 建议通过不同的随机种子评估多次并获得平均值。 一致性评估预计会是“n”形，这意味着一致性随着主题数的增加而增加，然后再减少。 困惑度和 `c_v` 一致性的示例图如下所示。

![困惑度](./media/scenario-document-collection-analysis/Perplexity_Value.png)

![c_v 一致性](./media/scenario-document-collection-analysis/c_v_Coherence.png)

在此方案中，困惑度在 200 个主题之后明显增加，而一致性值在 200 个主题之后大幅下降。 根据这些图和对更多一般主题与过度聚集主题的需要，选择 200 个主题应该是一个很好的选择。

可以在一个试验运行中定型一个 LDA 主题模型，或者在单个试验运行中定型和评估多个具有不同主题数配置的 LDA 模型。 建议对一个配置运行多次，然后获得一致性和/或困惑度平均评估值。 有关如何使用文档分析包的详细信息，可以在 `step3.py` 文件中找到。 示例代码片段如下所示。

```python
topicmodeler = TopicModeler(docs,
        stopWordFile=FUNCTION_WORDS_FILE,
        minWordCount=MIN_WORD_COUNT,
        minDocCount=MIN_DOC_COUNT,
        maxDocFreq=MAX_DOC_FREQ,
        workers=cpu_count()-1,
        numTopics=NUM_TOPICS,
        numIterations=NUM_ITERATIONS,
        passes=NUM_PASSES,
        chunksize=CHUNK_SIZE,
        random_state=RANDOM_STATE,
        test_ratio=test_ratio)

# Train an LDA topic model
lda = topicmodeler.TrainLDA(saveModel=saveModel)

# Evaluate coherence metrics
coherence = topicmodeler.EvaluateCoherence(lda, coherence_types)

# Evaluate perplexity on a held-out corpus
perplex = topicmodeler.EvaluatePerplexity(lda)
```

> [!NOTE]
> 定型 LDA 主题模型的执行时间取决于多种因素，例如语料库的大小、超参数配置以及计算机上的内核数。 使用多个 CPU 内核可以更快地定型模型。 但是，如果超参数设置相同，则更多的内核意味着在定型期间更新更少。 建议**至少通过 100 个更新来定型融合的 LDA 模型**。 [此贴](https://groups.google.com/forum/#!topic/gensim/ojySenxQHi4)和[此贴](http://miningthedetails.com/blog/python/lda/GensimLDA/)中讨论了更新数和超参数个数之间的关系。 在我们的测试中，在具有 16 个内核 (2.0 GHz) 的计算机上，使用 `workers=15`、`passes=10`、`chunksize=1000` 的配置对具有 200 个主题的 LDA 模型进行定型需要大约 3 个小时。
>

### <a name="topic-summarization-and-analysis"></a>主题摘要和分析

主题摘要和分析包括两个笔记本，而在文档分析包中没有相应的功能。

在 `4_Topic_Model_Summarization.ipynb` 中，它演示了如何根据定型的 LDA 主题模型来汇总文档的内容。 摘要应用于在步骤 3 中学习的 LDA 主题模型。 它演示了如何使用主题来衡量主题的重要性或质量，以便记录纯度测量。 此纯度测量假设与在许多文档中分布稀少的潜在主题相比，主导其所在文档的潜在主题从语义上更重要。 此概念是在“[针对音频语料库摘要的潜在主题建模](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf)”一文中引入的。

Notebook `5_Topic_Model_Analysis.ipynb` 演示如何分析文档集的主题内容，并针对与文档集相关联的其他元数据关联主题信息。 该笔记本中介绍了几个绘图，以帮助用户更好地了解学习主题和文档集。

Notebook `6_Interactive_Visualization.ipynb` 演示如何以交互方式直观显示学习的主题模型。 它包括四个交互式可视化任务。

## <a name="conclusion"></a>结束语

这个实际方案重点介绍了如何使用已知文本分析技术（在这种情况下，是短语学习和 LDA 主题建模）来生成可靠的模型，以及 Azure Machine Learning Workbench 如何帮助跟踪模型性能并以更大的规模无缝运行学习器。 在更多详细信息中：

* 使用短语学习和主题建模来处理文档集和构建可靠的模型。 如果文档集很大，Azure Machine Learning Workbench 可以轻松纵向和横向扩展它。此外，用户可以自由地在 Azure Machine Learning Workbench 中轻松地在不同的计算环境中运行试验。

* Azure Machine Learning Workbench 提供了可以分步运行笔记本的两个选项，还提供了运行整个试验的 Python 脚本。

* 使用 Azure Machine Learning Workbench 进行超参数调整，以找到需要了解的最佳主题数。 Azure Machine Learning Workbench 可以帮助跟踪模型性能，并以更大规模无缝运行不同的学习器。

* Azure Machine Learning Workbench 可以管理运行历史记录和学习的模型。 它使数据科学家能够快速识别性能最佳的模型，并找到用于生成它们的脚本和数据。

## <a name="references"></a>参考

* **Timothy J. Hazen, Fred Richardson**, [_Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Spoken Language Technology Workshop (SLT), 2012 IEEE. IEEE, 2012.

* **Timothy J. Hazen**, [_Latent Topic Modeling for Audio Corpus Summarization_](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf). 12th Annual Conference of the International Speech Communication Association. 2011.

* **Michael Roder, Andreas Both, Alexander Hinneburg**, [_Exploring the Space of Topic Coherence Measures_](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). Proceedings of the eighth ACM international conference on Web search and data mining. ACM, 2015.
