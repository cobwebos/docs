---
title: 用设计器生成 ML 模型
titleSuffix: Azure Machine Learning
description: 了解构成 Azure 机器学习设计器的术语、概念和工作流。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: ee97322e58fe7ab3a1474f55c6294822b8ce90da
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517860"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>什么是 Azure 机器学习设计器（预览）？ 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Azure 机器学习的设计器使你能够在不编写代码的情况下准备数据、定型、测试、部署、管理和跟踪机器学习模型。

无需编程，您可以直观地连接[数据集](#datasets)和[模块](#module)来构造模型。

设计器使用您的 Azure 机器学习[工作区](concept-workspace.md)来执行以下操作：

+ 在工作区中创建、编辑和运行[管道](#pipeline)。
+ 访问[数据集](#datasets)。
+ 使用工作区中的[计算资源](#compute)来运行管道。 
+ 注册[模型](concept-azure-machine-learning-architecture.md#models)。
+ 将管道[发布](#publish)为 REST 终结点。
+ 将模型[部署](#deployment)为管道终结点（对于批处理推理）或工作区中计算资源上的实时终结点。

![设计器概述](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>工作流

设计器提供交互式的可视画布，可快速生成、测试和循环访问模型。 

+ 将[数据集](#datasets)和[模块](#module)拖放到画布上。
+ 将模块连接在一起以形成[管道](#pipeline)。
+ 使用机器学习服务工作区的计算资源来运行管道。
+ 通过编辑管道并再次运行来循环访问模型设计。
+ 准备就绪后，将**定型管道**转换为**推理管道**。
+ 如果你想要重新提交你的管道，而不是在其上构建 Python 代码，请将其[发布](#publish)为 REST 终结点。
+ 将推理管道[部署](#deployment)为管道终结点或实时终结点，以便其他人可以访问模型。

## <a name="pipeline"></a>管道

从头开始创建 ML[管道](concept-azure-machine-learning-architecture.md#ml-pipelines)，或使用现有的示例管道作为模板。 每次运行管道时，项目都存储在您的工作区中。 管道运行分组为[试验](concept-azure-machine-learning-architecture.md#experiments)。

管道由数据集和分析模块组成，它们连接在一起以构造模型。 具体而言，有效的管道具有以下特征：

* 数据集只能连接到模块。
* 模块可以连接到数据集或其他模块。
* 模块的所有输入端口都必须连接到数据流。
* 必须设置每个模块的所有必需参数。


若要了解如何开始设计设计器，请参阅[教程：通过设计器预测汽车价格](tutorial-designer-automobile-price-train-score.md)。

## <a name="datasets"></a>数据集

使用机器学习数据集可以轻松地访问和处理数据。 设计器中包含了许多示例数据集，以便您体验。 你可以根据需要[注册](./how-to-create-register-datasets.md)更多的数据集。

## <a name="module"></a>模块

模块是可对数据执行的算法。 设计器提供了许多模块，其中包括将数据入口函数到定型、评分和验证过程。

模块可能提供一组参数用于配置模块的内部算法。 选择画布上的模块时，模块的参数将显示在画布右侧的 "属性" 窗格中。 可以在该窗格中修改参数来调整模型。

![模块属性](media/ui-concept-visual-interface/properties.png)

有关在可用的机器学习算法库中导航的帮助，请参阅[算法 & 模块参考概述](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>计算资源

使用工作区中的计算资源来运行管道，并将已部署的模型作为实时终结点或管道终结点进行托管（对于批处理推理）。 支持的计算目标为：

| 计算目标 | 培训 | 部署 |
| ---- |:----:|:----:|
| Azure 机器学习计算 | ✓ | |
| Azure Kubernetes 服务 | | ✓ |

计算目标会附加到机器学习[工作区](concept-workspace.md)。 在[Azure 机器学习 studio](https://ml.azure.com)中的工作区中管理计算目标。

## <a name="publish"></a>发布

准备好管道后，可以将其发布为 REST 终结点。 可以在不使用构造它的 Python 代码的情况下提交[PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) 。

此外，可以使用 PublishedPipeline 重新提交具有不同 PipelineParameter 值和输入的管道。

## <a name="deployment"></a>部署

预测模型准备就绪后，可直接从设计器将它部署为管道终结点或实时终结点。

管道终结点是 PublishedPipeline，可以提交具有不同 PipelineParameter 值的管道运行和用于批处理推理的输入。

实时终结点在应用程序和评分模型之间提供了一个接口。 外部应用程序可与评分模型实时通信。 对实时终结点的调用将预测结果返回到外部应用程序。 若要调用实时终结点，需传递部署终结点时创建的 API 密钥。 终结点基于 REST，这是 web 编程项目的常用体系结构选项。

若要了解如何部署模型，请参阅[教程：使用设计器部署机器学习模型](tutorial-designer-automobile-price-deploy.md)。

## <a name="moving-from-the-visual-interface-to-the-designer"></a>从可视界面移到设计器

视觉对象界面（预览）已更新，现在 Azure 机器学习设计器（预览）。 设计器已重建使用基于管道的后端，该后端与 Azure 机器学习的其他功能完全集成。 

由于这些更新的原因，视觉对象界面的一些概念和术语已更改或重命名。 有关最重要的概念更改，请参阅下表。 

| 设计器中的概念 | 之前在视觉对象界面中 |
| ---- |:----:|
| 管道草案 | 试验 |
| 实时终结点 | Web 服务 |

### <a name="migrating-to-the-designer"></a>迁移到设计器

可以在设计器中将现有的视觉对象界面试验和 web 服务转换为管道和实时端点。 使用以下步骤来迁移你的可视界面资产：

[!INCLUDE [migrate from the visual interface](../../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>后续步骤

* 了解有关预测分析和机器学习的基本知识[教程：通过设计器预测汽车价格](tutorial-designer-automobile-price-train-score.md)
* 使用其中一个示例并进行修改以满足你的需求：

- [示例 1-回归：预测汽车的价格](how-to-designer-sample-regression-automobile-price-basic.md)
- [示例 2-回归：比较汽车价格预测的算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [示例 3-通过功能选择进行分类：收入预测](how-to-designer-sample-classification-predict-income.md)
- [示例 4-分类：预测信用风险（区分成本）](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [示例 5-分类：预测改动](how-to-designer-sample-classification-churn.md)
- [示例 6-分类：预测航班延迟](how-to-designer-sample-classification-flight-delay.md)
- [示例 7-文本分类：维基百科 SP 500 数据集](how-to-designer-sample-text-classification.md)

