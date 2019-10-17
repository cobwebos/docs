---
title: 使用 TensorFlow 训练深度学习神经网络
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习大规模运行 TensorFlow 训练脚本。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 11b16f91d600c20b48fbdc5887a4a0a4b538e916
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330659"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>使用 Azure 机器学习大规模构建 TensorFlow 深度学习模型

本文介绍如何使用 Azure 机器学习的[TensorFlow 估计器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)类，按比例运行你的[TensorFlow](https://www.tensorflow.org/overview)训练脚本。 此示例训练并注册 TensorFlow 模型，以便使用深层神经网络（DNN）对手写数字进行分类。

无论是从头开发 TensorFlow 模型还是将[现有模型](how-to-deploy-existing-model.md)引入云中，都可以使用 Azure 机器学习来横向扩展开源定型作业，以生成、部署、版本和监视生产级模型.

了解有关[深度学习与机器学习](concept-deep-learning-vs-machine-learning.md)的详细信息。

## <a name="prerequisites"></a>必备组件

在以下任一环境中运行此代码：

 - Azure 机器学习笔记本 VM-无需下载或安装

     - 完成[教程：设置环境和工作区](tutorial-1st-experiment-sdk-setup.md)，创建随 SDK 和示例存储库预先加载的专用笔记本服务器。
    - 在笔记本服务器上的示例深度学习文件夹中，通过导航到以下目录查找已完成且扩展的笔记本：操作**说明 > ml-框架 > tensorflow > 部署 >超参数-tensorflow**文件夹的优化。 
 
 - 你自己的 Jupyter 笔记本服务器

    - [安装 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [创建工作区配置文件](how-to-configure-environment.md#workspace)。
    - [下载示例脚本文件](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)`mnist-tf.py` 和 `utils.py`
     
    你还可以在 GitHub 示例页上找到本指南的已完成[Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb)。 此笔记本包含扩展的部分，涵盖智能超参数优化、模型部署和笔记本小组件。

## <a name="set-up-the-experiment"></a>设置试验

本部分通过加载所需的 Python 包、初始化工作区、创建试验以及上传定型数据和训练脚本来设置训练实验。

### <a name="import-packages"></a>导入包

首先，导入必需的 Python 库。

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

[Azure 机器学习工作区](concept-workspace.md)是服务的顶级资源。 它为您提供了一个集中的位置来处理您创建的所有项目。 在 Python SDK 中，可以通过创建[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)对象来访问工作区项目。

从 "[先决条件" 部分](#prerequisites)创建的 @no__t 0 文件创建工作区对象。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>创建深度学习试验

创建试验和文件夹来保存训练脚本。 在此示例中，创建一个名为 "mnist" 的实验。

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

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

使用 `register()` 方法将数据集注册到你的工作区，以便可以与其他人共享数据集、在各种试验中重复使用这些数据集，并在训练脚本中按名称引用这些数据集。

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
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

## <a name="create-a-tensorflow-estimator"></a>创建 TensorFlow 估计器

[TensorFlow 估计器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)提供了一种简单的方法来启动计算目标上的 TensorFlow 培训作业。

TensorFlow 估计器是通过泛型[@no__t 1](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)类实现的，可用于支持任何框架。 有关使用泛型估计器定型模型的详细信息，请参阅[使用估计器 Azure 机器学习训练模型](how-to-train-ml-models.md)

如果训练脚本需要额外的 pip 或 conda 包来运行，则可以通过将包的名称通过 `pip_packages` 和 @no__t 1 参数传递，将包安装在生成的 Docker 映像中。

```python
script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
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

[运行对象](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)在作业运行和完成后，为运行历史记录提供接口。

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

在执行运行时，它将经历以下几个阶段：

- **准备**：按 TensorFlow 估计器创建 Docker 映像。 该映像将上传到工作区的容器注册表中，并进行缓存以供稍后运行。 还会将日志流式传输到运行历史记录，并可以查看日志来监视进度。

- **缩放**：如果 Batch AI 群集需要的节点数多于当前可用的节点数，则群集将尝试增加。

- **正在运行**：脚本文件夹中的所有脚本都将上载到计算目标，装载或复制数据存储，然后执行 entry_script。 输出从 stdout 开始，/logs 文件夹将流式传输到运行历史记录，并可用于监视运行情况。

- **后期处理**：将运行的/outputs 文件夹复制到运行历史记录中。

## <a name="register-or-download-a-model"></a>注册或下载模型

训练模型后，可以将其注册到工作区。 利用模型注册，可以在工作区中存储模型并对模型进行版本管理，从而简化[模型管理和部署](concept-model-management-and-deployment.md)。

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

您还可以使用 Run 对象下载模型的本地副本。 在训练脚本 `mnist-tf.py` 中，TensorFlow 的保护程序对象将模型保留到本地文件夹（计算目标的本地）。 您可以使用 "运行" 对象下载副本。

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

[@No__t-1](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)估计器还支持跨 CPU 和 GPU 群集的分布式培训。 您可以轻松地运行分布式 TensorFlow 作业，Azure 机器学习将为您管理业务流程。

Azure 机器学习支持 TensorFlow 中的两种分布式训练方法：

- 使用[Horovod](https://github.com/uber/horovod)框架的[基于 MPI 的](https://www.open-mpi.org/)分布式培训
- 使用参数服务器方法的本机[分布式 TensorFlow](https://www.tensorflow.org/deploy/distributed)

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod)是一个开源框架，用于由 Uber 开发的分布式培训。 它提供了到分布式 GPU TensorFlow 作业的简单途径。

若要使用 Horovod，请在 TensorFlow 构造函数中为 `distributed_training` 参数指定[`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py)对象。 此参数可确保安装 Horovod 库，以便在训练脚本中使用。

```Python
from azureml.core.runconfig import MpiConfiguration
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

### <a name="parameter-server"></a>参数服务器

此外，你还可以运行[本机分布式 TensorFlow](https://www.tensorflow.org/deploy/distributed)，它使用参数服务器模型。 在此方法中，你将在一组参数服务器和工作线程中进行训练。 工作线程在训练期间计算梯度，而参数服务器聚合梯度。

若要使用参数服务器方法，请在 TensorFlow 构造函数中为 `distributed_training` 参数指定[`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py)对象。

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

还需要[@no__t](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)的群集的网络地址和端口，因此 Azure 机器学习为你设置 @no__t 2 环境变量。

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

对于 TensorFlow 的高级别[`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) API，TensorFlow 会分析 @no__t 2 变量并为你生成群集规范。

对于 TensorFlow 的低级别核心 Api，请分析 `TF_CONFIG` 变量，并在训练代码中生成 @no__t。

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

本文介绍了如何培训和注册 TensorFlow 模型。 若要了解如何将模型部署到启用了 GPU 的群集，请继续学习 GPU 模型部署一文。

> [!div class="nextstepaction"]
> [部署模型的方式和位置](how-to-deploy-and-where.md)
* [在训练期间跟踪运行指标](how-to-track-experiments.md)
* [优化超参数](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
* [Azure 中的分布式深层学习培训参考体系结构](/azure/architecture/reference-architectures/ai/training-deep-learning)
