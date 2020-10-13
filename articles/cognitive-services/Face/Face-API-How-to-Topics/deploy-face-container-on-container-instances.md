---
title: 在 Azure 容器实例中运行人脸容器
titleSuffix: Azure Cognitive Services
description: 将人脸容器部署到 Azure 容器实例，并在 Web 浏览器中对其进行测试。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: 5fbe316580cfa2ca280a2587536df037146e8d02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86538116"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>将人脸容器部署到 Azure 容器实例

> [!IMPORTANT]
> 已达到人脸容器用户的限制。 目前不接受人脸容器的新应用程序。

了解如何将认知服务[人脸](../face-how-to-install-containers.md)容器部署到 Azure [容器实例](https://docs.microsoft.com/azure/container-instances/)。 此过程演示如何创建 Azure 人脸资源。 然后讨论如何拉取关联的容器映像。 最后，我们重点介绍了从浏览器中执行这两个业务流程的能力。 使用容器可以将开发人员的注意力从管理基础结构转移到应用程序开发上。

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]