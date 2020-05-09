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
ms.openlocfilehash: 52716e070437dd7a6b3b880a5a7f3a4afafe8738
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995010"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Azure 机器学习的分布式培训

本文介绍分布式培训以及 Azure 机器学习如何为深度学习模型提供支持。 

在分布式培训中，用于定型模型的工作负荷将在多个称为辅助角色节点的小型处理器之间拆分和共享。 这些工作节点会并行工作以加速模型定型。 分布式培训可用于传统的 ML 模型，但更适合用于计算和时间密集型任务，如[深入了解](concept-deep-learning-vs-machine-learning.md)培训 deep 神经网络。 

## <a name="deep-learning-and-distributed-training"></a>深度学习和分布式培训 

分布式培训分为两种主要类型：[数据并行度](#data-parallelism)和[模型并行](#model-parallelism)性。 对于深度学习模型的分布式培训， [Python 中的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)支持与常用框架、PyTorch 和 TensorFlow 的集成。 这两个框架都利用了数据并行来实现分布式培训，并可以利用[horovod](https://horovod.readthedocs.io/en/latest/summary_include.html)来优化计算速度。 

* [PyTorch 的分布式培训](how-to-train-pytorch.md#distributed-training)

* [TensorFlow 的分布式培训](how-to-train-tensorflow.md#distributed-training)

对于不需要分布式培训的 ML 模型，请参阅使用[Azure 机器学习训练模型](concept-train-machine-learning-model.md#python-sdk)，了解使用 Python SDK 训练模型的不同方式。

## <a name="data-parallelism"></a>数据并行度

数据并行性是实现两种分布式定型方法的最简单方法，对于大多数用例来说已经足够了。

在此方法中，数据划分为多个分区，其中分区数等于计算群集中可用节点的总数。 将在这些工作节点中复制每个工作节点的模型，并且每个工作线程都在其自己的数据子集上运行。 请记住，每个节点都必须具有支持正在进行训练的模型的能力，这是模型必须完全适合每个节点。 下图提供了此方法的直观演示。

![数据并行-概念-关系图](./media/concept-distributed-training/distributed-training.svg)

每个节点单独计算其定型样本和标记输出的预测之间的错误。 反过来，每个节点会根据错误更新其模型，并且必须将其所有更改传达给其他节点，以更新其对应的模型。 这意味着，辅助角色节点需要在批处理计算结束时同步模型参数或渐变，以确保它们定型一致的模型。 

## <a name="model-parallelism"></a>模型并行度

在模型并行（也称为网络并行）中，该模型划分为可同时在不同节点中运行的不同部件，每个部件将在相同数据上运行。 此方法的可伸缩性取决于算法的任务并行度，实现方式比数据并行性更复杂。 

在模型并行中，工作节点只需同步共享参数，对于每个正向或向后传播步骤通常是一次。 而且，较大的模型也不是一个考虑因素，因为每个节点都在同一定型数据的模型的子节上操作。

## <a name="next-steps"></a>后续步骤

* 了解如何通过 Python SDK[设置培训环境](how-to-set-up-training-targets.md)。
* 有关技术示例，请参阅[参考体系结构方案](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/training-deep-learning)。
* [用 TensorFlow 训练 ML 模型](how-to-train-tensorflow.md)。
* [用 PyTorch 训练 ML 模型](how-to-train-pytorch.md)。 