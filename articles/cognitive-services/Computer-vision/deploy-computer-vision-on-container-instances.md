---
title: 在 Azure 容器实例中运行计算机视觉容器
titleSuffix: Azure Cognitive Services
description: 将计算机视觉容器部署到 Azure 容器实例，并在 web 浏览器中对其进行测试。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 2de879d271ab001e62725424ea72e0604176efb4
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516874"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>将计算机视觉容器部署到 Azure 容器实例

了解如何将认知服务[计算机视觉](computer-vision-how-to-install-containers.md)容器部署到 Azure[容器实例](https://docs.microsoft.com/azure/container-instances/)。 此过程演示计算机视觉资源的创建。 然后讨论如何拉取关联的容器映像。 最后，我们重点介绍了从浏览器中执行这两个业务流程的能力。 使用容器可以将开发人员的注意力转向管理基础结构，而不是专注于应用程序开发。

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>请求访问专用容器注册表

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]