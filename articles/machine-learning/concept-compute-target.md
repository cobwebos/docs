---
title: 什么是计算目标
titleSuffix: Azure Machine Learning
description: 定义要将模型定型或部署到 Azure 机器学习的位置。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: 3d6439424f2a39bf5b5477cebd5fadb23a6370b0
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984806"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>什么是 Azure 机器学习中的计算目标？ 

**计算目标**是指定的计算资源/环境，你可以在其中运行训练脚本或托管你的服务部署。 此位置可以是本地计算机，也可以是基于云的计算资源。 使用计算目标可以轻松地更改计算环境，而无需更改代码。  

在典型的模型开发生命周期中，您可以：
1. 首先开发和试验少量数据。 在此阶段，我们建议你的本地环境（本地计算机或基于云的 VM）作为计算目标。 
2. 使用其中一种[定型计算目标](#train)，增加到更大的数据或进行分布式培训。  
3. 准备好模型后，使用这些[部署计算目标](#deploy)之一将其部署到 web 托管环境或 IoT 设备。

用于计算目标的计算资源会附加到[工作区](concept-workspace.md)。 工作区用户共享非本地计算机的计算资源。

## <a name="train"></a>训练计算目标

Azure 机器学习在不同的计算资源中具有不同的支持。  你还可以附加自己的计算资源，但对各种方案的支持可能会有所不同。

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

详细了解如何[设置和使用计算目标进行模型定型](how-to-set-up-training-targets.md)。

## <a name="deploy"></a>部署目标

以下计算资源可用于托管模型部署。

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

了解[将模型部署到计算目标的位置和方式](how-to-deploy-and-where.md)。

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure 机器学习计算（托管）

托管计算资源由 Azure 机器学习创建和管理。 此计算针对机器学习工作负荷进行了优化。 Azure 机器学习计算群集和[计算实例](concept-compute-instance.md)是唯一托管的计算。 将来可能会添加其他托管的计算资源。

你可以在中创建 Azure 机器学习计算实例（预览版）或计算群集：

| | Azure 机器学习工作室 | Azure 门户 | SDK | 资源管理器模板 | CLI |
|---| ----- | ----- | ----- | ----- | ----- |
| 计算实例 | 是 | 是 | 是 | 是 |  |
| 计算群集 | 是 | 是 | 是 | 是 | 是 |

创建这些计算资源时，会自动将工作区的一部分与其他类型的计算目标相同。

> [!NOTE]
> 计算实例仅适用于区域为**美国中北部**、**美国东部 2**、**北欧**或**英国南部**的工作区，并为即将推出的其他区域提供支持。
>如果你的工作区在任何其他区域，则可以继续创建并使用[笔记本 VM](concept-compute-instance.md#notebookvm)。 

### <a name="compute-clusters"></a>计算群集

你可以使用 Azure 机器学习计算群集进行培训和批处理推断（预览版）。  使用此计算资源，你可以：

* 单节点或多节点群集
* 每次提交运行时措施 
* 自动群集管理和作业计划 
* 为 CPU 和 GPU 资源提供支持



## <a name="unmanaged-compute"></a>非托管计算

非托管计算目标*不*受 Azure 机器学习管理。 在 Azure 机器学习之外创建这种类型的计算目标，并将其附加到工作区。 非托管计算资源可能需要其他步骤来维护或提高机器学习工作负荷的性能。

## <a name="next-steps"></a>后续步骤

了解如何操作：
* [设置计算目标以训练模型](how-to-set-up-training-targets.md)
* [将模型部署到计算目标](how-to-deploy-and-where.md)