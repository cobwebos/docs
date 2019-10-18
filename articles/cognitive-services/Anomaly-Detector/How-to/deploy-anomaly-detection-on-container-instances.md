---
title: 在 Azure 容器实例中运行异常探测器容器
titleSuffix: Azure Cognitive Services
description: 将异常探测器容器部署到 Azure 容器实例，并在 web 浏览器中对其进行测试。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 52d2e3ed59a7ad71177f34909e37ce885d603297
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515269"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>将异常探测器容器部署到 Azure 容器实例

了解如何将认知服务[异常检测](../anomaly-detector-container-howto.md)容器容器部署到 Azure[容器实例](https://docs.microsoft.com/azure/container-instances/)。 此过程演示如何创建异常探测器资源。 然后讨论如何拉取关联的容器映像。 最后，我们重点介绍了从浏览器中执行这两个业务流程的能力。 使用容器可以将开发人员的注意力转向管理基础结构，而不是专注于应用程序开发。

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>请求访问专用容器注册表

必须先完成并提交[异常探测器容器请求窗体](https://aka.ms/adcontainer)，才能请求访问容器。

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>后续步骤

* 查看[安装并运行](../anomaly-detector-container-configuration.md)容器以拉取容器映像并运行容器
* 查看[配置容器](../anomaly-detector-container-configuration.md)了解配置设置
* [详细了解异常探测器 API 服务](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
