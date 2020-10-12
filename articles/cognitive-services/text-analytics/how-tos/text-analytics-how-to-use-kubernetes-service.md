---
title: 运行 Azure Kubernetes 服务 - 文本分析
titleSuffix: Azure Cognitive Services
description: 将文本分析容器映像部署到 Azure Kubernetes 服务，并在 Web 浏览器中对其进行测试。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b7a5953edd9aec96a7f75e747c39e8f07f7210bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88243762"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>将文本分析容器部署到 Azure Kubernetes 服务

了解如何将 Azure 认知服务[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)容器映像部署到 Azure Kubernetes 服务 (AKS)。 该过程展示了创建文本分析资源的方法、创建关联的情绪分析映像的方法，以及在浏览器中练习前两项的相关业务流程的方法。 使用容器可以将你的关注点从管理基础结构转移到应用程序开发上。

## <a name="prerequisites"></a>先决条件

此过程要求必须在本地安装和运行多个工具。 请勿使用 Azure Cloud Shell。 需要满足以下条件：

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services)。
* 文本编辑器，例如 [Visual Studio Code](https://code.visualstudio.com/download)。
* 已安装 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* 已安装 [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。
* 具有适当定价层的 Azure 资源。 并非所有定价层都适用于此容器：
    * 仅具有 F0 或标准定价层的 Azure 文本分析资源。
    * 具有 S0 定价层的 Azure 认知服务资源。

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[关键短语提取](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[语言检测](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[情绪分析](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>后续步骤

* 使用更多 [认知服务容器](../../cognitive-services-container-support.md)
* 使用[文本分析连接服务](../vs-text-connected-service.md)
