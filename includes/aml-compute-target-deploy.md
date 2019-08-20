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
ms.date: 05/30/2019
ms.openlocfilehash: c5a6f957cd3f799f9d7eff1df22df2200779ac30
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946041"
---
| 计算目标 | 用法 | GPU 支持 | FPGA 支持 | 描述 |
| ----- | ----- | ----- | ----- | ----- |
| [本地&nbsp;web&nbsp;服务](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | 测试/调试 | 可能 | &nbsp; | 适用于有限的测试和故障排除。 硬件加速依赖于使用本地系统中的库。
| [笔记本 VM&nbsp;web&nbsp;服务](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | 测试/调试 | 可能 | &nbsp; | 适用于有限的测试和故障排除。 
| [Azure Kubernetes 服务 (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | 实时推理 |  [是](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [是](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |非常适合用于大规模生产部署。 提供所部署服务的快速响应时间和自动缩放。 不支持通过 Azure 机器学习 SDK 进行群集自动缩放。 若要更改 AKS 群集中的节点, 请在 Azure 门户中使用 AKS 群集的 UI。 AKS 是可用于视觉对象界面的唯一选项。 |
| [Azure 容器实例 (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | 测试或开发 | &nbsp;  | &nbsp; | 适用于低规模、需要 < 48 GB RAM 的基于 CPU 的工作负荷 |
| [Azure 机器学习计算](../articles/machine-learning/service/how-to-run-batch-predictions.md) | 效果批处理&nbsp;推理 | 是 | &nbsp;  | 对无服务器计算运行批处理评分。 支持普通 Vm 和低优先级 Vm。 |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | 效果IoT&nbsp;模块 |  &nbsp; | &nbsp; | 部署 & 在 IoT 设备上提供 ML 模型。 |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | 通过 IoT Edge |  &nbsp; | 是 | 部署 & 在 IoT 设备上提供 ML 模型。 |
