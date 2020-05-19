---
title: 身份验证
titleSuffix: Azure Cognitive Services
description: 有三种方法可以对 Azure 认知服务资源的请求进行身份验证：订阅密钥、持有者令牌或多服务订阅。 本文介绍每种方法以及如何发出请求。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: erhopf
ms.openlocfilehash: d36961a12162a587def76b1ffeb2109f9ed63f4d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587674"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>对 Azure 认知服务的请求进行身份验证

对 Azure 认知服务的每个请求都必须包含身份验证标头。 此标头传递订阅密钥或访问令牌，用于验证服务或服务组订阅。 本文介绍三种对请求进行身份验证的方法以及每种方法的要求。

* [使用单服务订阅密钥进行身份验证](#authenticate-with-a-single-service-subscription-key)
* [使用多服务订阅密钥进行身份验证](#authenticate-with-a-multi-service-subscription-key)
* [使用令牌进行身份验证](#authenticate-with-an-authentication-token)
* [在 Azure Active Directory （AAD）中进行身份验证](#authenticate-with-azure-active-directory)

## <a name="prerequisites"></a>先决条件

在发出请求之前，需要具有 Azure 帐户和 Azure 认知服务订阅。 如果已有帐户，请继续并跳到下一节。 如果你没有帐户，可以在几分钟内完成设置：[为 Azure 创建认知服务帐户](cognitive-services-apis-create-account.md)。

创建帐户或激活[免费试用版](https://azure.microsoft.com/try/cognitive-services/my-apis)后，可以从[Azure 门户](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)获取订阅密钥。

## <a name="authentication-headers"></a>身份验证标头

让我们快速查看可用于 Azure 认知服务的身份验证标头。

| 标头 | 说明 |
|--------|-------------|
| Ocp-Apim-Subscription-Key | 使用此标头通过特定服务订阅密钥或多服务订阅密钥进行身份验证。 |
| Ocp-Apim-Subscription-Region | 仅当将多服务订阅密钥与[转换器服务](./Translator/reference/v3-0-reference.md)一起使用时，才需要此标头。 使用此标头指定订阅区域。 |
| 授权 | 如果使用的是身份验证令牌，则使用此标头。 以下各节详细介绍了执行令牌交换的步骤。 提供的值遵循以下格式：`Bearer <TOKEN>`。 |

## <a name="authenticate-with-a-single-service-subscription-key"></a>使用单服务订阅密钥进行身份验证

第一种方法是使用特定服务（例如转换器）的订阅密钥对请求进行身份验证。 Azure 门户中的密钥可用于已创建的每个资源。 要使用订阅密钥对请求进行身份验证，必须将其作为 `Ocp-Apim-Subscription-Key` 标头传递。

这些示例请求演示了如何使用 `Ocp-Apim-Subscription-Key` 标头。 请记住，使用此示例时，需要包括有效的订阅密钥。

这是对必应 Web 搜索 API 的调用示例：
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

这是对转换器服务的示例调用：
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

以下视频演示如何使用认知服务密钥。

## <a name="authenticate-with-a-multi-service-subscription-key"></a>使用多服务订阅密钥进行身份验证

>[!WARNING]
> 目前，以下服务**不**支持多服务密钥： QnA Maker、语音服务、自定义视觉和异常检测程序。

此选项仍使用订阅密钥对请求进行身份验证。 主要区别在于订阅密钥未绑定到特定服务，而单个密钥可用于对多个认知服务的请求进行身份验证。 有关区域可用性、支持的功能和定价的信息，请参阅[认知服务定价](https://azure.microsoft.com/pricing/details/cognitive-services/)。

订阅密钥在每个请求中作为 `Ocp-Apim-Subscription-Key` 标头提供。

[![针对认知服务的多服务订阅密钥演示](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>支持的区域

使用多服务订阅密钥向 `api.cognitive.microsoft.com` 发出请求时，必须在 URL 中包含该区域。 例如： `westus.api.cognitive.microsoft.com`。

将多服务订阅密钥与转换器服务一起使用时，必须使用标头指定订阅区域 `Ocp-Apim-Subscription-Region` 。

以下区域支持多服务身份验证：

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>示例请求

这是对必应 Web 搜索 API 的调用示例：

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

这是对转换器服务的示例调用：

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>使用身份验证令牌进行身份验证

某些 Azure 认知服务接受并在某些情况下需要身份验证令牌。 目前，以下服务支持身份验证令牌：

* 文本翻译 API
* 语音服务：语音到文本 REST API
* 语音服务：文本到语音 REST API

>[!NOTE]
> QnA Maker 也使用授权标头，但需要终结点密钥。 有关详细信息，请参阅[QnA Maker：从知识库获取答案](./qnamaker/quickstarts/get-answer-from-knowledge-base-using-url-tool.md)。

>[!WARNING]
> 支持身份验证令牌的服务可能会随时间而变化，请在使用此身份验证方法之前检查服务的 API 参考。

单服务订阅密钥和多服务订阅密钥均可用于交换认证令牌。 身份验证令牌有效期为 10 分钟。

身份验证令牌作为 `Authorization` 标头包含在请求中。 提供的令牌值必须以 `Bearer` 开头，例如：`Bearer YOUR_AUTH_TOKEN`。

### <a name="sample-requests"></a>示例请求

使用此 URL 交换身份验证令牌的订阅密钥：`https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`。

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

以下多服务区域支持令牌交换：

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

获得身份验证令牌后，需要在每个请求中将其作为 `Authorization` 标头传递。 这是对转换器服务的示例调用：

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

[!INCLUDE [](../../includes/cognitive-services-azure-active-directory-authentication.md)]

## <a name="see-also"></a>请参阅

* [什么是认知服务？](welcome.md)
* [认知服务定价](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [自定义子域](cognitive-services-custom-subdomains.md)
