---
title: 在 Azure 容器实例中运行异常检测器容器
titleSuffix: Azure Cognitive Services
description: 将异常检测器容器部署到 Azure 容器实例，并在 Web 浏览器中测试它。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a4973254c8a53917d99daf38b0d11e5dbecbb731
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875095"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>将异常检测器容器部署到 Azure 容器实例

了解如何将认知服务[异常检测器](../anomaly-detector-container-howto.md)容器部署到 Azure[容器实例](https://docs.microsoft.com/azure/container-instances/)。 此过程演示如何创建异常探测器资源。 然后讨论如何拉取关联的容器映像。 最后，我们重点介绍了从浏览器中执行这两个业务流程的能力。 使用容器可以将开发人员的注意力从管理基础结构转移到应用程序开发上。

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>请求访问专用容器注册表

您必须首先填写并提交[异常检测器容器请求表单](https://aka.ms/adcontainer)，以请求访问容器。

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>后续步骤

* 查看[安装和运行容器](../anomaly-detector-container-configuration.md)以拉取容器映像并运行容器
* 查看[配置容器](../anomaly-detector-container-configuration.md)的配置设置
* [了解有关异常检测器 API 服务的更多信息](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
