---
title: 运行 Azure Kubernetes 服务-文本分析
titleSuffix: Azure Cognitive Services
description: 将文本分析容器映像部署到 Azure Kubernetes 服务，并在 web 浏览器中对其进行测试。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: dapine
ms.openlocfilehash: db1c26805f76b0ca8991a562531b4476b6899d86
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130081"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>将文本分析容器部署到 Azure Kubernetes 服务

了解如何将 Azure 认知服务[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)容器映像部署到 Azure Kubernetes 服务（AKS）。 此过程说明如何创建文本分析资源、如何创建关联的情绪分析映像, 以及如何从浏览器中执行这两种操作的业务流程。 使用容器可以从管理基础结构中转移, 而不是关注应用程序开发。

## <a name="prerequisites"></a>先决条件

此过程要求必须在本地安装和运行多个工具。 不要使用 Azure Cloud Shell。 需要满足以下条件：

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 文本编辑器, 例如[Visual Studio Code](https://code.visualstudio.com/download)。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)安装。
* [KUBERNETES CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/)已安装。
* 具有适当定价层的 Azure 资源。 并非所有定价层都适用于此容器：
    * 仅具有 F0 或标准定价层的**Azure 文本分析**资源。
    * 具有 S0 定价层的**Azure 认知服务**资源。

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extractiontabkeyphrase"></a>[关键短语提取](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detectiontablanguage"></a>[语言检测](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysistabsentiment"></a>[情绪分析](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>后续步骤

* 使用更多[认知服务容器](../../cognitive-services-container-support.md)
* 使用[文本分析连接服务](../vs-text-connected-service.md)
