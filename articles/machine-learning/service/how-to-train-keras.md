---
title: 用 Keras 训练深度学习神经网络
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务训练和注册在 TensorFlow 上运行的 Keras 深神经网络分类模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: b4f2bf5c762b6f93c2d23714d860e8261f6756d1
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847694"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning-service"></a>使用 Azure 机器学习 service 定型和注册 Keras 分类模型

本文介绍如何使用 Azure 机器学习服务培训和注册基于 TensorFlow 构建的 Keras 分类模型。 它使用常用的[MNIST 数据集](http://yann.lecun.com/exdb/mnist/), 通过使用在[TensorFlow](https://www.tensorflow.org/overview)顶部运行的[Keras Python 库](https://keras.io)生成的深度神经网络 (DNN) 对手写数字进行分类。

Keras 是一种高级神经网络 API, 能够运行其他流行的 DNN 框架, 以简化开发。 使用 Azure 机器学习 service, 你可以使用弹性云计算资源快速横向扩展培训作业。 还可以跟踪定型运行、版本模型、部署模型, 等等。

无论是从头开始开发 Keras 模型, 还是将现有模型引入云中, Azure 机器学习服务都可以帮助您构建生产就绪模型。

有关机器学习和深度学习之间的差异的信息, 请参阅[概念文章](concept-deep-learning-vs-machine-learning.md)。

## <a name="prerequisites"></a>系统必备

在以下任一环境中运行此代码:

 - Azure 机器学习笔记本 VM-无需下载或安装

     - 在开始本教程之前完成[教程：设置环境和工作](tutorial-1st-experiment-sdk-setup.md)区, 创建随 SDK 和示例存储库预先加载的专用笔记本服务器。
    - 在笔记本服务器上的 "示例" 文件夹中, 通过导航到以下目录来查找已完成且扩展的笔记本: 操作**方法 > 培训-深度学习 > 超参数-keras**文件夹。

 - 你自己的 Jupyter 笔记本服务器

    - [安装 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [创建工作区配置文件](how-to-configure-environment.md#workspace)。
    - [下载示例脚本文件](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)`mnist-keras.py`和`utils.py`

    你还可以在 GitHub 示例页上找到本指南的已完成[Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb)。 此笔记本包含扩展的部分, 涵盖智能超参数优化、模型部署和笔记本小组件。

## <a name="set-up-the-experiment"></a>设置试验

本部分通过加载所需的 python 包、初始化工作区、创建试验以及上传定型数据和训练脚本来设置训练实验。

### <a name="import-packages"></a>导入包

首先, 导入必需的 Python 库。

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>初始化工作区

" [Azure 机器学习服务" 工作区](concept-workspace.md)是服务的顶级资源。 它为您提供了一个集中的位置来处理您创建的所有项目。 在 Python SDK 中, 可以通过创建[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)对象来访问工作区项目。

从 "[先决条件" 部分](#prerequisites)创建`config.json`的文件中创建工作区对象。

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>创建试验

创建试验和文件夹来保存训练脚本。 在此示例中, 创建一个名为 "keras-mnist" 的试验。

```Python
script_folder = './keras-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='keras-mnist')
```

### <a name="upload-dataset-and-scripts"></a>上传数据集和脚本

数据[存储](how-to-access-data.md)是可通过装载数据或将数据复制到计算目标来存储和访问数据的位置。 每个工作区都提供默认数据存储。 将数据和培训脚本上传到数据存储, 以便在训练期间轻松访问这些脚本。

1. 在本地下载 MNIST 数据集。

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. 将 MNIST 数据集上传到默认数据存储。

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. 将 Keras 训练脚本、 `keras_mnist.py`和帮助程序`utils.py`文件上传。

    ```Python
    shutil.copy('./keras_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>创建计算目标

为要在其上运行的 TensorFlow 作业创建计算目标。 在此示例中, 创建一个启用了 GPU 的 Azure 机器学习计算群集。

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

有关计算目标的详细信息, 请参阅[什么是计算目标一](concept-compute-target.md)文。

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>创建 TensorFlow 估计器并导入 Keras

[TensorFlow 估计器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)提供了一种简单的方法来启动计算目标上的 TensorFlow 培训作业。 由于 Keras 在 TensorFlow 上运行, 因此可以使用 TensorFlow 估计器并使用`pip_packages`参数导入 Keras 库。

TensorFlow 估计器是通过泛型[`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)类实现的, 它可用于支持任何框架。 此外, 创建包含 DNN `script_params`超参数设置的字典。 有关使用泛型估计器定型模型的详细信息, 请参阅[使用估计器 Azure 机器学习训练模型](how-to-train-ml-models.md)

```Python
script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
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

[运行对象](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)在作业运行和完成后, 为运行历史记录提供接口。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

在执行运行时, 它将经历以下几个阶段:

- **准备**:根据 TensorFlow 估计器创建 docker 映像。 该映像将上传到工作区的容器注册表中, 并进行缓存以供稍后运行。 还会将日志流式传输到运行历史记录, 并可以查看日志来监视进度。

- **缩放**：如果 Batch AI 群集需要的节点数多于当前可用的节点数, 则群集将尝试增加。

- **Running**：脚本文件夹中的所有脚本都将上载到计算目标, 装载或复制数据存储, 并执行 entry_script。 输出从 stdout 开始,/logs 文件夹将流式传输到运行历史记录, 并可用于监视运行情况。

- **后期处理**：运行的/outputs 文件夹将复制到运行历史记录中。

## <a name="register-the-model"></a>注册模型

训练 DNN 模型后, 可以将其注册到工作区。 利用模型注册, 可以在工作区中存储模型并对模型进行版本管理, 从而简化[模型管理和部署](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

您还可以下载模型的本地副本。 这对于在本地执行其他模型验证工作非常有用。 在训练脚本`mnist-keras.py`中, TensorFlow 保护对象将模型保留到本地文件夹 (在计算目标的本地)。 可以使用 "运行" 对象从数据存储下载副本。

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

本文介绍了如何在 Azure 机器学习服务上定型并注册 Keras 模型。 若要了解如何部署模型, 请继续学习我们的模型部署一文。

> [!div class="nextstepaction"]
> [部署模型的方式和位置](how-to-deploy-and-where.md)
