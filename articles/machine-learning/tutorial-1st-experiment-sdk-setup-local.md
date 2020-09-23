---
title: 教程：机器学习入门 - Python
titleSuffix: Azure Machine Learning
description: 在本教程中，你将开始使用在你的个人开发环境中运行的 Azure 机器学习 Python SDK。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 48edc138e7ab16e712339d6291db52a4a2b36b32
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929249"
---
# <a name="tutorial-get-started-with-azure-machine-learning-on-your-development-environment-part-1-of-4"></a>教程：开始在你的开发环境中使用 Azure 机器学习（第 1 部分，共 4 部分）

在这个**由四个部分组成的教程系列**中，你将了解 Azure 机器学习的基础知识，并在 Azure 云中完成基于作业的 Python ML 任务，包括：

1. 设置工作区和本地机器学习开发人员环境。
2. 使用 Azure 机器学习的 Python SDK 在云中运行代码。
3. 管理用于模型训练的 Python 环境。
4. 将数据上传到 Azure，并在训练中使用该数据。

在**本教程系列的第 1 部分**中，你将执行以下操作：

> [!div class="checklist"]
> * 安装 Azure 机器学习 SDK
> * 设置代码的目录结构
> * 创建 Azure 机器学习工作区
> * 配置本地开发环境
> * 设置计算群集

>[!NOTE]
> 本教程系列重点介绍适用于 Python 基于作业的机器学习任务的 Azure 机器学习概念，这些任务是计算密集型的，且/或需要可再现性。 如果你的机器学习任务不适合此配置文件，请[在 Azure 机器学习计算实例上使用 Jupyter 或 RStudio 功能](tutorial-1st-experiment-sdk-setup.md)，以便加入 Azure 机器学习。

## <a name="prerequisites"></a>先决条件

- 一个 Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用 [Azure 机器学习](https://aka.ms/AMLFree)。
- 熟悉 Python 和[机器学习概念](concept-azure-machine-learning-architecture.md)。 例如，环境、训练、评分等。
- 本地开发环境 - 安装了 Python 和你最喜欢的 IDE（例如：VSCode、Pycharm、Jupyter 等）的笔记本电脑。

## <a name="install-the-azure-machine-learning-sdk"></a>安装 Azure 机器学习 SDK

本教程从头到尾都将使用 Azure 机器学习 Python SDK。

你可以使用你最熟悉的工具（例如：conda、pip 等）来设置要在本教程中使用的环境。 通过 pip 将 Azure 机器学习 Python SDK 安装到环境中：

```bash
pip install azureml-sdk
```

## <a name="create-directory-structure-for-code"></a>创建代码的目录结构
建议为本教程设置以下简单目录：

```markdown
tutorial
└──.azureml
```

- **tutorial**（项目的顶级目录）
- **.azureml**（教程的隐藏子目录）：`.azureml` 目录用于存储 Azure 机器学习配置文件。

## <a name="create-an-azure-machine-learning-workspace"></a>创建 Azure 机器学习工作区

工作区是 Azure 机器学习的顶级资源，可集中执行以下操作：

- 对资源（例如计算）进行管理
- 存储笔记本、环境、数据集、管道、模型、终结点等资产
- 与其他团队成员协作

在顶部父目录 (`tutorial`) 中添加一个新的名为 `01-create-workspace.py` 的 Python 文件，代码如下所示。 根据你的偏好，对参数（名称、订阅 ID、资源组和[位置](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)）进行调整。

可以在交互式会话中运行代码，也可以将其作为 Python 文件运行。

>[!NOTE]
> 如果使用本地开发环境（例如笔记本电脑），则在你第一次执行以下代码时，系统会要求你使用设备代码向工作区进行身份验证。 按照屏幕上的说明进行操作。

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

从 `tutorial` 目录运行此代码：

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

运行上述代码片段后，你的文件夹结构将如下所示：

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

文件 `.azureml/config.json` 包含连接到 Azure 机器学习工作区所需的元数据，即订阅 ID、资源组和工作区名称。 

> [!NOTE]
> `config.json` 的内容不是机密 - 共享这些详细信息没有任何问题。
> 与 Azure 机器学习工作区交互仍需要进行身份验证。

## <a name="create-an-azure-machine-learning-compute-cluster"></a>创建 Azure 机器学习计算群集

在 `tutorial` 顶级目录中创建一个名为 `02-create-compute.py` 的 python 脚本，并使用以下代码填充该脚本，以便创建将在 0 到 4 个节点之间自动缩放的 Azure 机器学习计算群集：

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # this automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=4, 
                                                            idle_seconds_before_scaledown=2400)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

运行 Python 文件：

```bash
python ./02-create-compute.py
```


> [!NOTE]
> 创建群集后，它将预配 0 个节点。 因此，在你提交作业之前，群集不会产生成本。 此群集会在闲置 2400 秒（40 分钟）后纵向缩减。

文件夹结构现在将如下所示：

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

## <a name="next-steps"></a>后续步骤

在本设置教程中，你已：

- 创建了 Azure 机器学习工作区
- 设置本地开发环境
- 创建了 Azure 机器学习计算群集。

下一教程介绍如何将脚本提交到 Azure 机器学习计算群集。

> [!div class="nextstepaction"]
> [教程：在 Azure 上运行“Hello World”Python 脚本](tutorial-1st-experiment-hello-world.md)
