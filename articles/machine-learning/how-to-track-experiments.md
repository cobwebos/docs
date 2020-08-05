---
title: 记录 ML 试验和指标
titleSuffix: Azure Machine Learning
description: 监视 Azure ML 试验和运行指标，以便改进模型创建过程。 使用 run .log、start_logging 或 ScriptRunConfig 将日志记录添加到定型脚本。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: d0c6488f9a75bbf9ba6775138edeed9c4a397abf
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552214"
---
# <a name="enable-logging-in-azure-ml-training-runs"></a>在 Azure ML 定型运行中启用日志记录
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 机器学习 Python SDK 允许使用默认的 Python 日志记录包和 SDK 特定功能记录实时信息。 你可以在本地记录并将日志发送到门户中的工作区。

日志可帮助你诊断错误和警告，或跟踪参数和模型性能等性能指标。 本文介绍如何在以下情况下启用日志记录：

> [!div class="checklist"]
> * 交互式培训会话
> * 使用 ScriptRunConfig 提交培训作业
> * Python 本机 `logging` 设置
> * 来自其他源的日志记录


> [!TIP]
> 本文说明如何监视模型训练过程。 如果你有兴趣监视 Azure 机器学习中的资源使用情况和事件，如配额、已完成的培训运行或已完成的模型部署，请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。

## <a name="data-types"></a>数据类型

可以记录多个数据类型，包括标量值、列表、表、图像、目录等。 有关不同数据类型的详细信息和 Python 代码示例，请参阅[运行类引用页](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)。

## <a name="interactive-logging-session"></a>交互式日志记录会话

交互式日志记录会话通常用于笔记本环境。 方法进行[试验。 start_logging ( # B1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-)开始交互式日志记录会话。 在会话期间记录的所有指标都将添加到试验中的运行记录。 方法[运行。 complete ( # B1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-)结束会话并将运行标记为 "已完成"。

下面的代码片段使用交互式日志记录会话，通过[运行 ( # B1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#log-name--value--description----)方法记录定型参数和性能指标。 它还将定型模型上载到指定的输出位置。

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=create_experiment)]

有关使用交互式日志记录的完整示例笔记本，请参阅[在笔记本中训练模型](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)。

## <a name="scriptrunconfig-logs"></a>ScriptRunConfig 日志

本部分介绍如何在 ScriptConfig 运行中添加日志记录代码。 可以使用[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py)类封装用于可重复运行的脚本和环境。 你还可以使用此选项来显示要监视的 visual Jupyter 笔记本小组件。

此示例对 alpha 值执行参数扫描，并使用[run .log ( # B1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#log-name--value--description----)方法来捕获结果。

1. 创建包括日志记录逻辑的训练脚本 `train.py` 。

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. 提交 ```train.py``` 要在用户管理环境中运行的脚本。 提交整个脚本文件夹以进行定型。

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    `show_output`参数可启用详细日志记录，通过该日志记录，您可以查看来自定型过程的详细信息以及有关任何远程资源或计算目标的信息。 提交试验时，使用以下代码启用详细日志记录。

```python
run = exp.submit(src, show_output=True)
```

还可以在生成的运行上的 `wait_for_completion` 函数中使用相同的参数。

```python
run.wait_for_completion(show_output=True)
```

有关使用 ScriptRunConfigs 日志的完整示例笔记本，请参阅在[本地定型模型](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local/train-on-local.ipynb)。

## <a name="native-python-logging"></a>本机 Python 日志记录

SDK 中的某些日志可能包含错误，指示你将日志记录级别设置为 "调试"。 若要设置日志记录级别，请在脚本中添加以下代码。

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>其他日志记录源

Azure 机器学习还可以在训练期间记录来自其他源的信息，例如自动机器学习运行或运行作业的 Docker 容器。 未记录这些日志，但如果遇到问题，请与 Microsoft 支持部门联系，他们可以在故障排除过程中使用这些日志。

有关 Azure 机器学习设计器中的日志记录度量值的信息 (预览版) ，请参阅[如何在设计器中记录度量值 (预览版) ](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>示例笔记本
下面的笔记本展示了本文中的概念：
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解有关如何使用 Azure 机器学习的详细信息：

* 了解如何[在 Azure 机器学习设计器中记录指标 (预览版) ](how-to-track-designer-experiments.md)。

* 查看教程[使用 Azure 机器学习训练图像分类模型](tutorial-train-models-with-aml.md)中的示例，了解如何注册和部署最佳模型。
