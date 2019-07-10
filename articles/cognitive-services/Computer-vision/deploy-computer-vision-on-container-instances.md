---
title: 运行 Azure 容器实例
titleSuffix: Azure Cognitive Services
description: 将计算机视觉容器部署到 Azure 容器实例，并在 web 浏览器中进行测试。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 859147d23ea78abac2da4a4c2f1fa26a8d976d02
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711611"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>将计算机视觉容器部署到 Azure 容器实例

了解如何部署认知服务[计算机视觉](computer-vision-how-to-install-containers.md)到 Azure 容器[容器实例](https://docs.microsoft.com/azure/container-instances/)。 此过程演示了如何创建计算机视觉资源。 然后我们将讨论拉取相关联的容器映像。 最后，我们重点介绍能够练习从浏览器两个业务流程。 使用容器可以移动开发人员的注意力从管理到改为将重点放在应用程序开发的基础结构。

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>请求访问专用容器注册表

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]