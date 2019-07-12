---
title: 训练和注册 PyTorch 模型
titleSuffix: Azure Machine Learning service
description: 本文介绍如何训练和注册使用 Azure 机器学习服务的 PyTorch 模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 06/18/2019
ms.custom: seodec18
ms.openlocfilehash: d9c953eeecedf14a8f3fae43c5d4713252d58b4c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840082"
---
# <a name="train-and-register-pytorch-models-at-scale-with-azure-machine-learning-service"></a>训练和大规模 PyTorch 模型注册到 Azure 机器学习服务

本文介绍如何训练和注册使用 Azure 机器学习服务的 PyTorch 模型。 它基于[PyTorch 的传输学习教程](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)生成深度神经网络 (DNN) 分类器 chickens 和 turkeys 的图像。

[PyTorch](https://pytorch.org/)是一种开放源代码计算框架通常用于创建深度神经网络 (DNN)。 使用 Azure 机器学习服务时，你可以快速横向扩展使用灵活的云计算资源的开放源代码培训作业。 您还可以跟踪训练运行、 版本模型部署模型，以及更多。

无论要开发思路加以的 PyTorch 模型还是正在将现有模型到云中，Azure 机器学习服务可以帮助你生成生产的模型。

## <a name="prerequisites"></a>先决条件

在两种环境上运行此代码：

 - Azure 机器学习 Notebook VM-无下载或安装有必要

    - 完成[基于云的笔记本快速入门](quickstart-run-cloud-notebook.md)来使用 SDK 和示例存储库创建的专用的笔记本服务器预加载。
    - 在 notebook 服务器上的示例文件夹中，通过导航到此目录查找已完成和展开 notebook:**说明-到-使用-azureml > 培训使用深度学习 > train-hyperparameter-tune-deploy-with-pytorch**文件夹。 
 
 - 你自己的 Jupyter 笔记本服务器

    - [安装 Azure 机器学习的 Python SDK](setup-create-workspace.md#sdk)
    - [创建工作区配置文件](setup-create-workspace.md#write-a-configuration-file)
    - [下载示例脚本文件](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    您还可以查找已完成[Jupyter 笔记本版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb)本指南的 GitHub 示例页。 此 notebook 包括扩展的部分，介绍智能超参数优化、 模型部署和笔记本小组件。

## <a name="set-up-the-experiment"></a>设置的实验

本部分会通过加载所需的 python 包、 初始化工作区、 创建试验，和上传的定型数据和训练脚本来设置训练实验。

### <a name="import-packages"></a>导入包

首先，导入所需的 Python 库。

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>初始化工作区

[Azure 机器学习服务工作区](concept-workspace.md)是服务的顶级资源。 它为您提供集中的位置来使用您创建的所有内容。 在 Python SDK 中，您可以通过创建访问工作区项目[ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)对象。

创建工作区对象从`config.json`文件中创建[先决条件部分](#prerequisites)。

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>创建试验

创建试验和用于保存训练脚本的文件夹。 在此示例中，创建名为"pytorch 鸟"的试验。

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>获取数据

此数据集包含 120 个训练图像每个 turkeys 和 chickens，与每个类的 100 个验证映像。 我们将下载并提取数据集作为一部分我们训练脚本`pytorch_train.py`。 映像是的子集[打开的图像 v5 数据集](https://storage.googleapis.com/openimages/web/index.html)。

### <a name="prepare-training-scripts"></a>准备训练脚本

在本教程中，训练脚本， `pytorch_train.py`，已提供。 在实践中，您可以采用任何自定义培训脚本，原样，并使用 Azure 机器学习服务运行。

上传 Pytorch 训练脚本， `pytorch_train.py`。

```Python
shutil.copy('pytorch_train.py', project_folder)
```

但是，如果你想要使用 Azure 机器学习服务跟踪和度量值功能，您将必须添加训练脚本内的少量代码。 跟踪的指标的示例可在`pytorch_train.py`。

## <a name="create-a-compute-target"></a>创建计算目标

创建计算目标为 PyTorch 作业上运行。 在此示例中，创建启用了 GPU 的 Azure 机器学习计算群集。

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

计算目标的详细信息，请参阅[什么是计算目标](concept-compute-target.md)一文。

## <a name="create-a-pytorch-estimator"></a>创建 PyTorch 估算器

[PyTorch 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)提供简单的方法启动的计算目标上的 PyTorch 培训作业。

PyTorch 估算器实现通过通用[ `estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)类，该类可用于支持任何框架。 训练模型使用的泛型估计器的详细信息，请参阅[与使用估计器的 Azure 机器学习训练模型](how-to-train-ml-models.md)

如果其他 pip 或 conda 包在运行，需要训练脚本，您可以通过传递通过其名称的结果 docker 映像上安装的程序包`pip_packages`和`conda_packages`参数。

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

## <a name="submit-a-run"></a>提交运行

[运行对象](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)运行作业时，完成后提供的运行历史记录的接口。

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

执行运行时，它将经历以下阶段：

- **正在准备**:PyTorch 估算器根据创建 docker 映像。 图像上传到工作区的容器注册表并缓存的更高版本运行。 日志还流式传输到运行历史记录，可以查看，以监视进度。

- **缩放**：群集会尝试纵向扩展如果 Batch AI 群集需要更多的节点不是当前可执行运行。

- **Running**：脚本文件夹中的所有脚本都上载到计算目标、 数据存储已装载或复制，并执行 entry_script。 从 stdout 的输出和。 日志文件夹的流式传输到运行历史记录和可用于监视运行。

- **后期处理**：。 / 输出运行的文件夹复制到运行历史记录。

## <a name="register-or-download-a-model"></a>注册或下载模型

一旦已训练模型，可以将其注册到你的工作区中。 模型注册允许您存储和版本来简化工作区中的模型[管理和部署的模型](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

此外可以使用 Run 对象来下载该模型的本地副本。 在训练脚本`pytorch_train.py`，PyTorch 保存对象仍然存在于本地文件夹 （本地到计算目标） 的模型。 Run 对象可用于下载副本。

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>分布式训练

[ `PyTorch` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)估算器还在 CPU 和 GPU 群集之间支持分布式的培训。 你可以轻松运行分布式的 PyTorch 作业和 Azure 机器学习服务将管理您的业务流程。

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod)是一个开放源代码，所有减少分布式的培训开发 Uber 的框架。 它提供了分布式 GPU PyTorch 作业的简便途径。

若要使用 Horovod，指定[ `MpiConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py)对象`distributed_training`PyTorch 构造函数参数中的。 此参数可确保已为你在训练脚本中使用安装 Horovod 库。


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```
Horovod 和及其依赖项将安装，以便它导入训练脚本`train.py`，如下所示：

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>将导出到 ONNX

优化使用推理[ONNX 运行时](concept-onnx.md)，将训练的 PyTorch 模型转换为 ONNX 格式。 推理，或模型评分，阶段已部署的模型不使用的是用于预测，最常在生产数据。 请参阅[教程](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)有关的示例。

## <a name="next-steps"></a>后续步骤

在本文中，将训练和注册 Azure 机器学习服务的 PyTorch 模型。 若要了解如何部署模型，可以继续学习我们的模型部署文章。

> [!div class="nextstepaction"]
> [如何以及在何处部署模型](how-to-deploy-and-where.md)
* [在训练期间跟踪运行指标](how-to-track-experiments.md)
* [优化超参数](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
