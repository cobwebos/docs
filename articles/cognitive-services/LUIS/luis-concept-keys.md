---
title: 了解 LUIS 密钥 - Azure | Microsoft Docs
description: 使用语言理解 (LUIS) 密钥来创作应用并查询终结点。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/23/2018
ms.author: v-geberr
ms.openlocfilehash: 70bca3b181e02f42da50e827154193936544131a
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263812"
---
# <a name="keys-in-luis"></a>LUIS 中的密钥
LUIS 使用两种密钥：[创作](#programmatic-key)密钥和[终结点](#endpoint-key)密钥。 在创建 LUIS 帐户时会自动创建创作密钥。 准备好发布 LUIS 应用时，需要[创建终结点密钥](luis-how-to-azure-subscription.md#create-luis-endpoint-key)、将[终结点密钥分配](Manage-keys.md#assign-endpoint-key)到 LUIS 应用并[将其与终结点查询配合使用](#use-endpoint-key-in-query)。 

|密钥|目的|
|--|--|
|[创作密钥](#programmatic-key)|创作、发布、管理协作者、版本控制|
|[终结点密钥](#endpoint-key)| 查询|

请务必在想进行发布和查询的[区域](luis-reference-regions.md#publishing-regions)中创作 LUIS 应用。

<a name="programmatic-key" ></a>
## <a name="authoring-key"></a>创作密钥

创作密钥也称为初学者密钥，是在创建 LUIS 帐户时自动创建的免费密钥。 每个创作[区域](luis-reference-regions.md)的所有 LUIS 应用共享一个创作密钥。 创作密钥用于创作 LUIS 应用或测试终结点查询。 

若要找到创作密钥，请登录 [LUIS][LUIS] 并单击右上方导航栏中的帐户名称，打开“帐户设置”。

![创作密钥](./media/luis-concept-keys/programatic-key.png)

如果想进行生产终结点查询，请创建 Azure [LUIS 订阅](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)。 

> [!CAUTION]
> 为了方便起见，很多示例都采用创作密钥，因为它在[配额](luis-boundaries.md#key-limits)中提供了几个终结点调用。  

## <a name="endpoint-key"></a>终结点密钥
 如果需要进行生产终结点查询，请在 Azure 门户中创建 [LUIS 密钥](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)。 请记住用于创建密钥的名称，在将密钥添加到应用时需要该名称。

完成 LUIS 订阅流程后，[将密钥添加](Manage-keys.md#assign-endpoint-key)到“发布”页面上的应用。 

终结点密钥支持的终结点命中次数配额取决于创建密钥时所指定的使用计划。 请参阅[认知服务定价](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h)了解定价信息。

终结点密钥可用于所有 LUIS 应用或特定 LUIS 应用。 

请勿将终结点密钥用于创作 LUIS 应用。 

## <a name="use-endpoint-key-in-query"></a>在查询中使用终结点密钥
LUIS 终结点接受两种样式的查询，这两种查询都使用终结点密钥，但是位置不同：

|Verb|示例 URL 和密钥位置|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/98998dcf-66d2-468e-840a-7c7c57549b5a?subscription-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn 开灯<br><br>`subscription-key` 的查询字符串值<br><br>将 `subscription-key` 的终结点查询值从创作（初学者）密钥更改为新的终结点密钥，以便使用 LUIS 终结点密钥配额率。 如果创建并分配了该密钥，但是没有更改 subscription-key` 的终结点查询值，则不会使用终结点密钥配额。|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/98998dcf-66d2-468e-840a-7c7c57549b5a<br><br> `Ocp-Apim-Subscription-Key` 的标头值<br><br>将 `Ocp-Apim-Subscription-Key` 的终结点查询值从创作（初学者）密钥更改为新的终结点密钥，以便使用 LUIS 终结点密钥配额率。 如果创建并分配了该密钥，但是没有更改 `Ocp-Apim-Subscription-Key` 的终结点查询值，则不会使用终结点密钥配额。|

## <a name="api-usage-of-ocp-apim-subscription-key"></a>Ocp-Apim-Subscription-Key 的 API 使用情况
LUIS API 使用标头 `Ocp-Apim-Subscription-Key`。 标头名称不会基于所使用的密钥和 API 集产生变化。 将标头设为创作 API 的创作密钥。 如果使用的是终结点，则将标头设为终结点密钥。 

不能为创作 API 传递终结点密钥。 如果这样做，则会遇到 401 错误 - 由于订阅密钥无效，访问被拒绝。 

## <a name="key-limits"></a>密钥限制
请参阅[密钥限制](luis-boundaries.md#key-limits)和 [Azure 区域](luis-reference-regions.md)。 创作密钥是用于创作的免费密钥。 LUIS 订阅密钥有一个免费层，但是必须由你创建并与“发布”页面上的 LUIS 应用关联。 它不能用于创作，只能用于终结点查询。

发布区域不同于创作区域。 请确保在对应于所需发布区域的创作区域中创建应用。

## <a name="key-limit-errors"></a>密钥限制错误
如果超过了每秒配额，则会遇到 HTTP 429 错误。 如果超过了每月配额，则会遇到 HTTP 403 错误。 若要修复这些错误，请获取一个 LUIS [终结点](#endpoint-key)密钥，将该密钥[分配](Manage-keys.md#assign-endpoint-key)到 [LUIS][LUIS] 网站“发布”页面上的应用。

## <a name="next-steps"></a>后续步骤

* 了解创作密钥和终结点密钥的相关[概念](Manage-Keys.md#assign-endpoint-key)。

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
