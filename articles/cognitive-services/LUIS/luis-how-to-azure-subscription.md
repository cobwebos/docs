---
title: 订阅密钥
titleSuffix: Language Understanding - Azure Cognitive Services
description: '无需创建订阅密钥即可免费使用前 1000 个终结点查询。 如果收到 HTTP 403 或 429 形式的“超出配额”错误，则需要创建密钥并将其分配到应用。 '
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/10/2019
ms.author: diberry
ms.openlocfilehash: dedc498ebc910b448b1684136c288b2045780e00
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797957"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>将订阅密钥与 LUIS 应用配合使用

首次使用语言理解 (LUIS)，您不需要创建订阅密钥。 你首先可以 1000年终结点查询。 

如果仅用于测试和原型，请使用免费 (F0) 层。 对于生产系统，请使用[付费](https://aka.ms/luis-price-tier)层。 不要将[创作密钥](luis-concept-keys.md#authoring-key)用于生产中的终结点查询。


<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"/>

## <a name="create-prediction-endpoint-runtime-resource-in-the-azure-portal"></a>在 Azure 门户中创建预测终结点运行时资源

您创建[预测终结点资源](get-started-portal-deploy-app.md#create-the-endpoint-resource)在 Azure 门户中。 此资源仅应用于终结点预测查询。 请勿使用此资源来创建对应用的更改。

您可以创建语言理解资源或认知服务资源。 如果要创建语言理解资源，较好的做法是的 postpend 的资源类型为资源名称。 

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

### <a name="using-resource-from-luis-portal"></a>使用从 LUIS 门户的资源

如果使用 LUIS 门户的资源，您不需要知道你的密钥和位置。 相反，您需要知道资源租户、 订阅和资源名称。

一次你[分配](#assign-resource-key-to-luis-app-in-luis-portal)到 LUIS 门户、 密钥和位置中的 LUIS 应用资源提供的管理部分中的查询预测终结点 URL 的一部分**密钥和终结点设置**页。
 
### <a name="using-resource-from-rest-api-or-sdk"></a>使用通过 REST API 或 SDK 资源

如果使用的 REST API(s) 或 SDK 中的资源，您需要知道你的密钥和位置。 管理部分中的查询预测终结点 URL 的一部分提供此信息**密钥和终结点设置**页以及如资源的概述和密钥页上的 Azure 门户中所示。

## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>在 LUIS 门户中将资源密钥分配到 LUIS 应用

每次为 LUIS 创建新的资源，需要[将资源分配到 LUIS 应用](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal)。 分配后，除非创建新资源，否则无需再次执行此步骤。 可以创建新资源来扩展应用的区域或支持更多预测查询。

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
在“终结点 URL 设置”中，选中“必应拼写检查器”会让 LUIS 在预测之前更正错拼的单词。   创建[必应拼写检查密钥](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)  。 

添加 spellCheck=true querystring 参数和 bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}   。 将 `{YOUR_BING_KEY_HERE}` 替换为必应拼写检查器密钥。

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

    |Header|值|
    |--|--|
    |`Authorization`|`Authorization` 的值为 `Bearer {token}`。 请注意，单词 `Bearer` 和空格前面必须是令牌值。| 
    |`Ocp-Apim-Subscription-Key`|你的[创作密钥](luis-how-to-account-settings.md)。|

    此 API 将返回 LUIS 订阅的 JSON 对象的数组，包括订阅 ID、资源组和资源名称（作为帐户名称返回）。 在要将 LUIS 资源分配给 LUIS 应用的数组中查找一个项。 

1. 使用[将 LUIS azure 帐户分配给应用程序](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API，将令牌分配给 LUIS 资源。 

    此 POST API 需要以下设置：

    |type|设置|值|
    |--|--|--|
    |Header|`Authorization`|`Authorization` 的值为 `Bearer {token}`。 请注意，单词 `Bearer` 和空格前面必须是令牌值。|
    |Header|`Ocp-Apim-Subscription-Key`|你的[创作密钥](luis-how-to-account-settings.md)。|
    |Header|`Content-type`|`application/json`|
    |Querystring|`appid`|LUIS 应用 ID。 
    |正文||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    此 API 成功时，将返回“201 - 已创建状态”。 

## <a name="change-pricing-tier"></a>更改定价层

1.  在 [Azure](https://portal.azure.com) 中查找你的 LUIS 订阅。 选择该 LUIS 订阅。
    ![查找 LUIS 订阅](./media/luis-usage-tiers/find.png)
1.  选择“定价层”以查看可用的定价层  。 
    ![查看定价层](./media/luis-usage-tiers/subscription.png)
1.  选择定价层，然后选择“选择”以保存更改  。 
    ![更改 LUIS 支付层](./media/luis-usage-tiers/plans.png)
1.  定价更改完成后，页面将出现一个供于验证新定价层的弹出窗口。 
    ![验证 LUIS 支付层](./media/luis-usage-tiers/updated.png)
1. 请记住在“发布”页[分配此终结点密钥](#assign-endpoint-key)，并将其用于所有终结点查询  。 

## <a name="fix-http-status-code-403-and-429"></a>修复 403 和 429 的 HTTP 状态代码

当超过每秒事务或事务，每月的定价层时获取以状态代码 403 和 429 错误。

### <a name="when-you-receive-an-http-403-error-status-code"></a>当收到 HTTP 403 错误状态代码

当您使用所有这些免费 1000年终结点查询或超过定价层的每月的事务配额时，您将收到 HTTP 403 错误状态代码。 

若要修复此错误，则需要[更改定价层](luis-how-to-azure-subscription.md#change-pricing-tier)到较高级别或[创建新的资源](get-started-portal-deploy-app.md#create-the-endpoint-resource)并[将其分配给您的应用程序](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal)。

此错误的解决方案包括：

* 在中[Azure 门户](https://portal.azure.com)，在您的语言资源，了解上**资源管理-> 定价层**，将定价层更改为更高的 TPS 层。 您不必执行任何操作语言理解门户中，如果你的资源已分配给您的语言理解应用程序。
*  如果你的使用情况超过最高的定价层，添加与在它们的前面的负载均衡器的更多语言理解资源。 [语言理解容器](luis-container-howto.md)与 Kubernetes 或 Docker Compose 可以与此帮助。

### <a name="when-you-receive-an-http-429-error-status-code"></a>当收到 HTTP 429 错误状态代码

返回此状态代码时你每秒事务数超过了定价层。  

解决方案包括：

* 你可以[提升定价层](#change-pricing-tier)，如果你不在最高的层。
* 如果你的使用情况超过最高的定价层，添加与在它们的前面的负载均衡器的更多语言理解资源。 [语言理解容器](luis-container-howto.md)与 Kubernetes 或 Docker Compose 可以与此帮助。
* 可以入口在客户端应用程序请求[重试策略](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines)时收到此状态代码实现您自己。 

## <a name="viewing-summary-usage"></a>查看使用概况
可在 Azure 中查看 LUIS 使用情况信息。 “概述”页显示包含调用和错误在内的最新摘要信息  。 如果发出 LUIS 终结点请求并立即查看“概述”页，则最多需要五分钟才会显示使用情况  。

![查看使用概况](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>自定义使用情况图表
通过指标可更加详细地了解数据。

![默认指标](./media/luis-usage-tiers/metrics-default.png)

可针对时间期限和指标类型配置度量值图表。 

![自定义指标](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>总事务数阈值警报
如果希望在达到特定事务阈值（例如 10,000 个事务）时收到通知，则可以创建警报。 

![默认警报](./media/luis-usage-tiers/alert-default.png)

添加针对特定时间段内“总调用数”指标的指标警报  。 添加应接收该警报的所有人员的电子邮件地址。 添加应接收该警报的所有系统的 Webhook。 还可在触发警报时运行逻辑应用。 

## <a name="next-steps"></a>后续步骤

了解如何使用[版本](luis-how-to-manage-versions.md)管理对 LUIS 应用的更改。
