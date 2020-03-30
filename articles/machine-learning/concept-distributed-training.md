---
title: 什么是分布式培训？
titleSuffix: Azure Machine Learning
description: 了解分布式培训以及 Azure 机器学习如何支持它。
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: a0d5bf795e4759a105b9a235770f37aa10bd6751
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385540"
---
# <a name="distributed-training-with-azure-machine-learning"></a>使用 Azure 机器学习进行分布式培训

在本文中，您将了解分布式培训以及 Azure 机器学习如何支持它用于深度学习模型。 

在分布式训练中，训练模型的工作负载在多个微型处理器（称为辅助节点）之间拆分并共享。 这些辅助节点并行工作以加快模型训练。 分布式训练可用于传统的 ML 模型，但更适合计算和时间密集型任务，如用于训练深层神经网络[的深层学习](concept-deep-learning-vs-machine-learning.md)。

## <a name="deep-learning-and-distributed-training"></a>深度学习和分布式培训 

分布式训练主要有两种类型：[数据并行性和](#data-parallelism)[模型并行性](#model-parallelism)。 对于深度学习模型的分布式培训[，Python 中的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)支持与常用框架 PyTorch 和 TensorFlow 的集成。 这两个框架都采用数据并行性进行分布式培训，并可以利用[horovod](https://horovod.readthedocs.io/en/latest/summary_include.html)优化计算速度。 

* [使用 PyTorch 进行分布式培训](how-to-train-pytorch.md#distributed-training)

* [使用 TensorFlow 进行分布式培训](how-to-train-tensorflow.md#distributed-training)

对于不需要分布式训练的 ML 模型，请参阅使用[Azure 机器学习训练模型](concept-train-machine-learning-model.md#python-sdk)，了解使用 Python SDK 训练模型的不同方法。

## <a name="data-parallelism"></a>数据并行度

数据并行性是两种分布式培训方法最容易实现的，对于大多数用例来说已经足够了。

在此方法中，数据被划分为分区，其中分区数等于计算群集中的可用节点总数。 模型在每个辅助节点中复制，每个辅助角色都按其自己的数据子集操作。 请记住，每个节点必须具有支持正在训练的模型的能力，即模型必须完全适合每个节点。

每个节点独立计算其训练样本的预测和标记的输出之间的错误。 反过来，每个节点会根据错误更新其模型，并且必须将其所有更改传达给其他节点以更新其相应的模型。 这意味着辅助节点需要在批处理计算结束时同步模型参数或梯度，以确保它们正在训练一致的模型。 

## <a name="model-parallelism"></a>模型并行性

在模型并行性（也称为网络并行性）中，模型被分割到不同的部分，这些部分可以在不同的节点中同时运行，并且每个部分都将在同一数据上运行。 该方法的可伸缩性取决于算法的任务并行化程度，实现比数据并行性复杂。 

在模型并行性中，辅助节点只需要同步共享参数，通常每个向前或向后传播步骤同步一次。 此外，较大的模型不是问题，因为每个节点都根据模型的一个子部分对相同的训练数据进行操作。

## <a name="next-steps"></a>后续步骤

* 了解如何使用 Python SDK[设置训练环境](how-to-set-up-training-targets.md)。
* 有关技术示例，请参阅[参考体系结构方案](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/training-deep-learning)。
* [用TensorFlow训练ML模型](how-to-train-tensorflow.md)。
* [用PyTorch训练ML模型](how-to-train-pytorch.md)。 