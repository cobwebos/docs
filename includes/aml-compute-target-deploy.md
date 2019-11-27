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
ms.date: 08/23/2019
ms.openlocfilehash: 1bf1202a635184ad7f0022cf5baa9d72e8547f14
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74549354"
---
| 计算目标 | 用途 | GPU 支持 | FPGA 支持 | 说明 |
| ----- | ----- | ----- | ----- | ----- |
| [本地&nbsp;web&nbsp;服务](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | 测试/调试 | &nbsp; | &nbsp; | 用于有限的测试和故障排除。 硬件加速依赖于本地系统中的库使用。
| [&nbsp;web&nbsp;服务 Azure 机器学习计算实例](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | 测试/调试 | &nbsp; | &nbsp; | 用于有限的测试和故障排除。
| [Azure Kubernetes 服务 (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | 实时推理 |  [是](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)（web 服务部署） | [是](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |用于大规模生产部署。 提供所部署服务的快速响应时间和自动缩放。 不支持通过 Azure 机器学习 SDK 进行群集自动缩放。 若要更改 AKS 群集中的节点，请在 Azure 门户中使用 AKS 群集的 UI。 AKS 是可用于设计器的唯一选项。 |
| [Azure 容器实例](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | 测试或开发 | &nbsp;  | &nbsp; | 用于需要小于 48 GB RAM 的基于 CPU 的低缩放工作负荷。 |
| [Azure 机器学习计算群集](../articles/machine-learning/service/how-to-run-batch-predictions.md) | 效果批处理&nbsp;推理 | [是](../articles/machine-learning/service/how-to-run-batch-predictions.md)（机器学习管道） | &nbsp;  | 对无服务器计算运行批处理评分。 支持普通 Vm 和低优先级 Vm。 |
| [Azure Functions](../articles/machine-learning/service/how-to-deploy-functions.md) | 效果实时推理 | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | 效果IoT&nbsp;模块 |  &nbsp; | &nbsp; | 在 IoT 设备上部署和服务 ML 模型。 |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | 通过 IoT Edge |  &nbsp; | 是 | 在 IoT 设备上部署和服务 ML 模型。 |

> [!NOTE]
> 尽管计算目标（如本地）、Azure 机器学习计算实例和 Azure 机器学习计算群集支持 GPU 进行定型和试验，但__在部署为 web 服务时__，仅在 Azure 上支持使用 gpu 进行推理Kubernetes 服务。
>
> 仅在 Azure 机器学习计算__时__，才支持使用 GPU 进行推理。