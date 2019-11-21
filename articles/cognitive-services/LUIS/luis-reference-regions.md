---
title: Publishing regions & endpoints - LUIS
titleSuffix: Azure Cognitive Services
description: The region specified in the Azure portal is the same where you will publish the LUIS app and an endpoint URL is generated for this same region.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 4f8e5d1c5ac3b0383fee5a901d2bb34edab8dae2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225495"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>创作和发布区域及关联的密钥

Three authoring regions are supported by corresponding LUIS portals. 若要将 LUIS 应用发布到多个区域，每个区域至少需要一个密钥。 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS Authoring regions
There are three LUIS authoring portals, based on region. 必须在同一区域中创建和发布应用。 

|LUIS|创作区域|Azure region name|
|--|--|--|
|[www.luis.ai][www.luis.ai] <br>[preview.luis.ai](https://preview.luis.ai)|美属<br>非欧洲<br>非澳大利亚| `westus`|
|[au.luis.ai][au.luis.ai] <br>[preview.au.luis.ai](https://preview.au.luis.ai)|澳大利亚| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] <br>[preview.eu.luis.ai](https://preview.eu.luis.ai)|欧洲|`westeurope`|

Authoring regions have [paired fail-over regions](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publishing regions and Azure resources
该应用将发布到与 LUIS 门户中添加的 LUIS 资源关联的所有区域。 For example, for an app created on [www.luis.ai][www.luis.ai], if you create a LUIS or Cognitive Service resource in **westus** and [add it to the app as a resource](luis-how-to-azure-subscription.md), the app is published in that region. 

## <a name="public-apps"></a>公共应用
公共应用在所有区域中发布，以便有基于区域的 LUIS 资源密钥的用户可以在与其资源密钥关联的任何区域中访问该应用。

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Publishing regions are tied to authoring regions

创作区域中的应用仅可发布到对应的发布区域。 如果应用目前位于错误的创作区域中，请导出应用，然后将其导入发布区域对应的正确创作区域。 

https://www.luis.ai 上创建的 LUIS 应用可以发布到除[欧洲](#publishing-to-europe)和[澳大利亚](#publishing-to-australia)区域之外的所有终结点。 

## <a name="publishing-to-europe"></a>发布到欧洲

若要发布到欧洲区域，请仅在 https://eu.luis.ai 创建 LUIS 应用。 如果尝试使用欧洲区域中的密钥将应用发布到其他区域，LUIS 会显示警告消息。 请改用 https://eu.luis.ai 。 LUIS apps created at [https://eu.luis.ai][eu.luis.ai] don't automatically migrate to other regions. 要实现迁移，请导出然后再导入 LUIS 应用。

## <a name="europe-publishing-regions"></a>Europe publishing regions

 全球区域 | 创作 API 区域和创作网站| 发布和查询区域<br>`API region name`   |  终结点 URL 格式   |
|-----|------|------|------|
| [欧洲](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 法国中部<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [欧洲](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 北欧<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [欧洲](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 欧洲西部<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [欧洲](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 英国南部<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>发布到澳大利亚

若要发布到澳大利亚区域，请仅在 https://au.luis.ai 创建 LUIS 应用。 如果尝试使用澳大利亚区域中的密钥将应用发布到其他区域，LUIS 会显示警告消息。 请改用 https://au.luis.ai 。 LUIS apps created at [https://au.luis.ai][au.luis.ai] don't automatically migrate to other regions. 要实现迁移，请导出然后再导入 LUIS 应用。

## <a name="australia-publishing-regions"></a>Australia publishing regions

 全球区域 | 创作 API 区域和创作网站| 发布和查询区域<br>`API region name`   |  终结点 URL 格式   |
|-----|------|------|------|
| [澳大利亚](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| 澳大利亚东部<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Publishing to other regions

To publish to the other regions, you create LUIS apps at [https://www.luis.ai](https://www.luis.ai) only. 

## <a name="other-publishing-regions"></a>Other publishing regions

 全球区域 | 创作 API 区域和创作网站| 发布和查询区域<br>`API region name`   |  终结点 URL 格式   |
|-----|------|------|------|
| 非洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 南非北部<br>`southafricanorth` |  https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 亚洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 印度中部<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 亚洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 亚洲东部<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 亚洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 日本东部<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 亚洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 日本西部<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 亚洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 韩国中部<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 亚洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 亚洲东南部<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美 |`westus`<br>[www.luis.ai][www.luis.ai] | 加拿大中部<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美 |`westus`<br>[www.luis.ai][www.luis.ai] | 美国中部<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美 |`westus`<br>[www.luis.ai][www.luis.ai] | 美国东部<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美 | `westus`<br>[www.luis.ai][www.luis.ai] | 美国东部 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美 | `westus`<br>[www.luis.ai][www.luis.ai] | 美国中北部<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 北美 | `westus`<br>[www.luis.ai][www.luis.ai] | 美国中南部<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 北美 |`westus`<br>[www.luis.ai][www.luis.ai] | 美国中西部<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美 | `westus`<br>[www.luis.ai][www.luis.ai] | 美国西部<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 北美 |`westus`<br>[www.luis.ai][www.luis.ai] | 美国西部 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 南美 | `westus`<br>[www.luis.ai][www.luis.ai] | 巴西南部<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>终结点

LUIS currently has 2 endpoints: one for authoring and one for query prediction analysis.

|用途|URL|
|--|--|
|创作|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|文本分析（查询预测）|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

下表说明了上表中用大括号 `{}` 表示的参数。

|参数|用途|
|--|--|
|region|Azure 区域 - 创作和发布具有不同的区域|
|appID|在 URL 路由中使用的 LUIS 应用 ID，可在应用仪表板上找到|
|q|从客户端应用程序（如聊天机器人）发送的话语文本|

## <a name="failover-regions"></a>Failover regions

Each region has a secondary region to fail over to. Europe fails over inside Europe and Australia fails over inside Australia.

Authoring regions have [paired fail-over regions](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [预生成实体参考](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
