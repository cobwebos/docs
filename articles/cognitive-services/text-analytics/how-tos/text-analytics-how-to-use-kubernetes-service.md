---
title: 运行 Azure 库伯奈斯服务 - 文本分析
titleSuffix: Azure Cognitive Services
description: 将文本分析容器映像部署到 Azure 库伯奈斯服务，并在 Web 浏览器中测试它。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 155f32ee76f69fe0f16e7698123381fdc12efd0e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877798"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>将文本分析容器部署到 Azure 库伯奈斯服务

了解如何将 Azure 认知服务[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)容器映像部署到 Azure 库伯奈斯服务 （AKS）。 此过程演示如何创建文本分析资源、如何创建关联的情绪分析图像以及如何从浏览器执行这两个业务流程。 使用容器可以将注意力从管理基础结构转移到专注于应用程序开发上。

## <a name="prerequisites"></a>先决条件

此过程要求必须在本地安装和运行多个工具。 不要使用 Azure 云外壳。 需要满足以下条件：

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 文本编辑器，例如[，可视化工作室代码](https://code.visualstudio.com/download)。
* 已安装[Azure CLI。](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [库伯内斯 CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/)已安装。
* 具有适当定价层的 Azure 资源。 并非所有定价层都适用于此容器：
    * **Azure 文本分析**资源，仅具有 F0 或标准定价层。
    * 具有 S0 定价层的**Azure 认知服务**资源。

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

* 使用更多[认知服务容器](../../cognitive-services-container-support.md)
* 使用[文本分析连接服务](../vs-text-connected-service.md)
