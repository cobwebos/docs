---
title: 生成机器学习管道 - Azure 机器学习服务
description: 本文介绍通过适用于 Python 的 Azure 机器学习 SDK 生成的机器学习管道，以及使用管道的优势。 机器学习 (ML) 管道由数据科学家用来生成、优化和管理其机器学习工作流。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 09/24/2018
ms.openlocfilehash: b450d2ca2af0c0f7598d74e2f07c0acc81b811f3
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116447"
---
# <a name="pipelines-and-azure-machine-learning"></a>管道和 Azure 机器学习

本文介绍通过适用于 Python 的 Azure 机器学习 SDK 生成的机器学习管道，以及使用管道的优势。

## <a name="what-are-machine-learning-pipelines"></a>什么是机器学习管道？

机器学习 (ML) 管道由数据科学家用来生成、优化和管理其机器学习工作流。 常规管道涉及一系列步骤，这些步骤涵盖以下方面：

+ 数据准备，例如规范化和转换
+ 模型训练，例如超参数优化和验证
+ 模型部署和评估  

下图显示了一个示例管道：

[ ![Azure 机器学习服务中的机器学习管道](./media/concept-ml-pipelines/pipelines.png) ] (./media/concept-ml-pipelines/machine-learning-pipelines-big.png#lightbox)

## <a name="why-build-pipelines-with-azure-machine-learning"></a>为何使用 Azure 机器学习来生成管道？

可以使用[适用于 Python 的 Azure 机器学习 SDK](#the-python-sdk-for-pipelines) 来创建 ML 管道并提交和跟踪单个管道运行。

使用管道，可以在简洁性、速度、可移植性和重用性方面优化工作流。 使用 Azure 机器学习来生成管道时，可以重点关注最了解的部分 &mdash; 机器学习 &mdash; 不需关注基础设施。

使用不同的步骤可以在调整和测试工作流时只重新运行所需步骤。 在管道中，一个步骤是一个计算单元。 如上图所示，数据准备任务可能涉及许多步骤，包括但不限于：规范化、转换、验证和特征化。

管道设计好以后，通常会有更多围绕着管道的训练循环进行的优化。 重新运行某个管道时，运行会跳到需要重新运行的步骤（例如更新的训练脚本），跳过尚未更改的。 同一范例适用于未更改的用于执行此步骤的脚本。 

使用 Azure 机器学习时，可以针对管道中的每个步骤使用各种工具包和框架，例如 Microsoft 认知工具包或 TensorFlow。 Azure 会在你使用的各种[计算目标](concept-azure-machine-learning-architecture.md)之间进行协调，使中间数据可以轻松地供下游计算目标共享。 

## <a name="key-advantages"></a>主要优点

为机器学习工作流生成管道的主要优点包括：

|主要优点|Description|
|:-------:|-----------|
|**无人参与&nbsp;运行**|对一些步骤进行计划，使之以可靠且无人参与的方式并行或顺序运行。 由于数据准备和建模可能持续数天或数周，因此你现在可以在管道运行的时候专注于其他任务。 |
|**混合和多样化计算**|使用多个管道，这些管道已跨异类的可缩放计算和存储进行可靠的协调。 可以在不同的计算目标（例如 HDInsight、GPU Data Science VM 和 Databricks）上运行单独的管道步骤，以便有效地利用可用的计算选项。|
|**可重用性**|可以针对具体的方案（例如重新训练和批量评分）将管道模板化。  可以通过简单的 REST 调用从外部系统触发它们。|
|**跟踪和版本控制**|使用管道 SDK 可以显式地对数据源、输入和输出进行命名和版本控制，并可单独管理脚本和数据以提高工作效率，不需在循环访问时手动跟踪数据和结果路径。|

## <a name="the-python-sdk-for-pipelines"></a>用于管道的 Python SDK

使用 Python 来创建 ML 管道。 没有数据依赖项存在时，Azure 机器学习 SDK 提供的命令性构造用于对管道中的步骤进行序列化和并行化操作。 可以在 Jupyter Notebook 或其他首选 IDE 中与之交互。 

可以使用声明性数据依赖项来优化任务。 此 SDK 包含一个框架，其中的预构建模块适用于常规任务，例如数据传输、计算目标的创建和模型发布。 实施那些可以跨管道重用的自定义步骤即可扩展此框架，为你自己的约定建模。

可以将管道另存为模板并部署到某个 REST 终结点，以便对批量评分或重新训练作业进行计划。

请查看[适用于管道的 Python SDK 参考文档](http://aka.ms/aml-sdk)以及下一部分的 Notebook，了解如何生成你自己的。

## <a name="example-notebooks"></a>示例笔记本
 
以下 Notebook 演示与 Azure 机器学习一起使用的管道：[pipeline/pipeline-batch-scoring.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb)。
 
获取此笔记本：
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
