---
title: 语言理解 (LUIS) 区域 | Microsoft Docs
titleSuffix: Azure
description: 本文包含 LUIS 网站的 LUIS 区域、Azure 订阅和世界区域的列表。
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 86a20770178707f72cf2991ca08b6b98eaeaf0cf
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36236343"
---
# <a name="regions-and-keys"></a>区域和密钥

发布 LUIS 应用的区域对应于创建 Azure LUIS 终结点密钥时在 Azure 门户中指定的区域或位置。 [发布应用](./PublishApp.md)时，LUIS 会自动为与密钥关联的区域生成终结点 URL。 若要将 LUIS 应用发布到多个区域，每个区域至少需要一个密钥。 

## <a name="luis-website"></a>LUIS 网站
LUIS 网站基于区域分为三个网站。 必须在同一区域中创建和发布应用。 

|LUIS|区域|
|--|--|
|[www.luis.ai][www.luis.ai]|美国<br>非欧洲<br>非澳大利亚|
|[au.luis.ai][au.luis.ai]|澳大利亚|
|[eu.luis.ai][eu.luis.ai]|欧洲|


## <a name="publishing-regions"></a>发布区域

https://www.luis.ai 上创建的 LUIS 应用可以发布到除[欧洲](#publishing-to-europe)和[澳大利亚](#publishing-to-australia)区域之外的所有终结点。 

创作区域中的应用仅可发布到对应的发布区域。 如果应用目前位于错误的创作区域中，请导出应用，然后将其导入发布区域对应的正确创作区域。 

 全球区域 | 创作区域 | 发布和查询区域   |   LUIS 网站 | 终结点 URL 格式   |
|-----|------|------|------|------|
| 亚洲 | 美国西部| 东亚     | [www.luis.ai][www.luis.ai] |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 亚洲 | 美国西部| 东南亚     | [www.luis.ai][www.luis.ai] |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[澳大利亚](#publishing-to-australia) | 澳大利亚东部| 澳大利亚东部     |   [au.luis.ai][au.luis.ai] | https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[欧洲](#publishing-to-europe)| 欧洲西部| 北欧     | [eu.luis.ai][eu.luis.ai]|  https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[欧洲](#publishing-to-europe) | 欧洲西部| 欧洲西部     | [eu.luis.ai][eu.luis.ai]|  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 北美 | 美国西部 | 美国东部      |[www.luis.ai][www.luis.ai] |   https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美 | 美国西部 | 美国东部 2     | [www.luis.ai][www.luis.ai] |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美 | 美国西部 | 美国中南部     | [www.luis.ai][www.luis.ai] |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 北美 | 美国西部 | 美国中西部     |[www.luis.ai][www.luis.ai] |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北美 | 美国西部 | 美国西部 |  [www.luis.ai][www.luis.ai] | https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 北美 | 美国西部 | 美国西部 2    | [www.luis.ai][www.luis.ai] |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 南美洲 | 美国西部 | 巴西南部     | [www.luis.ai][www.luis.ai] |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-europe"></a>发布到欧洲

若要发布到欧洲区域，请仅在 https://eu.luis.ai 创建 LUIS 应用。 如果尝试使用欧洲区域中的密钥将应用发布到其他区域，LUIS 会显示警告消息。 请改用 https://eu.luis.ai。 在 [https://eu.luis.ai][eu.luis.ai] 中创建的 LUIS 应用不会自动迁移到其他区域。 要实现迁移，请导出然后再导入 LUIS 应用。

## <a name="publishing-to-australia"></a>发布到澳大利亚

若要发布到澳大利亚区域，请仅在 https://au.luis.ai 创建 LUIS 应用。 如果尝试使用澳大利亚区域中的密钥将应用发布到其他区域，LUIS 会显示警告消息。 请改用 https://au.luis.ai。 在 [https://au.luis.ai][au.luis.ai] 中创建的 LUIS 应用不会自动迁移到其他区域。 要实现迁移，请导出然后再导入 LUIS 应用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [预生成实体参考](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai