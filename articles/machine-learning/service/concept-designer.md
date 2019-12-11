---
title: 用设计器生成 ML 模型
titleSuffix: Azure Machine Learning
description: 了解构成 Azure 机器学习设计器的术语、概念和工作流。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 11/12/2019
ms.openlocfilehash: efc9d7fb31dc9f63780c3f94238bbbfb17756089
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973622"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>什么是 Azure 机器学习设计器（预览）？ 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Azure 机器学习设计器使您能够以可视方式连接交互式画布上的[数据集](#datasets)和[模块](#module)，以创建机器学习模型。 若要了解如何开始设计设计器，请参阅[教程：利用设计器预测汽车价格](tutorial-designer-automobile-price-train-score.md)

![Azure 机器学习设计器示例](./media/concept-ml-pipelines/designer-drag-and-drop.gif)

设计器使用您的 Azure 机器学习[工作区](concept-workspace.md)来组织共享的资源，例如：

+ [管道](#pipeline)
+ [数据集](#datasets)
+ [计算资源](#compute)
+ [已注册模型](concept-azure-machine-learning-architecture.md#models)
+ [已发布管道](#publish)
+ [实时终结点](#deploy)

## <a name="model-training-and-deployment"></a>模型定型和部署

设计器提供了用于生成、测试和部署机器学习模型的可视化画布。 利用设计器，你可以：

+ 将[数据集](#datasets)和[模块](#module)拖放到画布上。
+ 将模块连接在一起以创建[管道草案](#pipeline-draft)。
+ 使用 Azure 机器学习工作区中的计算资源提交[管道运行](#pipeline-run)。
+ 将**定型管道**转换为**推理管道**。
+ 将管道[发布](#publish)到 REST**管道终结点**，以使用不同的参数和数据集提交新的管道运行。
    + 发布**定型管道**，以重复使用单个管道在更改参数和数据集的同时训练多个模型。
    + 发布**批处理推理管道**，以使用以前训练的模型对新数据进行预测。
+ 将**实时推理管道**[部署](#deploy)到实时终结点，以对新数据进行实时预测。

![设计器中用于定型、批处理推理和实时推理的工作流关系图](media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>管道

[管道](concept-azure-machine-learning-architecture.md#ml-pipelines)由数据集和分析模块组成，这些模块将连接在一起。 管道有许多用途：您可以创建一条模拟单个模型的管道，或模拟多个模型的管道。 您可以创建一个管道，使其实时或批量进行预测，或者创建只清理数据的管道。 管道使你可以重复使用工作并组织你的项目。

### <a name="pipeline-draft"></a>管道草案

当你在设计器中编辑管道时，你的进度将保存为**管道草稿**。 可以通过添加或删除模块、配置计算目标、创建参数等，随时编辑管道草案。

有效的管道具有以下特征：

* 数据集只能连接到模块。
* 模块只能连接到数据集或其他模块。
* 模块的所有输入端口都必须连接到数据流。
* 必须设置每个模块的所有必需参数。

当您准备好运行管道草案时，您将提交一个管道运行。

### <a name="pipeline-run"></a>管道运行

每次运行管道时，管道及其结果的配置都作为**管道运行**存储在工作区中。 你可以返回到任何管道运行，以检查其是否存在故障排除或审核目的。 **克隆**管道运行以创建新的管道草稿供你编辑。

管道运行分组为[试验](concept-azure-machine-learning-architecture.md#experiments)，以组织运行历史记录。 可以设置每个管道运行的试验。 

## <a name="datasets"></a>数据集

使用机器学习数据集可以轻松地访问和处理数据。 设计器中包含了许多示例数据集，以便您体验。 你可以根据需要[注册](./how-to-create-register-datasets.md)更多的数据集。

## <a name="module"></a>模块

模块是可对数据执行的算法。 设计器提供了许多模块，其中包括将数据入口函数到定型、评分和验证过程。

模块可能提供一组参数用于配置模块的内部算法。 选择画布上的模块时，模块的参数将显示在画布右侧的 "属性" 窗格中。 可以在该窗格中修改参数来调整模型。 可以在设计器中设置各个模块的计算资源。 

![模块属性](media/concept-designer/properties.png)

有关在可用的机器学习算法库中导航的帮助，请参阅[算法 & 模块参考概述](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>计算资源

使用工作区中的计算资源来运行管道，并将已部署的模型作为实时终结点或管道终结点进行托管（对于批处理推理）。 支持的计算目标为：

| 计算目标 | 培训 | 部署 |
| ---- |:----:|:----:|
| Azure 机器学习计算 | ✓ | |
| Azure Kubernetes 服务 | | ✓ |

计算目标会附加到[Azure 机器学习工作区](concept-workspace.md)。 在[Azure 机器学习 Studio （经典）](https://ml.azure.com)中的工作区中管理计算目标。

## <a name="deploy"></a>部署

若要执行实时推断，必须将管道部署为**实时终结点**。 实时终结点在外部应用程序和评分模型之间创建接口。 对实时端点的调用会将预测结果实时返回给应用程序。 若要调用实时终结点，需传递部署终结点时创建的 API 密钥。 终结点基于 REST，这是 web 编程项目的常用体系结构选项。

必须将实时终结点部署到 Azure Kubernetes 服务群集。

若要了解如何部署模型，请参阅[教程：使用设计器部署机器学习模型](tutorial-designer-automobile-price-deploy.md)。

## <a name="publish"></a>发布

还可以将管道发布到**管道终结点**。 与实时终结点类似，管道终结点允许你使用 REST 调用从外部应用程序提交新的管道运行。 但是，不能使用管道端点实时发送或接收数据。

已发布的管道是灵活的，它们可用于定型或重新训练模型、[执行批处理推断](how-to-run-batch-predictions-designer.md)、处理新数据等。 可以将多个管道发布到单个管道终结点，并指定要运行的管道版本。

已发布的管道在每个模块的管道草稿中定义的计算资源上运行。

设计器将创建与 SDK 相同的[PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py)对象。


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

