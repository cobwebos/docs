---
title: Azure Batch AI 服务 - AI 训练 | Microsoft Docs
description: 了解如何使用托管 Azure Batch AI 服务在 GPU 和 CPU 群集上训练人工智能 (AI) 和其他机器学习模型。
services: batch-ai
documentationcenter: ''
author: alexsuttonms
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/01/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 37f1bf5dc20d097f7f5f560e3bf1fdd25dc38045
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408045"
---
# <a name="what-is-azure-batch-ai"></a>什么是 Azure Batch AI？

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Azure Batch AI 是一种托管服务，用于帮助数据科学家和 AI 研究人员在 Azure 中大规模训练和测试机器学习与 AI 模型而无需管理复杂的基础设施。 你只需描述计算资源、要运行的作业、在何处存储模型输入和输出，其余的由 Batch AI 处理。

可以单独使用 Batch AI，也可以在更大型的开发工作流中进行模型训练：

* 使用 Batch AI 本身在 [GPU](../virtual-machines/linux/sizes-gpu.md) 或 CPU 群集上训练、测试机器学习和 AI 模型并对其进行批量评分。 

* 在工作流中使用 [Azure 机器学习](../machine-learning/service/overview-what-is-azure-ml.md)或其他 [Azure AI 平台工具](https://azure.microsoft.com/overview/ai-platform/)以 Batch AI 群集为目标进行操作。 Azure ML 提供数据准备、试验和作业历史记录方面的丰富体验。 Azure ML 也可将训练过的模型打包到容器中并部署用于推理的模型，或者将其部署到 IoT 设备。  

## <a name="train-machine-learning-and-ai-models"></a>训练机器学习和 AI 模型

使用 Batch AI 训练机器学习模型以及深层神经网络（深度学习）和其他 AI 方法。 Batch AI 提供的内置支持适用于常用开源 AI 库和框架，其中包括 [TensorFlow](https://github.com/tensorflow/tensorflow)、[PyTorch](https://github.com/pytorch/pytorch)、[Chainer](https://github.com/chainer/chainer) 和 [Microsoft Cognitive Toolkit (CNTK)](https://github.com/Microsoft/CNTK)。

在确定问题区域并准备数据以后，请通过交互方式使用 Batch AI 来测试模型理念。 然后，在做好大规模试验的准备以后，请通过 MPI 或其他通信库跨多个 GPU 启动作业，并以并行方式运行更多的试验。

Batch AI 可以通过多种方式大规模训练模型。 例如： 

|  |  |
|---------|---------|
| **分布式训练**<br/>![分布式训练](./media/overview/distributed-training.png)  | 将单个作业的训练纵向扩展到许多通过网络连接的 GPU，以便通过大量数据训练更大型的网络。|
| **试验**<br/>![试验](./media/overview/experimentation.png) | 使用多个作业横向扩展训练。 通过运行参数扫描来测试新理念，或者通过优化超参数来提高准确性和性能。 |
| **并行执行**![并行执行](./media/overview/parallel-execution.png) | 一次训练多个模型或对其评分，跨多个服务器并行运行以加快作业完成速度。|

训练某个模型以后，请使用 Batch AI 来测试该模型（如果此操作不包含在训练脚本中），或者进行批量评分。

## <a name="how-it-works"></a>工作原理

使用 Batch AI SDK、命令行脚本或 Azure 门户来管理计算资源并计划作业，以便进行 AI 训练和测试： 

* **预配和缩放 VM 群集** - 选择节点 (VM) 数目，然后选择支持 GPU 的 VM 大小或其他符合训练需求的 VM 大小。 自动或手动进行节点数目的纵向缩放，确保只根据需要来使用资源。 

* **管理依赖项和容器** - 默认情况下，Batch AI 群集运行的 Linux VM 映像已预先安装依赖项，因此可以在 GPU 或 CPU 上运行基于容器的训练框架。 若要进行其他配置，请使用自定义映像或运行启动脚本。

* **分配数据** - 若要管理输入数据和脚本以及作业输出，请选择一个或多个存储选项：Azure 文件、Azure Blob 存储或托管型 NFS 服务器。 Batch AI 还支持自定义存储解决方案，包括高性能并行文件系统。 请使用简单的配置文件将存储文件系统装载到群集节点和作业容器。

* **计划作业** - 将作业提交到基于优先级的作业队列，以便共享群集资源并利用低优先级 VM 和预订实例。

* **处理故障** - 在作业运行时间可能很长的情况下监视作业状态，在出现 VM 故障时重启作业。

* **收集结果** - 轻松访问输出日志、Stdout、Stderr 和已训练的模型。 请配置 Batch AI 作业，以便将输出直接推送到装载的存储。

作为 Azure 服务，Batch AI 支持常用的工具，例如基于角色的访问控制 (RBAC) 和 Azure 虚拟网络，目的是确保安全性。  

## <a name="next-steps"></a>后续步骤

* 详细了解 [Batch AI 资源](resource-concepts.md)，以便训练机器学习或 AI 模型。

* 从使用 Batch AI [训练示例性的深度学习模型](quickstart-tensorflow-training-cli.md)着手。

* 查看适用于常用 AI 框架的示例[训练方案](https://github.com/Azure/BatchAI/blob/master/recipes)。
