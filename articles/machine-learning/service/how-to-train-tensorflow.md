---
title: 使用 TensorFlow 训练深度学习神经网络
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务大规模运行 TensorFlow 训练脚本。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/10/2019
ms.custom: seodec18
ms.openlocfilehash: 41ebca7bd4ea299bda7e2d7a95edced583866527
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966793"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>使用 Azure 机器学习大规模构建 TensorFlow 深度学习模型

本文介绍如何使用 Azure 机器学习的[TensorFlow 估计器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)类, 按比例运行你的[TensorFlow](https://www.tensorflow.org/overview)训练脚本。 此示例训练并注册 TensorFlow 模型, 以便使用深层神经网络 (DNN) 对手写数字进行分类。

无论是从头开发 TensorFlow 模型还是将[现有模型](how-to-deploy-existing-model.md)引入云中, 都可以使用 Azure 机器学习来横向扩展开源定型作业, 以生成、部署、版本和监视生产级模型.

了解有关[深度学习与机器学习](concept-deep-learning-vs-machine-learning.md)的详细信息。

## <a name="prerequisites"></a>先决条件

在以下任一环境中运行此代码:

 - Azure 机器学习笔记本 VM-无需下载或安装

     - 在开始本教程之前完成[教程：设置环境和工作](tutorial-1st-experiment-sdk-setup.md)区, 创建随 SDK 和示例存储库预先加载的专用笔记本服务器。
    - 在笔记本服务器上的示例深度学习文件夹中, 通过导航到以下目录查找已完成且扩展的笔记本: 操作**方法 > 培训-深入了解-使用深度学习 > 超参数-tensorflow**folder. 
 
 - 你自己的 Jupyter 笔记本服务器

    - [安装 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [创建工作区配置文件](how-to-configure-environment.md#workspace)。
    - [下载示例脚本文件](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)`mnist-tf.py`和`utils.py`
     
    你还可以在 GitHub 示例页上找到本指南的已完成[Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb)。 此笔记本包含扩展的部分, 涵盖智能超参数优化、模型部署和笔记本小组件。

## <a name="set-up-the-experiment"></a>设置试验

本部分通过加载所需的 Python 包、初始化工作区、创建试验以及上传定型数据和训练脚本来设置训练实验。

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

### <a name="create-a-deep-learning-experiment"></a>创建深度学习试验

创建试验和文件夹来保存训练脚本。 在此示例中, 创建一个名为 "mnist" 的实验。

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
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

1. 将 TensorFlow 训练脚本、 `tf_mnist.py`和帮助程序`utils.py`文件上传。

    ```Python
    shutil.copy('./tf_mnist.py', script_folder)
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

## <a name="create-a-tensorflow-estimator"></a>创建 TensorFlow 估计器

[TensorFlow 估计器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)提供了一种简单的方法来启动计算目标上的 TensorFlow 培训作业。

TensorFlow 估计器是通过泛型[`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)类实现的, 它可用于支持任何框架。 有关使用泛型估计器定型模型的详细信息, 请参阅[使用估计器 Azure 机器学习训练模型](how-to-train-ml-models.md)

如果训练脚本需要额外的 pip 或 conda 包来运行, 则可以通过`pip_packages`和`conda_packages`参数传递包, 从而将包安装在生成的 Docker 映像中。

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True)
```

## <a name="submit-a-run"></a>提交运行

[运行对象](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)在作业运行和完成后, 为运行历史记录提供接口。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

在执行运行时, 它将经历以下几个阶段:

- **准备**:根据 TensorFlow 估计器创建 Docker 映像。 该映像将上传到工作区的容器注册表中, 并进行缓存以供稍后运行。 还会将日志流式传输到运行历史记录, 并可以查看日志来监视进度。

- **缩放**：如果 Batch AI 群集需要的节点数多于当前可用的节点数, 则群集将尝试增加。

- **Running**：脚本文件夹中的所有脚本都将上载到计算目标, 装载或复制数据存储, 并执行 entry_script。 输出从 stdout 开始,/logs 文件夹将流式传输到运行历史记录, 并可用于监视运行情况。

- **后期处理**：运行的/outputs 文件夹将复制到运行历史记录中。

## <a name="register-or-download-a-model"></a>注册或下载模型

训练模型后, 可以将其注册到工作区。 利用模型注册, 可以在工作区中存储模型并对模型进行版本管理, 从而简化[模型管理和部署](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

您还可以使用 Run 对象下载模型的本地副本。 在训练脚本`mnist-tf.py`中, TensorFlow 保护对象将模型保留到本地文件夹 (在计算目标的本地)。 您可以使用 "运行" 对象下载副本。

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

[`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)估计器还支持跨 CPU 和 GPU 群集的分布式培训。 您可以轻松地运行分布式 TensorFlow 作业, Azure 机器学习服务将管理业务流程。

Azure 机器学习服务在 TensorFlow 中支持两种分布式培训方法:

- 使用[Horovod](https://github.com/uber/horovod)框架的[基于 MPI 的](https://www.open-mpi.org/)分布式培训
- 使用参数服务器方法的本机[分布式 TensorFlow](https://www.tensorflow.org/deploy/distributed)

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod)是一个开源框架, 用于由 Uber 开发的分布式培训。 它提供了到分布式 GPU TensorFlow 作业的简单途径。

若要使用 Horovod, 请[`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py)在 TensorFlow 构造`distributed_training`函数中为参数指定一个对象。 此参数可确保安装 Horovod 库, 以便在训练脚本中使用。

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```

### <a name="parameter-server"></a>服务器参数

此外，你还可以运行[本机分布式 TensorFlow](https://www.tensorflow.org/deploy/distributed)，它使用参数服务器模型。 在此方法中，你将在一组参数服务器和工作线程中进行训练。 工作线程在训练期间计算梯度，而参数服务器聚合梯度。

若要使用参数服务器方法, 请在[`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) TensorFlow 构造函数`distributed_training`中为该参数指定一个对象。

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>定义 "TF_CONFIG" 中的群集规范

还需要群集[`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)的网络地址和端口, 因此 Azure 机器学习为你`TF_CONFIG`设置环境变量。

`TF_CONFIG` 环境变量是一个 JSON 字符串。 下面是介绍参数服务器变量的示例：

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

对于 TensorFlow 的高级[`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) API, TensorFlow 会`TF_CONFIG`分析变量并生成群集规范。

对于用于定型的 TensorFlow 低级别核心 api, 分析`TF_CONFIG`变量并`tf.train.ClusterSpec`在定型代码中生成。

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="next-steps"></a>后续步骤

本文介绍了如何培训和注册 TensorFlow 模型。 若要了解如何将模型部署到启用了 GPU 的群集, 请继续学习 GPU 模型部署一文。

> [!div class="nextstepaction"]
> [部署模型的方式和位置](how-to-deploy-and-where.md)
* [在训练期间跟踪运行指标](how-to-track-experiments.md)
* [优化超参数](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
* [Azure 中的分布式深层学习培训参考体系结构](/azure/architecture/reference-architectures/ai/training-deep-learning)
