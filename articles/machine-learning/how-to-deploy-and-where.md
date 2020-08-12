---
title: 部署模型的方式和位置
titleSuffix: Azure Machine Learning
description: 了解部署 Azure 机器学习模型（包括 Azure 容器实例、Azure Kubernetes 服务、Azure IoT Edge 和现场可编程门阵列）的方式和位置。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
zone_pivot_groups: aml-control-methods
ms.openlocfilehash: 439bf51aac4d04ee12968588a4e38c4643b4808a
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120333"
---
# <a name="deploy-models-with-azure-machine-learning"></a>使用 Azure 机器学习部署模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

了解如何将机器学习模型作为 Web 服务部署在 Azure 云或 Azure IoT Edge 设备中。

无论你在何处部署模型，工作流都是类似的：

1. 注册模型。
1. 准备推理配置
1. 准备条目脚本 (，除非使用[无代码部署](how-to-deploy-no-code-deployment.md)) 
1. 将模型部署到计算目标。
1. 测试已部署的模型（也称为“Web 服务”）。

有关部署工作流涉及的概念的详细信息，请参阅[使用 Azure 机器学习管理、部署和监视模型](concept-model-management-and-deployment.md)。


::: zone pivot="py-sdk"

[!INCLUDE [SDK quickstart](../../includes/machine-learning-how-to-deploy-and-where-sdk.md)]

::: zone-end

::: zone pivot="cli"

[!INCLUDE [CLI quickstart](../../includes/machine-learning-how-to-deploy-and-where-cli.md)]

::: zone-end

### <a name="understanding-service-state"></a>了解服务状态

在模型部署期间，当模型完全部署时，你可能会看到服务状态发生更改。

下表描述了各种服务状态：

| Webservice 状态 | 说明 | 最终状态？
| ----- | ----- | ----- |
| 正在转换 | 此服务正在进行部署。 | 否 |
| 不正常 | 此服务已部署，但当前无法访问。  | 否 |
| 不可安排 | 由于缺少资源，此时无法部署此服务。 | 否 |
| 已失败 | 由于出现错误或崩溃，服务未能部署。 | 是 |
| 正常 | 服务正常，终结点可用。 | 是 |


### <a name="batch-inference"></a><a id="azuremlcompute"></a> 批量推理
Azure 机器学习计算目标由 Azure 机器学习创建和管理。 它们可用于 Azure 机器学习管道中的批量预测。

若要查看使用 Azure 机器学习计算进行批量推理的演练，请参阅[如何运行批量预测](tutorial-pipeline-batch-scoring-classification.md)。

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge 推理
对部署到边缘的支持处于预览阶段。 有关详细信息，请参阅[将 Azure 机器学习部署为 IoT Edge 模块](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)。


## <a name="next-steps"></a>后续步骤

* [排查失败的部署问题](how-to-troubleshoot-deployment.md)
* [部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md)
* [创建使用 web 服务的客户端应用程序](how-to-consume-web-service.md)
* [更新 Web 服务](how-to-deploy-update-web-service.md)
* [如何使用自定义 Docker 映像部署模型](how-to-deploy-custom-docker-image.md)
* [使用 TLS 通过 Azure 机器学习保护 Web 服务](how-to-secure-web-service.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
* [为模型部署创建事件警报和触发器](how-to-use-event-grid.md)

