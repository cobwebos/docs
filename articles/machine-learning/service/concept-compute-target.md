---
title: 计算目标： 训练和部署模型的位置
titleSuffix: Azure Machine Learning service
description: 定义你想要训练或部署使用 Azure 机器学习服务对模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/10/2019
ms.openlocfilehash: a7944b284a9c1c0424af54874554d05d49ad4b20
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806044"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning-service"></a>在 Azure 机器学习服务中的计算目标是什么？ 

一个**计算目标**是指定的计算资源/环境，您运行训练脚本或主机服务部署。 此位置可能是在本地计算机或基于云的计算资源。 使用计算目标，使您方便以后若要更改您的计算环境而无需更改代码。  

在典型的模型开发生命周期中，您可能会：
1. 首先，开发和试验上的少量数据。 在此阶段，我们建议你的本地环境 （本地计算机或基于云的 VM） 作为计算目标。 
2. 纵向扩展到更大的数据，或执行分布式定型使用其中一种[培训的计算目标](#train)。  
3. 准备好您的模型后，将其部署到 web 宿主环境或 IoT 设备与下列任一[部署的计算目标](#deploy)。

用于计算目标的计算资源附加到[工作区](concept-workspace.md)。 计算由工作区的用户共享的本地计算机以外的资源。

## <a name="train"></a> 培训的计算目标

Azure 机器学习服务具有不同支持跨不同的计算资源。  您还可以将附加自己的计算资源，尽管对不同的情况下可能会有所不同的支持。

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

详细了解如何[设置和使用计算目标，以便为模型定型](how-to-set-up-training-targets.md)。

## <a name="deploy"></a>部署目标

可以使用以下的计算资源来承载您的模型部署。

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

了解[其中以及如何将您的模型部署到计算目标](how-to-deploy-and-where.md)。

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure 机器学习计算 （托管）

托管的计算资源是由创建和管理 Azure 机器学习服务。 此计算适用于机器学习工作负荷。 Azure 机器学习计算是从 2019 年 5 月 30 日，开始仅托管的计算。 可能在将来添加更多托管的计算资源。

可以使用 Azure 机器学习计算定型和推断 batch （预览版）。  使用此计算资源，可以：

* 单-或多-node 群集
* 每次提交运行措施 
* 自动群集管理和作业计划 
* 为 CPU 和 GPU 资源提供支持

可以在 Azure 门户中，使用 SDK，或使用 CLI 创建 Azure 机器学习计算实例。 创建它时自动是与其他类型的计算目标的不同工作区的一部分。

## <a name="unmanaged-compute"></a>非托管的计算

非托管的计算目标是*不*由 Azure 机器学习服务。 创建此类型的外部 Azure 机器学习计算目标，然后将其附加到工作区。 非托管的计算资源可能需要额外的步骤以维护或提高机器学习工作负荷的性能。

## <a name="next-steps"></a>后续步骤

了解如何：
* [设置计算目标，以训练模型](how-to-set-up-training-targets.md)
* [将您的模型部署到计算目标](how-to-deploy-and-where.md)