---
title: "使用深度学习和 Azure 机器学习进行情绪分析 |Microsoft Docs"
description: "如何使用深度学习和 Azure ML Workbench 进行情绪分析。"
services: machine-learning
documentationcenter: 
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: miprasad
ms.openlocfilehash: 255c39ea21378aae23fc61da6dc882138fb66ab1
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="sentiment-analysis-using-deep-learning-with-azure-machine-learning"></a>使用深度学习和 Azure 机器学习进行情绪分析

情绪分析是自然语言处理领域中的已知任务。 假如有一组文本，目的是确定该文本的情绪。 该解决方案的目标是使用 CNTK 作为 Keras 的后端（模型级库，为开发深度学习模型提供高级构建块），并实现对电影评论的情绪分析。

该解决方案位于 https://github.com/Azure/MachineLearningSamples-SentimentAnalysis

## <a name="link-to-the-gallery-github-repository"></a>库 GitHub 存储库的链接

使用此链接访问公共 GitHub 存储库：

[https://github.com/Azure/MachineLearningSamples-SentimentAnalysis](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis)

## <a name="use-case-overview"></a>用例概述

数据的爆炸式增长和移动设备的激增，为客户创造了许多机会来随时表达自己对所有事物的感受和态度。 这种观点或“情绪”通常是通过社交渠道以评论、聊天、分享、点赞、推特等形式产生的。对于期望改进产品和服务、做出更明智的决策以及更好地推广品牌的企业来说，情绪是无价的。

为了从情绪分析中获得价值，企业必须有能力挖掘大量非结构化社交数据以获取可行的见解。 在此示例中，我们将开发深度学习模型，以使用 AMLWorkbench 对电影评论进行情绪分析

## <a name="prerequisites"></a>先决条件

* [Azure 帐户](https://azure.microsoft.com/free/)（有免费试用版可用）。

* 遵循[安装快速入门指南](./quickstart-installation.md)安装 [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) 的副本，以安装程序并创建工作区。

* 若要进行实施，最好是安装 Docker 引擎并在本地运行。 如果没有，你可以使用群集选项。 但是，运行 Azure 容器服务 (ACS) 可能成本高昂。

* 该解决方案假设在本地安装有 Docker 引擎的 Windows 10 上运行 Azure Machine Learning Workbench。 在 macOS 上，说明大体上是相同的。

## <a name="data-description"></a>数据说明

用于此示例的数据集是一个小型手工数据集，位于 [data 文件夹](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/tree/master/data)中。

第一列包含电影评论，第二列包含它们的情绪（0 - 负，1 - 正）。 数据集仅用于演示目的，但要获得强有力的情绪评分，你通常需要一个大型数据集。 例如，来自 Keras 的 [IMDB 电影评论情绪分类问题](https://keras.io/datasets/#datasets )包含一个具有来自 IMDB 的 25,000 条电影评论的数据集，通过情绪进行标记（正分或负分）。 该实验旨在向你展示如何使用深度学习和 AMLWorkbench 进行情绪分析。

## <a name="scenario-structure"></a>方案结构

文件夹结构的组织方式如下所述：

1. 根文件夹包含与使用 AMLWorkbench 进行情绪分析相关的所有代码
2. data：包含解决方案中使用的数据集
3. docs：包含所有动手实验

执行解决方案时动手实验的执行顺序如下所述：

| 顺序| 文件名 | 相关的文件 |
|--|-----------|------|
| 1 | [`SentimentAnalysisDataPreparation.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisDataPreparation.md) | 'data/sampleReviews.txt' |
| #N/A | [`SentimentAnalysisModelingKeras.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingKeras.md) | 'SentimentExtraction.py' |
| 4 | [`SentimentAnalysisOperationalization.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisOperationalization.md) | 'Operaionalization' |

## <a name="conclusion"></a>结束语

总之，此解决方案向你介绍如何使用深度学习和 Azure Machine Learning Workbench 执行情绪分析。 还使用 HDF5 模型进行操作化。
