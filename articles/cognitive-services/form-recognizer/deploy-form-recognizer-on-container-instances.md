---
title: 运行 Azure 容器实例
titleSuffix: Azure Cognitive Services
description: 将窗体识别器容器部署到 Azure 容器实例，并在 web 浏览器中进行测试。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 3c424465678a9989940d92910c5d288fa2fb1cab
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711391"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>将窗体识别器容器部署到 Azure 容器实例

了解如何部署认知服务[窗体识别器](form-recognizer-container-howto.md)到 Azure 容器[容器实例](https://docs.microsoft.com/azure/container-instances/)。 此过程演示了如何创建 Azure 窗体识别器资源。 然后我们将讨论拉取相关联的容器映像。 最后，我们重点介绍能够练习从浏览器两个业务流程。 使用容器可以移动开发人员的注意力从管理到改为将重点放在应用程序开发的基础结构。

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>请求访问容器注册表

在请求访问该容器之前，必须先填写并提交[认知服务表单识别器容器访问请求表单](https://aka.ms/FormRecognizerRequestAccess)。 这也会注册计算机视觉。 不需要单独注册计算机视觉请求表单。 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
