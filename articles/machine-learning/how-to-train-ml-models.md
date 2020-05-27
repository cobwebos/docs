---
title: 使用估算器训练 ML 模型
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习估算器类对传统机器学习和深度学习模型执行单节点和分布式训练
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: c47bdc17041b2c388b01681dc1e65ddea29b0efb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584393"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>通过估算器使用 Azure 机器学习训练模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

凭借 Azure 机器学习，可以使用 [RunConfiguration 对象](how-to-set-up-training-targets.md#whats-a-run-configuration)和 [ScriptRunConfig 对象](how-to-set-up-training-targets.md#submit)轻松地将训练脚本提交到[各种计算目标](how-to-set-up-training-targets.md#compute-targets-for-training)。 该模式提供了很强的灵活性和最大程度的控制度。


借助估算器类，可以更轻松地通过深入学习和强化学习来训练模型。 它提供了一个高级抽象，可便于轻松地构造运行配置。 可以创建泛型[估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)，并使用它在所选的任何计算目标（无论是本地计算机、Azure 中的单个 VM 还是 Azure 中的 GPU 群集）上提交使用任何所选的学习框架（如 scikit-learn）的训练脚本。 对于 PyTorch、TensorFlow、Chainer 和强化学习任务，Azure 机器学习还提供了相应的 [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)、[TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)、[Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) 和[强化学习](how-to-use-reinforcement-learning.md)估算器来简化这些框架的使用。

## <a name="train-with-an-estimator"></a>使用估算器进行训练

创建[工作区](concept-workspace.md)并设置[开发环境](how-to-configure-environment.md)后，在 Azure 机器学习中训练模型包括以下步骤：  
1. 创建[远程计算目标](how-to-set-up-training-targets.md)（注意：也可将本地计算机用作计算目标）
2. 将[训练数据](how-to-access-data.md)上传到数据存储（可选）
3. 创建[训练脚本](tutorial-train-models-with-aml.md#create-a-training-script)
4. 创建 `Estimator` 对象
5. 将估算器提交到工作区下的实验对象

本文重点介绍步骤 4-5。 对于步骤 1-3，请以[训练模型教程](tutorial-train-models-with-aml.md)为例。

### <a name="single-node-training"></a>单节点训练

对在 Azure 中的远程计算上为 scikit 学习模型运行的单节点训练使用 `Estimator`。 你应该已经创建了[计算目标](how-to-set-up-training-targets.md#amlcompute)对象 `compute_target` 和 [FileDataset](how-to-create-register-datasets.md) 对象 `ds`。

```Python
from azureml.train.estimator import Estimator

script_params = {
    # to mount files referenced by mnist dataset
    '--data-folder': ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

此代码片段指定了 `Estimator` 构造函数的以下参数。

参数 | 说明
--|--
`source_directory`| 包含训练作业所需的所有代码的本地目录。 此文件夹从本地计算机复制到远程计算。
`script_params`| 字典，指定要以 `<command-line argument, value>` 对的形式传递到训练脚本 `entry_script` 的命令行参数。 若要在 `script_params` 中指定详细标志，请使用 `<command-line argument, "">`。
`compute_target`| 运行训练脚本的远程计算目标，在本例中为 Azure 机器学习计算 ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) 群集。 （请注意，尽管 AmlCompute 群集是常用目标，也可以选择其他计算目标类型，如 Azure VM，或甚至是本地计算机。）
`entry_script`| 要在远程计算上运行的训练脚本的文件路径（相对于 `source_directory`）。 此文件及其依赖的其他任何文件都应位于此文件夹中。
`conda_packages`| 要通过训练脚本所需的 conda 安装的 Python 包列表。  

构造函数有另一个名为 `pip_packages` 的参数，可用于所需的任何 pip 包。

创建了 `Estimator` 对象后，请提交要在远程计算上通过调用[实验](concept-azure-machine-learning-architecture.md#experiments)对象 `experiment` 上的 `submit` 函数来运行的训练作业。 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **特殊文件夹**两个文件夹 *outputs* 和 *logs* 接收 Azure 机器学习的特殊处理。 在训练期间，如果将文件写入相对于根目录（分别为 `./outputs` 和 `./logs`）的名为 outputs 和 logs 的文件夹，则会将这些文件自动上传到运行历史记录，以便在完成运行后对其具有访问权限 。
>
> 要在训练期间创建项目（如模型文件、检查点、数据文件或绘制的图像），请将其写入 `./outputs` 文件夹。
>
> 同样，可以将任何运行训练日志写入 `./logs` 文件夹。 要利用 Azure 机器学习的 [TensorBoard 集成](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb)，请确保将 TensorBoard 日志写入此文件夹。 正在运行时，你将能够启动 TensorBoard 并流式传输这些日志。  稍后，还能够从任何先前运行中还原日志。
>
> 例如，在运行远程训练后将写入 *outputs* 文件夹的文件下载到本地计算机：`run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>分布式训练和自定义 Docker 映像

使用 `Estimator` 可以执行以下两个额外的训练方案：
* 使用自定义 Docker 映像
* 多节点群集上的分布式训练

以下代码演示了如何为 Keras 模型执行分布式训练。 此外，它没有使用默认 Azure 机器学习映像，而是指定 Docker 中心 `continuumio/miniconda` 的自定义 Docker 映像来进行训练。

应已创建[计算目标](how-to-set-up-training-targets.md#amlcompute)对象 `compute_target`。 按如下所示创建估算器：

```Python
from azureml.train.estimator import Estimator
from azureml.core.runconfig import MpiConfiguration

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),        
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_image='continuumio/miniconda')
```

上述代码显示了 `Estimator` 构造函数的以下新参数：

参数 | 说明 | 默认
--|--|--
`custom_docker_image`| 要使用的映像的名称。 仅提供公共 docker 存储库（这种情况下为 Docker 中心）中可用的映像。 若要使用专用 docker 存储库中的映像，请改为使用构造函数的 `environment_definition` 参数。 [请参阅示例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)。 | `None`
`node_count`| 要用于训练作业的节点数。 | `1`
`process_count_per_node`| 要在每个节点上运行的进程（或“工作线程”）数。 在这种情况下，使用每个节点上均可用的 `2`GPU。| `1`
`distributed_training`| 用于使用 MPI 后端来启动分布式训练的 [MPIConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) 对象。  | `None`


最后，提交训练作业：
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>注册模型

训练模型后，就可以将它保存并注册到工作区。 借助模型注册，可以在工作区中存储模型，并对它进行版本管理，从而简化[模型管理和部署](concept-model-management-and-deployment.md)。

运行下面的代码会将模型注册到工作区，并可在远程计算上下文或部署脚本中按名称引用模型。 有关详细信息和其他参数，请参阅参考文档中的 [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)。

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>GitHub 跟踪和集成

如果你启动训练运行（其中源目录为本地 Git 存储库），存储库的相关信息存储在运行历史记录中。 有关详细信息，请参阅 [Azure 机器学习的 Git 集成](concept-train-model-git-integration.md)。

## <a name="examples"></a>示例
有关显示估算器模式基础知识的笔记本，请参阅：
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

有关使用估算器来训练 scikit-learn 模型的笔记本，请参阅：
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

有关使用深度学习框架专用估算器来训练模型的笔记本，请参阅：

* [how-to-use-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

* [在训练期间跟踪运行指标](how-to-track-experiments.md)
* [训练 PyTorch 模型](how-to-train-pytorch.md)
* [训练 TensorFlow 模型](how-to-train-tensorflow.md)
* [训练强化学习深度神经网络](how-to-use-reinforcement-learning.md)
* [优化超参数](how-to-tune-hyperparameters.md)
* [部署定型的模型](how-to-deploy-and-where.md)
* [创建和管理用于训练和部署的环境](how-to-use-environments.md)