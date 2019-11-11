---
title: 在 Azure 容器实例上部署 LUIS 容器
titleSuffix: Azure Cognitive Services
description: 将 LUIS 容器部署到 Azure 容器实例，并在 web 浏览器中对其进行测试。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: aecbb9bb94fc251ee0142b611c54d16304793e50
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2019
ms.locfileid: "73901813"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>将语言理解（LUIS）容器部署到 Azure 容器实例

了解如何将认知服务[LUIS](luis-container-howto.md)容器部署到 Azure[容器实例](https://docs.microsoft.com/azure/container-instances/)。 此过程演示如何创建异常探测器资源。 然后讨论如何拉取关联的容器映像。 最后，我们重点介绍了从浏览器中执行这两个业务流程的能力。 使用容器可以将开发人员的注意力从管理基础结构转移到应用程序开发上。

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
