---
title: 使用 Azure 机器学习训练机器学习模型
description: 了解如何使用 Azure 机器学习服务对传统机器学习和深度学习模型执行单节点和分布式训练
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 50b808b5769f2160d765ecdb431d71856e651b33
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983576"
---
# <a name="how-to-train-models-with-azure-machine-learning"></a>如何使用 Azure 机器学习训练模型

训练机器学习模型（尤其是深度神经网络）通常是时间密集型和计算密集型任务。 编写训练脚本并在本地计算机的一小部分数据上运行后，你可能会希望扩大工作负载。

为了便于训练，Azure 机器学习 Python SDK 提供了高级抽象（即估算器类），支持用户在 Azure 生态系统中轻松训练其模型。 可以创建并使用估算器对象来提交希望在远程计算上运行的任何训练代码，无论是单节点运行的训练还是跨 GPU 群集的分布式训练。 对于 PyTorch 和 TensorFlow 作业，Azure 机器学习还提供了相应的自定义 PyTorch 和 TensorFlow 估算器，以便于使用这些框架。

## <a name="train-with-an-estimator"></a>使用估算器进行训练

创建[工作区](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace)并设置[开发环境](how-to-configure-environment.md)后，在 Azure 机器学习中训练模型包括以下步骤：  
1. [创建远程计算目标](how-to-set-up-training-targets.md)
2. [上传训练数据](how-to-access-data.md)（可选）
3. 创建训练脚本
4. 创建估算器对象
5. 提交训练作业

本文重点介绍步骤 4-5。 对于步骤 1-3，请以此[教程](tutorial-train-models-with-aml.md)为例。

### <a name="single-node-training"></a>单节点训练

以下代码介绍了在 Azure 中的远程计算上为 scikit 学习模型运行的单节点训练。 应已创建[计算目标](how-to-set-up-training-targets.md#batch)对象 `compute_target` 和[数据存储](how-to-access-data.md)对象 `ds`。

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

上述代码片段指定了估算器构造函数的以下参数：
* `source_directory`：包含训练作业所需的所有代码的本地目录。 此文件夹已从本地计算机复制到远程计算 
* `script_params`：指定训练脚本 `entry_script` 的命令行参数的字典，格式为 <命令行参数, 值> 对
* `compute_target`：将运行训练脚本的远程计算，这种情况下为 [Batch AI](how-to-set-up-training-targets.md#batch) 群集
* `entry_script`：要在远程计算上运行的训练脚本的文件路径（相对于 `source_directory`）。 此文件以及所依赖的任何其他文件应位于此文件夹中
* `conda_packages`：要通过训练脚本所需的 conda 安装的 Python 包列表。  
构造函数具有名为 `pip_packages` 的另一个参数，可以将其用于任何所需的 pip 包

由于已创建估算器对象，因此可以提交要在远程计算上通过调用[实验](concept-azure-machine-learning-architecture.md#experiment)对象 `experiment` 上的 `submit` 函数来运行的训练作业。 

```Python
run = experiment.submit(sk_est)
print(run.get_details().status)
```

> [!IMPORTANT]
> **特殊文件夹**两个文件夹 *outputs* 和 *logs* 接收 Azure 机器学习的特殊处理。 在训练期间，如果将文件写入相对于根目录的名为 *outputs* 和 *logs* 的文件夹（分别为 `./outputs` 和 `./logs`），则会将这些文件自动上载到运行历史记录，以便在完成运行后对其具有访问权限。 
>
> 要访问在训练期间创建的项目（如模型文件、检查点、数据文件或绘制的图像），请将其写入 `./outputs` 文件夹。
>
> 同样，可以将任何运行训练日志写入 `./logs` 文件夹。 要利用 Azure 机器学习的 [TensorBoard 集成](https://aka.ms/aml-notebook-tb)，请确保将 TensorBoard 日志写入此文件夹。 正在运行时，将能够启动 TensorBoard 并流式传输这些日志。  稍后，还能够从任何先前运行中还原日志。
>
> 例如，在运行远程训练后将写入 *outputs* 文件夹的文件下载到本地计算机：`run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>分布式训练和自定义 Docker 映像

使用估算器可以执行以下两个额外的训练方案：
1. 使用自定义 Docker 映像
2. 多节点群集上的分布式训练

以下代码演示了如何为 CNTK 模型执行分布式训练。 此外，假定有要用于训练的自定义 docker 映像，而不是使用默认的 Azure 机器学习映像。

应已创建[计算目标](how-to-set-up-training-targets.md#batch)对象 `compute_target`。 可以按如下所示创建估算器：

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-cntk-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      pip_packages=['cntk==2.5.1'],
                      custom_docker_base_image='microsoft/mmlspark:0.12')
```

上述代码显示了估算器构造函数的以下新参数：
* `custom_docker_base_image`：要使用的映像的名称。 只能提供公共 docker 存储库（这种情况下为 Docker 中心）中可用的映像。 要使用专用 docker 存储库中的映像，请改为使用构造函数的 `environment_definition` 参数
* `node_count`：要用于训练作业的节点数。 此参数默认为 `1`
* `process_count_per_node`：要在每个节点上运行的进程（或“工作线程”）数。 在这种情况下，使用每个节点上均可用的 `2`GPU。 此参数默认为 `1`
* `distributed_backend`：用于启动由估算器通过 MPI 提供的分布式训练的后端。 此参数默认为 `None`。 如果要执行并行或分布式训练（例如，`node_count`>1 或 `process_count_per_node`>1，或两者），请设置 `distributed_backend='mpi'`。 AML 所使用的 MPI 实现是 [Open MPI](https://www.open-mpi.org/)。

最后，提交训练作业：
```Python
run = experiment.submit(cntk_est)
```

## <a name="examples"></a>示例
有关训练 sklearn 模型的教程，请参阅：
* `tutorials/01.train-models.ipynb`

有关使用自定义 docker 的分布式 CNTK 的教程，请参阅：
* `training/06.distributed-cntk-with-custom-docker/06.distributed-cntk-with-custom-docker.ipynb`

获取以下笔记本：

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

* [在训练期间跟踪运行指标](how-to-track-experiments.md)
* [训练 PyTorch 模型](how-to-train-pytorch.md)
* [训练 TensorFlow 模型](how-to-train-tensorflow.md)
* [优化超参数](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
