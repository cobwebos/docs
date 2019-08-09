---
title: 管道：优化机器学习工作流
titleSuffix: Azure Machine Learning service
description: 本文介绍通过适用于 Python 的 Azure 机器学习 SDK 生成的机器学习管道，以及使用管道的优势。 数据科学家使用机器学习(ML)管道来生成、优化和管理自己的机器学习工作流。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: a9965dbbca939f566048312af921061a188ee50d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884239"
---
# <a name="build-reusable-ml-pipelines-in-azure-machine-learning-service"></a>在 Azure 机器学习服务中生成可重用的 ML 管道

本文介绍了通过适用于 Python 的 Azure 机器学习 SDK 生成的机器学习管道，以及使用管道的优势。

## <a name="what-are-machine-learning-pipelines"></a>什么是机器学习管道？

使用机器学习 (ML) 管道，数据科学家、数据工程师和 IT 专业人员可以按以下内容涉及的步骤进行协作：
+ 数据准备，例如规范化和转换
+ 模型定型
+ 模型评估
+ 部署

下图显示了一个示例管道进程:

![Azure 机器学习服务中的机器学习管道](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>我应该使用哪种 Azure 管道技术？

Azure 云提供多个其他管道, 每个都有不同的用途。 下表列出了不同的管道以及它们的用途:

| 管道 | 作用 | 规范管道 |
| ---- | ---- | ---- |
| Azure 机器学习管道 | 定义可重复使用的机器学习工作流, 可用作机器学习方案的模板。 | 数据 > 模型 |
| [Azure 数据工厂管道](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | 对执行任务所需的数据移动、转换和控制活动进行分组。  | 数据 > 数据 |
| [Azure 管道](https://azure.microsoft.com/services/devops/pipelines/) | 将你的应用程序持续集成和交付到任何平台/任何云  | 代码 > 应用/服务 |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>为何使用 Azure 机器学习来生成管道？

可以使用[适用于 Python 的 Azure 机器学习 SDK](#the-python-sdk-for-pipelines) 来创建 ML 管道并提交和跟踪单个管道运行。

使用管道，可以在简洁性、速度、可移植性和重用性方面优化工作流。 当通过 Azure 机器学习构建管道时, 可以专注于专业技能、机器学习, 而不是在基础结构和自动化上。

管道是从多个**步骤**构造的, 这些步骤是管道中的不同计算单元。 每个步骤都可以独立运行并使用独立的计算资源。 这允许多个数据科学家在同一时间同时处理同一管道, 而不会产生过多的计算资源, 同时还可以轻松地对每个步骤使用不同的计算类型/大小。

管道设计好以后，通常会有更多围绕着管道的训练循环进行的优化。 重新运行管道时, 运行将跳转到需要重新运行的不同步骤, 如更新的训练脚本, 并跳过未更改的内容。 同一范例适用于未更改的用于执行此步骤的脚本。 如果基础数据未更改, 则此功能可帮助避免运行成本高昂且耗时较高的步骤, 如数据引入和转换。

使用 Azure 机器学习, 可以对管道中的每个步骤使用各种工具包和框架, 如 PyTorch 或 TensorFlow。 Azure 会在你使用的各种[计算目标](concept-azure-machine-learning-architecture.md)之间进行协调，使中间数据可以轻松地供下游计算目标共享。

可以直接在 Azure 门户中[跟踪管道试验的指标](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments)。 在发布管道之后, 可以配置 REST 终结点, 以便从任何平台或堆栈重新运行管道。

## <a name="key-advantages"></a>主要优点

为机器学习工作流使用管道的主要优点是:

|主要优点|描述|
|:-------:|-----------|
|**无人参与&nbsp;运行**|计划以可靠且无人参与的方式并行运行或按顺序运行的步骤。 数据准备和建模可以过去几天或几周, 而管道可用于在进程运行时将精力集中在其他任务上。 |
|**异类计算**|使用跨异类和可缩放计算资源和存储位置可靠协调的多个管道。 在不同的计算目标 (例如 HDInsight、GPU 数据科学 Vm 和 Databricks) 上运行单个管道步骤。 这可以高效地利用可用的计算选项。|
|**可重用性**|为特定方案 (如重新训练和批处理计分) 创建管道模板。 通过简单的 REST 调用从外部系统触发已发布的管道。|
|**跟踪和版本控制**|可以使用管道 SDK 显式对数据源、输入和输出进行命名和版本控制，而不是在循环访问时手动跟踪数据和结果路径。 还可以将脚本和数据分开管理以提高工作效率。|
|**协作**|管道使数据科学家能够在机器学习设计过程的所有方面进行协作, 同时能够同时处理管道步骤。|

## <a name="the-python-sdk-for-pipelines"></a>用于管道的 Python SDK

使用 Python 来创建 ML 管道。 没有数据依赖项存在时，Azure 机器学习 SDK 提供的命令性构造用于对管道中的步骤进行序列化和并行化操作。 可以在 Jupyter 笔记本或其他首选 IDE 中与它交互。

可以使用声明性数据依赖项来优化任务。 此 SDK 包含用于常见任务（如数据传输和模型发布）的预构建模块框架。 通过实施可以跨管道重用的自定义步骤，可以扩展此框架来为你自己的约定建模。 还可以直接通过此 SDK 管理计算目标和存储资源。

可以将管道另存为模板并部署到某个 REST 终结点，以便对批量评分或重新训练作业进行计划。

若要了解如何生成自己的实现，请查看[适用于管道的 Python SDK 参考文档](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)以及下一部分中的 Notebook。

## <a name="example-notebooks"></a>示例笔记本

以下笔记本演示了 Azure 机器学习中的管道：[how-to-use-azureml/machine-learning-pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

了解如何[创建你的第一个管道](how-to-create-your-first-pipeline.md)。
