---
title: 使用 Azure 机器学习训练 PyTorch 模型
description: 了解如何使用 PyTorch 估算器运行 PyTorch 模型的单节点和分布式训练
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 27d4ad03e4a7f911fe3c9981618337a2fff51317
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114611"
---
# <a name="how-to-train-pytorch-models"></a>如何训练 PyTorch 模型

对于使用 PyTorch 的深度神经网络 (DNN) 训练，Azure 机器学习提供了 `Estimator` 的自定义 `PyTorch` 类。 Azure SDK 的 `PyTorch` 估算器使你能够轻松为 Azure 计算上的单节点和分布式运行提交 PyTorch 训练作业。

## <a name="single-node-training"></a>单节点训练
使用 `PyTorch` 估算器进行训练与使用[基础 `Estimator`](how-to-train-ml-models.md) 类似，因此，请首先阅读操作说明文章并确保你了解其中介绍的概念。
  
若要运行 PyTorch 作业，请实例化 `PyTorch` 对象。 应已创建[计算目标](how-to-set-up-training-targets.md#batch)对象 `compute_target` 和[数据存储](how-to-access-data.md)对象 `ds`。

```Python
from azureml.train.dnn import PyTorch

script_params = {
    '--data_dir': ds
}

pt_est = PyTorch(source_directory='./my-pytorch-proj',
                 script_params=script_params,
                 compute_target=compute_target,
                 entry_script='train.py',
                 use_gpu=True)
```

此处，我们为 PyTorch 构造函数指定以下参数：
参数 | Description
--|--
`source_directory` |  包含训练作业所需的所有代码的本地目录。 此文件夹已从本地计算机复制到远程计算
`script_params` |  指定训练脚本 `entry_script` 的命令行参数的字典，格式为 <命令行参数, 值> 对
`compute_target` |  将运行训练脚本的远程计算，这种情况下为 [Batch AI](how-to-set-up-training-targets.md#batch) 群集
`entry_script` |  要在远程计算上运行的训练脚本的文件路径（相对于 `source_directory`）。 此文件以及所依赖的任何其他文件应位于此文件夹中
`conda_packages` |  要通过训练脚本所需的 conda 安装的 Python 包列表。 构造函数具有名为 `pip_packages` 的另一个参数，可以将其用于任何所需的 pip 包
`use_gpu` |  将此标志设置为 `True` 以利用 GPU 进行训练。 默认为 `False`

由于你使用的是 `PyTorch` 估算器，因此，用于训练的容器将包含 PyTorch 包，以及在 CPU 和 GPU 上进行训练所需的相关依赖项。

然后，提交 PyTorch 作业：
```Python
run = exp.submit(pt_est)
```

## <a name="distributed-training"></a>分布式训练
此外，使用 `PyTorch` 估算器，你还能够在 Azure VM 的 CPU 和 GPU 群集中大规模地训练模型。 你可以通过一些 API 调用轻松运行分布式 PyTorch 训练，而 Azure 机器学习将在后台管理执行这些工作负载所需的所有基础结构和业务流程。

Azure 机器学习目前支持使用 Horovod 框架的 PyTorch 的基于 MPI 的分布式训练。

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) 是 Uber 开发的用于分布式训练的开放源代码 ring-allreduce 框架。

若要使用 Horovod 框架运行分布式 PyTorch，请按如下方式创建 PyTorch 对象：

```Python
from azureml.train.dnn import PyTorch

pt_est = PyTorch(source_directory='./my-pytorch-project',
                 script_params={},
                 compute_target=compute_target,
                 entry_script='train.py',
                 node_count=2,
                 process_count_per_node=1,
                 distributed_backend='mpi',
                 use_gpu=True)
```

该代码显示了 PyTorch 构造函数的以下新参数：
参数 | Description | 默认
--|--|--
`node_count` |  要用于训练作业的节点数。 | `1`
`process_count_per_node` |  要在每个节点上运行的进程（或“工作线程”）数。 | `1`
`distributed_backend` |  用于启动由估算器通过 MPI 提供的分布式训练的后端。  若要使用 MPI（和 Horovod）执行并行或分布式训练（例如，`node_count`>1 或 `process_count_per_node`>1，或两者），请设置 `distributed_backend='mpi'`。 Azure 机器学习使用的 MPI 实现是 [Open MPI](https://www.open-mpi.org/)。 | `None`

上述示例将使用两个工作线程（每个节点一个工作线程）来运行分布式训练。

将为你安装 Horovod 及其依赖项，因此，你只需将其导入训练脚本 `train.py`，如下所示：
```Python
import torch
import horovod
```

最后，提交你的分布式 PyTorch 作业：
```Python
run = exp.submit(pt_est)
```

## <a name="examples"></a>示例
有关单节点 PyTorch 训练的教程，请参阅：
* [training/01.train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/training/01.train-hyperparameter-tune-deploy-with-pytorch)

有关使用 Horovod 运行分布式 PyTorch 的教程，请参阅：
* [training/02.distributed-pytorch-with-horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/training/02.distributed-pytorch-with-horovod)

获取以下笔记本：

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤
* [在训练期间跟踪运行指标](how-to-track-experiments.md)
* [优化超参数](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
