---
title: Azure 语言理解 (LUIS) 简介 | Microsoft Docs
description: 了解如何通过语言理解 (LUIS) 服务在应用程序中运用机器学习。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/22/2017
ms.author: v-geberr
ms.openlocfilehash: 1c68c586fb799a540f70804d181aa66b2bda9e97
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952532"
---
# <a name="what-is-language-understanding-luis"></a>什么是语言理解 (LUIS)？
语言理解 (LUIS) 是一种基于云的服务，可在用户对话的自然语言文本中应用自定义机器学习，从而预测整体含义并提炼出相关的详细信息。 

LUIS 可在客户端用于各种以自然语言与用户沟通进而完成任务的对话应用程序。 这些客户端应用程序包括社交媒体应用、聊天机器人以及支持语音的桌面应用程序。  

![描述 LUIS 向 3 个应用程序馈送信息的概念图](./media/luis-overview/luis-entry-point.png)

## <a name="what-is-a-luis-app"></a>什么是 LUIS 应用？
LUIS 应用包含一个用户设计的域特定的自然语言模型。 可通过预构建的域模型启动 LUIS 应用、自行构建应用，还可将预构建的域的各个部分与自己的自定义信息进行混合。

[预构建的域模型](luis-how-to-use-prebuilt-domains.md)包含上述所有部分，是实现 LUIS 快速入门的绝佳方式。

LUIS 应用还包含集成设置、[协作程序](luis-concept-collaborator.md)和[版本](luis-concept-version.md)。

## <a name="using-a-luis-app"></a>使用 LUIS 应用
<a name="Accessing-LUIS"></a>LUIS 应用一旦发布，客户端应用程序即可向 LUIS [终结点 API][endpoint-apis] 发送话语并接收预测结果作为 JSON 响应。

在下图中，客户端聊天机器人首先用自己的语言在 HTTP 请求中发送描述用户所需操作的文本。 接下来，LUIS 应用学习模型来分析自然语言，从而理解用户输入的内容并返回 JavaScript 对象表示法 (JSON) 格式的响应。 然后，客户端聊天机器人通过 JSON 响应处理用户的请求。 

![描述 LUIS 与聊天机器人配合使用的概念图](./media/luis-overview/luis-overview-process-2.png)

### <a name="example-of-json-endpoint-response"></a>JSON 终结点响应示例

JSON 终结点响应至少包含查询话语和得分最高的意向。 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>
## <a name="what-is-a-natural-language-model"></a>什么是自然语言模型？
模型首先具有一列常规用户意向（称为“意向”），比如“预订航班”或“联系技术支持”。 你针对意向提供用户的示例文本（称为“示例话语”）。 然后在话语中标出重要的字词或短语（称为“实体”）。


一个模型包括：

