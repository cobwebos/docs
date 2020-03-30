---
title: 训练深度学习 Chainer 模型
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习 Chainer 估算器类在企业范围内运行 PyTorch 训练脚本。  示例脚本使用基于 numpy 运行的 Chainer Python 库来对手写数字图像进行分类，以构建深度学习神经网络。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: e2840a6295140e0dc22a032fa844c0488403c5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75536611"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>使用 Azure 机器学习大规模训练并注册 Chainer 模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，你将了解如何使用 Azure 机器学习 [Chainer 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)类在企业范围内运行 [Chainer](https://chainer.org/) 训练脚本。 本文中的示例训练脚本使用热门的 [MNIST 数据集](http://yann.lecun.com/exdb/mnist/)对手写数字进行分类，它借助了使用基于 [numpy](https://www.numpy.org/) 运行的 Chainer Python 库构建的深度神经网络 (DNN)。

无论是从头开始训练深度学习 Chainer 模型，还是将现有模型引入云中，都可以通过 Azure 机器学习使用弹性云计算资源来横向扩展开源训练作业。 你可以通过 Azure 机器学习来构建、部署和监视生产级模型以及对其进行版本控制。 

详细了解[深度学习与机器学习](concept-deep-learning-vs-machine-learning.md)。

如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

## <a name="prerequisites"></a>先决条件

在以下任一环境中运行此代码：

- Azure 机器学习计算实例 - 无需下载或安装

    - 完成[教程：设置环境和工作区](tutorial-1st-experiment-sdk-setup.md)，以创建预加载 SDK 和示例存储库的专用笔记本服务器。
    - 在笔记本服务器上的示例深度学习文件夹中，在 **how-to-use-azureml > ml-frameworks > chainer > deployment > train-hyperparameter-tune-deploy-with-chainer** 文件夹中找到已完成的笔记本和文件。  该笔记本包含扩展部分，其中涵盖智能超参数优化、模型部署和笔记本小组件。

- 你自己的 Jupyter 笔记本服务器

    - [安装 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [创建工作区配置文件](how-to-configure-environment.md#workspace)。
    - 下载示例脚本文件 [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer)。
     - 此外，还可以在 GitHub 示例页上查找已完成的本指南的 [Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)。 该笔记本包含扩展部分，其中涵盖智能超参数优化、模型部署和笔记本小组件。

## <a name="set-up-the-experiment"></a>设置试验

本部分将准备训练实验，包括加载所需 python 包、初始化工作区、创建实验以及上传训练数据和训练脚本。

### <a name="import-packages"></a>导入包

首先，导入 azureml.core Python 库并显示版本号。

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>初始化工作区

[Azure 机器学习工作区](concept-workspace.md)是服务的顶级资源。 它提供了一个集中的位置来处理创建的所有项目。 在 Python SDK 中，您可以通过创建[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)对象来访问工作区项目。

通过读取在[先决条件部分](#prerequisites)中创建的 `config.json` 文件创建工作区对象：

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>创建项目目录
创建一个目录，该目录将用于包含本地计算机中您在远程资源上需要访问的所有必要代码。 这包括训练脚本以及您的训练脚本所依赖的任何其他文件。

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>准备训练脚本

在本教程中，已为你提供了训练脚本 **chainer_mnist.py**。 实际上，你应该能够原样获取任何自定义训练脚本，并使用 Azure ML 运行它，而无需修改你的代码。

若要使用 Azure ML 跟踪和指标功能，请在你的训练脚本内添加少量 Azure ML 代码。  训练脚本 **chainer_mnist.py** 展示了如何在脚本中使用 `Run` 对象将一些指标记录到 Azure ML 运行中。

提供的训练脚本使用了来自 `datasets.mnist.get_mnist` 函数的示例数据。  对于你自己的数据，你可能需要使用[上传数据集和脚本](how-to-train-keras.md#data-upload)之类的步骤来使数据在训练期间可用。

将训练脚本 **chainer_mnist.py** 复制到你的项目目录中。

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>创建深度学习试验

创建试验。 在此示例中，创建一个名为“chainer-mnist”的试验。

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>创建或获取计算目标

你需要一个[计算目标](concept-compute-target.md)来训练你的模型。 在此示例中，你将 Azure ML 托管计算 (AmlCompute) 用于你的远程训练计算资源。

**创建 Aml 计算大约需要 5 分钟**。 如果你的工作区中已有使用该名称的 AmlCompute，则此代码会跳过创建流程。  

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

有关计算目标的详细信息，请参阅[什么是计算目标](concept-compute-target.md)一文。

## <a name="create-a-chainer-estimator"></a>创建 Chainer 估算器

[Chainer 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)提供了一种在计算目标上启动 Chainer 训练作业的简单方法。

链式估计器通过泛型[`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)类实现，该类可用于支持任何框架。 有关使用泛型估算器训练模型的详细信息，请参阅[通过估算器使用 Azure 机器学习训练模型](how-to-train-ml-models.md)

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

[运行对象](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)在作业运行时和运行后提供运行历史记录的接口。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

执行运行时，会经历以下阶段：

- **准备**： 根据链子估计器创建 docker 映像。 将映像上传到工作区的容器注册表，缓存以用于后续运行。 还会将日志流式传输到运行历史记录，可以查看日志以监视进度。

- **缩放**：如果 Batch AI 群集需要比当前可用的节点更多的节点来执行运行，群集将尝试向上扩展。

- **正在运行**：脚本文件夹中的所有脚本都将上载到计算目标，将装载或复制数据存储，并执行entry_script。 将 stdout 和 ./logs 文件夹中的输出流式传输到运行历史记录，可将其用于监视运行。

- **后处理**：运行的 ./输出文件夹将复制到运行历史记录。

## <a name="save-and-register-the-model"></a>保存并注册模型

训练模型后，可以将其保存并注册到工作区。 凭借模型注册，可以在工作区中存储模型并对其进行版本控制，从而简化[模型管理和部署](concept-model-management-and-deployment.md)。


在模型训练完成后，使用以下代码将模型注册到你的工作区。  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> 无论使用哪种估计器进行训练，刚注册的模型的部署方式都与 Azure 机器学习中其他任何已注册模型完全相同。 部署指南包含有关模型注册的部分，但由于你已有一个已注册的模型，因而可以直接跳到[创建计算目标](how-to-deploy-and-where.md#choose-a-compute-target)进行部署。

你还可以下载模型的本地副本。 这对于在本地执行其他模型验证工作非常有用。 在训练脚本 `chainer_mnist.py` 中，一个保护程序对象将模型保存到本地文件夹（计算目标的本地）。 可以使用“运行”对象从数据存储下载副本。

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

在本文中，你使用 Azure 机器学习中的 Chainer 训练并注册了一个深度学习神经网络。 要了解如何部署模型，请继续执行[模型部署](how-to-deploy-and-where.md)文章。

* [优化超参数](how-to-tune-hyperparameters.md)

* [在训练期间跟踪运行指标](how-to-track-experiments.md)

* [查看 Azure 中分布式深度学习训练的参考体系结构](/azure/architecture/reference-architectures/ai/training-deep-learning)
