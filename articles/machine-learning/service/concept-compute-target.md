---
title: 计算目标
titleSuffix: Azure Machine Learning service
description: 计算目标，可以指定运行训练脚本或主机服务部署的计算资源。 此位置可能是在本地计算机或基于云的计算资源。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/30/2019
ms.openlocfilehash: 42c0f5460a63b781aafdd43410761e2d7b17944d
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755345"
---
#  <a name="what-is-a-compute-target-in-azure-machine-learning-service"></a>什么是 Azure 机器学习服务中的计算目标？ 

计算目标，可以指定运行训练脚本或主机服务部署的计算资源。 此位置可能是在本地计算机或基于云的计算资源。

计算目标，使其可以轻松地更改您的计算环境而无需更改你的代码。  典型的模型开发生命周期：

* 开始使用适用于开发人员/试验上的少量数据。 在此阶段，我们建议使用本地环境。 例如，本地计算机或基于云的 VM。
* 纵向扩展更大的数据集，您的培训或执行分布式定型使用之一[培训目标](#train)。  
* 将部署到多个 web 托管环境中，或使用其中一个的 IoT 设备[部署目标](#deploy)。

用于计算目标的计算资源附加到[工作区](concept-workspace.md)。 计算由工作区的用户共享的本地计算机以外的资源。

## <a name="train"></a> 培训目标

Azure 机器学习服务具有不同支持跨不同的计算资源。  你也可以附加自己的计算资源，不过，为各种方案提供的支持可能有所不同，详情如下：

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


## <a name="deploy"></a>部署目标

可以使用以下的计算资源来承载您的模型部署。

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]


## <a name="managed-compute"></a>托管计算

托管的计算资源是由创建和管理 Azure 机器学习服务。 此计算适用于机器学习工作负荷。 Azure 机器学习计算是从 2019 年 5 月 30 日，开始仅托管的计算。 可能在将来添加更多托管的计算资源。

### <a name="amlcompute"></a> Azure 机器学习计算

可以使用 Azure 机器学习计算定型和推断 batch （预览版）。  使用此计算资源，可以：

* 单-或多-node 群集
* 每次提交运行措施 
* 自动群集管理和作业计划 
* 为 CPU 和 GPU 资源提供支持

可以使用以下任一创建 Azure 机器学习计算实例：

* Azure 门户
* Azure 机器学习 SDK
* Azure CLI

必须创建工作区，然后附加到它的所有其他计算资源。

## <a name="unmanaged-compute"></a>非托管的计算

是一种非托管的计算资源*不*由 Azure 机器学习服务。 创建此类型的外部 Azure 机器学习的计算，然后将其附加到工作区。 非托管的计算资源可能需要额外的步骤以维护或提高机器学习工作负荷的性能。

## <a name="next-steps"></a>后续步骤

* [设置用于为模型定型的计算目标](how-to-set-up-training-targets.md)
* [部署模型与 Azure 机器学习服务](how-to-deploy-and-where.md)