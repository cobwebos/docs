---
title: 在 LUIS 中管理创作和终结点密钥
titleSuffix: Azure Cognitive Services
description: 在 Azure 门户中创建 LUIS 终结点密钥后，将该密钥分配给 LUIS 应用，并获取正确的终结点 URL。 使用此终结点 URL 来获取 LUIS 预测。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 62081f96e2081833eb705992914899a6764bd792
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033203"
---
# <a name="add-an-azure-luis-resource-to-app"></a>向应用中添加 Azure LUIS 资源

在 Azure 门户中创建 LUIS 资源后，将该资源分配给 LUIS 应用，并获取正确的终结点 URL。 使用此终结点 URL 来获取 LUIS 预测。

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

## <a name="assign-resource"></a>分配资源

1. 在 [Azure 门户](https://portal.azure.com)上创建一个 LUIS 密钥。 有关详细说明，请参阅[使用 Azure 创建终结点密钥](luis-how-to-azure-subscription.md)。
 
2. 在右上角的菜单中选择“管理”，然后选择“密钥和终结点”。

    [ ![密钥和终结点页面](./media/luis-manage-keys/keys-and-endpoints.png) ](./media/luis-manage-keys/keys-and-endpoints.png#lightbox)

3. 若要添加 LUIS，请选择“分配资源 +”。

    ![将资源分配给应用](./media/luis-manage-keys/assign-key.png)

4. 在对话框中选择与你用来登录 LUIS 网站的电子邮件地址关联的租户。  

5. 选择与要添加的 Azure 资源关联的**订阅名称**。

6. 选择 **LUIS 资源名称**。 

7. 选择“分配资源”。 

8. 在表中找到新行并复制终结点 URL。 它构造正确，用以针对用于预测的 LUIS 终结点发出 HTTP GET 请求。 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="unassign-resource"></a>取消分配资源
取消分配终结点密钥时，不会将其从 Azure 中删除。 只会将其从 LUIS 取消链接。 

当某个终结点密钥被取消分配或者未分配给应用时，针对终结点 URL 的任何请求都会返回错误：`401 This application cannot be accessed with the current subscription`。 

## <a name="include-all-predicted-intent-scores"></a>包括所有预测的意向分数
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

## <a name="enable-bing-spell-checker"></a>启用必应拼写检查器 
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


## <a name="publishing-regions"></a>发布区域

详细了解发布[区域](luis-reference-regions.md)，包括[欧洲](luis-reference-regions.md#publishing-to-europe)和[澳大利亚](luis-reference-regions.md#publishing-to-australia)的发布。 发布区域不同于创作区域。 请在与要为查询终结点使用的发布区域对应的创作区域中创建应用。

## <a name="next-steps"></a>后续步骤

使用密钥，在“发布应用”页面中发布应用。 若要查看发布的相关说明，请参阅[发布应用](luis-how-to-publish-app.md)。

请参阅 [LUIS 中的密钥](luis-concept-keys.md)，了解 LUIS 创作和终结点密钥概念。