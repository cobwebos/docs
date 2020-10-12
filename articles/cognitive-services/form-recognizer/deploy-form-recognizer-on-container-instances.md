---
title: 在 Azure 容器实例中运行表单识别器容器
titleSuffix: Azure Cognitive Services
description: 将窗体识别器容器部署到 Azure 容器实例，并在 web 浏览器中对其进行测试。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: de81fcb5ee62f9b295c93132b271507c040af46a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86512745"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>将窗体识别器容器部署到 Azure 容器实例

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

了解如何将认知服务 [窗体识别器](form-recognizer-container-howto.md) 容器部署到 Azure [容器实例](https://docs.microsoft.com/azure/container-instances/)。 此过程演示如何创建 Azure 窗体识别器资源。 然后讨论如何拉取关联的容器映像。 最后，我们重点介绍了从浏览器中执行这两个业务流程的能力。 使用容器可以将开发人员的注意力从管理基础结构转移到应用程序开发上。

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
