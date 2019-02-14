---
title: 订阅密钥
titleSuffix: Language Understanding - Azure Cognitive Services
description: 无需创建订阅密钥即可免费使用前 1000 个终结点查询。 如果收到 HTTP 403 或 429 形式的“超出配额”错误，则需要创建密钥并将其分配到应用。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/29/2019
ms.author: diberry
ms.openlocfilehash: 3fd05e2dd5b55dd590af24f0757229bead041b6d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859107"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>将订阅密钥与 LUIS 应用配合使用

无需创建订阅密钥即可免费使用前 1000 个终结点查询。 使用这些终结点查询后，在 [Azure 门户](http://portal.azure.com)中创建一个 Azure 资源，然后在 [LUIS 门户](https://www.luis.ai)中将该资源分配到 LUIS 应用。

如果收到 HTTP 403 或 429 形式的“超出配额”错误，则需要创建密钥并将其分配到应用。 

如果仅用于测试和原型，请使用免费 (F0) 层。 对于生产系统，请使用[付费](https://aka.ms/luis-price-tier)层。 不要将[创作密钥](luis-concept-keys.md#authoring-key)用于生产中的终结点查询。

<a name="create-luis-service"></a>

## <a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>在 Azure 门户中创建语言理解终结点密钥

此过程将创建一个**语言理解**资源。 如果希望某个资源可在不同的认知服务中使用，请创建一体化的**[认知服务](../cognitive-services-apis-create-account.md)** 密钥，而不要创建语言理解资源。 

此密钥应该仅用于终结点预测查询。 更改模型或应用时，请不要使用此密钥。 

1. 登录到 **[Azure 门户](https://ms.portal.azure.com/)**。 
1. 选择左上角面板中的绿色 **+** 符号，在市场中搜索 `Language Understanding`，然后选择“语言理解”，并遵循**创建体验**创建 LUIS 订阅帐户。 

    ![Azure 搜索](./media/luis-azure-subscription/azure-search.png) 

1. 为订阅配置帐户名、定价层等设置。 

    ![Azure API 选择](./media/luis-azure-subscription/azure-api-choice.png) 

1. 创建语言理解资源后，可以在“资源管理”->“密钥”中查看生成的密钥。 下一部分会介绍如何在 LUIS 门户中将此新资源连接到 LUIS 应用。 到时需要使用在步骤 3 中指定的 LUIS 资源名称。

    ![Azure 密钥](./media/luis-azure-subscription/azure-keys.png)

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>
<a name="assign-resource"></a>


## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>在 LUIS 门户中将资源密钥分配到 LUIS 应用

1. 登录到 LUIS 门户，选择要将新密钥添加到的应用，在右上角菜单中选择“管理”，然后选择“密钥和终结点”。

    [ ![密钥和终结点页面](./media/luis-manage-keys/keys-and-endpoints.png) ](./media/luis-manage-keys/keys-and-endpoints.png#lightbox)

1. 若要添加 LUIS，请选择“分配资源 +”。

    ![将资源分配给应用](./media/luis-manage-keys/assign-key.png)

1. 在对话框中选择与用来登录 LUIS 网站的电子邮件地址关联的租户。  

1. 选择与要添加的 Azure 资源关联的**订阅名称**。

1. 选择 **LUIS 资源名称**。 

1. 选择“分配资源”。 

1. 在表中找到新行并复制终结点 URL。 它构造正确，用以针对用于预测的 LUIS 终结点发出 HTTP GET 请求。 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

### <a name="unassign-resource"></a>取消分配资源
取消分配终结点密钥时，不会将其从 Azure 中删除。 只会将其从 LUIS 取消链接。 

当某个终结点密钥被取消分配或者未分配给应用时，针对终结点 URL 的任何请求都会返回错误：`401 This application cannot be accessed with the current subscription`。 

### <a name="include-all-predicted-intent-scores"></a>包括所有预测的意向分数
选中“包括所有预测的意向分数”复选框即可让终结点查询响应包括每个意向的预测分数。 

此设置允许聊天机器人或调用 LUIS 的应用程序根据返回的意向的分数进行程序性的决策。 通常情况下，最前面的两个意向是最相关的。 如果分数最高的是 None 意向，则聊天机器人可以选择提问一个跟进性的问题，以便在 None 意向和其他高分意向之间进行决定性的选择。 

意向及其分数也包括在终结点日志中。 可以[导出](luis-how-to-start-new-app.md#export-app)这些日志并分析分数。 

```JSON
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>启用必应拼写检查器 
在“终结点 URL 设置”中，选中“必应拼写检查器”会让 LUIS 在预测之前更正错拼的单词。 创建[必应拼写检查密钥](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)。 

添加 spellCheck=true querystring 参数和 bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}。 将 `{YOUR_BING_KEY_HERE}` 替换为必应拼写检查器密钥。

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

### <a name="publishing-regions"></a>发布区域

详细了解发布[区域](luis-reference-regions.md)，包括[欧洲](luis-reference-regions.md#publishing-to-europe)和[澳大利亚](luis-reference-regions.md#publishing-to-australia)的发布。 发布区域不同于创作区域。 请在与要为查询终结点使用的发布区域对应的创作区域中创建应用。

## <a name="assign-resource-without-luis-portal"></a>不使用 LUIS 门户分配资源

出于 CI/CD 管道等自动化目的，你可能想要将 LUIS 资源自动分配给 LUIS 应用。 为此，需要执行以下步骤：

1. 从此[网站](https://resources.azure.com/api/token?plaintext=true)获取 Azure 资源管理器令牌。 此令牌即将过期，请立即使用。 该请求将返回 Azure 资源管理器令牌。

    ![请求 Azure 资源管理器令牌和接收 Azure 资源管理器令牌](./media/luis-manage-keys/get-arm-token.png)

1. 使用该令牌从用户帐户有权访问的[获取 LUIS azure 帐户 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c) 跨订阅请求 LUIS 资源。 

    此 POST API 需要以下设置：

    |标头|值|
    |--|--|
    |`Authorization`|`Authorization` 的值为 `Bearer {token}`。 请注意，单词 `Bearer` 和空格前面必须是令牌值。| 
    |`Ocp-Apim-Subscription-Key`|你的[创作密钥](luis-how-to-account-settings.md)。|

    此 API 将返回 LUIS 订阅的 JSON 对象的数组，包括订阅 ID、资源组和资源名称（作为帐户名称返回）。 在要将 LUIS 资源分配给 LUIS 应用的数组中查找一个项。 

1. 使用[将 LUIS azure 帐户分配给应用程序](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API，将令牌分配给 LUIS 资源。 

    此 POST API 需要以下设置：

    |Type|设置|值|
    |--|--|--|
    |标头|`Authorization`|`Authorization` 的值为 `Bearer {token}`。 请注意，单词 `Bearer` 和空格前面必须是令牌值。|
    |标头|`Ocp-Apim-Subscription-Key`|你的[创作密钥](luis-how-to-account-settings.md)。|
    |标头|`Content-type`|`application/json`|
    |Querystring|`appid`|LUIS 应用 ID。 
    |Body||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    此 API 成功时，将返回“201 - 已创建状态”。 

## <a name="change-pricing-tier"></a>更改定价层

1.  在 [Azure](https://portal.azure.com) 中查找你的 LUIS 订阅。 选择该 LUIS 订阅。
    ![查找 LUIS 订阅](./media/luis-usage-tiers/find.png)
1.  选择“定价层”以查看可用的定价层。 
    ![查看定价层](./media/luis-usage-tiers/subscription.png)
1.  选择定价层，然后选择“选择”以保存更改。 
    ![更改 LUIS 支付层](./media/luis-usage-tiers/plans.png)
1.  定价更改完成后，页面将出现一个供于验证新定价层的弹出窗口。 
    ![验证 LUIS 支付层](./media/luis-usage-tiers/updated.png)
1. 请记住在“发布”页[分配此终结点密钥](#assign-endpoint-key)，并将其用于所有终结点查询。 

## <a name="how-to-fix-out-of-quota-errors-when-the-key-exceeds-pricing-tier-usage"></a>当密钥超过定价层用量时如何修复“超出配额”错误
每层允许以特定速率向 LUIS 帐户发送终结点请求。 如果请求速率高于计费帐户的每分钟或每月的允许速率，则请求会出现 HTTP 错误“429:请求过多”。

每层允许按月累计请求数。 如果总请求数大于允许的速率，则请求会出现 HTTP 错误“403: 禁止”。  

## <a name="viewing-summary-usage"></a>查看使用概况
可在 Azure 中查看 LUIS 使用情况信息。 “概述”页显示包含调用和错误在内的最新摘要信息。 如果发出 LUIS 终结点请求并立即查看“概述”页，则最多需要五分钟才会显示使用情况。

![查看使用概况](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>自定义使用情况图表
通过指标可更加详细地了解数据。

![默认指标](./media/luis-usage-tiers/metrics-default.png)

可针对时间期限和指标类型配置度量值图表。 

![自定义指标](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>总事务数阈值警报
如果希望在达到特定事务阈值（例如 10,000 个事务）时收到通知，则可以创建警报。 

![默认警报](./media/luis-usage-tiers/alert-default.png)

添加针对特定时间段内“总调用数”指标的指标警报。 添加应接收该警报的所有人员的电子邮件地址。 添加应接收该警报的所有系统的 Webhook。 还可在触发警报时运行逻辑应用。 

## <a name="next-steps"></a>后续步骤

了解如何使用[版本](luis-how-to-manage-versions.md)管理对 LUIS 应用的更改。
