---
title: 教程：机器学习入门 - Python
titleSuffix: Azure Machine Learning
description: 在本教程中，你将开始使用在个人开发环境中运行的适用于 Python 的 Azure 机器学习 SDK。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: c0fe3c3808709de732bec8ce0599d380094405e8
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91368475"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>教程：在你的开发环境中开始使用 Azure 机器学习（第 1 部分，共 4 部分）

在这个由四部分组成的教程系列中，你将了解 Azure 机器学习的基础知识，并在 Azure 云平台上完成基于作业的 Python 机器学习任务。 这些任务包括：

1. 设置用于机器学习的工作区和本地开发人员环境。
2. 通过使用适用于 Python 的 Azure 机器学习 SDK 在云中运行代码。
3. 管理用于模型训练的 Python 环境。
4. 将数据上传到 Azure，并在训练中使用该数据。

在本教程系列的第 1 部分中，你将执行以下操作：

> [!div class="checklist"]
> * 安装 Azure 机器学习 SDK。
> * 设置代码的目录结构。
> * 创建 Azure 机器学习工作区。
> * 配置本地开发环境。
> * 设置计算群集。

>[!NOTE]
> 本教程系列重点介绍适用于 Python 基于作业的机器学习任务的 Azure 机器学习概念，这些任务是计算密集型的，并且/或者需要可再现性。 如果你的机器学习任务不符合上述特点，请[对 Azure 机器学习计算实例使用 Jupyter 或 RStudio 功能](tutorial-1st-experiment-sdk-setup.md)，以便开始使用 Azure 机器学习。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 尝试 [Azure 机器学习](https://aka.ms/AMLFree)。
- 熟悉 Python 和[机器学习概念](concept-azure-machine-learning-architecture.md)。 例如，环境、训练，以及评分。
- 本地开发环境：安装了 Python 和常用 IDE（例如，Visual Studio Code、PyCharm 或 Jupyter）的笔记本电脑。

## <a name="install-the-azure-machine-learning-sdk"></a>安装 Azure 机器学习 SDK

本教程从头到尾都将使用适用于 Python 的 Azure 机器学习 SDK。

你可以使用自己最熟悉的工具（例如，Conda 和 pip）来设置要在本教程中使用的环境。 通过 pip 将适用于 Python 的 Azure 机器学习 SDK 安装到该环境中：

```bash
pip install azureml-sdk
```

## <a name="create-a-directory-structure-for-code"></a>创建代码的目录结构
建议为本教程设置以下简单目录结构：

```markdown
tutorial
└──.azureml
```

- `tutorial`：项目的顶级目录。
- `.azureml`：用于存储 Azure 机器学习配置文件的隐藏子目录。

## <a name="create-an-azure-machine-learning-workspace"></a>创建 Azure 机器学习工作区

工作区是 Azure 机器学习的顶级资源，可集中执行以下操作：

- 管理资源（如计算）。
- 存储资产，如笔记本、环境、数据集、管道、模型和终结点。
- 与其他团队成员协作。

在顶级目录 `tutorial` 中，通过使用以下代码来添加名为“`01-create-workspace.py`”的新 Python 文件。 根据自己的需要对参数（名称、订阅 ID、资源组和[位置](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)）进行调整。

代码可以在交互式会话中运行，或者也可以作为 Python 文件运行。

>[!NOTE]
> 如果使用本地开发环境（例如笔记本电脑），在首次运行以下代码时，系统会提示你通过使用设备代码进行工作区身份验证。 按照屏幕上的说明进行操作。

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

运行上述代码片段后，文件夹结构将会类似于：

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

文件 `.azureml/config.json` 包含连接到 Azure 机器学习工作区所需的元数据。 也就是说，其中包含订阅 ID、资源组和工作区名称。 

> [!NOTE]
> `config.json` 的内容不是机密。 可以共享这些详细信息。
>
> 与 Azure 机器学习工作区交互仍需要进行身份验证。

## <a name="create-an-azure-machine-learning-compute-cluster"></a>创建 Azure 机器学习计算群集

在顶级目录 `tutorial` 中创建名为“`02-create-compute.py`”的 Python 脚本。 使用以下代码填充该脚本，以创建 Azure 机器学习计算群集，该群集将会在 0 到 4 个节点之间自动缩放：

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # This automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that the cluster does not exist already
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

运行该 Python 文件：

```bash
python ./02-create-compute.py
```


> [!NOTE]
> 该群集在创建之后将会预配 0 个节点。 因此在你提交作业之前，该群集不会产生成本。 此群集在空闲 2,400 秒（40 分钟）之后将会纵向缩减。

文件夹结构现在将如下所示：

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

## <a name="next-steps"></a>后续步骤

在本设置教程中，你已经：

- 创建了 Azure 机器学习工作区。
- 设置了本地开发环境。
- 创建了 Azure 机器学习计算群集。

下一教程介绍如何将脚本提交到 Azure 机器学习计算群集。

> [!div class="nextstepaction"]
> [教程：在 Azure 上运行“Hello world!”Python 脚本](tutorial-1st-experiment-hello-world.md)
