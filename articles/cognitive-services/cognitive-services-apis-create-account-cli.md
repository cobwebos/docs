---
title: 使用 Azure CLI 创建认知服务资源
titleSuffix: Azure Cognitive Services
description: 通过使用 Azure 命令行接口创建和订阅资源，开始使用 Azure 认知服务。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: aahi
ms.openlocfilehash: 5d3aba2f6010ba16656be96433c521d2adae4c58
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169159"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>使用 Azure 命令行接口（CLI）创建认知服务资源

使用此快速入门，通过[Azure 命令行界面（CLI）](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)开始使用 Azure 认知服务。 认知服务由你在 Azure 订阅中创建的 Azure[资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)表示。 创建资源后，请使用生成的密钥和终结点对应用程序进行身份验证。 


在本快速入门中，你将了解如何使用[Azure 命令行接口（CLI）](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)注册 Azure 认知服务并创建具有单一服务或多服务订阅的帐户。 这些服务由 Azure[资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)表示，使你能够连接到一个或多个 azure 认知服务 API。

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>必备组件

* 有效的 Azure 订阅-免费[创建一个](https://azure.microsoft.com/free/)。
* [Azure 命令行接口（CLI）](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>安装 Azure CLI 并登录 

安装 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 若要登录到 CLI 的本地安装，请运行[az login](https://docs.microsoft.com/cli/azure/reference-index#az-login)命令：

```console
az login
```

你还可以在浏览器中使用绿色的 "**试用**" 按钮来运行这些命令。
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>创建新的 Azure 认知服务资源组

在创建认知服务资源之前，必须具有 Azure 资源组才能包含资源。 创建新资源时，可以选择创建新的资源组或使用现有资源组。 本文介绍如何创建新的资源组。

### <a name="choose-your-resource-group-location"></a>选择资源组位置

若要创建资源，需要提供订阅的 Azure 位置之一。 您可以使用[az account list-位置](/cli/azure/account#az-account-list-locations)命令检索可用位置的列表。 大多数认知服务都可以从多个位置进行访问。 选择最靠近你的帐户，或查看哪些位置可用于服务。

> [!IMPORTANT]
> * 请记住你的 Azure 位置，因为在调用 Azure 认知服务时需要用到它。
> * 某些认知服务的可用性可能因区域而异。 有关详细信息，请参阅[Azure 产品（按区域](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)）。  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

获得 azure 位置之后，使用[az group create](/cli/azure/group#az-group-create)命令在 Azure CLI 中创建新的资源组。

在下面的示例中，将 azure location `westus2` 替换为适用于订阅的 Azure 位置之一。

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>创建认知服务资源

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>选择认知服务和定价层

创建新资源时，需要知道要使用的服务的 "种类"，以及所需的[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/)（或 sku）。 创建资源时，将使用此信息和其他信息作为参数。

### <a name="multi-service"></a>多服务

| 服务                    | 种类                      |
|----------------------------|---------------------------|
| 多个服务。 有关更多详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/)页。            | `CognitiveServices`     |


> [!NOTE]
> 下面的许多认知服务都有一个免费层，可用于尝试该服务。 若要使用免费层，请使用 `F0` 作为资源的 sku。

### <a name="vision"></a>影像

| 服务                    | 种类                      |
|----------------------------|---------------------------|
| 计算机影像            | `ComputerVision`          |
| 自定义视觉-预测 | `CustomVision.Prediction` |
| 自定义视觉-定型   | `CustomVision.Training`   |
| 人脸                       | `Face`                    |
| 表单识别器            | `FormRecognizer`          |
| 墨迹识别器             | `InkRecognizer`           |

### <a name="search"></a>搜索

| 服务            | 种类                  |
|--------------------|-----------------------|
| 必应自动推荐   | `Bing.Autosuggest.v7` |
| 必应自定义搜索 | `Bing.CustomSearch`   |
| 必应实体搜索 | `Bing.EntitySearch`   |
| 必应搜索        | `Bing.Search.v7`      |
| 必应拼写检查   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>语音

| 服务            | 种类                 |
|--------------------|----------------------|
| 语音服务    | `SpeechServices`     |
| 语音识别 | `SpeakerRecognition` |

### <a name="language"></a>语言

| 服务            | 种类                |
|--------------------|---------------------|
| 窗体理解 | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| 文本分析     | `TextAnalytics`     |
| 文本翻译   | `TextTranslation`   |

### <a name="decision"></a>决策

| 服务           | 种类               |
|-------------------|--------------------|
| 异常检测器  | `AnomalyDetector`  |
| 内容审查器 | `ContentModerator` |
| 个性化体验创建服务      | `Personalizer`     |

你可以使用[az cognitiveservices account account list](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) command 命令查找可用认知服务 "种类" 的列表：

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>将新资源添加到资源组

若要创建并订阅新的认知服务资源，请使用[az cognitiveservices account account create](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create)命令。 此命令向之前创建的资源组添加新的可计费资源。 创建新资源时，需要知道要使用的服务的 "类型"，以及其定价层（或 sku）和 Azure 位置：

可以使用以下命令，为 `anomaly-detector-resource` 的异常探测器创建一个 F0 （免费）资源。

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-resource"></a>获取资源的密钥

若要登录到命令行接口（CLI）的本地安装，请使用[az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login)命令。

```console
az login
```

使用[az cognitiveservices account account keys list](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list)命令获取认知服务资源的密钥。

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>定价层和计费

定价层（以及您收取的费用）基于使用身份验证信息发送的事务数。 每个定价层指定：
* 每秒允许的最大事务数（TPS）。
* 在定价层中启用的服务功能。
* 预定义事务量的成本。 超出此数量将导致服务的[定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)中指定额外的费用。

## <a name="get-current-quota-usage-for-your-resource"></a>获取资源的当前配额使用率

使用[az cognitiveservices account account list 使用条款](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-usage)命令获取认知服务资源的使用情况。

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>清理资源

如果要清理并删除认知服务资源，则可以将其删除或资源组。 删除资源组也会删除组中包含的任何其他资源。

若要删除资源组及其关联的资源，请使用 az group delete 命令。

```azurecli-interactive
az group delete --name storage-resource-group
```

## <a name="see-also"></a>另请参阅

* [对 Azure 认知服务的请求进行身份验证](authentication.md)
* [什么是 Azure 认知服务？](Welcome.md)
* [自然的语言支持](language-support.md)
* [Docker 容器支持](cognitive-services-container-support.md)
