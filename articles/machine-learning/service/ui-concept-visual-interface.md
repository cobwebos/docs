---
title: 可视界面
titleSuffix: Azure Machine Learning
description: 了解构成 Azure 机器学习的可视化界面（预览）的术语、概念和工作流。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.date: 9/23/2019
ms.openlocfilehash: a23f123c6ffadaad4f830e1f4eab3820e6ef56f6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692228"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning"></a>什么是用于 Azure 机器学习的视觉对象接口？ 

利用的视觉界面（预览 Azure 机器学习），可以在不编写代码的情况下准备数据、定型、测试、部署、管理和跟踪机器学习模型。

无需编程，您可以直观地连接[数据集](#datasets)和[模块](#module)来构造模型。

视觉对象接口使用 Azure 机器学习[工作区](concept-workspace.md)执行以下操作：

+ 在工作区中创建、编辑和运行[管道](#pipeline)。
+ 访问[数据集](#datasets)。
+ 使用工作区中的[计算资源](#compute)来运行管道。 
+ 注册[模型](concept-azure-machine-learning-architecture.md#models)。
+ 将管道[发布](#publish)为 REST 终结点。
+ 将模型[部署](#deployment)为管道终结点（对于批处理推理）或工作区中计算资源上的实时终结点。

![视觉对象界面概述](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>工作流

视觉对象界面提供交互式的可视画布，可快速生成、测试和循环访问模型。 

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


若要了解如何开始处理视觉对象界面，请参阅[教程：通过视觉对象界面预测汽车价格](ui-tutorial-automobile-price-train-score.md)。

## <a name="datasets"></a>数据集

使用机器学习数据集可以轻松地访问和处理数据。 视觉对象界面中包含了许多示例数据集供你试用。 你可以根据需要[注册](./how-to-create-register-datasets.md)更多的数据集。

## <a name="module"></a>模块

模块是可对数据执行的算法。 视觉对象接口具有多个模块，从数据入口函数到定型、评分和验证过程。

模块可能提供一组参数用于配置模块的内部算法。 选择画布上的模块时，模块的参数将显示在画布右侧的 "属性" 窗格中。 可以在该窗格中修改参数来调整模型。

![模块属性](media/ui-concept-visual-interface/properties.png)

有关在可用的机器学习算法库中导航的帮助，请参阅[算法 & 模块参考概述](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>计算资源

使用工作区中的计算资源来运行管道，并将已部署的模型作为实时终结点或管道终结点进行托管（对于批处理推理）。 支持的计算目标为：

| 计算目标 | 培训 | 部署 |
| ---- |:----:|:----:|
| Azure 机器学习计算 | ✓ | |
| Azure Kubernetes 服务 | | ✓ |

计算目标会附加到机器学习[工作区](concept-workspace.md)。 可在工作区中的[Azure 门户](https://portal.azure.com)或[工作区登陆页面（预览版）](https://ml.azure.com)中管理计算目标。

## <a name="publish"></a>发布

准备好管道后，可以将其发布为 REST 终结点。 可以在不使用构造它的 Python 代码的情况下提交[PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) 。

此外，可以使用 PublishedPipeline 重新提交具有不同 PipelineParameter 值和输入的管道。

## <a name="deployment"></a>部署

预测模型准备就绪后，可直接从视觉对象界面将其部署为管道终结点或实时终结点。

管道终结点是一个 [PublishedPipeline，它可以提交具有不同 PipelineParameter 值的管道运行和用于批处理推理的输入。

实时终结点在应用程序和评分模型之间提供了一个接口。 外部应用程序可与评分模型实时通信。 对实时终结点的调用将预测结果返回到外部应用程序。 若要调用实时终结点，需传递部署终结点时创建的 API 密钥。 终结点基于 REST，这是 web 编程项目的常用体系结构选项。

若要了解如何部署模型，请参阅[教程：使用可视界面部署机器学习模型](ui-tutorial-automobile-price-deploy.md)。

## <a name="next-steps"></a>后续步骤

* 了解有关预测分析和机器学习的基本知识[教程：通过视觉对象界面预测汽车价格](ui-tutorial-automobile-price-train-score.md)
* 使用其中一个示例并进行修改以满足你的需求：

    * [示例 1-回归：预测价格](how-to-ui-sample-regression-predict-automobile-price-basic.md)
    * [示例 2-回归：预测价格和比较算法](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [示例 3-分类：预测信用风险](how-to-ui-sample-classification-predict-credit-risk-basic.md)
    * [示例 4-分类：预测信用风险（区分成本）](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [示例 5-分类：预测变动率、亲和力和向上销售](how-to-ui-sample-classification-predict-churn.md)
    * [示例 6-分类：预测航班延迟](how-to-ui-sample-classification-predict-flight-delay.md)

