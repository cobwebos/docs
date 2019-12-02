---
title: 什么是语言理解 (LUIS)？
titleSuffix: Azure Cognitive Services
description: 语言理解 (LUIS) 是一种基于云的 API 服务，可在用户对话的自然语言文本中应用自定义机器学习智能，以便预测整体含义并提炼出相关的详细信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 99f312521727658788e96a57b619a7c0e3d4751b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456568"
---
# <a name="what-is-language-understanding-luis"></a>什么是语言理解 (LUIS)？

语言理解 (LUIS) 是一种基于云的 API 服务，可将自定义机器学习智能应用到自然语言文本，以便预测整体含义并提炼出相关的详细信息。 

例如，当客户端应用程序发送文本 `find me a wireless keyboard for $30` 时，LUIS 使用以下 JSON 对象进行响应。 

```JSON
{
    "query": "find me a wireless keyboard for $30",
    "prediction": {
        "topIntent": "Finditem",
        "intents": {
            "Finditem": {
                "score": 0.934672
            }
        },
        "entities": {
            "item": [
                "wireless keyboard"
            ],
            "money": [
        {
            "number": 30,
            "units": "Dollar"
        }
           ]
        }
        
    }
}
```
在上面的示例中， _**意向**_ 或者说短语的整体含义为用户正在尝试查找某个项。 LUIS 提取的详细信息片段称为 _**实体**_ 。 在本例中，实体为用户要查找的项的名称以及他们要花费的资金的金额。

客户端应用程序使用 LUIS 的返回的 JSON、_意向_（类别）和_实体_（提取的详细信息）来驱动客户端应用程序中的操作。 LUIS 的客户端应用程序通常是对话应用程序，其使用自然语言与用户通信，以便完成任务。 这些客户端应用程序包括社交媒体应用、聊天机器人以及支持语音的桌面应用程序。 

![3 个客户端应用程序使用认知服务语言理解 (LUIS) 的概念图](./media/luis-overview/luis-entry-point.png "3 个客户端应用程序使用认知服务语言理解 (LUIS) 的概念图")

## <a name="example-use-luis-in-a-chat-bot"></a>在聊天机器人中使用 LUIS 示例

<a name="Accessing-LUIS"></a>

客户端应用程序向已发布的 LUIS 自然语言处理终结点 [API][endpoint-apis] 发送言语（文本）并将结果作为 JSON 响应接收。 LUIS 的常用客户端应用程序是聊天机器人。


![LUIS 使用聊天机器人以通过自然语言理解 (NLP) 预测用户文本的概念图](./media/luis-overview/LUIS-chat-bot-request-response.svg "LUIS 使用聊天机器人以通过自然语言理解 (NLP) 预测用户文本的概念图像")

|步骤|操作|
|:--|:--|
|1|客户端应用程序将用户_话语_（采用自己的词汇的文本）“我想要呼叫 HR 代表”作为 HTTP 请求发送 给 LUIS 终结点。|
|2|LUIS 将机器习得的语言模型应用到用户的非结构化输入文本，并返回包含得分最高意向 `HRContact` 的 JSON 格式的响应。 JSON 终结点响应至少包含查询话语和得分最高的意向。 它还可以提取数据，例如“联系人类型”  实体。|
|3|客户端应用程序根据 JSON 响应来决定如何处理用户的请求。 这些决策可能包括机器人中的决策树，以及对其他服务的调用。 |

LUIS 应用提供的智能有助于客户端应用程序进行智能选择。 LUIS 不提供这些选择。 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>自然语言处理

LUIS 应用包含特定于域的自然语言模型，这些模型协同工作。 可通过一个或多个预生成的模型启动 LUIS 应用、生成自己的模型，还可将预生成的模型与自己的自定义信息混合。

* **预生成的模型** LUIS 具有许多预生成的域，其中包括用于完成常见使用方案的意向和实体模型，这些意向和实体模型协同工作。 这些域包括可进行检查和编辑的已标记言语，让你能够对其进行自定义。 [预构建的域模型](luis-how-to-use-prebuilt-domains.md)包含适合你的整个设计，是实现 LUIS 快速入门的绝佳方式。 此外，还有一些预生成的实体，例如可独立于预生成的域使用的货币和数字。

