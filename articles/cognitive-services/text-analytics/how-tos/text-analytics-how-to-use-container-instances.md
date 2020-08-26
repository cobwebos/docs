---
title: 运行 Azure 容器实例 - 文本分析
titleSuffix: Azure Cognitive Services
description: 将文本分析容器部署到 Azure 容器实例，并在 Web 浏览器中对其进行测试。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: f7dbb79e2d9009b00b21d4c12663d8e6c855a4c0
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2020
ms.locfileid: "88243745"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>将文本分析容器部署到 Azure 容器实例

了解如何将认知服务[文本分析][install-and-run-containers]容器部署到 Azure [容器实例][container-instances]。 该过程演示了创建文本分析资源的方法、创建关联的情绪分析映像的方法，以及在浏览器中练习前两项的相关业务流程的功能。 使用容器可以将开发人员的注意力从管理基础结构转移到应用程序开发上。

## <a name="prerequisites"></a>必备条件

* 使用 Azure 订阅。 如果你还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services)。

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[关键短语提取](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[语言检测](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[情绪分析](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

#### <a name="text-analytics-for-health"></a>[运行状况文本分析](#tab/health)

[!INCLUDE [Verify the health container instance](../includes/verify-health-container.md)]

***

## <a name="next-steps"></a>后续步骤 

* 使用更多[认知服务容器](../../cognitive-services-container-support.md)
* 使用[文本分析连接服务](../vs-text-connected-service.md)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances