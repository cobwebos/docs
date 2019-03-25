---
title: 什么是语言理解 (LUIS) - Azure 认知服务 | Microsoft Docs
description: 语言理解 (LUIS) 是一种基于云的 API 服务，可在用户对话的自然语言文本中应用自定义机器学习智能，以便预测整体含义并提炼出相关的详细信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: c26f3d6c3b34ed90a5455bb94ea71d7556b47751
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441961"
---
# <a name="what-is-language-understanding-luis"></a>什么是语言理解 (LUIS)？

语言理解 (LUIS) 是一种基于云的 API 服务，可在用户对话的自然语言文本中应用自定义机器学习智能，以便预测整体含义并提炼出相关的详细信息。 

LUIS 的客户端应用程序可以是任何传统的应用程序，只要其能够以自然语言与用户通信并完成任务即可。 这些客户端应用程序包括社交媒体应用、聊天机器人以及支持语音的桌面应用程序。  

![使用认知服务语言理解 (LUIS) 的 3 个客户端应用程序的概念图像](./media/luis-overview/luis-entry-point.png "使用认知服务语言理解 (LUIS) 的 3 个客户端应用程序的概念图像")

## <a name="use-luis-in-a-chat-bot"></a>在聊天机器人中使用 LUIS

<a name="Accessing-LUIS"></a>

LUIS 应用一旦发布，客户端应用程序即可向 LUIS 自然语言处理终结点 [API][endpoint-apis] 发送话语（文本）并接收结果作为 JSON 响应。 LUIS 的常用客户端应用程序是聊天机器人。


![使用聊天机器人通过自然语言理解 (NLP) 来预测用户文本的 LUIS 的概念图像](./media/luis-overview/luis-overview-process-2.png "使用聊天机器人通过自然语言理解 (NLP") 来预测用户文本的 LUIS 的概念图像

|步骤|操作|
|:--|:--|
|1|客户端应用程序将用户_话语_（采用自己的词汇的文本）“我想要呼叫 HR 代表”作为 HTTP 请求发送 给 LUIS 终结点。|
|2|LUIS 将学习过的模型应用到自然语言文本，以便提供有关用户输入的智能理解。 LUIS 返回包含得分最高意向“HRContact”的 JSON 格式的响应。 JSON 终结点响应至少包含查询话语和得分最高的意向。 它还可以提取数据，例如“联系人类型”实体。|
|3|客户端应用程序根据 JSON 响应来决定如何处理用户的请求。 这些决策可能包括机器人框架代码中的某棵决策树，以及对其他服务的调用。 |

LUIS 应用提供的智能有助于客户端应用程序进行智能选择。 LUIS 不提供这些选择。 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>自然语言处理

LUIS 应用包含一个特定于域的自然语言模型。 可通过预构建的域模型启动 LUIS 应用、构建你自己的模型，还可将预构建的域的各个部分与自己的自定义信息进行混合。

* **预构建的模型** LUIS 具有多个预构建的域模型，它们自带意向、话语和预构建的实体。 即使不使用预构建的模型中的意向和话语，也能使用预构建的实体。 [预构建的域模型](luis-how-to-use-prebuilt-domains.md)包含适合你的整个设计，是实现 LUIS 快速入门的绝佳方式。

* **自定义实体** LUIS 提供多种方式来自行标识自定义的意向和实体，比如机器学习到的实体、特定实体或文本实体，以及机器学习到的实体和文本实体的组合。

## <a name="build-the-luis-model"></a>构建 LUIS 模型
使用[创作](https://aka.ms/luis-authoring-apis) API 或 LUIS 门户构建模型。

LUIS 模型从称为**[意向](luis-concept-intent.md)** 的用户意向的类别开始。 每个意向都需要用户**[话语](luis-concept-utterance.md)** 的示例。 每个话语都可以提供各种数据，这些数据需要通过**[实体](luis-concept-entity-types.md)** 来提取。 

|示例用户话语|意向|实体|
|-----------|-----------|-----------|
|“预订到西雅图的航班？”|BookFlight|西雅图|
|“你的店铺何时开门？”|店铺营业时间和位置|开门|
|“安排下午 1 点与分销部的何石谈话”|安排谈话|下午 1 点，何石|

## <a name="query-prediction-endpoint"></a>查询预测终结点

在生成模型并将其发布到终结点以后，客户端应用程序会将话语发送到已发布的预测[终结点](https://aka.ms/luis-endpoint-apis) API。 API 将模型应用于要分析的文本。 API 使用 JSON 格式的预测结果进行响应。  

JSON 终结点响应至少包含查询话语和得分最高的意向。 它还可以提取数据，例如下面的“联系人类型”实体。 

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

## <a name="improve-model-prediction"></a>改进模型预测

在 LUIS 模型发布和接收实时用户话语之后，LUIS 提供了多种方式来提升预测的准确性：针对终结点话语的[主动学习](luis-concept-review-endpoint-utterances.md)、针对专业领域字词内容的[短语列表](luis-concept-feature.md)，以及用于减少所需话语数的[模式](luis-concept-patterns.md)。

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>开发生命周期
LUIS 提供工具、版本控制以及与其他 LUIS 创建者的协作，以便在客户端应用程序和语言模型级别集成到完整的开发生命周期。 

## <a name="implementing-luis"></a>实现 LUIS
作为 REST API，LUIS 可以与任何发送 HTTP 请求的产品、服务或框架配合使用。 以下列表包含与 LUIS 配合使用的顶级 Microsoft 产品和服务。

LUIS 的顶级客户端应用程序是：
* [Web 应用机器人](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-3.0)可以快速创建支持 LUIS 的聊天机器人，该机器人可以通过文本输入与用户交谈。 使用 [Bot Framework][bot-framework] [3.x](https://github.com/Microsoft/BotBuilder) 或 [4.x](https://github.com/Microsoft/botbuilder-dotnet) 版以获取完整的机器人体验。

通过机器人快速轻松地使用 LUIS 的工具：
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS)：NPM 包以独立命令行工具或导入的形式提供创作和预测。 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen)：LUISGen 是一个用于从导出的 LUIS 模型生成强类型 C# 和 typescript 源代码的工具。
* 启用[调度](https://aka.ms/dispatch-tool)时，可以使用调度程序模型通过父应用使用多个 LUIS 和 QnA Maker 应用。
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown)：LUDown 是一个命令行工具，可帮助你管理机器人的语言模型。

与 LUIS 配合使用的其他认知服务：
* [QnA Maker][qnamaker] 可将多种类型的文本组合到一个问题答案知识库中。
* [必应拼写检查 API](../bing-spell-check/proof-text.md) 提供预测前的文本更正功能。 
* [语音服务](../Speech-Service/overview.md)可将口述语言请求转化成文本。 
* 可以使用[聊天学习器](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview)，通过 LUIS 更快速地生成机器人聊天内容。
* [项目个性聊天](https://docs.microsoft.com/azure/cognitive-services/project-personality-chat/overview)，用于处理机器人小型谈话。

使用 LUIS 的示例：
* [对话 AI](https://github.com/Microsoft/AI) GitHub 存储库。
* [语言理解](https://github.com/Azure-Samples/cognitive-services-language-understanding) Azure 示例

## <a name="next-steps"></a>后续步骤

使用[预构建的](luis-get-started-create-app.md)或[自定义的](luis-quickstart-intents-only.md)域创作新的 LUIS 应用。 [查询公用 IoT 应用的预测终结点](luis-get-started-cs-get-intent.md)。

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/
