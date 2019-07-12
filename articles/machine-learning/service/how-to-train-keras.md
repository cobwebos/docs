---
title: 训练和注册 TensorFlow 上运行的 Keras 模型
titleSuffix: Azure Machine Learning service
description: 本文说明您如何训练和注册在 TensorFlow 上运行的 Keras 模型使用 Azure 机器学习服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 06/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9d405b454d755e0c848e9422c8d4cf6e7c505b68
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840049"
---
# <a name="train-and-register-keras-models-at-scale-with-azure-machine-learning-service"></a>训练和大规模的 Keras 模型注册到 Azure 机器学习服务

本文说明您如何训练和注册基于 TensorFlow Keras 模型使用 Azure 机器学习服务。 它使用热门[MNIST 数据集](http://yann.lecun.com/exdb/mnist/)分类使用使用构建的深度神经网络 (DNN) 的手写的数字[Keras Python 库](https://keras.io)之上运行[TensorFlow](https://www.tensorflow.org/overview).

Keras 是高级神经网络 API 能够运行其他常用的 DNN 框架的顶部来简化开发。 使用 Azure 机器学习服务时，您可以快速横向扩展训练作业使用的弹性云计算资源。 您还可以跟踪训练运行、 版本模型部署模型，以及更多。

无论要开发思路加以的 Keras 模型还是正在将现有模型到云中，Azure 机器学习服务可以帮助你生成生产的模型。

## <a name="prerequisites"></a>先决条件

在两种环境上运行此代码：

 - Azure 机器学习 Notebook VM-无下载或安装有必要

     - 完成[基于云的笔记本快速入门](quickstart-run-cloud-notebook.md)来使用 SDK 和示例存储库创建的专用的笔记本服务器预加载。
    - 在 notebook 服务器上的示例文件夹中，通过导航到此目录查找已完成和展开 notebook:**说明-到-使用-azureml > 培训使用深度学习 > train-hyperparameter-tune-deploy-with-keras**文件夹。 
 
 - 你自己的 Jupyter 笔记本服务器

     - [安装 Azure 机器学习的 Python SDK](setup-create-workspace.md#sdk)
    - [创建工作区配置文件](setup-create-workspace.md#write-a-configuration-file)
    - [下载示例脚本文件](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)`mnist-keras.py`和 `utils.py`
     
    您还可以查找已完成[Jupyter 笔记本版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb)本指南的 GitHub 示例页。 此 notebook 包括扩展的部分，介绍智能超参数优化、 模型部署和笔记本小组件。

## <a name="set-up-the-experiment"></a>设置的实验

本部分会通过加载所需的 python 包、 初始化工作区、 创建试验，和上传的定型数据和训练脚本来设置训练实验。

### <a name="import-packages"></a>导入包

首先，导入所需的 Python 库。

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

[Azure 机器学习服务工作区](concept-workspace.md)是服务的顶级资源。 它为您提供集中的位置来使用您创建的所有内容。 在 Python SDK 中，您可以通过创建访问工作区项目[ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)对象。

创建工作区对象从`config.json`文件中创建[先决条件部分](#prerequisites)。

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>创建试验

创建试验和用于保存训练脚本的文件夹。 在此示例中，创建名为"keras mnist"的试验。

```Python
script_folder = './keras-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='keras-mnist')
```

### <a name="upload-dataset-and-scripts"></a>上传数据集和脚本

[数据存储](how-to-access-data.md)是一个位置可以存储和访问装载或将数据复制到计算目标的数据。 每个工作区提供了默认数据存储。 将数据和训练脚本上传到数据存储，这样用户可以轻松地访问在定型期间。

1. 下载本地 MNIST 数据集。

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

1. Keras 训练脚本上, 传`keras_mnist.py`，并帮助程序文件， `utils.py`。

    ```Python
    shutil.copy('./keras_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>创建计算目标

创建计算目标为 TensorFlow 作业上运行。 在此示例中，创建启用了 GPU 的 Azure 机器学习计算群集。

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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>创建 TensorFlow 估算器并导入 Keras

[TensorFlow 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)提供了启动 TensorFlow 培训作业计算目标上的简单方法。 因为 Keras 在 TensorFlow 之上运行，可以使用 TensorFlow 估算器和 Keras 库使用导入`pip_packages`参数。

TensorFlow 估算器实现通过通用[ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)类，该类可用于支持任何框架。 训练模型使用的泛型估计器的详细信息，请参阅[与使用估计器的 Azure 机器学习训练模型](how-to-train-ml-models.md)

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

[运行对象](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)运行作业时，完成后提供的运行历史记录的接口。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

执行运行时，它将经历以下阶段：

- **正在准备**:TensorFlow 估算器根据创建 docker 映像。 图像上传到工作区的容器注册表并缓存的更高版本运行。 日志还流式传输到运行历史记录，可以查看，以监视进度。

- **缩放**：群集会尝试纵向扩展如果 Batch AI 群集需要更多的节点不是当前可执行运行。

- **Running**：脚本文件夹中的所有脚本都上载到计算目标、 数据存储已装载或复制，并执行 entry_script。 从 stdout 的输出和。 日志文件夹的流式传输到运行历史记录和可用于监视运行。

- **后期处理**：。 / 输出运行的文件夹复制到运行历史记录。

## <a name="register-the-model"></a>注册模型

一旦已训练模型，可以将其注册到你的工作区中。 模型注册允许您存储和版本来简化工作区中的模型[管理和部署的模型](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

此外可以下载该模型的本地副本。 这可用于执行本地的其他模型验证工作。 在训练脚本中， `mnist-keras.py`，TensorFlow 保护程序对象会一直保持到本地文件夹 （本地到计算目标） 模型。 Run 对象可用于从数据存储中下载的副本。

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

在本文中，将训练和注册 Azure 机器学习服务的 Keras 模型。 若要了解如何部署模型，可以继续学习我们的模型部署文章。

> [!div class="nextstepaction"]
> [如何以及在何处部署模型](how-to-deploy-and-where.md)
