---
title: 管道：优化机器学习工作流
titleSuffix: Azure Machine Learning service
description: 本文介绍通过适用于 Python 的 Azure 机器学习 SDK 生成的机器学习管道，以及使用管道的优势。 机器学习 (ML) 管道由数据科学家用来生成、优化和管理其机器学习工作流。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 11/07/2018
ms.custom: seodec18
ms.openlocfilehash: e8532e9821501c7b10fbb47c531228e439a8b289
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556867"
---
# <a name="build-machine-learning-pipelines-with-the-azure-machine-learning-service"></a>使用 Azure 机器学习服务生成机器学习管道

本文介绍了通过适用于 Python 的 Azure 机器学习 SDK 生成的机器学习管道，以及使用管道的优势。

## <a name="what-are-machine-learning-pipelines"></a>什么是机器学习管道？

使用机器学习 (ML) 管道，数据科学家、数据工程师和 IT 专业人员可以按以下内容涉及的步骤进行协作：
+ 数据准备，例如规范化和转换
+ 模型定型
+ 模型评估
+ 部署 

下图显示了一个示例管道：

![Azure 机器学习服务中的机器学习管道](./media/concept-ml-pipelines/pipelines.png)

## <a name="why-build-pipelines-with-azure-machine-learning"></a>为何使用 Azure 机器学习来生成管道？

可以使用[适用于 Python 的 Azure 机器学习 SDK](#the-python-sdk-for-pipelines) 来创建 ML 管道并提交和跟踪单个管道运行。

使用管道，可以在简洁性、速度、可移植性和重用性方面优化工作流。 使用 Azure 机器学习来生成管道时，你可以将精力集中于专业知识、机器学习，不需要关注基础设施。

使用不同的步骤可以在调整和测试工作流时只重新运行所需步骤。 在管道中，一个步骤是一个计算单元。 如上图中所示，准备数据的任务可能涉及到许多步骤。 这些步骤包括但不限于规范化、转换、验证和特征化。 数据源和中间数据在整个管道中重复使用，从而节省了计算时间和资源。 

管道设计好以后，通常会有更多围绕着管道的训练循环进行的优化。 重新运行某个管道时，运行会跳到需要重新运行的步骤（例如更新的训练脚本），跳过尚未更改的。 同一范例适用于未更改的用于执行此步骤的脚本。 

使用 Azure 机器学习时，可以针对管道中的每个步骤使用各种工具包和框架，例如 Microsoft 认知工具包或 TensorFlow。 Azure 会在你使用的各种[计算目标](concept-azure-machine-learning-architecture.md)之间进行协调，使中间数据可以轻松地供下游计算目标共享。 

可以直接在 Azure 门户中[跟踪管道试验的指标](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments)。 

## <a name="key-advantages"></a>主要优点

为机器学习工作流生成管道的主要优点包括：

|主要优点|Description|
|:-------:|-----------|
|**无人参与&nbsp;运行**|对一些步骤进行计划，使之以可靠且无人参与的方式并行或顺序运行。 由于数据准备和建模可能持续数天或数周，因此你现在可以在管道运行的时候专注于其他任务。 |
|**混合和多样化计算**|使用多个管道，这些管道已跨异类的可缩放计算和存储进行可靠的协调。 可以在不同的计算目标（例如 HDInsight、GPU Data Science VM 和 Databricks）上运行单独的管道步骤。 这可以高效地利用可用的计算选项。|
|**可重用性**|可以针对具体的方案（例如重新训练和批量评分）将管道模板化。 可以通过简单的 REST 调用从外部系统触发它们。|
|**跟踪和版本控制**|可以使用管道 SDK 显式对数据源、输入和输出进行命名和版本控制，而不是在循环访问时手动跟踪数据和结果路径。 还可以将脚本和数据分开管理以提高工作效率。|

## <a name="the-python-sdk-for-pipelines"></a>用于管道的 Python SDK

使用 Python 来创建 ML 管道。 没有数据依赖项存在时，Azure 机器学习 SDK 提供的命令性构造用于对管道中的步骤进行序列化和并行化操作。 可以在 Jupyter Notebook 或其他首选集成开发环境中与之交互。 

可以使用声明性数据依赖项来优化任务。 此 SDK 包含用于常见任务（如数据传输和模型发布）的预构建模块框架。 通过实施可以跨管道重用的自定义步骤，可以扩展此框架来为你自己的约定建模。 还可以直接通过此 SDK 管理计算目标和存储资源。

可以将管道另存为模板并部署到某个 REST 终结点，以便对批量评分或重新训练作业进行计划。

若要了解如何生成自己的实现，请查看[适用于管道的 Python SDK 参考文档](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)以及下一部分中的 Notebook。

## <a name="example-notebooks"></a>示例笔记本
 
以下笔记本演示了 Azure 机器学习中的管道：[how-to-use-azureml/machine-learning-pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)。
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

了解如何[创建你的第一个管道](how-to-create-your-first-pipeline.md)。
