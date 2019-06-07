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
ms.openlocfilehash: a463ac9f9584cb13cadbcf79674d56b2f8e47c2c
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753106"
---
| 计算目标 | 使用情况 | GPU 支持 | FPGA 支持 | 描述 |
| ----- | ----- | ----- | ----- | ----- |
| [本地 web 服务](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | 测试/调试 | 您可能 | &nbsp; | 适用于有限的测试和故障排除。 硬件加速取决于在本地系统中使用库。
| [Azure Kubernetes 服务 (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | 实时推理 |  [yes](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [yes](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |非常适合用于大规模生产部署。 提供了自动缩放和快速响应时间。  AKS 是唯一可用的可视界面的选项。 |
| [Azure 容器实例 (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | 测试或开发人员 | &nbsp;  | &nbsp; | 适用于小规模，需要基于 CPU 的工作负荷 < 为 48 GB RAM |
| [Azure 机器学习计算](../articles/machine-learning/service/how-to-run-batch-predictions.md) | （预览版）批处理推理 | 是 | &nbsp;  | 运行批处理评分上无服务器计算。 支持的普通和低优先级 Vm。 |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | （预览版）IoT 模块 |  &nbsp; | &nbsp; | 部署和 IoT 设备上提供机器学习模型。 |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | via IoT Edge |  &nbsp; | 是 | 部署和 IoT 设备上提供机器学习模型。 |