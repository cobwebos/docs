---
title: 使用 Azure 机器学习服务部署模型的位置 | Microsoft 文档
description: 了解可使用 Azure 机器学习服务将模型部署到生产环境中的不同方法。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/29/2018
ms.openlocfilehash: 606e8aed42bce69d5b3210b4e97f8cbfeaaf104c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961002"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>使用 Azure 机器学习服务部署模型

Azure 机器学习服务提供多种部署已训练模型的方法。 本文档介绍了如何将模型作为 Web 服务部署在 Azure 云或 IoT 边缘设备中。

可以将模型部署到以下计算目标：

- Azure 容器实例 (ACI)
- Azure Kubernetes 服务 (AKS)
- Azure IoT Edge
- 现场可编程门阵列 (FPGA)

本文档的其余部分详细介绍了以上每个选项。

## <a name="azure-container-instances"></a>Azure 容器实例

如果满足以下一个或多个条件，请使用 Azure 容器实例将模型部署为 REST API 终结点：
- 想要实现快速部署，以便对模型进行评分和验证。 ACI 部署通常在 5 分钟内完成。
- 想要在开发或测试环境中部署模型。 ACI 支持每个订阅部署 20 个容器组。 有关详细信息，请参阅文档 [Azure 容器实例的配额和区域可用性](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)。

有关详细信息，请参阅文档[将模型部署到 Azure 容器实例](how-to-deploy-to-aci.md)。

## <a name="azure-kubernetes-service"></a>Azure Kubernetes 服务

对于大规模生产方案，可以将模型部署到 Azure Kubernetes 服务 (AKS)。 可以使用现有 AKS 群集，也可使用 Azure 机器学习 SDK、CLI 或 Azure 门户创建新群集。

创建 AKS 群集是工作区的一次性过程。 创建后，即可重复使用此群集进行多个部署。 如果删除该群集或包含该群集的资源组，则在下次需要进行部署时必须创建新群集。

通过部署到 AKS，可以为 Web 服务提供自动缩放、日志记录、模型数据收集和快速响应时间功能。 

创建 AKS 群集需要大约 20 分钟。

有关详细信息，请参阅文档[将模型部署到 Azure Kubernetes 服务](how-to-deploy-to-aks.md)。

## <a name="azure-iot-edge"></a>Azure IoT Edge

使用 IoT 设备，可更加快速地在设备上执行评分，而无需将数据发送到云并等待云托管模型返回数据。 使用 Azure IoT Edge，可在边缘设备上托管模型。 如果需要一个或多个以下功能，请将模型部署到 IoT Edge：
- 在本地处理优先级任务，甚至无需云连接
- 所用生成数据太大，无法快速从云端拉取
- 通过位于或接近本地设备的智能实现实时处理
- 满足数据隐私相关要求 

有关详细信息，请参阅文档[部署 Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)。

有关 IoT Edge 服务的详细信息，请参阅 [Azure IoT Edge 文档](https://docs.microsoft.com/azure/iot-edge/)。


## <a name="field-programmable-gate-arrays-fpga"></a>现场可编程门阵列 (FPGA)

使用 Project Brainwave 提供支持的硬件加速模型，可以实时推断请求的超低延迟。 Project Brainwave 可加快部署于 Azure 云中现场可编程门阵列上的深度神经网络 (DNN) 的速度。 常用 DNN 可用作传输学习的特征化器，或者可使用以自己的数据定型的权重进行自定义。

有关详细信息，请参阅文档[部署到 FPGA](how-to-deploy-fpga-web-service.md)。

## <a name="next-steps"></a>后续步骤

要详细了解如何将模型部署到特定计算目标，请参阅以下文档：

* [将模型部署到 Azure 容器实例](how-to-deploy-to-aci.md)
* [将模型部署到 Azure Kubernetes 服务](how-to-deploy-to-aks.md)
* [将模型部署到 Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)
* [将模型部署到 FPGA](how-to-deploy-fpga-web-service.md)
