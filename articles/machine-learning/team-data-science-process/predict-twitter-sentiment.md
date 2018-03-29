---
title: 使用 Azure 中的团队数据科学过程，通过字词嵌入预测 Twitter 情绪 |Microsoft Docs
description: 执行数据科学项目所需的步骤。
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: bradsev
ms.openlocfilehash: f22da892868a10ac18fdcd703249eaa172f8bf65
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="predict-twitter-sentiment-with-word-embeddings-by-using-the-team-data-science-process"></a>使用团队数据科学过程，通过字词嵌入预测 Twitter 情绪

本文演示如何使用 _Word2Vec_ 字词嵌入算法和_情绪特定字词嵌入 (SSWE)_ 算法，通过 [Azure 机器学习](../preview/index.yml)预测 Twitter 情绪，从而实现有效协作。 有关预测 Twitter 情绪极性的详细信息，请参阅 GitHub 上的 [MachineLearningSamples-TwitterSentimentPrediction 存储库](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction)。 促进数据科学项目的有效团队协作的关键，是通过既有的数据科学生命周期实现项目结构和文档的标准化。 [团队数据科学过程 (TDSP)](overview.md) 提供这种类型的结构化[生命周期](lifecycle.md)。 

通过 _TDSP 模板_创建数据科学项目，为 Azure 机器学习项目提供了标准化框架。 此前，TDSP 团队已发布[适用于 TDSP 项目结构和模板的 GitHub 存储库](https://github.com/Azure/Azure-TDSP-ProjectTemplate) 现在，启用了使用[适用于 Azure 机器学习的 TDSP 模板](https://github.com/amlsamples/tdsp)实例化的机器学习项目。 有关说明，请参阅 Azure 机器学习中的[如何通过 TDSP 模板使用 TDSP 结构项目](../preview/how-to-use-tdsp-in-azure-ml.md)。 


## <a name="twitter-sentiment-polarity-sample"></a>Twitter 情绪极性示例

本文使用示例演示如何实例化和执行机器学习项目。 本示例将在 Azure Machine Learning Workbench 中使用 TDSP 结构和模板。 [本演练](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md)中提供了完整的示例。 建模任务使用推文中的文字来预测情绪极性（积极或消极）。 本文概述了演练中所述的数据建模任务。 本演练涵盖以下任务：

- 机器学习模型的数据浏览、训练和部署，目的是解决“用例概述”中描述的预测问题。 这些任务使用了 [Twitter 情绪数据](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip)。
- 通过从 Azure 机器学习为此项目提供的 TDSP 模板执行项目。 执行和报告项目时，会使用 TDSP 生命周期。
- 直接从 Azure 容器服务中的 Azure 机器学习实现解决方案的操作化。

项目突显了 Azure 机器学习的以下功能：

- 实例化和使用 TDSP 结构。
- 在 Azure Machine Learning Workbench 中执行代码。
- 使用 Docker 和 Kubernetes 在容器服务中轻松操作。

## <a name="team-data-science-process"></a>Team Data Science Process
若要执行此示例，请在 Azure Machine Learning Workbench 中使用 TDSP 项目结构和文档模板。 此示例实现 [TDSP 生命周期](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)，如下图所示：

![TDSP 生命周期](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

基于[这些说明](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md)在 Azure Machine Learning Workbench 中创建 TDSP 项目，如下图所示：

![在 Azure Machine Learning Workbench 中创建 TDSP](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-preparationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[数据采集和准备](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
在此示例中的第一步是下载 sentiment140 数据集，并将数据划分为定型和测试数据集。 sentiment140 数据集包含推文的实际内容（删除了表情符号）。 数据集还包含每则推文的极性（消极 = 0，积极 = 4），并删除了中立推文。 划分数据后，定型数据有 130 万行，测试数据有 32 万行。


## <a name="model-developmenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[模型开发](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

此示例中的下一步是开发数据模型。 建模任务分为三个部分：

- 功能设计：使用不同的单词嵌入算法生成模型功能。 
- 模型创建：训练不同的模型来预测输入文字情绪。 这些模型的示例包括逻辑回归和渐进提升。
- 模型评估：通过测试数据评估定型模型。


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[功能设计](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

使用Word2Vec 和 SSWE 算法生成单词嵌入。 


#### <a name="word2vec-algorithm"></a>Word2Vec 算法

Skip-Gram 模型中使用了 Word2Vec 算法。 Tomas Mikolov 等人的论文《[Distributed Representations of Words and Phrases and their Compositionality.Advances in neural information processing systems.](https://arxiv.org/abs/1310.4546)》（单词和短语的分布式表示形式及其组成暨神经信息处理系统的进步）中对此模型进行了阐释。 2013.

Skip-Gram 模型是浅表神经网络。 将输入的目标单词编码为独热向量，用于预测邻近单词。 如果词汇库的大小为 V，则输出层的大小为 C*V，其中 C 为上下文窗口的大小。 下图显示了基于 Skip-Gram 模型的体系结构：

![Skip-Gram 模型](./media/predict-twitter-sentiment/skip-gram-model.PNG)

本示例不介绍 Word2Vec 算法和 Skip-Gram 模型的详细机制。 有关详细信息，请参阅以下资源：

- [02_A_Word2Vec.py 代码与引用 TensorFlow 示例](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py) 
- [词的矢量表示形式](https://www.tensorflow.org/tutorials/word2vec)
- [word2vec 的工作原理是什么？](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
- [有关干扰对比估计和负采样的说明](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)




#### <a name="sentiment-specific-word-embedding-algorithm"></a>情绪特定单词嵌入算法
情绪特定单词嵌入 (SSWE) 算法尝试克服 Word2Vec 算法中具有相同上下文和相反极性的单词可能拥有相同单词矢量的弱点。 对于情绪分析等任务，这种相似性可能导致 Word2Vec 算法估计不准确。 SSWE 算法尝试通过将句子极性和单词上下文合并到其损失函数，来应对这一弱点。

示例使用 SSWE 算法的变体，如下所示：

- 原始 ngram 和损坏 ngram 都用作输入。
- 使用排名风格枢纽损失函数来检语法损失和测语义损失。
- 最终损失函数是语法损失和语义损失的加权和。
- 为简单起见，仅将语义交叉熵用作损失函数。

示例显示，即使使用更简单的损失函数，SSWE 嵌入的性能仍优于 Word2Vec 嵌入。 下图显示了用于生成情绪特定单词嵌入的卷积模型：

![以 SSWE 为基础创建的神经网络模型](./media/predict-twitter-sentiment/embedding-model-2.PNG)

定型过程完成后，会为建模阶段生成制表符分隔值 (TSV) 格式的两个嵌入文件。

有关 SSWE 算法的详细信息，请参阅 Duyu Tang 等人编著的白皮书：《[Learning Sentiment-Specific Word Embedding for Twitter Sentiment Classification](http://www.aclweb.org/anthology/P14-1146)》（学习 Twitter 情绪分类所用的情绪特定单词嵌入）。 计算语言学协会 (1). 2014.


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[模型创建](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
使用 SSWE 或 Word2Vec 算法生成单词矢量后，将训练分类模型来预测实际情绪极性。 将两种功能（Word2Vec 和 SSWE）应用于两个模型：渐进提升模型和逻辑回归模型。 因此，会定型出四种模型。


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[模型评估](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
模型定型后，用其测试 Twitter 文本数据，并评估每个模型的性能。 示例计算以下四个模型：

- 渐进提升 x SSWE 嵌入。
- 逻辑回归 x SSWE 嵌入。
- 渐进提升 x Word2Vec 嵌入。
- 逻辑回归 x Word2Vec 嵌入。

下图显示了四个模型的性能比较：

![接收者操作特性 (ROC) 模型比较](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

使用曲线下面积 (AUC) 指标比较模型时，有 SSWE 功能的渐进提升模型性能最佳。


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[部署](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

最后一步是将定型的情绪预测模型部署到 Azure 容器服务中群集上的 Web 服务。 该示例使用渐进提升模型和 SSWE 嵌入算法作为定型模型。 操作化环境在群集中预配 Docker 和 Kubernetes 来管理 Web 服务部署，如下图所示： 

![Kubernetes 仪表板](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)

有关操作化过程的详细信息，请参阅[将 Azure 机器学习模型部署为 Web 服务](../preview/model-management-service-deploy.md)。

## <a name="conclusion"></a>结束语

在本文中，你学习了如何使用 Word2Vec 和情绪特定单词嵌入算法定型单词嵌入模型。 将提取的嵌入内容用作功能，训练多个模型来预测 Twitter 文本数据的情绪分数。 用于渐进提升模型的 SSWE 功能性能最佳。 然后，使用 Azure Machine Learning Workbench 将模型部署为容器服务中的实时 Web 服务。


## <a name="references"></a>参考

* [团队数据科学过程](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [如何在 Azure 机器学习中使用 Team Data Science Process (TDSP)](https://aka.ms/how-to-use-tdsp-in-aml)
* [适用于 Azure 机器学习的 TDSP 项目模板](https://aka.ms/tdspamlgithubrepo)
* [Azure Machine Learning Workbench](../preview/index.yml)
* [UCI ML 存储库中的美国收入数据集](https://archive.ics.uci.edu/ml/datasets/adult)
* [使用 TDSP 模板进行生物医学实体识别](../preview/scenario-tdsp-biomedical-recognition.md)
* [Tomas Mikolov 等人《Distributed Representations of Words and Phrases and their Compositionality.Advances in neural information processing systems.》（单词和短语的分布式表示形式及其组成暨神经信息处理系统的进步）。2013.](https://arxiv.org/abs/1310.4546)
* [Duyu Tang 等人《Learning Sentiment-Specific Word Embedding for Twitter Sentiment Classification》（学习 Twitter 情绪分类所用的情绪特定单词嵌入）。ACL (1).2014.](http://www.aclweb.org/anthology/P14-1146)
