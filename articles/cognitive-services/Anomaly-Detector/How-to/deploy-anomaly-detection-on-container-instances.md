---
title: 运行 Azure 容器实例
titleSuffix: Azure Cognitive Services
description: 将异常情况检测器容器部署到 Azure 容器实例，并在 web 浏览器中进行测试。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: b5adc3ed9234050d3977e812202717a0ce83e842
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711688"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>将异常情况检测器容器部署到 Azure 容器实例

了解如何部署认知服务[异常情况检测器](../anomaly-detector-container-howto.md)到 Azure 容器[容器实例](https://docs.microsoft.com/azure/container-instances/)。 此过程说明了创建异常情况检测程序资源。 然后我们将讨论拉取相关联的容器映像。 最后，我们重点介绍能够练习从浏览器两个业务流程。 使用容器可以移动开发人员的注意力从管理到改为将重点放在应用程序开发的基础结构。

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>请求访问专用容器注册表

必须先完成并提交[异常情况检测器容器请求窗体](https://aka.ms/adcontainer)容器请求访问。

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]