* [意向](#intents)：用户意向的类别（意向操作或结果）
* [实体](#entities)：话语中特定类型的数据，比如数字、电子邮件或名称
* [示例话语](#example-utterances)：用户在客户端应用程序中输入的示例文本

### <a name="intents"></a>意向 
[意向](luis-how-to-add-intents.md)指用户话语中表达出的目的或目标，比如预订航班、支付帐单或找到新闻文章。 由你创建每个操作的意向。 LUIS 旅游应用可能定义一个名为“预订航班”的意向。 而客户端应用程序可使用得分最高的意向来触发操作。 例如，当 LUIS 返回“预订航班”意向时，客户端应用程序触发一个到外部服务的 API 调用来预订机票。

### <a name="entities"></a>实体
[实体](luis-how-to-add-entities.md)表示在话语中找到的与用户请求相关的详细信息。 例如，在“预订到巴黎的机票”话语中，请求了单张机票，而“巴黎”指的是一个位置。 这里找到两个实体，“一张机票”表示单个机票，而“巴黎”表示目的地。 

当 LUIS 返回在用户话语中找到的实体之后，客户端应用程序可使用实体列表作为参数来触发操作。 例如，预订航班需要旅行目的地、日期和航线等实体。

LUIS 提供多种方式来标识和分类实体。

* **预构建的实体** LUIS 具有多个预构建的域模型，它们自带意向、话语和[预构建的实体](luis-prebuilt-entities.md)。 即使不使用预构建的模型中的意向和话语，也能使用预构建的实体。 预构建的实体可助你节省时间。

* **自定义实体** LUIS 提供多种方式来自行标识自定义的[实体](luis-concept-entity-types.md)，比如机器学习到的实体、特定或文本实体，以及机器学习到的实体和文本实体的组合。

### <a name="example-utterances"></a>示例陈述
示例[话语](luis-how-to-add-example-utterances.md)指客户端应用程序需要理解的用户文本输入。 它可能是一个句子，比如“预订到巴黎的机票”，也可能是句子的片段，比如“预订”或“巴黎航班”。 话语不总是格式正确，特定意向可能具有多个话语变体。 向每个意向添加 10 到 20 个示例话语，并在每个话语中标出实体。

|示例用户话语|意向|实体|
|-----------|-----------|-----------|
|“预订到西雅图的航班？”|预订航班|西雅图|
|“你的店铺何时开门？”|店铺营业时间和位置|开门|
|“安排下午 1 点与分销部的何石谈话”|安排谈话|下午 1 点，何石|

## <a name="improve-prediction-accuracy"></a>提高预测准确性
在 LUIS 应用发布和接收实时用户话语之后，LUIS 提供了多种方式来提升预测的准确性：针对终结点话语的[主动学习](#active-learning)、针对域字词内容的[短语列表](#phrase-lists)，以及用于减少所需话语数的[模式](#patterns)。

### <a name="active-learning"></a>主动学习
在[主动学习](luis-how-to-review-endoint-utt.md)过程中，可通过 LUIS 选择 LUIS 应用在终结点处接收供你审阅的话语，从而运用该应用分析真实世界的话语。 你可接受或更正终结点预测，还可进行再训练和重新发布。 在这个迭代过程中，LUIS 迅速学习，尽可能占用你很少的时间和精力。 

### <a name="phrase-lists"></a>短语列表 
LUIS 提供了[短语列表地](luis-concept-feature.md)，可用于向模型域指出重要的字词或短语。 LUIS 使用此类列表提升这些字词和短语的重要性；若不提升，则它们不出现在模型中。

### <a name="patterns"></a>模式 
模式可用于将就某意向收集到的话语简化成常见的字词选择和字词顺序[模板](luis-concept-patterns.md)。 这使得意向需要更少的示例话语，加速了 LUIS 的学习。 模式是一个混合系统，其中包含正则表达式以及通过机器学习到的表达式。 

<a name="using-luis"></a>

## <a name="authoring-and-accessing-luis"></a>创作和访问 LUIS
根据创作需求，可通过 LUIS 网站构建 LUIS 应用，或者使用[创作](https://aka.ms/luis-authoring-apis) API 以编程方式构建（也可两种方式联用）。 通过查询[终结点](https://aka.ms/luis-endpoint-apis)访问已发布的 LUIS 应用。 

LUIS 在全球提供三个网站，具体使用取决于你的创作区域。 创作区域决定了可在哪个 Azure 区域发布 LUIS 应用。
<!--
|Authoring region|Publishing region(s)|
|--|--|
|[www.luis.ai](https://www.luis.ai)|**U.S.**<br>West US<br>West US 2<br>East US<br>East US 2<br>South Central US<br>West Central US<br><br>**Asia**<br>Southeast Asia<br>East Asia<br><br>**South America**<br>Brazil South |
|[au.luis.ai](https://au.luis.ai)|Australia East|
|[eu.luis.ai](https://eu.luis.ai)|West Europe<br>North Europe|
-->

[详细了解](luis-reference-regions.md)创作和发布区域。

## <a name="what-technologies-work-with-luis"></a>LUIS 可使用哪些技术？
LUIS 可使用多项 Microsoft 技术：

* [必应拼写检查 API](../bing-spell-check/proof-text.md) 提供预测前的文本更正功能。 
* [Bot Framework][bot-framework] 让聊天机器人能够通过文本输入与用户交谈。 要享受完整的机器人体验，请选择 [3.x](https://github.com/Microsoft/BotBuilder) 或 [4.x](https://github.com/Microsoft/botbuilder-dotnet) SDK。
* [QnA Maker][qnamaker] 可将多种类型的文本组合到一个问题答案知识库中。
* [语音](../Speech/home.md)可将口述语言请求转化成文本。 转化成文本后，LUIS 就会处理请求。 有关详细信息，请参阅[语音 SDK](https://aka.ms/csspeech)。
* [文本分析](../text-analytics/overview.md)提供情绪分析和关键短语数据提取。

## <a name="next-steps"></a>后续步骤
使用[预构建的](luis-get-started-create-app.md)或[自定义的](luis-quickstart-intents-only.md)域创建新的 LUIS 应用。

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/