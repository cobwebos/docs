---
title: 什么是分布式训练？
titleSuffix: Azure Machine Learning
description: 了解分布式训练以及 Azure 机器学习如何支持分布式训练。
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 62edee6a882191551ce2409646ea8b617576c059
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89651153"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Azure 机器学习的分布式训练

本文介绍分布式训练以及 Azure 机器学习如何支持深度学习模型的分布式训练。 

在分布式训练中，用于训练模型的工作负载会在多个微型处理器之间进行拆分和共享，这些处理器称为工作器节点。 这些工作器节点并行工作以加速模型训练。 分布式训练可用于传统的 ML 模型，但更适用于计算和时间密集型任务，如用于训练深度神经网络的[深度学习](concept-deep-learning-vs-machine-learning.md)。 

## <a name="deep-learning-and-distributed-training"></a>深度学习和分布式训练 

分布式训练有两种主要类型：[数据并行](#data-parallelism)和[模型并行](#model-parallelism)。 对于深度学习模型上的分布式训练，[Python 中的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) 支持与常用框架 PyTorch 和 TensorFlow 进行集成。 两种框架都采用数据并行方式进行分布式训练，并且可以利用 [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) 来优化计算速度。 

* [使用 PyTorch 进行分布式训练](how-to-train-pytorch.md#distributed-training)

* [使用 TensorFlow 进行分布式训练](how-to-train-tensorflow.md#distributed-training)

对于不需要进行分布式训练的 ML 模型，请参阅[使用 Azure 机器学习训练模型](concept-train-machine-learning-model.md#python-sdk)，了解使用 Python SDK 训练模型的不同方法。

## <a name="data-parallelism"></a>数据并行

数据并行是两种分布式训练方法中较易实现的一个，对于大多数用例来说已经足够了。

在此方法中，数据划分到计算群集中的分区内，其中分区数等于可用节点的总数。 将在这些工作器节点的每一个中复制模型，每个工作器都操作和处理自己的数据子集。 请记住，每个节点都必须有能力支持正在进行训练的模型，也就是说，模型需要拟合每个节点。 下图直观地阐释了此方法。

![数据并行概念关系图](./media/concept-distributed-training/distributed-training.svg)

每个节点独立计算其训练样本的预测结果与标记输出之间的误差。 每个节点又会基于该误差更新其模型，且必须将其所有更改传达给其他节点，以便其相应更新其自己的模型。 这意味着，工作器节点需要在批处理计算结束时同步模型参数或梯度，以确保其训练的是一致的模型。 

## <a name="model-parallelism"></a>模型并行

在模型并行（也称为网络并行）中，模型将划分为可在不同节点中并发运行的不同部分，且每个部分都使用同一数据运行。 此方法的可伸缩性取决于算法的任务并行程度，其实现方式比数据并行更为复杂。 

在模型并行中，工作器节点只需要为每个前向或后向传播步骤同步共享的参数一次（通常情况下）。 并且，即使模型较大也没问题，因为每个节点只处理模型的一个部分，且使用一致的训练数据。

## <a name="next-steps"></a>后续步骤

* 了解如何使用 Python SDK [将计算目标用于模型训练](how-to-set-up-training-targets.md)。
* 有关技术示例，请参阅[体系结构方案参考信息](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/training-deep-learning)。
* [使用 TensorFlow 训练 ML 模型](how-to-train-tensorflow.md)。
* [使用 PyTorch 训练 ML 模型](how-to-train-pytorch.md)。 