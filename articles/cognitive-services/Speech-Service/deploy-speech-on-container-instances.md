---
title: 运行 Azure 容器实例
titleSuffix: Azure Cognitive Services
description: 将语音服务容器部署到 Azure 容器实例，并在 web 浏览器中进行测试。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 062765be22135b12abb29ff6f7ce8a772c67adae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711512"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>将语音服务容器部署到 Azure 容器实例

了解如何部署认知服务[语音服务](speech-container-howto.md)到 Azure 容器[容器实例](https://docs.microsoft.com/azure/container-instances/)。 此过程演示了如何创建 Azure 语音服务资源。 然后我们将讨论拉取相关联的容器映像。 最后，我们重点介绍能够练习从浏览器两个业务流程。 使用容器可以移动开发人员的注意力从管理到改为将重点放在应用程序开发的基础结构。

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>请求访问容器注册表

必须先完成并提交[认知服务语音容器请求窗体](https://aka.ms/speechcontainerspreview/)容器请求访问。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech Service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create an Speech Service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
