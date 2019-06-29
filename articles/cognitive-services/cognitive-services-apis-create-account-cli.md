---
title: 创建认知服务帐户使用 Azure CLI
titlesuffix: Azure Cognitive Services
description: 如何创建使用 Azure CLI 的 Azure 认知服务 Api 帐户。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: 26f7f3ab60347d9ec5f2a144410ad3de436f5b5c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454891"
---
# <a name="create-a-cognitive-services-account-using-the-azure-command-line-interfacecli"></a>创建认知服务帐户使用 Azure 命令行 Interface(CLI)

在此快速入门中，将了解如何注册 Azure 认知服务并创建具有单一服务或多服务订阅的帐户使用[Azure 命令行 Interface(CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 这些服务由 Azure[资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)，使你能够连接到一个或多个 Azure 认知服务 Api。

## <a name="prerequisites"></a>必备组件

* 有效的 Azure 订阅。 免费[创建一个帐户](https://azure.microsoft.com/free/)。
* [Azure 命令行 Interface(CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="install-the-azure-cli-and-sign-in"></a>安装 Azure CLI 并登录 

安装 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 若要登录到本地安装的 cli，运行[az 登录](https://docs.microsoft.com/cli/azure/reference-index#az-login)命令：

```console
az login
```

你还可以使用绿色**试用**按钮以在浏览器中运行以下命令。
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>创建新的 Azure 认知服务资源组

你的订阅到认知服务由 Azure 资源表示。 每个认知服务帐户 （和其关联的 Azure 资源） 必须属于 Azure 资源组。

### <a name="choose-your-resource-group-location"></a>选择你的资源组位置

若要创建资源时，将为你的订阅需要一个可用的 Azure 位置。 您可以检索与可用位置的列表[az 帐户列出位置](/cli/azure/account#az_account_list)命令。 可以从多个位置访问大多数认知服务。 选择最靠近你，或查看哪些位置可用的服务。

> [!IMPORTANT]
> * 请记住你的 Azure 位置，因为调用 Azure 认知服务时，将需要它。
> * 一些认知服务的可用性可能因区域而异。 有关详细信息，请参阅[按区域的 Azure 产品](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)。  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

你的 azure 位置后，使用在 Azure CLI 中创建新的资源组[az 组创建](/cli/azure/group#az_group_create)命令。

在以下示例中，将替换为 azure 位置`westus2`适用于你的订阅的 Azure 位置之一。

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>创建认知服务资源

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>选择认知服务和定价层

在创建新资源时，你将需要知道你想要使用，以及与服务的"类型"[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/)（或 sku） 所需。 创建资源时，将作为参数使用此和其他信息。

> [!NOTE]
> 许多认知服务提供可用来试用此服务免费层。 若要使用免费层，使用`F0`作为所需的资源的 sku。

### <a name="vision"></a>影像

| 服务                    | 种类                      |
|----------------------------|---------------------------|
| 计算机视觉            | `ComputerVision`          |
| 自定义影像的预测 | `CustomVision.Prediction` |
| 自定义视觉的培训   | `CustomVision.Training`   |
| 人脸 API                   | `Face`                    |
| 表单识别器            | `FormRecognizer`          |
| 墨迹识别器             | `InkRecognizer`           |

### <a name="search"></a>搜索

| 服务            | 种类                  |
|--------------------|-----------------------|
| 必应自动建议   | `Bing.Autosuggest.v7` |
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
| 了解窗体 | `FormUnderstanding` |
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

可以查找可用的认知服务的列表"类型" [az cognitiveservices 帐户列表类型](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds)命令：

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>将新的资源添加到你的资源组

若要创建并订阅新的认知服务资源，请使用[az cognitiveservices 帐户创建](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create)命令。 此命令将新的可计费资源添加到前面创建的资源组。 在创建新的资源时，将需要知道你想要使用，以及其定价层 （或 sku） 的服务的"类型"和 Azure 位置：

您可以为异常情况检测程序，名为创建 F0 （免费） 资源`anomaly-detector-resource`使用以下命令。

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-subscription"></a>获取订阅密钥

若要登录到本地安装的命令行 Interface(CLI) 时，使用[az 登录](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login)命令。

```console
az login
```

使用[az cognitiveservices 帐户密钥列表](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list)命令来获取有关认知服务资源的密钥。

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>清理资源

如果你想要清理和删除认知服务订阅，则可以删除资源或资源组。 删除资源组也会删除与资源组相关联的任何其他资源。

若要删除资源组和其关联的资源，包括新的存储帐户，使用 az group delete 命令。

```azurecli-interactive
az group delete --name storage-resource-group
```

## <a name="see-also"></a>另请参阅

* [对 Azure 认知服务的请求进行身份验证](authentication.md)
* [什么是 Azure 认知服务？](Welcome.md)
* [自然语言支持](language-support.md)
* [Docker 容器支持](cognitive-services-container-support.md)
