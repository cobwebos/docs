---
title: 在 Azure 容器实例中运行表单识别器容器
titleSuffix: Azure Cognitive Services
description: 将窗体识别器容器部署到 Azure 容器实例，并在 web 浏览器中对其进行测试。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: c45d0e316cf67d22095911fadfd3d84ef407e1b0
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515629"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>将窗体识别器容器部署到 Azure 容器实例

了解如何将认知服务[窗体识别器](form-recognizer-container-howto.md)容器部署到 Azure[容器实例](https://docs.microsoft.com/azure/container-instances/)。 此过程演示如何创建 Azure 窗体识别器资源。 然后讨论如何拉取关联的容器映像。 最后，我们重点介绍了从浏览器中执行这两个业务流程的能力。 使用容器可以将开发人员的注意力转向管理基础结构，而不是专注于应用程序开发。

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>请求访问容器注册表

在请求访问该容器之前，必须先填写并提交[认知服务表单识别器容器访问请求表单](https://aka.ms/FormRecognizerRequestAccess)。 这也会注册计算机视觉。 不需要单独注册计算机视觉请求表单。 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
