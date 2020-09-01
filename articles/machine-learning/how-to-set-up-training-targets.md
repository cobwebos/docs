---
title: 向计算目标提交定型运行
titleSuffix: Azure Machine Learning
description: 在各种培训环境上训练机器学习模型， (计算目标) 。 可以轻松地在训练环境之间切换。 在本地开始训练。 如果需要横向扩展，请切换到基于云的计算目标。
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: ca76ec5bef1d908ca3cea6ce0f58b1205c1676ca
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144084"
---
# <a name="submit-a-training-run-to-a-compute-target"></a>向计算目标提交定型运行

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何使用各种培训环境 ([计算目标](concept-compute-target.md)) 来训练机器学习模型。

训练时，通常会在本地计算机上开始，然后在不同的计算目标上运行该训练脚本。 利用 Azure 机器学习，你可以在不同的计算目标上运行脚本，而无需更改定型脚本。

只需为 **脚本运行配置**中的每个计算目标定义环境。  然后，当你想要在不同的计算目标上运行训练试验时，可以指定该计算的运行配置。

## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用 [Azure 机器学习的免费版或付费版](https://aka.ms/AMLFree)
* [适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Azure 机器学习工作区](how-to-manage-workspace.md)，`ws`
* 计算目标， `my_compute_target` 。  使用以下内容创建计算目标：
  * [Python SDK](how-to-create-attach-compute-sdk.md) 
  * [Azure 机器学习工作室](how-to-create-attach-compute-studio.md)

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>脚本运行配置是什么？

您可以使用 [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) 对象提交训练试验。  此对象包含：

* **source_directory**：包含训练脚本的源目录
* **script**：标识训练脚本
* **run_config**： [运行配置](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)，该配置又定义了定型发生的位置。 在中， `run_config` 指定运行训练脚本时要使用的计算目标和环境。  

## <a name="whats-an-environment"></a>什么是环境？

Azure 机器学习 [环境](concept-environments.md) 是计算机学习定型发生环境的封装。 此类学习环境指定了与训练和评分脚本有关的 Python 包、环境变量和软件设置。 它们还指定运行时（Python、Spark 或 Docker）。  

环境是在中的对象中指定的  `run_config` `ScriptRunConfig` 。

## <a name="train-your-model"></a><a id="submit"></a>训练模型

对于所有类型的计算目标，用于提交训练运行的代码模式都是相同的：

1. 创建要运行的试验
1. 创建脚本将在其中运行的环境
1. 创建一个引用计算目标和环境的脚本运行配置
1. 提交运行
1. 等待运行完成

或者可以：

* 根据[使用评估器训练机器学习模型](how-to-train-ml-models.md)中所述，使用 `Estimator` 对象提交试验。
* 提交用于[超参数优化](how-to-tune-hyperparameters.md)的 HyperDrive 运行。
* 通过 [VS Code 扩展](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)提交试验。

## <a name="create-an-experiment"></a>创建试验

在工作区中创建试验。

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'

experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="create-an-environment"></a>创建环境

特选环境包含 Python 包的集合，默认情况下可以在你的工作区中使用。 这些环境由缓存的 Docker 映像支持，降低了运行准备成本。 对于远程计算目标，可以使用以下常用特选环境之一：

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
my_environment = Environment.get(workspace=ws, name="AzureML-Minimal")
```

有关环境的详细信息和详细信息，请参阅 [Create & use software 环境 in Azure 机器学习](how-to-use-environments.md)。
  
### <a name="local-compute-target"></a>本地计算目标

如果计算目标是 **本地计算机**，则需负责确保在运行脚本的 Python 环境中提供所有必需的包。  使用 `python.user_managed_dependencies`) 指定的路径上的当前 Python 环境 (或 python。

```python
from azureml.core import Environment

# Editing a run configuration property on-fly.
my_environment = Environment("user-managed-env")

my_environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#my_environment.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-script-run-configuration"></a>创建脚本运行配置

现在，你已经有了计算目标 (`compute_target`) 和环境 (`my_environment`) ，请创建一个脚本运行配置，用于运行你的培训脚本 (`train.py`) 位于你的 `project_folder` 目录中：

```python
from azureml.core import ScriptRunConfig

script_run_config = ScriptRunConfig(source_directory=project_folder, script='train.py')

# Set compute target
script_run_config.run_config.target = my_compute_target

# Set environment.   If you don't do this, a default environment will be created.
script_run_config.run_config.environment = my_environment
```

你可能还需要为运行设置框架。

* 对于 HDI 群集：
    ```python
    src.run_config.framework = "pyspark"
    ```

* 对于远程虚拟机：
    ```python
    src.run_config.framework = "python"
    ```

## <a name="submit-the-experiment"></a>提交试验

```python
run = experiment.submit(config=script_run_config)
```

> [!IMPORTANT]
> 提交训练运行时，将创建包含训练脚本的目录的快照，并将其发送到计算目标。 目录快照也作为试验的一部分存储在工作区中。 如果更改文件并再次提交运行，只会上传已更改的文件。
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> 有关快照的详细信息，请参阅 [快照](concept-azure-machine-learning-architecture.md#snapshots)。


<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Git 跟踪和集成

如果以本地 Git 存储库作为源目录开始训练运行，有关存储库的信息将存储在运行历史记录中。 有关详细信息，请参阅 [Azure 机器学习的 Git 集成](concept-train-model-git-integration.md)。

## <a name="notebook-examples"></a>Notebook 示例

请参阅以下 Notebook，其中提供了有关使用各种计算目标进行训练的示例：
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

* [教程：训练模型](tutorial-train-models-with-aml.md)使用一个托管计算目标来训练模型。
* 若要构建更好的模型，请了解如何[高效地优化超参数](how-to-tune-hyperparameters.md)。
* 训练模型后，了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。
* 查看 [RunConfiguration 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) SDK 参考。
* [通过 Azure 虚拟网络使用 Azure 机器学习](how-to-enable-virtual-network.md)
