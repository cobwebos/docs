---
title: 关于 Batch AI 资源 - Azure | Microsoft Docs
description: Microsoft Azure Batch AI 服务中的工作区、群集、文件服务器、试验和作业概述。
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/15/2018
ms.author: danlep
ms.openlocfilehash: 4a9e3529f9d68ecdc614ea69cffc6897891f4548
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44056702"
---
# <a name="overview-of-resources-in-batch-ai"></a>Batch AI 中的资源概述

首次开始使用 Batch AI 服务时，最好是先了解可用的 Batch AI 资源。 与使用其他 Azure 服务时一样，可在一个或多个 Azure 资源组中创建 Batch AI 资源。 在一个资源组中可以创建一个或多个 Batch AI 工作区。 每个工作区包含 Batch AI 群集、文件服务器和试验的混合形式。 Batch AI 试验封装一组作业。

下图显示了 Batch AI 的示例资源层次结构。 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

以下部分将更详细地介绍 Batch AI 资源。

## <a name="workspace"></a>工作区

Batch AI 中的工作区是剩余 Batch AI 资源的顶级集合。 工作区可帮助隔离属于不同组或项目的工作。 例如，可以创建一个开发工作区和一个测试工作区。

## <a name="cluster"></a>群集

Batch AI 中的群集包含正在运行的作业的计算资源。 群集中的所有节点具有相同的 VM 大小和 OS 映像。 Batch AI 提供多个选项用于创建可根据不同需求进行自定义的群集。 一般情况下，我们会针对完成某个项目所需的每种处理能力类别设置不同的群集。 可根据需求和预算纵向扩展或缩减 Batch AI 群集。 有关详细信息，请参阅[使用 Batch AI 群集](clusters.md)。

## <a name="file-server"></a>文件服务器

可以选择性地在 Batch AI 中创建一个文件服务器用于存储数据、训练脚本和输出日志。 Batch AI 文件服务器是托管的单节点 NFS，可自动将其装载到群集节点，为作业提供可集中轻松访问的存储位置。 大多数情况下，一个工作区只需要一个文件服务器；可将训练作业的数据分为不同的目录。 如果 NFS 不适用于你的工作负荷，Batch AI 还支持其他存储选项，包括 [Azure 存储](use-azure-storage.md)或自定义解决方案，如 Gluster 或 Lustre 文件系统。

## <a name="experiment"></a>试验

试验将统一查询和管理的一系列相关作业分组在一起。 每个工作区可以包含多个试验，而每个试验会尝试解决一个具体的问题。

## <a name="job"></a>作业

作业是需要执行的单个任务或脚本，例如，训练深度学习模型。 每个作业在工作区中的一个群集上执行特定的脚本。 （该脚本可能存储在 Batch AI 文件服务器或其他存储解决方案上。）每个 Batch AI 作业具有一个关联的框架类型：TensorFlow、Horovod、CNTK、Caffe、Caffe2、pyTorch、Chainer、自定义 MPI 或自定义。 对于每个框架，Batch AI 服务将设置所需的基础结构并管理作业流程。 每个试验可以包含多个类似的作业，以及对不同的参数所做的一些更改。

## <a name="next-steps"></a>后续步骤

* 运行第一个 [Batch AI 训练作业](quickstart-tensorflow-training-cli.md)。

* 查看适用于不同框架的示例[训练诀窍](https://github.com/Azure/BatchAI/tree/master/recipes)。