---
title: 什么是 ML 管道
titleSuffix: Azure Machine Learning service
description: 本文介绍通过适用于 Python 的 Azure 机器学习 SDK 生成的机器学习管道，以及使用管道的优势。 机器学习 (ML) 管道由数据科学家用来生成、优化和管理其机器学习工作流。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 7d7333a9316e4d39fd550872c3df04024a75d21d
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128333"
---
# <a name="what-are-ml-pipelines-in-azure-machine-learning-service"></a>什么是 ML 管道 Azure 机器学习服务？

了解你可以通过 Azure 机器学习 service 构建和管理的机器学习管道。 

使用机器学习 (ML) 管道，数据科学家、数据工程师和 IT 专业人员可以按以下内容涉及的步骤进行协作：
+ 数据准备，例如规范化和转换
+ 模型定型
+ 模型评估
+ 部署

了解如何[创建你的第一个管道](how-to-create-your-first-pipeline.md)。

![Azure 机器学习服务中的机器学习管道](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>我应该使用哪种 Azure 管道技术？

Azure 云提供多个其他管道, 每个都有不同的用途。 下表列出了不同的管道以及它们的用途:

| 管道 | 作用 | 规范管道 |
| ---- | ---- | ---- |
| Azure 机器学习管道 | 定义可重复使用的机器学习工作流, 可用作机器学习方案的模板。 | 数据 > 模型 |
| [Azure 数据工厂管道](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | 对执行任务所需的数据移动、转换和控制活动进行分组。  | 数据 > 数据 |
| [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | 将你的应用程序持续集成和交付到任何平台/任何云  | 代码 > 应用/服务 |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>为何使用 Azure 机器学习来生成管道？

机器学习管道通过速度、可移植性和重复使用优化工作流, 使你可以专注于专业技能、机器学习, 而不是在基础结构和自动化上。

管道是从多个**步骤**构造的, 这些步骤是管道中的不同计算单元。 每个步骤都可以独立运行并使用独立的计算资源。
独立步骤允许多个数据科学家在同一时间同时处理同一管道, 而不会产生过多的计算资源, 同时还使每个步骤都可以轻松地使用不同的计算类型/大小。

管道设计好以后，通常会有更多围绕着管道的训练循环进行的优化。 重新运行管道时, 运行将跳转到需要重新运行的不同步骤, 如更新的训练脚本, 并跳过未更改的内容。 同一范例适用于未更改的用于执行此步骤的脚本。 如果基础数据未更改, 这种重复使用功能可帮助避免运行成本高昂且耗时较高的步骤, 如数据引入和转换。

使用 Azure 机器学习, 可以对管道中的每个步骤使用各种工具包和框架, 如 PyTorch 或 TensorFlow。 Azure 会在你使用的各种[计算目标](concept-azure-machine-learning-architecture.md)之间进行协调，使中间数据可以轻松地供下游计算目标共享。

可以直接在 Azure 门户中[跟踪管道试验的指标](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments)。 在发布管道之后, 可以配置 REST 终结点, 使你能够从任何平台或堆栈重新运行管道。

## <a name="key-advantages"></a>主要优点

为机器学习工作流使用管道的主要优点是:

|主要优点|描述|
|:-------:|-----------|
|**无人参与&nbsp;运行**|计划以可靠且无人参与的方式并行运行或按顺序运行的步骤。 数据准备和建模可以过去几天或几周, 而管道可用于在进程运行时将精力集中在其他任务上。 |
|**异类计算**|使用跨异类和可缩放计算资源和存储位置可靠协调的多个管道。 通过在不同的计算目标 (例如 HDInsight、GPU 数据科学 Vm 和 Databricks) 上运行单个管道步骤来有效地使用可用计算资源。|
|**可重用性**|为特定方案 (如重新训练和批处理计分) 创建管道模板。 通过简单的 REST 调用从外部系统触发已发布的管道。|
|**跟踪和版本控制**|可以使用管道 SDK 显式对数据源、输入和输出进行命名和版本控制，而不是在循环访问时手动跟踪数据和结果路径。 还可以将脚本和数据分开管理以提高工作效率。|
|**协作**|管道使数据科学家能够在机器学习设计过程的所有方面进行协作, 同时能够同时处理管道步骤。|

## <a name="the-python-sdk-for-pipelines"></a>用于管道的 Python SDK

[使用 PYTHON SDK](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)在首选集成开发环境 (IDE) 或 Jupyter 笔记本中创建 ML 管道。 没有数据依赖项存在时，Azure 机器学习 SDK 提供的命令性构造用于对管道中的步骤进行序列化和并行化操作。 

可以使用声明性数据依赖项来优化任务。 此 SDK 包含用于常见任务（如数据传输和模型发布）的预构建模块框架。 可以通过实现可在管道间重用的自定义步骤, 来扩展框架, 从而为自己的约定建模。 还可以直接通过此 SDK 管理计算目标和存储资源。

将管道另存为模板, 并将其部署到 REST 终结点以进行批处理计分或重新训练作业。

具有 Azure 机器学习的管道的两个 Python 包: [azureml-管线-核心](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)和[azureml-管道-步骤](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)。 若要快速开始使用, 请使用预构建的模块之一, 例如:

* 使用[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep)在步骤中运行 Python 脚本
* 在存储选项与[DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep)之间传输数据
* 使用[AutoMLStep](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlstep)创建 AutoML 管道步骤

## <a name="next-steps"></a>后续步骤

+ 了解如何[创建你的第一个管道](how-to-create-your-first-pipeline.md)。

+ 了解如何[对大型数据运行批预测](how-to-run-batch-predictions.md)。

+ 请参阅[SDK 参考文档](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)中的管道。

+ 试用 Jupyter 笔记本展示[Azure 机器学习管道](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)的示例。 了解如何[运行笔记本来浏览此服务](samples-notebooks.md)。
