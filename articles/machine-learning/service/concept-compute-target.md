---
title: 计算目标：在何处定型和部署模型
titleSuffix: Azure Machine Learning
description: 定义要将模型定型或部署到 Azure 机器学习的位置。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/10/2019
ms.openlocfilehash: fe60b740312ee49510ea931bba1346ceaef9f31a
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035521"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>什么是 Azure 机器学习中的计算目标？ 

**计算目标**是指定的计算资源/环境，你可以在其中运行训练脚本或托管你的服务部署。 此位置可以是本地计算机, 也可以是基于云的计算资源。 使用计算目标可以轻松地更改计算环境，而无需更改代码。  

在典型的模型开发生命周期中，您可以：
1. 首先开发和试验少量数据。 在此阶段，我们建议你的本地环境（本地计算机或基于云的 VM）作为计算目标。 
2. 使用其中一种[定型计算目标](#train)，增加到更大的数据或进行分布式培训。  
3. 准备好模型后，使用这些[部署计算目标](#deploy)之一将其部署到 web 托管环境或 IoT 设备。

用于计算目标的计算资源会附加到[工作区](concept-workspace.md)。 工作区用户共享非本地计算机的计算资源。

## <a name="train"></a>训练计算目标

Azure 机器学习在不同的计算资源中具有不同的支持。  你还可以附加自己的计算资源，但对各种方案的支持可能会有所不同。

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

详细了解如何[设置和使用计算目标进行模型定型](how-to-set-up-training-targets.md)。

## <a name="deploy"></a>部署目标

以下计算资源可用于托管模型部署。

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

了解[将模型部署到计算目标的位置和方式](how-to-deploy-and-where.md)。

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure 机器学习计算（托管）

托管计算资源由 Azure 机器学习创建和管理。 此计算针对机器学习工作负荷进行了优化。 Azure 机器学习计算是在5月30日2019的托管计算。 将来可能会添加其他托管的计算资源。

您可以使用 Azure 机器学习计算进行培训，使用 batch 推断（预览版）。  使用此计算资源，你可以：

* 单节点或多节点群集
* 每次提交运行时措施 
* 自动群集管理和作业计划 
* 为 CPU 和 GPU 资源提供支持

可以在 Azure 门户或[工作区登录页（预览版）](https://ml.azure.com)、SDK 或 CLI 中创建 Azure 机器学习计算实例。 创建后，它将自动成为你的工作区的一部分，这与其他类型的计算目标不同。

## <a name="unmanaged-compute"></a>非托管计算

非托管计算目标*不*受 Azure 机器学习管理。 在 Azure 机器学习之外创建这种类型的计算目标，并将其附加到工作区。 非托管计算资源可能需要其他步骤来维护或提高机器学习工作负荷的性能。

## <a name="next-steps"></a>后续步骤

了解如何：
* [设置计算目标以训练模型](how-to-set-up-training-targets.md)
* [将模型部署到计算目标](how-to-deploy-and-where.md)