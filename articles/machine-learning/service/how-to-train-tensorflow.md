---
title: 使用 TensorFlow 和 Keras 的训练模型
titleSuffix: Azure Machine Learning service
description: 了解如何使用 TensorFlow 和 Keras 房产运行单节点和分布式的 TensorFlow 和 Keras 模型定型
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 04/19/2019
ms.custom: seodec18
ms.openlocfilehash: 78db7d21774750892c831ac220244c54594b78f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817703"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>使用 Azure 机器学习服务的训练 TensorFlow 和 Keras 模型

对于使用 TensorFlow 的深度神经网络 (DNN) 训练，Azure 机器学习提供了 `Estimator` 的自定义 `TensorFlow` 类。 Azure SDK [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)估计器 (不以与交织[ `tf.estimator.Estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator/Estimator)类)，可轻松地提交 Azure 上运行的单节点和分布式 TensorFlow 培训作业计算。

## <a name="single-node-training"></a>单节点训练
使用 `TensorFlow` 估算器进行训练与使用[基础 `Estimator`](how-to-train-ml-models.md) 类似，因此，请首先阅读操作说明文章并确保你了解其中介绍的概念。
  
若要运行 TensorFlow 作业，请实例化 `TensorFlow` 对象。 应已创建[计算目标](how-to-set-up-training-targets.md#amlcompute)对象 `compute_target`。

```Python
from azureml.train.dnn import TensorFlow

script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py',
                    conda_packages=['scikit-learn'], # in case you need scikit-learn in train.py
                    use_gpu=True)
```

此处，我们为 TensorFlow 构造函数指定以下参数：

参数 | 描述
--|--
`source_directory` | 包含训练作业所需的所有代码的本地目录。 此文件夹已从本地计算机复制到远程计算
`script_params` | 指定训练脚本的命令行参数的字典`entry_script`，格式为 < 命令行参数，值 > 对。  若要指定在 verbose 标志`script_params`，使用`<command-line argument, "">`。
`compute_target` | 运行训练脚本的远程计算目标，在本例中为 Azure 机器学习计算 ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) 群集
`entry_script` | 要在远程计算上运行的训练脚本的文件路径（相对于 `source_directory`）。 此文件以及所依赖的任何其他文件应位于此文件夹中
`conda_packages` | 要通过训练脚本所需的 conda 安装的 Python 包列表。 在这种情况下，训练脚本使用 `sklearn` 来加载数据，因此请指定要安装的包。  构造函数具有名为 `pip_packages` 的另一个参数，可以将其用于任何所需的 pip 包
`use_gpu` | 将此标志设置为 `True` 以利用 GPU 进行训练。 默认为 `False`。

由于你使用的是 TensorFlow 估算器，因此，用于训练的容器将默认包含 TensorFlow 包，以及在 CPU 和 GPU 上进行训练所需的相关依赖项。

然后，提交 TensorFlow 作业：
```Python
run = exp.submit(tf_est)
```

## <a name="keras-support"></a>Keras 支持
[Keras](https://keras.io/)是一个受欢迎的高级别 DNN Python API，支持 TensorFlow、 CNTK 或作为后端 Theano。 如果 TensorFlow 用作后端，可轻松用于 TensFlow 估算器 Keras 模型定型。 下面是通过添加到其中的 Keras TensorFlow 估算器示例：

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py',
                       pip_packages=['keras'], # just add keras through pip
                       use_gpu=True)
```
上述 TensorFlow 估算器构造函数指示 Azure 机器学习服务通过对执行环境的 pip 安装 Keras。 和您`keras_train.py`然后导入 Keras API Keras 模型进行定型。 有关完整示例，探索[此 Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb)。

## <a name="distributed-training"></a>分布式训练
此外，使用 TensorFlow 估算器，你还能够在 Azure VM 的 CPU 和 GPU 群集中大规模地训练模型。 你可以通过一些 API 调用轻松运行分布式 TensorFlow 训练，而 Azure 机器学习将在后台管理执行这些工作负载所需的所有基础结构和业务流程。

Azure 机器学习支持 TensorFlow 中的两种分布式训练方法：
* 使用 [Horovod](https://github.com/uber/horovod) 框架进行基于 MPI 的分布式训练
* 通过参数服务器方法进行的本机[分布式 TensorFlow](https://www.tensorflow.org/deploy/distributed)

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) 是 Uber 开发的用于分布式训练的开放源代码 ring-allreduce 框架。

若要使用 Horovod 框架运行分布式 TensorFlow，请按如下方式创建 TensorFlow 对象：

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi',
                    use_gpu=True)
```

上述代码显示了 TensorFlow 构造函数的以下新参数：

参数 | 描述 | 默认
--|--|--
`node_count` | 要用于训练作业的节点数。 | `1`
`process_count_per_node` | 要在每个节点上运行的进程（或“工作线程”）数。|`1`
`distributed_backend` | 用于启动由估算器通过 MPI 提供的分布式训练的后端。 如果你想要执行并行或分布式定型 (例如， `node_count`> 1 或`process_count_per_node`> 1 或两个) 使用 MPI （和 Horovod），设置`distributed_backend='mpi'`。 Azure 机器学习使用的 MPI 实现是 [Open MPI](https://www.open-mpi.org/)。 | `None`

上述示例将使用两个工作线程（每个节点一个工作线程）来运行分布式训练。

Horovod 和及其依赖项将安装，以便它导入训练脚本`train.py`，如下所示：

```Python
import tensorflow as tf
import horovod
```

最后，提交 TensorFlow 作业：
```Python
run = exp.submit(tf_est)
```

### <a name="parameter-server"></a>参数服务器
此外，你还可以运行[本机分布式 TensorFlow](https://www.tensorflow.org/deploy/distributed)，它使用参数服务器模型。 在此方法中，你将在一组参数服务器和工作线程中进行训练。 工作线程在训练期间计算梯度，而参数服务器聚合梯度。

构造 TensorFlow 对象：

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps',
                    use_gpu=True)
```

请注意，上述代码中 TensorFlow 构造函数的以下参数：

参数 | 描述 | 默认
--|--|--
`worker_count` | 工作线程数。 | `1`
`parameter_server_count` | 参数服务器数。 | `1`
`distributed_backend` | 用于分布式训练的后端。 要通过参数服务器进行分布式训练，请设置 `distributed_backend='ps'` | `None`

#### <a name="note-on-tfconfig"></a>请注意 `TF_CONFIG`
另外，还需要 [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec) 的群集网络地址和端口，因此，Azure 机器学习会为你设置 `TF_CONFIG` 环境变量。

`TF_CONFIG` 环境变量是一个 JSON 字符串。 下面是介绍参数服务器变量的示例：
```
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

如果使用 TensorFlow 的高级别[ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API，TensorFlow 将分析这`TF_CONFIG`变量和生成群集为您的规范。 

如果改为使用 TensorFlow 的低级核心 API 进行训练，则需要分析 `TF_CONFIG` 变量并在训练代码中自行生成 `tf.train.ClusterSpec`。 在[此示例](https://aka.ms/aml-notebook-tf-ps)中，你将在训练脚本中执行此操作，如下所示：

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

编写完训练脚本并创建 TensorFlow 对象后，你可以提交训练工作：
```Python
run = exp.submit(tf_est)
```

## <a name="examples"></a>示例

探索各种[笔记本在 Github 上的分布式深度学习](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤
* [在训练期间跟踪运行指标](how-to-track-experiments.md)
* [优化超参数](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
