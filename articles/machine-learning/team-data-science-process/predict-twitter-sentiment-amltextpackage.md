---
title: 使用用于文本分析的 Azure 机器学习 (AML) 包 (AMLPTA) 和 Team Data Science Process (TDSP) 进行 Twitter 情绪分类 | Microsoft Docs
description: 介绍如何使用 TDSP (Team Data Science Process) 和 AMLPTA 进行情绪分类
services: machine-learning, team-data-science-process
documentationcenter: ''
author: deguhath
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: deguhath
ms.openlocfilehash: 559af47bcf41cd6af59f8ba1b27ff8e64e849925
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296894"
---
# <a name="twitter-sentiment-classification-with-azure-machine-learning-aml-package-for-text-analytics-amlpta-and-team-data-science-process-tdsp"></a>使用用于文本分析的 Azure 机器学习 (AML) 包 (AMLPTA) 和 Team Data Science Process (TDSP) 进行 Twitter 情绪分类

## <a name="introduction"></a>介绍
实现数据科学项目结构和文档的标准化，从而建立[数据科学生命周期](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)，对于促进数据科学团队的有效协作很重要。

我们此前已发布[适用于 TDSP 项目结构和模板的 GitHub 存储库](https://github.com/Azure/Azure-TDSP-ProjectTemplate)， 我们现在已允许用户创建通过[适用于 Azure 机器学习的 TDSP 结构和文档模板](https://github.com/amlsamples/tdsp)来实例化的 Azure 机器学习项目。 有关如何在 Azure 机器学习中使用 TDSP 结构和模板的说明，请参阅[此文](https://docs.microsoft.com/en-us/azure/machine-learning/preview/how-to-use-tdsp-in-azure-ml)。 

本示例将演示如何使用用于文本分析的 Azure 机器学习包与 TDSP 来为 Twitter 情绪分类开发和部署预测模型。


## <a name="use-case"></a>使用案例
### <a name="twitter-sentiment-polarity-sample"></a>Twitter 情绪极性示例
本文使用示例演示如何实例化和执行机器学习项目。 本示例将在 Azure Machine Learning Workbench 中使用 TDSP 结构和模板。 本演练中提供了完整的示例。 建模任务使用推文中的文字来预测情绪极性（积极或消极）。 本文概述了演练中所述的数据建模任务。 本演练涵盖以下任务：

1. 机器学习模型的数据浏览、训练和部署，目的是解决“用例概述”中描述的预测问题。 这些任务使用了 Twitter 情绪数据。
2. 通过从 Azure 机器学习为此项目提供的 TDSP 模板执行项目。 执行和报告项目时，会使用 TDSP 生命周期。
3. 直接从 Azure 容器服务中的 Azure 机器学习实现解决方案的操作化。

本项目突显了 Azure 机器学习文本分析包的用法。


## <a name="link-to-github-repository"></a>GitHub 存储库链接
[此处](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction)提供了 GitHub 存储库的链接。 

### <a name="purpose"></a>目的
本示例的主要目的是介绍如何在 Azure Machine Learning WorkBench 中使用 Team Data Science Process (TDSP) 结构和模板来实例化和执行机器学习项目。 为此，我们使用了 [Twitter 观点数据](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip)。 建模任务使用推文中的文字来预测情绪极性（积极或消极）。

### <a name="scope"></a>范围
- 机器学习模型的数据浏览、训练和部署，目的是解决“用例概述”中描述的预测问题。
- 使用 Azure 机器学习为本项目提供的 Team Data Science Process (TDSP) 模板在 Azure 机器学习中执行本项目。 我们将根据 TDSP 生命周期来执行和报告项目。
- 直接从 Azure 容器服务中的 Azure 机器学习实现解决方案的操作化。

本项目突显了 Azure 机器学习的多项功能，例如实例化和使用 TDSP 结构、执行 Azure Machine Learning WorkBench 中的代码，以及使用 Docker 和 Kubernetes 在 Azure 容器服务中轻松实现操作化。

## <a name="team-data-science-process-tds"></a>Team Data Science Process (TDS)
我们将使用 TDSP 项目结构和文档模板来执行此示例。 此模板遵循 [TDSP 生命周期](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/lifecycle)。 项目是根据[此处](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md)提供的说明创建的。


<img src="./media/predict-twitter-sentiment-amltextpackage/tdsp-lifecycle2.png" alt="tdsp-lifecycle" width="800" height="600">


## <a name="use-case-overview"></a>用例概述
任务是使用从推文中提取的单词嵌入特征来预测每篇推文的情绪二元极性。 有关详细说明，请参阅此[存储库](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction)。

### <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[数据采集和理解](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
本示例的第一个步骤是下载 sentiment140 数据集，并将其划分为训练和测试数据集。 Sentiment140 数据集包含推文的实际内容（已删除情绪），以及每篇推文的极性（消极=0，积极=4），并删除了中性推文。 生成的训练数据包含 130 万行，测试数据包含 32 万行。

### <a name="modelinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode02modeling"></a>[建模](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/02_modeling)
此示例部分进一步划分为三个子部分： 
- **特征工程**是指使用不同单词嵌入算法（即 Word2Vec）生成特征。 
- **模型创建**处理不同模型（例如“逻辑回归”和“渐进提升”）的训练，以预测输入文本的情绪。 
- **模型评估**通过测试数据应用训练的模型。

#### <a name="feature-engineering"></a>特性工程
我们将使用 <b>Word2Vec</b> 来生成单词嵌入。 首先，根据 Mikolov、Tomas 等人 2013 年在论文 [Distributed representations of words and phrases and their compositionality.Advances in neural information processing systems.2013.](https://arxiv.org/abs/1310.4546) （单词和短语的分布式表示形式及其组成暨神经信息处理系统的进步）中所述，使用 Skipgram 模式的 Word2Vec 算法。

Skip-gram 是一种浅表神经网络，它使用编码为独热向量的目标单词作为输入，并使用它来预测邻近的单词。 如果词汇库的大小为 V，则输出层的大小为 __C*V__，其中 C 为上下文窗口的大小。 下图显示了基于 Skip-gram 的体系结构。
 
<table class="image" align="center">
<caption align="bottom">Skip-gram 模型</caption>
<tr><td><img src="https://s3-ap-south-1.amazonaws.com/av-blog-media/wp-content/uploads/2017/06/05000515/Capture2-276x300.png" alt="Skip-gram model"/></td></tr>
</table>

本示例不介绍 Word2Vec 算法和 Skip-gram 模型的详细信息，有兴趣的读者请访问以下链接了解更多详细信息。 02_A_Word2Vec.py 代码参考了 [tensorflow 示例](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py)

* [单词的向量表示形式](https://www.tensorflow.org/tutorials/word2vec)
* [word2vec 的工作原理是什么？](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
* [有关干扰对比估计和负采样的说明](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)

训练过程完成后，将为建模阶段生成 TSV 格式的两个嵌入文件。

#### <a name="model-training"></a>模型定型
使用 SSWE 或 Word2Vec 算法生成单词向量后，下一步是训练分类模型，以预测实际情绪极性。 将两种类型的特征（Word2Vec 和 SSWE）应用到以下两个模型：逻辑回归和卷积神经网络 (CNN)。 

#### <a name="model-evaluation"></a>模型评估
我们提供了有关如何基于测试数据集加载和评估多个训练模型的代码。


### <a name="deploymenthttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode03deployment"></a>[部署](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/03_deployment)
此部分提供有关如何在 Azure 容器服务 (AKS) 中某个群集上的 Web 服务中操作化预先训练的情绪预测模型的说明链接。 操作化环境在群集中预配 Docker 和 Kubernetes 来管理 Web 服务部署。 可以在[此处](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy)找到有关操作化过程的更多信息。

## <a name="conclusion"></a>结束语
我们全面介绍了使用 Word2Vec 训练单词嵌入模型，然后使用提取的嵌入内容作为特征，来训练两个不同的模型，以预测 Twitter 文本数据的情绪评分。 其中一个模型部署在 Azure 容器服务 (AKS) 中。 

## <a name="next-steps"></a>后续步骤
在[用于文本分析的 Azure 机器学习包 (AMLPTA)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/textanalytics?view=azure-ml-py-latest) 和 [Team Data Science Process (TDSP)](https://aka.ms/tdsp) 中阅读其他入门信息。

## <a name="references"></a>参考
* [团队数据科学过程](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) 
* [如何在 Azure 机器学习中使用 Team Data Science Process (TDSP)](https://aka.ms/how-to-use-tdsp-in-aml)
* [适用于 Azure 机器学习的 TDSP 项目模板](https://aka.ms/tdspamlgithubrepo)
* [Azure ML WorkBench](https://docs.microsoft.com/en-us/azure/machine-learning/preview/)
* Tomas Mikolov 等人在 2013 年撰写的 [Distributed representations of words and phrases and their compositionality.Advances in neural information processing systems.](https://arxiv.org/abs/1310.4546)（单词和短语的分布式表示形式及其组成暨神经信息处理系统的进步）
