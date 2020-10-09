---
title: 什么是计算目标
titleSuffix: Azure Machine Learning
description: 定义要在何处使用 Azure 机器学习训练或部署你的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/29/2020
ms.openlocfilehash: 18f9af0198c7a89b607630c686fbf8dafdd01a50
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91841931"
---
# <a name="what-are-compute-targets-in-azure-machine-learning"></a>什么是 Azure 机器学习中的计算目标?

*计算目标*是指定的计算资源或环境，你可以在其中运行训练脚本或托管你的服务部署。 此位置可以是本地计算机，也可以是基于云的计算资源。 使用计算目标可以轻松地更改计算环境，而无需更改代码。

在典型的模型开发生命周期中，你可以：

1. 首先，基于少量数据进行开发和试验。 在此阶段，使用本地环境（例如本地计算机或基于云的虚拟机） (VM) 作为计算目标。
1. 通过使用其中一种 [定型计算目标](#train)，增加到更大的数据或进行分布式培训。
1. 准备好模型后，使用这些 [部署计算目标](#deploy)之一将其部署到 web 托管环境或 IoT 设备。

你用于计算目标的计算资源附加到[工作区](concept-workspace.md)。 本地计算机以外的计算资源由工作区的用户共享。

## <a name="training-compute-targets"></a><a name="train"></a> 训练计算目标

Azure 机器学习为不同的计算目标提供不同的支持。 典型的模型开发生命周期从开发或试验少量的数据开始。 在此阶段，使用本地环境（如本地计算机或基于云的 VM）。 随着您对更大的数据集或执行分布式培训的培训，使用 Azure 机器学习计算来创建每次提交运行时措施的单节点或多节点群集。 你还可以附加自己的计算资源，但不同方案的支持可能会有所不同。

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

了解有关如何向 [计算目标提交定型运行的](how-to-set-up-training-targets.md)详细信息。

## <a name="compute-targets-for-inference"></a><a name="deploy"></a> 用于推理的计算目标

以下计算资源可用来托管模型部署。

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

执行推理时，Azure 机器学习会创建托管模型和使用该模型所需的关联资源的 Docker 容器。 然后，在以下任一部署场景中使用此容器：

* 作为用于实时推理的 *web 服务* 。 Web 服务部署使用以下计算目标之一：

    * [本地计算机](how-to-attach-compute-targets.md#local)
    * [Azure 机器学习计算实例](how-to-create-manage-compute-instance.md)
    * [Azure 容器实例](how-to-attach-compute-targets.md#aci)
    * [Azure Kubernetes 服务](how-to-create-attach-kubernetes.md)
    * Azure Functions（预览版）。 仅部署到函数依赖于 Azure 机器学习来生成 Docker 容器。 从这里开始，使用函数来部署它。 有关详细信息，请参阅[将机器学习模型部署到 Azure Functions（预览版）](how-to-deploy-functions.md)。

* 用于定期处理批处理数据的 _批处理推理_ 终结点。 批处理推断使用 [Azure 机器学习计算群集](how-to-create-attach-compute-cluster.md)。

* 转到 IoT 设备（预览版）。 IoT 设备的部署仅依赖 Azure 机器学习来生成 Docker 容器。 在这里，它是使用 Azure IoT Edge 部署的。 有关详细信息，请参阅[部署为 IoT Edge 模块（预览版）](/azure/iot-edge/tutorial-deploy-machine-learning)。

了解[在何处以及如何将模型部署到计算目标](how-to-deploy-and-where.md)。

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure 机器学习计算（托管）

托管计算资源是由 Azure 机器学习创建和管理的。 此计算针对机器学习工作负荷进行了优化。 Azure 机器学习计算群集和[计算实例](concept-compute-instance.md)是仅有的托管计算。

可以通过以下方法创建 Azure 机器学习计算实例或计算群集：

* [Azure 机器学习 studio](how-to-create-attach-compute-studio.md)。
* Python SDK 和 CLI：
    * [计算实例](how-to-create-manage-compute-instance.md)。
    * [计算群集](how-to-create-attach-compute-cluster.md)。
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (预览) 。
* Azure 资源管理器模板。 有关示例模板，请参阅 [创建 Azure 机器学习计算群集](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute)。
* [用于 Azure CLI](reference-azure-machine-learning-cli.md#resource-management)的机器学习扩展。

创建后，这些计算资源会自动成为工作区的一部分，这与其他类型的计算目标不同。


|功能  |计算群集  |计算实例  |
|---------|---------|---------|
|单节点或多节点群集     |    **&check;**       |         |
|每次提交运行时自动缩放     |     **&check;**      |         |
|自动化群集管理和作业计划     |   **&check;**        |     **&check;**      |
|为 CPU 和 GPU 资源提供支持     |  **&check;**         |    **&check;**       |


> [!NOTE]
> 当计算 *群集* 处于空闲状态时，它会措施到0个节点，因此不需要支付。 计算 *实例* 始终处于打开和不自动缩放。 在不使用 [计算实例时应停止计算实例](how-to-create-manage-compute-instance.md#manage) ，以免产生额外的成本。

### <a name="supported-vm-series-and-sizes"></a>支持的 VM 系列和大小

为 Azure 机器学习中的托管计算资源选择节点大小时，可以从 Azure 提供的选定 VM 大小中进行选择。 Azure 针对不同工作负载为 Linux 和 Windows 提供了一系列大小。 若要了解详细信息，请参阅 [VM 类型和大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)。

选择 VM 大小时有几个例外和限制：

* Azure 机器学习中不支持某些 VM 系列。
* 某些 VM 系列是受限制的。 若要使用受限制的系列，请与支持团队联系并请求为该系列增加配额。 有关如何联系支持人员的信息，请参阅 [Azure 支持选项](https://azure.microsoft.com/support/options/)。

请查看下表，了解有关支持的系列和限制的详细信息。

| **支持的 VM 系列**  | **限制** |
|------------|------------|
| D | 无。 |
| Dv2 | 无。 |  
| Dv3 | 无。|
| DSv2 | 无。 | 
| DSv3 | 无。|
| FSv2 | 无。 | 
| HBv2 | 需要批准。 |  
| HCS | 需要批准。 |  
| M | 需要批准。 |
| NC | 无。 |    
| NCsv2 | 需要批准。 |
| NCsv3 | 需要批准。 |  
| NDs | 需要批准。 |
| NDv2 | 需要批准。 |
| NV | 无。 |
| NVv3 | 需要批准。 | 


虽然 Azure 机器学习支持这些 VM 系列，但它们可能不会在所有 Azure 区域中提供。 若要检查 VM 系列是否可用，请参阅 [按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)。

### <a name="compute-isolation"></a>计算隔离

Azure 机器学习计算提供隔离于特定硬件类型并专用于单个客户的 VM 大小。 独立 VM 大小最适合需要与其他客户的工作负荷进行高度隔离的工作负荷，原因包括满足符合性和法规要求。 利用隔离的大小可保证你的 VM 将是在该特定服务器实例上运行的唯一 VM。

当前隔离的 VM 产品/服务包括：

* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3*

*支持 RDMA

若要了解有关隔离的详细信息，请参阅 [Azure 公有云中的隔离](https://docs.microsoft.com/azure/security/fundamentals/isolation-choices)。

## <a name="unmanaged-compute"></a>非托管计算

非托管计算目标不是由 Azure 机器学习托管的。 在 Azure 机器学习之外创建这种类型的计算目标，然后将其附加到工作区。 对于非托管计算资源，可能需要执行额外的步骤才能保持或提高机器学习工作负荷的性能。

## <a name="next-steps"></a>后续步骤

了解如何：
* [使用计算目标训练模型](how-to-set-up-training-targets.md)
* [将模型部署到计算目标](how-to-deploy-and-where.md)
