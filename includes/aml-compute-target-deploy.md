---
title: include 文件
description: include 文件
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 06/25/2020
ms.openlocfilehash: bd3ac8d512c1b9d151c0dc549ffeee6a05c7f94b
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542757"
---
用于托管模型的计算目标将影响部署的终结点的成本和可用性。 使用下表选择合适的计算目标。

| 计算目标 | 用途 | GPU 支持 | FPGA 支持 | 说明 |
| ----- | ----- | ----- | ----- | ----- |
| [本地&nbsp;web&nbsp;服务](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | 测试/调试 | &nbsp; | &nbsp; | 用于有限的测试和故障排除。 硬件加速依赖于本地系统中库的使用情况。
| [Azure 机器学习计算实例&nbsp;web&nbsp;服务](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | 测试/调试 | &nbsp; | &nbsp; | 用于有限的测试和故障排除。
| [Azure Kubernetes 服务 (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | 实时推理 |  [是](../articles/machine-learning/how-to-deploy-inferencing-gpus.md)（Web 服务部署） | [是](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |用于大规模生产部署。 提供所部署服务的快速响应时间和自动缩放。 不支持通过 Azure 机器学习 SDK 进行群集自动缩放。 若要更改 AKS 群集中的节点，请在 Azure 门户中使用 AKS 群集的 UI。 AKS 是可用于设计器的唯一选项。 |
| [Azure 容器实例](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | 测试或开发 | &nbsp;  | &nbsp; | 用于需要小于 48 GB RAM 的基于 CPU 的小规模工作负载。 |
| [Azure 机器学习计算群集](../articles/machine-learning/how-to-use-parallel-run-step.md) | 批处理&nbsp;推理 | [是](../articles/machine-learning/how-to-use-parallel-run-step.md)（机器学习管道） | &nbsp;  | 对无服务器计算运行批量评分。 支持普通 VM 和低优先级 VM。 |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | （预览）实时推理 | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | （预览）IoT&nbsp;模块 |  &nbsp; | &nbsp; | 在 IoT 设备上部署和提供 ML 模型。 |
| [Azure Data Box Edge](../articles/databox-online/azure-stack-edge-overview.md)   | 通过 IoT Edge |  &nbsp; | 是 | 在 IoT 设备上部署和提供 ML 模型。 |

> [!NOTE]
> 尽管计算目标（例如本地、Azure 机器学习计算实例和 Azure 机器学习计算群集）支持用于训练和试验的 GPU，但在__部署为 Web 服务__时，使用 GPU 进行推理仅在 Azure Kubernetes 服务中受支持。
>
> 只有在 Azure 机器学习计算上，才能__在通过机器学习管道评分时__使用 GPU 进行推理。

> [!NOTE]
> * Azure 容器实例 (ACI) 仅适用于小于 1 GB 的小模型。 
> * 我们建议为较大模型的开发测试使用单节点 Azure Kubernetes 服务 (AKS) 群集。