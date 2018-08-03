---
title: 发布 LUIS 应用 | Microsoft Docs
description: 使用语言理解 (LUIS) 生成并测试应用以后，请在 Azure 上将其作为 Web 服务发布。
services: cognitive-services
titleSuffix: Azure
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 1bd24af0498755b7cdcb170624fd8f9f3b39c85a
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341283"
---
# <a name="publish-your-trained-app"></a>发布已训练的应用
生成并测试完 LUIS 应用以后，请将其发布。 发布应用后，“发布”页会显示所有关联的 HTTP [终结点](luis-glossary.md#endpoint)。 然后，这些终结点会按[区域](luis-reference-regions.md)和按[密钥](luis-how-to-manage-keys.md)集成到任何客户端、聊天机器人或后端应用程序中。 

始终可以在发布应用之前对其进行[测试](interactive-test.md)。 

## <a name="production-and-staging-slots"></a>生产槽和过渡槽
可以将应用发布到**过渡槽**或**生产槽**。 使用两个发布槽，可以对已发布的终结点使用两个不同的版本，或者在两个不同的终结点上使用同一版本。 

<!-- TBD: what is the technical difference? log files, endpoint quota? -->

## <a name="settings-configuration-requires-publishing-model"></a>设置配置需要发布模型
在对以下设置进行更改之后发布到终结点。 

## <a name="external-services-settings"></a>外部服务设置
外部服务设置包括**[情绪分析](#enable-sentiment-analysis)** 和**[语音启动](#enable-speech-priming)**。

### <a name="enable-sentiment-analysis"></a>启用情绪分析
在“外部服务设置”中选中“启用情绪分析”复选框后，LUIS 就可以与[文本分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)集成，以便为用户提供情绪和关键短语分析。 不需提供文本分析密钥，而且系统不会因为此服务向你的 Azure 帐户收费。 检查此设置后，它就会变成持久性的。 

情绪数据是一个 0 到 1 的分数，指示数据的正面情绪（分数接近 1）或负面情绪（分数接近 0）。

若要详细了解使用情绪分析时的 JSON 终结点响应，请参阅[情绪分析](luis-concept-data-extraction.md#sentiment-analysis)

### <a name="enable-speech-priming"></a>启用语音启动 
在“外部服务设置”中选中“启用语音启动”复选框即可通过单个终结点从聊天机器人或调用 LUIS 的应用程序处获取语音表述，以及接收 LUIS 预测响应。 语音启动使用认知服务[语音 API](../Speech-Service/rest-apis.md)。 

![语音启动确认对话框的图像](./media/luis-how-to-publish-app/speech-prime-modal.png)

启用此功能后，请发布应用。 发布 LUIS 应用时，应用模型会发送到你自己的语音服务来启动语音服务。 你的模型信息**不**在你自己的服务之外使用。 

为了完成语音启动功能的使用，需提供在[语音 SDK](../speech-service/speech-sdk-reference.md) 中使用的以下信息：
* LUIS 终结点密钥。
* LUIS 应用 ID。
* 一个终结点域，在语音 SDK 中称为“主机名”，例如“westus.api.cognitive.microsoft.com”，其中，第一个子域是发布应用的区域。

有关详细信息，请参阅[语音转意向](http://aka.ms/speechsdk)示例。

删除 LUIS 应用或语音服务时，会删除模型数据。 

## <a name="endpoint-url-settings"></a>终结点 URL 设置
终结点 URL 服务设置包括**[时区](#set-timezone-offset)** 偏移、**[所有预测的意向分数](#include-all-predicted-intent-scores)** 以及**[必应拼写检查器](#enable-bing-spell-checker)**。

### <a name="set-timezone-offset"></a>设置时区偏移 
部分槽选择是时区选择。 在预测过程中，LUIS 可以通过此时区设置[更改](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)任何预置的 datetimeV2 时间值，使返回的实体数据正确反映所选时区。 

### <a name="include-all-predicted-intent-scores"></a>包括所有预测的意向分数
选中“包括所有预测的意向分数”复选框即可让终结点查询响应包括每个意向的预测分数。 

此设置允许聊天机器人或调用 LUIS 的应用程序根据返回的意向的分数进行程序性的决策。 通常情况下，最前面的两个意向是最相关的。 如果分数最高的是 None 意向，则聊天机器人可以选择提问一个跟进性的问题，以便在 None 意向和其他高分意向之间进行决定性的选择。 

意向及其分数也包括在终结点日志中。 可以[导出](create-new-app.md#export-app)这些日志并分析分数。 

```
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
在“终结点 URL 设置”中选中“启用必应拼写检查器”复选框即可让 LUIS 在预测之前更正错拼的单词。 这需要创建一个**[必应拼写检查密钥](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**。 创建此密钥以后，系统就会向发布页上的终结点 URL 添加两个查询字符串参数。 

如果针对调用 LUIS 的应用程序构建你自己的 URL，请确保设置 **spellCheck=true** 查询字符串参数和 **bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}**。 将 `{YOUR_BING_KEY_HERE}` 替换为必应拼写检查器密钥。

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

## <a name="publish-your-trained-app-to-an-http-endpoint"></a>将已训练的应用发布到 HTTP 终结点
单击“我的应用”页上的应用名称以打开应用，然后单击顶部面板中的“发布”。 

![“发布”页-](./media/luis-how-to-publish-app/publish-to-production.png)
 
成功发布应用后，浏览器顶部会显示一个表示成功的绿色通知。 

* 在“选择槽”下的下拉菜单中选择是发布到“生产”槽还是发布到“过渡”槽。 

## <a name="assign-key"></a>分配密钥

若要使用所显示的免费 Starter_Key 之外的密钥，请单击“添加密钥”按钮。 此操作会打开一个对话框，可以在其中选择分配给应用的现有终结点密钥。 若要详细了解如何创建终结点密钥并将其添加到 LUIS 应用，请参阅[管理密钥](luis-how-to-manage-keys.md)。

若要查看与其他区域关联的终结点和密钥，请使用用于切换区域的单选按钮。 “资源和密钥”表中的每一行都列出了与帐户相关联的 Azure 资源以及与该资源相关联的终结点密钥。

## <a name="endpoint-url-construction"></a>终结点 URL 构建
终结点 URL 对应于与终结点密钥相关联的 Azure 区域。

此表反映了你在 URL 终结点中的发布配置以及路由选择和查询字符串值，十分方便。 如果为调用 LUIS 的应用程序构建终结点 URL，请确保为所用终结点设置这些相同的路由和查询字符串值（如果需要进行设置）。

URL 路由和区域以及应用 ID 一起构建。 如果在其他区域发布，或者使用其他应用进行发布，则可通过更改区域和应用 ID 值来构建终结点 URL。 

* 选择“生产”槽和“发布”按钮。 发布成功以后，请使用显示的终结点 URL 来访问 LUIS 应用。 

### <a name="optional-query-string-parameters"></a>可选的查询字符串参数
以下查询字符串参数可以与终结点 URL 配合使用：

<!-- TBD: what about speech priming? -->

|查询字符串|Type|示例值|目的|
|--|--|--|--|
|verbose|布尔值|是|包括表述的[所有意向分数](#include-all-predicted-intent-scores)|
|timezoneOffset|数字（单位为分钟）|60|为 [datetimeV2 预置实体](luis-reference-prebuilt-datetimev2.md)设置[时区偏移](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)|
|spellCheck|布尔值|是|[更正表述的拼写](#enable-bing-spell-checker) -- 与 bing-spell-check-subscription-key 查询字符串参数配合使用|
|bing-spell-check-subscription-key|订阅 ID||与 spellCheck 查询字符串参数配合使用|
|staging|布尔值|false|选择过渡型或生产型终结点|
|log|布尔值|是|向日志添加查询和结果|


## <a name="test-your-published-endpoint-in-a-browser"></a>在浏览器中测试已发布的终结点
测试已发布终结点的方法是在“终结点”列中选择 URL。 系统会通过生成的 URL 打开默认浏览器。 请将 URL 参数“&q”设置为测试查询。 例如，将 `&q=Book me a flight to Boston on May 4` 追加到 URL，然后按 Enter。 浏览器会显示 HTTP 终结点的 JSON 响应。 

![来自已发布的 HTTP 终结点的 JSON 响应](./media/luis-how-to-publish-app/luis-publish-app-json-response.png)

## <a name="next-steps"></a>后续步骤

* 请参阅[管理密钥](./luis-how-to-manage-keys.md)，了解如何向 LUIS 应用添加密钥，并了解如何将密钥映射到区域。
* 有关如何在测试控制台中测试已发布应用的说明，请参阅[训练和测试应用](interactive-test.md)。
