---
title: 训练深度学习 Keras 模型
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习培训和注册在 TensorFlow 上运行的 Keras 深神经网络分类模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 2fe15b09e32fee538958626d1f2dd7dac8fe9e51
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "75539133"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>使用 Azure 机器学习培训和注册 Keras 分类模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何使用 Azure 机器学习培训和注册基于 TensorFlow 构建的 Keras 分类模型。 它使用常用的[MNIST 数据集](http://yann.lecun.com/exdb/mnist/)，通过使用在[TensorFlow](https://www.tensorflow.org/overview)顶部运行的[Keras Python 库](https://keras.io)生成的深度神经网络（DNN）对手写数字进行分类。

Keras 是一种高级神经网络 API，能够运行其他流行的 DNN 框架，以简化开发。 使用 Azure 机器学习，可以使用弹性云计算资源快速横向扩展培训作业。 还可以跟踪定型运行、版本模型、部署模型，等等。

无论是从头开始开发 Keras 模型，还是将现有模型引入云中，Azure 机器学习都可以帮助您构建生产就绪模型。

有关机器学习和深度学习之间的差异的信息，请参阅[概念文章](concept-deep-learning-vs-machine-learning.md)。

## <a name="prerequisites"></a>必备组件

在以下任一环境中运行此代码：

- Azure 机器学习计算实例-无需下载或安装

     - 完成[教程：设置环境和工作区](tutorial-1st-experiment-sdk-setup.md)，创建随 SDK 和示例存储库预先加载的专用笔记本服务器。
    - 在笔记本服务器上的 "示例" 文件夹中，通过导航到以下目录来查找已完成且扩展的笔记本：操作**方法 > 培训-深度学习 > 超参数-keras**文件夹。

 - 你自己的 Jupyter 笔记本服务器

    - [安装 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [创建工作区配置文件](how-to-configure-environment.md#workspace)。
    - `mnist-keras.py` 和 `utils.py`[下载示例脚本文件](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)

    你还可以在 GitHub 示例页上找到本指南的已完成[Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb)。 此笔记本包含扩展的部分，涵盖智能超参数优化、模型部署和笔记本小组件。

## <a name="set-up-the-experiment"></a>设置试验

本部分通过加载所需的 python 包、初始化工作区、创建试验以及上传定型数据和训练脚本来设置训练实验。

### <a name="import-packages"></a>导入包

首先，导入必需的 Python 库。

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>初始化工作区

[Azure 机器学习工作区](concept-workspace.md)是服务的顶级资源。 它为您提供了一个集中的位置来处理您创建的所有项目。 在 Python SDK 中，可以通过创建[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)对象来访问工作区项目。

从 "[先决条件" 部分](#prerequisites)创建的 `config.json` 文件创建工作区对象。

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>创建试验

在工作区中创建一个名为 "keras-mnist" 的试验。

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
### <a name="create-a-file-dataset"></a>创建文件数据集

`FileDataset` 对象引用工作区数据存储或公共 URL 中的一个或多个文件。 文件可以是任何格式，该类提供将文件下载或装载到计算机的功能。 通过创建 `FileDataset`，可以创建对数据源位置的引用。 如果将任何转换应用于数据集，则它们也会存储在数据集中。 数据会保留在其现有位置，因此不会产生额外的存储成本。 有关详细信息，请参阅 `Dataset` 包中的[操作](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-register-datasets)指南。

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

使用 `register()` 方法将数据集注册到你的工作区，以便将其与他人共享，在各种试验中重复使用，并在训练脚本中按名称引用。

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

## <a name="create-a-compute-target"></a>创建计算目标

为要在其上运行的 TensorFlow 作业创建计算目标。 在此示例中，创建一个启用了 GPU 的 Azure 机器学习计算群集。

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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>创建 TensorFlow 估计器并导入 Keras

[TensorFlow 估计器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)提供了一种简单的方法来启动计算目标上的 TensorFlow 培训作业。 由于 Keras 在 TensorFlow 上运行，因此你可以使用 TensorFlow 估计器并使用 `pip_packages` 参数导入 Keras 库。

首先，使用 `Dataset` 类从工作区数据存储中获取数据。

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

TensorFlow 估计器是通过泛型[`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)类实现的，它可用于支持任何框架。 此外，创建包含 DNN 超参数设置的字典 `script_params`。 有关使用泛型估计器定型模型的详细信息，请参阅[使用估计器 Azure 机器学习训练模型](how-to-train-ml-models.md)

```python
from azureml.train.dnn import TensorFlow

script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
                 use_gpu=True)
```

## <a name="submit-a-run"></a>提交运行

[运行对象](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)在作业运行和完成后，为运行历史记录提供接口。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

在执行运行时，它将经历以下几个阶段：

- **准备**：按 TensorFlow 估计器创建 docker 映像。 该映像将上传到工作区的容器注册表中，并进行缓存以供稍后运行。 还会将日志流式传输到运行历史记录，并可以查看日志来监视进度。

- **缩放**：如果 Batch AI 群集需要的节点数多于当前可用的节点数，则群集将尝试增加。

- **正在运行**：脚本文件夹中的所有脚本都将上载到计算目标，装载或复制数据存储，然后执行 entry_script。 输出从 stdout 开始，/logs 文件夹将流式传输到运行历史记录，并可用于监视运行情况。

- **后期处理**：将运行的/outputs 文件夹复制到运行历史记录中。

## <a name="register-the-model"></a>注册模型

训练 DNN 模型后，可以将其注册到工作区。 利用模型注册，可以在工作区中存储模型并对模型进行版本管理，从而简化[模型管理和部署](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

> [!TIP]
> 刚注册的模型的部署方式与 Azure 机器学习中任何其他已注册的模型完全相同，无论你使用哪种估计器进行定型。 部署操作方法包含有关注册模型的部分，但你可以直接跳到创建用于部署的[计算目标](how-to-deploy-and-where.md#choose-a-compute-target)，因为你已有一个已注册的模型。

您还可以下载模型的本地副本。 这对于在本地执行其他模型验证工作非常有用。 在训练脚本中 `mnist-keras.py`，TensorFlow 的保护程序对象将模型保留到本地文件夹（计算目标的本地）。 可以使用 "运行" 对象从数据存储下载副本。

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

本文介绍了如何在 Azure 机器学习上定型并注册 Keras 模型。 若要了解如何部署模型，请继续学习我们的模型部署一文。

> [!div class="nextstepaction"]
> [部署模型的方式和位置](how-to-deploy-and-where.md)
* [在训练期间跟踪运行指标](how-to-track-experiments.md)
* [优化超参数](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
* [Azure 中的分布式深层学习培训参考体系结构](/azure/architecture/reference-architectures/ai/training-deep-learning)
