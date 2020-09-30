---
title: 配置训练运行
titleSuffix: Azure Machine Learning
description: 在各种训练环境（计算目标）上训练机器学习模型。 可以轻松地在训练环境之间切换。 在本地开始训练。 如果需要横向扩展，请切换到基于云的计算目标。
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 2f05ab2bc7e514f3e58f383faf47a74ef69f94b6
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535087"
---
# <a name="configure-and-submit-training-runs"></a>配置并提交定型运行

本文介绍如何配置和提交 Azure 机器学习运行，以对模型进行定型。

训练时，通常会在本地计算机上启动，然后再扩展到基于云的群集。 使用 Azure 机器学习，你可以在各种计算目标上运行脚本，而无需更改训练脚本。

只需在脚本运行配置中为每个计算目标定义环境即可。  然后，当你想要在不同的计算目标上运行训练试验时，可以指定该计算的运行配置。

## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用 [Azure 机器学习的免费版或付费版](https://aka.ms/AMLFree)
* [用于 Python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) ( # B0 = 1.13.0) 
* [Azure 机器学习工作区](how-to-manage-workspace.md) `ws`
* 计算目标 `my_compute_target`。  使用以下工具创建计算目标：
  * [Python SDK](how-to-create-attach-compute-sdk.md) 
  * [Azure 机器学习工作室](how-to-create-attach-compute-studio.md)

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>什么是脚本运行配置？
[ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true)用于配置作为实验的一部分提交定型运行所需的信息。

使用 ScriptRunConfig 对象提交训练实验。  此对象包含：

* **source_directory**：包含训练脚本的源目录
* **脚本**：要运行的训练脚本
* **compute_target**：要在其上运行的计算目标
* **环境**：运行脚本时要使用的环境
* 其他一些可配置选项 (参阅 [参考文档](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) 以了解详细信息) 

## <a name="train-your-model"></a><a id="submit"></a>训练模型

对于所有类型的计算目标，用于提交训练运行的代码模式都是相同的：

1. 创建要运行的试验
1. 创建脚本将在其中运行的环境
1. 创建 ScriptRunConfig，它指定计算目标和环境
1. 提交运行
1. 等待运行完成

或者可以：

* 提交用于[超参数优化](how-to-tune-hyperparameters.md)的 HyperDrive 运行。
* 通过 [VS Code 扩展](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)提交试验。

## <a name="create-an-experiment"></a>创建试验

在工作区中创建试验。

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>选择计算目标

选择要在其中运行训练脚本的计算目标。 如果未在 ScriptRunConfig 中指定任何计算目标，或者如果为 `compute_target='local'` ，则 AZURE ML 将在本地执行脚本。 

本文中的示例代码假设您已 `my_compute_target` 根据 "先决条件" 部分创建了计算目标。

## <a name="create-an-environment"></a>创建环境
Azure 机器学习[环境](concept-environments.md)是（机器学习训练发生于其中的）环境的封装。 它们指定了有关训练和评分脚本的 Python 包、Docker 映像、环境变量和软件设置。 它们还 (Python、Spark 或 Docker) 指定运行时。

可以定义自己的环境，也可以使用 Azure ML 特选环境。 [特选环境](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments#use-a-curated-environment) 是工作区中默认提供的预定义环境。 这些环境由缓存的 Docker 映像支持，降低了运行准备成本。 有关可用特选环境的完整列表，请参阅 [Azure 机器学习特选的环境](https://docs.microsoft.com/azure/machine-learning/resource-curated-environments) 。

对于远程计算目标，可以从使用以下常用特选环境之一开始：

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

有关环境的更多信息和细节，请参阅[在 Azure 机器学习中创建和使用软件环境](how-to-use-environments.md)。
  
### <a name="local-compute-target"></a>本地计算目标

如果计算目标是本地计算机，你需要负责确保所有必需的包在脚本运行于的 Python 环境中可用。  使用 `python.user_managed_dependencies` 来使用当前的 Python 环境（或指定路径上的 Python）。

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>创建脚本运行配置

现在，你已经有了一个计算目标 (`my_compute_target`) 和环境 (`myenv`)，接下来创建一个脚本运行配置，该配置运行位于 `project_folder` 目录中的培训脚本 (`train.py`)：

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='train.py',
                      compute_target=my_compute_target,
                      environment=myenv)
```

如果未指定环境，则将为您创建一个默认环境。

如果要将命令行参数传递给训练脚本，可以通过 **`arguments`** ScriptRunConfig 构造函数的参数指定这些参数，例如 `arguments=['--arg1', arg1_val, '--arg2', arg2_val]` 。

如果要重写默认允许运行的最长时间，可以通过参数执行此操作 **`max_run_duration_seconds`** 。 如果运行时间超过此值，系统将尝试自动取消运行。

### <a name="specify-a-distributed-job-configuration"></a>指定分布式作业配置
如果要运行分布式培训作业，请向参数提供分布式作业特定配置 **`distributed_job_config`** 。 支持的配置类型包括 [MpiConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py&preserve-view=true)、 [TensorflowConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py&preserve-view=true)和 [PyTorchConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?view=azure-ml-py&preserve-view=true)。 

有关运行分布式 Horovod、TensorFlow 和 PyTorch 作业的详细信息和示例，请参阅：

* [训练 TensorFlow 模型](https://docs.microsoft.com/azure/machine-learning/how-to-train-tensorflow#distributed-training)
* [训练 PyTorch 模型](https://docs.microsoft.com/azure/machine-learning/how-to-train-pytorch#distributed-training)

## <a name="submit-the-experiment"></a>提交试验

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> 提交训练运行时，将创建包含训练脚本的目录的快照，并将其发送到计算目标。 目录快照也作为试验的一部分存储在工作区中。 如果更改文件并再次提交运行，只会上传已更改的文件。
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> 有关快照的详细信息，请参阅[快照](concept-azure-machine-learning-architecture.md#snapshots)。

> [!IMPORTANT]
> **特殊文件夹**两个文件夹 *outputs* 和 *logs* 接收 Azure 机器学习的特殊处理。 在训练期间，如果将文件写入相对于根目录（分别为 `./outputs` 和 `./logs`）的名为 outputs 和 logs 的文件夹，则会将这些文件自动上传到运行历史记录，以便在完成运行后对其具有访问权限 。
>
> 要在训练期间创建项目（如模型文件、检查点、数据文件或绘制的图像），请将其写入 `./outputs` 文件夹。
>
> 同样，可以将任何运行训练日志写入 `./logs` 文件夹。 要利用 Azure 机器学习的 [TensorBoard 集成](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb)，请确保将 TensorBoard 日志写入此文件夹。 正在运行时，你将能够启动 TensorBoard 并流式传输这些日志。  稍后，还能够从任何先前运行中还原日志。
>
> 例如，在运行远程训练后将写入 *outputs* 文件夹的文件下载到本地计算机：`run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Git 跟踪与集成

如果以本地 Git 存储库作为源目录开始训练运行，有关存储库的信息将存储在运行历史记录中。 有关详细信息，请参阅 [Azure 机器学习的 Git 集成](concept-train-model-git-integration.md)。

## <a name="notebook-examples"></a>Notebook 示例

有关为各种培训方案配置运行的示例，请参阅以下笔记本：
* [针对各种计算目标的培训](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [ML 框架培训](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

* [教程：训练模型](tutorial-train-models-with-aml.md)使用一个托管计算目标来训练模型。
* 了解如何通过特定 ML 框架（如 [scikit-learn](how-to-train-scikit-learn.md)、 [TensorFlow](how-to-train-tensorflow.md)和 [PyTorch](how-to-train-pytorch.md)）训练模型。
* 若要构建更好的模型，请了解如何[高效地优化超参数](how-to-tune-hyperparameters.md)。
* 训练模型后，了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。
* 查看 [ScriptRunConfig 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) SDK 参考。
* [通过 Azure 虚拟网络使用 Azure 机器学习](how-to-enable-virtual-network.md)
