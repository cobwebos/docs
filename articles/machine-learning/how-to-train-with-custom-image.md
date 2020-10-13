---
title: 使用自定义 Docker 映像训练模型
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习中通过自定义 Docker 映像训练模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 8239d037d6bd68638998cbb36c47c7dac4bce30d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537610"
---
# <a name="train-a-model-using-a-custom-docker-image"></a>使用自定义 Docker 映像训练模型

本文介绍在通过 Azure 机器学习训练模型时如何使用自定义 Docker 映像。 

本文中的示例脚本用于通过创建卷积神经网络来对宠物图像进行分类。 

虽然 Azure 机器学习提供默认的 Docker 基础映像，但你也可以使用 Azure 机器学习环境来指定特定的基础映像，例如系统维护的一组 [Azure ML 基础映像](https://github.com/Azure/AzureML-Containers)中的一个或你自己的[自定义映像](how-to-deploy-custom-docker-image.md#create-a-custom-base-image)。 借助自定义基础映像，你可以在执行训练作业时严密管理依赖项，以及更加严格地控制组件版本。 

## <a name="prerequisites"></a>先决条件 
在以下任一环境中运行此代码：
* Azure 机器学习计算实例 - 无需下载或安装
    * 在开始本教程之前完成[教程：设置环境和工作区](tutorial-1st-experiment-sdk-setup.md)，以创建预先加载了 SDK 和示例存储库的专用笔记本服务器。
    * 在 Azure 机器学习 [示例存储库](https://github.com/Azure/azureml-examples)中，通过导航到以下目录查找已完成的笔记本：操作 **方法-使用情况-azureml > ml 框架 > fastai > 培训-docker** 

* 你自己的 Jupyter 笔记本服务器
    * 创建[工作区配置文件](how-to-configure-environment.md#workspace)。
    * [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)。 
    * 可在 Internet 上访问的 [Azure 容器注册表](/azure/container-registry)或其他 Docker 注册表。

## <a name="set-up-the-experiment"></a>设置试验 
本部分将准备训练实验，包括初始化工作区、创建实验以及上传训练数据和训练脚本。

### <a name="initialize-a-workspace"></a>初始化工作区
[Azure 机器学习工作区](concept-workspace.md)是服务的顶级资源。 它提供了一个集中的位置来处理创建的所有项目。 在 Python SDK 中，可以通过创建 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) 对象来访问工作区项目。

根据在[先决条件部分](#prerequisites)中创建的 `config.json` 文件创建工作区对象。

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>准备脚本
本教程使用[此处](https://github.com/Azure/azureml-examples/blob/main/code/models/fastai/pets-resnet34/train.py)提供的训练脚本 train.py。 实际上，你可以原样接受任何自定义的训练脚本，并使用 Azure 机器学习运行它。

### <a name="define-your-environment"></a>定义环境
创建环境对象并启用 Docker。 

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

下面指定的基本映像支持用于分布式深层学习功能的 fast.ai 库。 有关详细信息，请参阅 [fast.ai DockerHub](https://hub.docker.com/u/fastdotai)。 

使用自定义 Docker 映像时，你可能已经正确设置了 Python 环境。 在这种情况下，请将 `user_managed_dependencies` 标志设置为 True，以便利用自定义映像的内置 python 环境。 默认情况下，Azure ML 会使用你指定的依赖项构建一个 Conda 环境，并会在该环境中执行运行，而不是使用你在基础映像上安装的任何 Python 库。

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

若要使用不在工作区中的专用容器注册表中的映像，必须使用 `docker.base_image_registry` 指定存储库的地址以及用户名和密码：

```python
# Set the container registry information
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

还可以使用自定义 Dockerfile。 如果需要安装非 Python 包作为依赖项，请使用此方法，并记住将基础映像设置为“无”。

```python 
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

有关创建和管理 Azure ML 环境的详细信息，请参阅 [创建 & 使用软件环境](how-to-use-environments.md)。 

### <a name="create-or-attach-existing-amlcompute"></a>创建或附加现有 AmlCompute
你需要创建一个[计算目标](concept-azure-machine-learning-architecture.md#compute-targets)来训练模型。 在本教程中，创建 AmlCompute 作为训练计算资源。

创建 AmlCompute 需要花费大约 5 分钟。 如果工作区中已有使用该名称的 AmlCompute，则此代码会跳过创建流程。

与其他 Azure 服务一样，与 Azure 机器学习服务关联的某些资源（例如 AmlCompute）存在限制。 若要了解默认限制以及申请更多配额的方法，请阅读[此文章](how-to-manage-quotas.md)。 

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current AmlCompute
print(compute_target.get_status().serialize())
```

### <a name="create-a-scriptrunconfig"></a>创建 ScriptRunConfig
此 ScriptRunConfig 会将作业配置为在所需[计算目标](how-to-set-up-training-targets.md)上执行。

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

### <a name="submit-your-run"></a>提交运行
使用 ScriptRunConfig 对象提交训练运行时，submit 方法将返回 ScriptRun 类型的对象。 返回的 ScriptRun 对象使你能够以编程方式访问有关训练运行的信息。 

```python
from azureml.core import Experiment

run = Experiment(ws,'fastai-custom-image').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure 机器学习通过复制整个源目录来运行训练脚本。 如果你有不想上传的敏感数据，请使用 [.ignore 文件](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)或不将其包含在源目录中。 改为使用[数据存储](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true)来访问数据。

## <a name="next-steps"></a>后续步骤
本文介绍了如何使用自定义 Docker 映像来训练模型。 有关 Azure 机器学习的详细信息，请参阅以下其他文章。
* 在训练期间[跟踪运行指标](how-to-track-experiments.md)
* 使用自定义 Docker 映像[部署模型](how-to-deploy-custom-docker-image.md)。
