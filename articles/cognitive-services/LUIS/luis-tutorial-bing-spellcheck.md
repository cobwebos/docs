---
title: Correct misspelled words - LUIS
titleSuffix: Azure Cognitive Services
description: 通过将必应拼写检查 API V7 添加到 LUIS 终结点查询来更正表述中拼写错误的字词。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 51b0d02443df872a7fae13116ea77b13d05055fa
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225450"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>使用必应拼写检查更正拼写错误的字词

在 LUIS 预测表述的分数和实体前，可将 LUIS 应用与[必应拼写检查 API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) 集成，以更正表述中拼写错误的字词。 

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>为必应拼写检查 V7 创建第一个密钥

[第一个必应拼写检查 API v7 密钥](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)是免费的。 

![创建免费密钥](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name="create-subscription-key"></a>

## <a name="create-endpoint-key"></a>创建终结点密钥
如果免费密钥已过期，请创建一个终结点密钥。

1. 登录到 [Azure 门户](https://portal.azure.com)。 

2. 选择左上角的“创建资源”。

3. 在搜索框中输入 `Bing Spell Check API V7`。

    ![搜索必应拼写检查 API V7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. 选择该服务。 

5. 右侧随即出现一个包含“法律声明”等信息的信息面板。 选择“创建”开始创建订阅。 

6. 在下一个面板中，输入服务设置。 等待服务创建过程完成。

    ![输入服务设置](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. 在左侧导航的“收藏夹”标题下，选择“所有资源”。

8. 选择该新服务。 它的类型是“认知服务”，位置为“全局”。 

9. 在主面板中，选择“密钥”以查看新密钥。

    ![获取密钥](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. 复制第一个密钥。 您只需要两个密钥中的一个即可。 

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>将密钥添加到终结点 URL
终结点查询需要在要应用拼写更正的每个查询的查询字符串参数中传递的密钥。 可使用调用 LUIS 的聊天机器人或直接调用 LUIS 终结点 API。 无论如何调用终结点，每个调用都必须包含拼写更正所需的信息，以确保正常工作。

终结点 URL 具有几个需要正确传递的值。 必应拼写检查 API v7 密钥仅是其中一个。 必须将 spellCheck 参数设置为 true，并且必须将 bing-spell-check-subscription-key 的值设置为密钥值：

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>将拼写错误的表述发送到 LUIS
1. 在 Web 浏览器中，复制上面的字符串，并将 `region`、`appId`、`luisKey` 和 `bingKey` 替换为你自己的值。 如果终结点区域与你的发布[区域](luis-reference-regions.md)不同，请务必使用终结点区域。

2. 添加拼写错误的表述，如“How far is the mountainn?”。 在英语中，含有一个 `n` 的 `mountain` 才是正确的拼写。 

3. 按 Enter，将查询发送到 LUIS。

4. LUIS 对 `How far is the mountain?` 的响应是一个 JSON 结果。 如果必应拼写检查 API v7 检测到拼写错误，LUIS 应用的 JSON 响应中的 `query` 字段包含原始查询，而 `alteredQuery` 字段包含更正后发送到 LUIS 的查询。

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>忽略拼写错误

If you don't want to use the Bing Spell Check API v7 service, you need to add the correct and incorrect spelling. 

Two solutions are:

* Label example utterances that have the all the different spellings so that LUIS can learn proper spelling as well as typos. 使用此选项比使用拼写检查器需要更多标记操作。
* Create a phrase list with all variations of the word. With this solution, you do not need to label the word variations in the example utterances. 

## <a name="publishing-page"></a>发布页面
[发布](luis-how-to-publish-app.md)页面包含一个“启用必应拼写检查器”复选框。 这便于创建密钥和了解终结点 URL 的更改。 为更正每个表述中的拼写，则仍必须使用正确的终结点参数。 

> [!div class="nextstepaction"]
> [详细了解示例陈述](luis-how-to-add-example-utterances.md)
