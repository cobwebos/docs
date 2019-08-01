---
title: 训练和注册 Chainer 模型
titleSuffix: Azure Machine Learning service
description: 本文介绍如何使用 Azure 机器学习服务训练和注册 Chainer 模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 06/15/2019
ms.openlocfilehash: 7cf5650708cd951e872e3df6ea533a62bde0389d
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68618340"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning-service"></a>Azure 机器学习服务, 大规模定型和注册 Chainer 模型

本文介绍如何使用 Azure 机器学习服务训练和注册 Chainer 模型。 它使用常用的[MNIST 数据集](http://yann.lecun.com/exdb/mnist/), 通过使用在[numpy](https://www.numpy.org/)顶部运行的[Chainer Python 库](https://Chainer.org)生成的深度神经网络 (DNN) 对手写数字进行分类。

Chainer 是一种高级神经网络 API, 能够在其他热门 DNN 框架之上运行以简化开发。 使用 Azure 机器学习 service, 你可以使用弹性云计算资源快速横向扩展培训作业。 还可以跟踪定型运行、版本模型、部署模型, 等等。

无论是从头开始开发 Chainer 模型, 还是将现有模型引入云中, Azure 机器学习服务都可以帮助您构建生产就绪模型。

如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

## <a name="prerequisites"></a>先决条件

在以下任一环境中运行此代码:

- Azure 机器学习笔记本 VM-无需下载或安装

    - 完成[基于云的笔记本快速入门](quickstart-run-cloud-notebook.md), 创建随 SDK 和示例存储库预先加载的专用笔记本服务器。
    - 在笔记本服务器上的 "示例" 文件夹中, 在 "操作**方法"/"使用深度学习/超参数-chainer** " 文件夹中查找已完成的笔记本和文件。  此笔记本包含扩展的部分, 涵盖智能超参数优化、模型部署和笔记本小组件。

- 你自己的 Jupyter 笔记本服务器

    - [安装适用于 Python 的 Azure 机器学习 SDK](setup-create-workspace.md#sdk)
    - [创建工作区配置文件](setup-create-workspace.md#write-a-configuration-file)
    - 下载示例脚本文件[chainer_mnist. py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py)
     - 你还可以在 GitHub 示例页上找到本指南的完整[Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)。 此笔记本包含扩展的部分, 涵盖智能超参数优化、模型部署和笔记本小组件。

## <a name="set-up-the-experiment"></a>设置试验

本部分通过加载所需的 python 包、初始化工作区、创建试验以及上传定型数据和训练脚本来设置训练实验。

### <a name="import-packages"></a>导入包

首先, 导入 azureml 的 Python 库并显示版本号。

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>初始化工作区

" [Azure 机器学习服务" 工作区](concept-workspace.md)是服务的顶级资源。 它为您提供了一个集中的位置来处理您创建的所有项目。 在 Python SDK 中, 可以通过创建[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)对象来访问工作区项目。

通过读取在[先决条件部分](#prerequisites)中创建`config.json`的文件来创建工作区对象:

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>创建项目目录
创建一个目录, 该目录包含本地计算机中需要远程资源访问的所有必要代码。 这包括训练脚本以及定型脚本所依赖的任何其他文件。

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>准备训练脚本

在本教程中, 已为你提供训练脚本**chainer_mnist。** 在实践中, 您应该能够按原样获取任何自定义训练脚本, 并使用 Azure ML 运行它, 而无需修改您的代码。

若要使用 Azure ML 的跟踪和指标功能, 请在训练脚本中添加少量的 Azure ML 代码。  训练脚本**chainer_mnist。 py**演示如何使用脚本中的`Run`对象将一些指标记录到 Azure ML 运行中。

提供的训练脚本使用 chainer `datasets.mnist.get_mnist`函数中的示例数据。  对于您自己的数据, 您可能需要使用 "[上传数据集" 和 "脚本](how-to-train-keras.md#upload-dataset-and-scripts)" 等步骤, 使数据在训练过程中可用。

将训练脚本**chainer_mnist**复制到项目目录中。

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-an-experiment"></a>创建试验

创建试验。 在此示例中, 创建一个名为 "chainer-mnist" 的试验。

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>创建或获取计算目标

需要使用[计算目标](concept-compute-target.md)来定型模型。 在此示例中, 为远程定型计算资源使用 Azure ML 托管计算 (AmlCompute)。

**AmlCompute 的创建时间大约为5分钟**。 如果工作区中已存在具有该名称的 AmlCompute, 则此代码将跳过创建进程。  

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

有关计算目标的详细信息, 请参阅[什么是计算目标一](concept-compute-target.md)文。

## <a name="create-a-chainer-estimator"></a>创建 Chainer 估计器

[Chainer 估计器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)提供了一种简单的方法, 可在计算目标上启动 Chainer 培训作业。

Chainer 估计器是通过泛型[`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)类实现的, 它可用于支持任何框架。 有关使用泛型估计器定型模型的详细信息, 请参阅[使用估计器 Azure 机器学习训练模型](how-to-train-ml-models.md)

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

[运行对象](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)在作业运行和完成后, 为运行历史记录提供接口。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

在执行运行时, 它将经历以下几个阶段:

- **准备**:根据 Chainer 估计器创建 docker 映像。 该映像将上传到工作区的容器注册表中, 并进行缓存以供稍后运行。 还会将日志流式传输到运行历史记录, 并可以查看日志来监视进度。

- **缩放**：如果 Batch AI 群集需要的节点数多于当前可用的节点数, 则群集将尝试增加。

- **Running**：脚本文件夹中的所有脚本都将上载到计算目标, 装载或复制数据存储, 并执行 entry_script。 输出从 stdout 开始,/logs 文件夹将流式传输到运行历史记录, 并可用于监视运行情况。

- **后期处理**：运行的/outputs 文件夹将复制到运行历史记录中。

## <a name="save-and-register-the-model"></a>保存并注册模型

对模型进行定型后, 可以将其保存并注册到工作区。 利用模型注册, 可以在工作区中存储模型并对模型进行版本管理, 从而简化[模型管理和部署](concept-model-management-and-deployment.md)。


完成模型定型后, 将模型注册到工作区, 并在其中包含以下代码。  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> 如果收到错误消息, 指出找不到该模型, 请稍等片刻, 然后重试。  有时训练运行结束与输出目录中模型的可用性之间略有延迟。

您还可以下载模型的本地副本。 这对于在本地执行其他模型验证工作非常有用。 在训练脚本`chainer_mnist.py`中, "保护程序" 对象将模型保留到本地文件夹 (计算目标的本地)。 可以使用 "运行" 对象从数据存储下载副本。

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure 机器学习服务上训练 Chainer 模型。 

* 若要了解如何部署模型, 请继续学习我们的[模型部署](how-to-deploy-and-where.md)一文。

* [优化超参数](how-to-tune-hyperparameters.md)

* [在训练期间跟踪运行指标](how-to-track-experiments.md)
