---
title: 发布区域和终结点
titleSuffix: Azure Cognitive Services
description: 发布 LUIS 应用的区域对应于创建 Azure LUIS 终结点密钥时在 Azure 门户中指定的区域或位置。 发布应用时，LUIS 会自动为与密钥关联的区域生成终结点 URL。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 5d8d3d4d55d4d03eb6d6a62898823158812d7c1f
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135825"
---
# <a name="regions-and-keys"></a>区域和密钥

发布 LUIS 应用的区域对应于创建 Azure LUIS 终结点密钥时在 Azure 门户中指定的区域或位置。 [发布应用](./luis-how-to-publish-app.md)时，LUIS 会自动为与密钥关联的区域生成终结点 URL。 若要将 LUIS 应用发布到多个区域，每个区域至少需要一个密钥。 

## <a name="luis-website"></a>LUIS 网站
LUIS 网站基于区域分为三个网站。 必须在同一区域中创建和发布应用。 

|LUIS|区域|
|--|--|
|[www.luis.ai][www.luis.ai]|美国<br>非欧洲<br>非澳大利亚|
|[au.luis.ai][au.luis.ai]|澳大利亚|
|[eu.luis.ai][eu.luis.ai]|欧洲|

## <a name="regions-and-azure-resources"></a>区域和 Azure 资源
该应用将发布到与 LUIS 门户中添加的 LUIS 资源关联的所有区域。 例如，对于在 [www.luis.ai][www.luis.ai] 上创建的应用，如果你在 **westus** 中创建 LUIS 资源并将其作为资源添加到该应用，则该应用将发布到该区域中。 

## <a name="public-apps"></a>公共应用
公共应用在所有区域中发布，以便有基于区域的 LUIS 资源密钥的用户可以在与其资源密钥关联的任何区域中访问该应用。

## <a name="publishing-regions"></a>发布区域

https://www.luis.ai 上创建的 LUIS 应用可以发布到除[欧洲](#publishing-to-europe)和[澳大利亚](#publishing-to-australia)区域之外的所有终结点。 

创作区域中的应用仅可发布到对应的发布区域。 如果应用目前位于错误的创作区域中，请导出应用，然后将其导入发布区域对应的正确创作区域。 

 全球区域 | 创作区域<br>`API region name` | 发布和查询区域<br>`API region name`   |   LUIS 网站 | 终结点 URL 格式   |
|-----|------|------|------|------|
| 亚洲 | 美国西部<br>`westus`| 东亚<br>`eastasia`     | [www.luis.ai][www.luis.ai] |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 亚洲 | 美国西部<br>`westus`| 东南亚<br>`souteastasia`     | [www.luis.ai][www.luis.ai] |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[澳大利亚](#publishing-to-australia) | 澳大利亚东部<br>`australiaeast`| 澳大利亚东部<br>`australiaeast`     |   [au.luis.ai][au.luis.ai] | https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[欧洲](#publishing-to-europe)| 西欧<br>`westeurope`| 北欧<br>`northeurope`     | [eu.luis.ai][eu.luis.ai]|  https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[欧洲](#publishing-to-europe) | 西欧<br>`westeurope`| 西欧<br>`westeurope`     | [eu.luis.ai][eu.luis.ai]|  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 北美 | 美国西部<br>`westus` | 美国东部<br>`eastus`      |[www.luis.ai][www.luis.ai] |   https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美 | 美国西部<br>`westus` | 美国东部 2<br>`eastus2`     | [www.luis.ai][www.luis.ai] |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美 | 美国西部<br>`westus` | 美国中南部<br>`southcentralus`     | [www.luis.ai][www.luis.ai] |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 北美 | 美国西部<br>`westus` | 美国中西部<br>`westcentralus`     |[www.luis.ai][www.luis.ai] |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美 | 美国西部<br>`westus` | 美国西部<br>`westus`  |  [www.luis.ai][www.luis.ai] | https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 北美 | 美国西部<br>`westus` | 美国西部 2<br>`westus2`    | [www.luis.ai][www.luis.ai] |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 南美洲 | 美国西部<br>`westus` | 巴西南部<br>`brazilsouth`     | [www.luis.ai][www.luis.ai] |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-europe"></a>发布到欧洲

若要发布到欧洲区域，请仅在 https://eu.luis.ai 创建 LUIS 应用。 如果尝试使用欧洲区域中的密钥将应用发布到其他区域，LUIS 会显示警告消息。 请改用 https://eu.luis.ai。 在 [https://eu.luis.ai][eu.luis.ai] 中创建的 LUIS 应用不会自动迁移到其他区域。 要实现迁移，请导出然后再导入 LUIS 应用。

## <a name="publishing-to-australia"></a>发布到澳大利亚

若要发布到澳大利亚区域，请仅在 https://au.luis.ai 创建 LUIS 应用。 如果尝试使用澳大利亚区域中的密钥将应用发布到其他区域，LUIS 会显示警告消息。 请改用 https://au.luis.ai。 在 [https://au.luis.ai][au.luis.ai] 中创建的 LUIS 应用不会自动迁移到其他区域。 要实现迁移，请导出然后再导入 LUIS 应用。

## <a name="endpoints"></a>终结点

LUIS 当前具有 2 个终结点：一个用于创作，一个用于文本分析。

|目的|代码|
|--|--|
|创作|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|文本分析（查询预测）|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

下表说明了上表中用大括号 `{}` 表示的参数。

|参数|目的|
|--|--|
|region|Azure 区域 - 创作和发布具有不同的区域|
|appID|在 URL 路由中使用的 LUIS 应用 ID，可在应用仪表板上找到|
|q|从客户端应用程序（如聊天机器人）发送的话语文本|


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [预生成实体参考](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai