---
title: 训练深度学习 PyTorch 模型
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习的 PyTorch 估计器类在企业规模上运行 PyTorch 训练脚本。  示例脚本将小鸡和土耳其映像分类，以根据 PyTorch 的传输学习教程生成深度学习神经网络。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 2c704e36f4353b34733ccc29b785c752b2a5c559
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822714"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>大规模定型 Pytorch 深度学习模型，Azure 机器学习
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，了解如何使用 Azure 机器学习的[PyTorch 估计器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)类在企业范围内运行[PyTorch](https://pytorch.org/)训练脚本。  

本文中的示例脚本用于根据 PyTorch 的传输学习[教程](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)来分类小鸡和土耳其映像，以生成深度学习神经网络。 

无论是从头开始训练深度学习 PyTorch 模型，还是将现有模型引入云中，都可以使用 Azure 机器学习来使用弹性云计算资源来横向扩展开源定型作业。 可以通过 Azure 机器学习来构建、部署、版本和监视生产级模型。 

了解有关[深度学习与机器学习](concept-deep-learning-vs-machine-learning.md)的详细信息。

## <a name="prerequisites"></a>先决条件

在以下任一环境中运行此代码：

 - Azure 机器学习笔记本 VM-无需下载或安装

    - 完成[教程：设置环境和工作区](tutorial-1st-experiment-sdk-setup.md)，创建随 SDK 和示例存储库预先加载的专用笔记本服务器。
    - 在笔记本服务器上的 "示例深度学习" 文件夹中，通过导航到以下目录查找已完成且展开的笔记本：通过导航到以下目录：**超参数-azureml > 培训-深入了解-深入 > 了解-pytorch**文件夹。 
 
 - 你自己的 Jupyter 笔记本服务器

    - [安装 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [创建工作区配置文件](how-to-configure-environment.md#workspace)。
    - [下载示例脚本文件](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    你还可以在 GitHub 示例页上找到本指南的已完成[Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb)。 此笔记本包含扩展的部分，涵盖智能超参数优化、模型部署和笔记本小组件。

## <a name="set-up-the-experiment"></a>设置试验

本部分通过加载所需的 python 包、初始化工作区、创建试验以及上传定型数据和训练脚本来设置训练实验。

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

[Azure 机器学习工作区](concept-workspace.md)是服务的顶级资源。 它为您提供了一个集中的位置来处理您创建的所有项目。 在 Python SDK 中，可以通过创建[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)对象来访问工作区项目。

从 "[先决条件" 部分](#prerequisites)创建的 `config.json` 文件创建工作区对象。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>创建深度学习试验

创建试验和文件夹来保存训练脚本。 在此示例中，创建一个名为 "pytorch" 的试验。

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>获取数据

数据集包含大约120个 turkeys 和 chickens 的培训图像，每个类包含100个验证图像。 我们将下载并提取数据集作为训练脚本 `pytorch_train.py`的一部分。 映像是[Open images V5 数据集](https://storage.googleapis.com/openimages/web/index.html)的子集。

### <a name="prepare-training-scripts"></a>准备训练脚本

在本教程中，已提供训练脚本 `pytorch_train.py`。 在实践中，您可以采用任何自定义训练脚本，并使用 Azure 机器学习运行该脚本。

`pytorch_train.py`上传 Pytorch 训练脚本。

```Python
shutil.copy('pytorch_train.py', project_folder)
```

但是，如果想要使用 Azure 机器学习跟踪和指标功能，则必须在训练脚本中添加少量代码。 可在 `pytorch_train.py`中找到指标跟踪的示例。

## <a name="create-a-compute-target"></a>创建计算目标

为要在其上运行的 PyTorch 作业创建计算目标。 在此示例中，创建一个启用了 GPU 的 Azure 机器学习计算群集。

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

有关计算目标的详细信息，请参阅[什么是计算目标一](concept-compute-target.md)文。

## <a name="create-a-pytorch-estimator"></a>创建 PyTorch 估计器

[PyTorch 估计器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)提供了一种简单的方法来启动计算目标上的 PyTorch 培训作业。

PyTorch 估计器是通过泛型[`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)类实现的，它可用于支持任何框架。 有关使用泛型估计器定型模型的详细信息，请参阅[使用估计器 Azure 机器学习训练模型](how-to-train-ml-models.md)

如果训练脚本需要额外的 pip 或 conda 包来运行，则可以通过将包的名称通过 `pip_packages` 和 `conda_packages` 参数传递这些包，从而将包安装在生成的 docker 映像中。

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

[运行对象](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)在作业运行和完成后，为运行历史记录提供接口。

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

在执行运行时，它将经历以下几个阶段：

- **准备**：按 PyTorch 估计器创建 docker 映像。 该映像将上传到工作区的容器注册表中，并进行缓存以供稍后运行。 还会将日志流式传输到运行历史记录，并可以查看日志来监视进度。

- **缩放**：如果 Batch AI 群集需要的节点数多于当前可用的节点数，则群集将尝试增加。

- **正在运行**：脚本文件夹中的所有脚本都将上载到计算目标，装载或复制数据存储，然后执行 entry_script。 输出从 stdout 开始，/logs 文件夹将流式传输到运行历史记录，并可用于监视运行情况。

- **后期处理**：将运行的/outputs 文件夹复制到运行历史记录中。

## <a name="register-or-download-a-model"></a>注册或下载模型

训练模型后，可以将其注册到工作区。 利用模型注册，可以在工作区中存储模型并对模型进行版本管理，从而简化[模型管理和部署](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

您还可以使用 Run 对象下载模型的本地副本。 在训练脚本 `pytorch_train.py`中，PyTorch save 对象将模型保留到本地文件夹（计算目标的本地）。 您可以使用 "运行" 对象下载副本。

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

[`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)估计器还支持跨 CPU 和 GPU 群集的分布式培训。 您可以轻松地运行分布式 PyTorch 作业，Azure 机器学习将为您管理业务流程。

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod)是一个开放源代码，可用于 Uber 开发的分布式培训的所有框架。 它提供了到分布式 GPU PyTorch 作业的简单途径。

若要使用 Horovod，请在 PyTorch 构造函数中指定 `distributed_training` 参数的[`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py)对象。 此参数可确保安装 Horovod 库，以便在训练脚本中使用。


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
将为你安装 Horovod 及其依赖项，因此你可以将其导入到训练脚本 `train.py`，如下所示：

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>导出到 ONNX

若要通过[ONNX 运行时](concept-onnx.md)优化推理，请将训练的 PyTorch 模型转换为 ONNX 格式。 推理或模型计分是部署模型用于预测的阶段，最常见的是生产数据。 有关示例，请参阅[教程](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)。

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure 机器学习上使用 PyTorch 训练和注册深度学习和神经网络。 若要了解如何部署模型，请继续学习我们的模型部署一文。

> [!div class="nextstepaction"]
> [部署模型的方式和位置](how-to-deploy-and-where.md)
* [在训练期间跟踪运行指标](how-to-track-experiments.md)
* [优化超参数](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
* [Azure 中的分布式深层学习培训参考体系结构](/azure/architecture/reference-architectures/ai/training-deep-learning)
