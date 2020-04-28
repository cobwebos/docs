---
title: 训练深度学习 PyTorch 模型
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习 PyTorch 估算器类在企业范围内运行 PyTorch 训练脚本。  示例脚本对鸡和火鸡图像进行分类，以基于 PyTorch 的迁移学习教程构建深度学习神经网络。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 136ee197271fc659497c169e27a6399c3940c19e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "75834857"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>使用 Azure 机器学习大规模训练 Pytorch 深度学习模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，你将了解如何使用 Azure 机器学习 [PyTorch 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)类在企业范围内运行 [PyTorch](https://pytorch.org/) 训练脚本。  

本文中的示例脚本用来对鸡和火鸡图像进行分类，以基于 PyTorch 的迁移学习[教程](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)构建深度学习神经网络。 

无论是从头开始训练深度学习 PyTorch 模型，还是将现有模型引入云中，都可以通过 Azure 机器学习使用弹性云计算资源来横向扩展开源训练作业。 你可以通过 Azure 机器学习来构建、部署和监视生产级模型以及对其进行版本控制。 

详细了解[深度学习与机器学习](concept-deep-learning-vs-machine-learning.md)。

## <a name="prerequisites"></a>先决条件

在以下任一环境中运行此代码：

- Azure 机器学习计算实例 - 无需下载或安装

    - 在开始本教程之前完成[教程：设置环境和工作区](tutorial-1st-experiment-sdk-setup.md)以创建预先装载了 SDK 和示例存储库的专用笔记本服务器。
    - 在笔记本服务器上的示例深度学习文件夹中，导航到以下目录，查找已完成且已展开的笔记本：**how-to-use-azureml > training-with-deep-learning > train-hyperparameter-tune-deploy-with-pytorch** 文件夹。 
 
 - 你自己的 Jupyter 笔记本服务器

    - [安装 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [创建工作区配置文件](how-to-configure-environment.md#workspace)。
    - [下载示例脚本文件](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    此外还可以在 GitHub 示例页上查找本指南的完整 [Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb)。 该笔记本包含扩展部分，其中涵盖智能超参数优化、模型部署和笔记本小组件。

## <a name="set-up-the-experiment"></a>设置试验

本部分将准备训练实验，包括加载所需 python 包、初始化工作区、创建实验以及上传训练数据和训练脚本。

### <a name="import-packages"></a>导入程序包

首先，导入必需的 Python 库。

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

[Azure 机器学习工作区](concept-workspace.md)是服务的顶级资源。 它提供了一个集中的位置来处理创建的所有项目。 在 Python SDK 中，可以通过创建 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) 对象来访问工作区项目。

根据在[先决条件部分](#prerequisites)中创建的 `config.json` 文件创建工作区对象。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>创建深度学习试验

创建一个试验和文件夹来容纳训练脚本。 在此示例中，创建一个名为“pytorch-birds”的试验。

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>获取数据

该数据集针对火鸡和鸡分别包含了大约 120 个训练图像，每个类有 100 个验证图像。 在训练脚本 `pytorch_train.py` 中，我们将下载并提取该数据集。 这些图像是 [Open Images v5 Dataset](https://storage.googleapis.com/openimages/web/index.html) 的子集。

### <a name="prepare-training-scripts"></a>准备训练脚本

在本教程中，已提供了训练脚本 `pytorch_train.py`。 实际上，你可以原样接受任何自定义的训练脚本，并使用 Azure 机器学习运行它。

上传 Pytorch 训练脚本 `pytorch_train.py`。

```Python
shutil.copy('pytorch_train.py', project_folder)
```

但是，如果想使用 Azure 机器学习跟踪和指标功能，则必须在训练脚本中添加少量代码。 可以在 `pytorch_train.py` 中找到指标跟踪示例。

## <a name="create-a-compute-target"></a>创建计算目标

创建用于运行 PyTorch 作业的计算目标。 在此示例中，创建启用了 GPU 的 Azure 机器学习计算群集。

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

有关计算目标的详细信息，请参阅[什么是计算目标](concept-compute-target.md)一文。

## <a name="create-a-pytorch-estimator"></a>创建 PyTorch 估算器

[PyTorch 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)提供了一种在计算目标上启动 PyTorch 训练作业的简单方法。

PyTorch 估算器是通过泛型 [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 类实现的，可用于支持任何框架。 有关使用泛型估算器训练模型的详细信息，请参阅[通过估算器使用 Azure 机器学习训练模型](how-to-train-ml-models.md)

如果你的训练脚本需要额外的 pip 或 conda 包才能运行，则可以通过使用 `pip_packages` 和 `conda_packages` 参数传递其名称在生成的 Docker 映像中安装这些包。

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

有关自定义 Python 环境的详细信息，请参阅[创建和管理用于训练和部署的环境](how-to-use-environments.md)。

## <a name="submit-a-run"></a>提交运行

[运行对象](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)在作业运行时和运行后提供运行历史记录的接口。

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

执行运行时，会经历以下阶段：

- **准备**：根据 PyTorch 估算器创建 Docker 映像。 将映像上传到工作区的容器注册表，缓存以用于后续运行。 还会将日志流式传输到运行历史记录，可以查看日志以监视进度。

- **缩放**：如果 Batch AI 群集执行运行所需的节点多于当前可用节点，则群集将尝试纵向扩展。

- **正在运行**：将脚本文件夹中的所有脚本上传到计算目标，装载或复制数据存储，然后执行 entry_script。 将 stdout 和 ./logs 文件夹中的输出流式传输到运行历史记录，可将其用于监视运行。

- **后期处理**：将运行的 ./outputs 文件夹复制到运行历史记录。

## <a name="register-or-download-a-model"></a>注册或下载模型

训练模型后，可以将其注册到工作区。 凭借模型注册，可以在工作区中存储模型并对其进行版本控制，从而简化[模型管理和部署](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> 无论使用哪种估计器进行训练，刚注册的模型的部署方式都与 Azure 机器学习中其他任何已注册模型完全相同。 部署指南包含有关模型注册的部分，但由于你已有一个已注册的模型，因而可以直接跳到[创建计算目标](how-to-deploy-and-where.md#choose-a-compute-target)进行部署。

此外，还可以使用“运行”对象下载模型的本地副本。 在训练脚本 `pytorch_train.py` 中，一个 PyTorch“保存”对象将模型保存到本地文件夹（计算目标的本地）。 可以使用“运行”对象下载副本。

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

[`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) 估算器还支持跨 CPU 和 GPU 群集的分布式训练。 你可以轻松运行分布式 PyTorch 作业，Azure 机器学习将为你管理业务流程。

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) 是 Uber 开发的用于分布式训练的开放源代码 all reduce 框架。 它提供了通向分布式 GPU PyTorch 作业的简单路径。

若要使用 Horovod，请在 PyTorch 构造函数中为 `distributed_training` 参数指定一个 [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) 对象。 此参数可确保为你安装 Horovod 库，以便在训练脚本中使用。


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
将为你安装 Horovod 及其依赖项，因此，你可以在训练脚本 `train.py` 中导入它，如下所示：

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>导出到 ONNX

若要使用 [ONNX 运行时](concept-onnx.md)优化推理，请将训练后的 PyTorch 模型转换为 ONNX 格式。 推理或模型评分是将部署的模型用于预测（通常针对生产数据）的阶段。 有关示例，请参阅此[教程](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)。

## <a name="next-steps"></a>后续步骤

在本文中，你使用 Azure 机器学习中的 PyTorch 训练并注册了一个深度学习神经网络。 若要了解如何部署模型，请继续参阅模型部署一文。

> [!div class="nextstepaction"]
> [部署模型的方式和位置](how-to-deploy-and-where.md)
* [在训练期间跟踪运行指标](how-to-track-experiments.md)
* [优化超参数](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
* [Azure 中分布式深度学习训练的参考体系结构](/azure/architecture/reference-architectures/ai/training-deep-learning)

