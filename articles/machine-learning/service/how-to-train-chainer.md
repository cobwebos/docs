---
title: 训练和注册链接器模型
titleSuffix: Azure Machine Learning service
description: 本文介绍如何训练和注册使用 Azure 机器学习服务的链接器模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 06/15/2019
ms.openlocfilehash: 5057a8cf6c8769761a1a1a9f513ec46d152289ab
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67488675"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning-service"></a>训练和链接器在规模较大的模型注册到 Azure 机器学习服务

本文介绍如何训练和注册使用 Azure 机器学习服务的链接器模型。 它使用热门[MNIST 数据集](http://yann.lecun.com/exdb/mnist/)分类使用使用构建的深度神经网络 (DNN) 的手写的数字[Chainer Python 库](https://Chainer.org)之上运行[numpy](https://www.numpy.org/)。

链接器是高级神经网络 API，能够在顶部的其他常用的 DNN 框架上运行以简化开发。 使用 Azure 机器学习服务时，您可以快速横向扩展训练作业使用的弹性云计算资源。 您还可以跟踪训练运行、 版本模型部署模型，以及更多。

无论要开发思路加以从链接器模型或到云中，正在将现有模型，Azure 机器学习服务可以帮助你构建生产模型。

如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

## <a name="prerequisites"></a>必备组件

在两种环境上运行此代码：

- Azure 机器学习 Notebook VM-无下载或安装有必要

    - 完成[基于云的笔记本快速入门](quickstart-run-cloud-notebook.md)来使用 SDK 和示例存储库创建的专用的笔记本服务器预加载。
    - Notebook 服务器上的 samples 文件夹中找到的已完成的笔记本和中的文件**how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer**文件夹。  此 notebook 包括扩展的部分，介绍智能超参数优化、 模型部署和笔记本小组件。

- 你自己的 Jupyter 笔记本服务器

    - [安装 Azure 机器学习的 Python SDK](setup-create-workspace.md#sdk)
    - [创建工作区配置文件](setup-create-workspace.md#write-a-configuration-file)
    - 下载示例脚本文件[chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py)
     - 您还可以查找已完成[Jupyter 笔记本版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)GitHub 示例页本指南。 此 notebook 包括扩展的部分，介绍智能超参数优化、 模型部署和笔记本小组件。

## <a name="set-up-the-experiment"></a>设置的实验

本部分会通过加载所需的 python 包、 初始化工作区、 创建试验，和上传的定型数据和训练脚本来设置训练实验。

### <a name="import-packages"></a>导入包

首先，导入 azureml.core Python 库 ad 显示的版本号。

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>初始化工作区

[Azure 机器学习服务工作区](concept-workspace.md)是服务的顶级资源。 它为您提供集中的位置来使用您创建的所有内容。 在 Python SDK 中，您可以通过创建访问工作区项目[ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)对象。

创建工作区对象从`config.json`文件中创建[先决条件部分](#prerequisites)。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>创建项目目录
创建一个目录将包含所有必要的代码从本地计算机，您将需要访问远程资源。 这包括训练脚本和训练脚本依赖于任何其他文件。

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>准备训练脚本

在本教程中，训练脚本**chainer_mnist.py**已为你提供了。 在实践中，您应能够将任何自定义培训脚本和 Azure 机器学习使用它运行而无需修改代码时。

若要使用 Azure 机器学习的跟踪和度量值功能，您将需要添加少量的 Azure 机器学习训练脚本代码。  训练脚本**chainer_mnist.py**演示如何登录到运行在 Azure 机器学习一些指标。 若要执行此操作，你可以访问 Azure 机器学习`Run`脚本中的对象。

将训练脚本复制**chainer_mnist.py**到你的项目目录。

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-an-experiment"></a>创建试验

创建试验和用于保存训练脚本的文件夹。 在此示例中，创建名为"链接器 mnist"的试验。

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>创建或获取计算目标

你将需要[计算目标](concept-compute-target.md)用于训练模型。 在本教程中，将远程训练计算资源来使用托管的 Azure 机器学习计算 (AmlCompute)。

**创建的 AmlCompute 需要大约 5 分钟**。 如果具有该名称 AmlCompute 已在工作区中，此代码将跳过创建过程。  

```Python
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

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

计算目标的详细信息，请参阅[什么是计算目标](concept-compute-target.md)一文。

## <a name="create-a-chainer-estimator"></a>创建链接器评估器

[Chainer 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)提供的启动将训练作业链接器在计算目标上的简单方法。

链接器估算器实现通过通用[ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)类，该类可用于支持任何框架。 训练模型使用的泛型估计器的详细信息，请参阅[与使用估计器的 Azure 机器学习训练模型](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>提交运行

[运行对象](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)运行作业时，完成后提供的运行历史记录的接口。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

执行运行时，它将经历以下阶段：

- **正在准备**:链接器估算器根据创建 docker 映像。 图像上传到工作区的容器注册表并缓存的更高版本运行。 日志还流式传输到运行历史记录，可以查看，以监视进度。

- **缩放**：群集会尝试纵向扩展如果 Batch AI 群集需要更多的节点不是当前可执行运行。

- **Running**：脚本文件夹中的所有脚本都上载到计算目标、 数据存储已装载或复制，并执行 entry_script。 从 stdout 的输出和。 日志文件夹的流式传输到运行历史记录和可用于监视运行。

- **后期处理**：。 / 输出运行的文件夹复制到运行历史记录。

## <a name="save-and-register-the-model"></a>保存并注册模型

一旦已训练模型，您可以保存并将其注册到你的工作区。 模型注册允许您存储和版本来简化工作区中的模型[管理和部署的模型](concept-model-management-and-deployment.md)。

将以下代码添加到训练脚本， **chainer_mnist.py**、 保存模型。 

``` Python
    serializers.save_npz(os.path.join(args.output_dir, 'model.npz'), model)
```

用下面的代码注册到你的工作区模型。

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```



## <a name="next-steps"></a>后续步骤

在本文中，在 Azure 机器学习服务链接器模型定型。 

* 若要了解如何将模型部署，继续到我们[模型部署](how-to-deploy-and-where.md)一文。

* [优化超参数](how-to-tune-hyperparameters.md)

* [在训练期间跟踪运行指标](how-to-track-experiments.md)