* **自定义模型** LUIS 提供多种方法用于生成自己的自定义模型，包括意向和实体。 实体包括机器习得实体、模式匹配实体，以及机器习得实体和模式匹配实体的组合。

## <a name="build-the-luis-app"></a>生成 LUIS 应用
使用[创作](https://go.microsoft.com/fwlink/?linkid=2092087) API 或 [LUIS 门户](https://www.luis.ai)生成应用。

LUIS 应用从称为 **[意向](luis-concept-intent.md)** 的输入文本类别入手。 每个意向都需要用户 **[话语](luis-concept-utterance.md)** 的示例。 每个言语都可以提供需要提取的数据。 

|示例用户话语|Intent|提取的数据|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|BookFlight|西雅图|
|`When does your store __open__?`|店铺营业时间和位置|开门|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|安排谈话|下午 1 点，何石|

## <a name="query-prediction-endpoint"></a>查询预测终结点

在训练应用并将其发布到终结点以后，客户端应用程序会将言语发送到预测[终结点](https://go.microsoft.com/fwlink/?linkid=2092356) API。 API 将应用程序应用到言语进行分析，并使用 JSON 格式的预测结果进行响应。  

JSON 终结点响应至少包含查询话语和得分最高的意向。 它还可以提取数据，例如下面的“联系人类型”  实体和整体情绪。 

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "normalizedQuery": "i want to call my hr rep",
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "negative",
            "score": 0.103343368
        }
    }
}
```

## <a name="improve-model-prediction"></a>改进模型预测

发布 LUIS 应用并收到真实用户言语后，LUIS 提供终结点言语的[主动学习](luis-concept-review-endpoint-utterances.md)以提高预测准确性。 

<a name="using-luis"></a>

## <a name="iterative-development-lifecycle"></a>迭代开发生命周期
LUIS 提供工具、版本控制以及与其他 LUIS 创建者的协作，以便集成到完整的迭代[开发生命周期](luis-concept-app-iteration.md)。 

## <a name="implementing-luis"></a>实现 LUIS
作为 REST API，语言理解 (LUIS) 可以与任何发送 HTTP 请求的产品、服务或框架配合使用。 以下列表包含与 LUIS 配合使用的顶级 Microsoft 产品和服务。

LUIS 的顶级客户端应用程序是：
* [Web 应用机器人](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)可以快速创建支持 LUIS 的聊天机器人，该机器人可以通过文本输入与用户交谈。 使用 [Bot Framework][bot-framework] [4.x](https://github.com/Microsoft/botbuilder-dotnet) 版以获取完整的机器人体验。

通过机器人快速轻松地使用 LUIS 的工具：
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS)：NPM 包以独立命令行工具或导入的形式提供创作和预测。 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen)：LUISGen 是一个用于从导出的 LUIS 模型生成强类型 C# 和 typescript 源代码的工具。
* 启用[调度](https://aka.ms/dispatch-tool)时，可以使用调度程序模型通过父应用使用多个 LUIS 和 QnA Maker 应用。
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown)：LUDown 是一个命令行工具，可帮助你管理机器人的语言模型。
* [Bot Framework - Composer](https://github.com/microsoft/BotFramework-Composer) - 一个集成开发工具，供开发人员和多学科团队通过 Microsoft Bot Framework 构建机器人和聊天体验

与 LUIS 配合使用的其他认知服务：
* [QnA Maker][qnamaker] 可将多种类型的文本组合到一个问题答案知识库中。
* [语音服务](../Speech-Service/overview.md)可将口述语言请求转化成文本。 
* 可以使用[聊天学习器](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview)，通过 LUIS 更快速地生成机器人聊天内容。

使用 LUIS 的示例：
* [对话 AI](https://github.com/Microsoft/AI) GitHub 存储库。
* [机器人框架 - 机器人示例](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>后续步骤

* [新增功能](whats-new.md)
* 使用[预生成的](luis-get-started-create-app.md)或[自定义的](luis-quickstart-intents-only.md)域创作新的 LUIS 应用
* [查询公用 IoT 应用的预测终结点](luis-get-started-get-intent-from-browser.md)。 
* 面向 LUIS 的[开发人员资源](developer-reference-resource.md)。 

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
