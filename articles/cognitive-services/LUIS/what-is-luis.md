---
title: 什么是语言理解 (LUIS)？
description: 语言理解 (LUIS) - 一种基于云的 API 服务，将机器学习用于对话式自然语言，来预测含义和提取信息。
keywords: Azure, 人工智能, ai, 自然语言处理, nlp, 自然语言理解, nlu, ai 对话, 对话 ai, ai 聊天机器人, 聊天机器人生成器, LUIS, nlp ai, luis ai, azure luis, 理解自然语言
ms.topic: overview
ms.date: 09/02/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: ff9aa2652fe8a1e503b5e2c93ca149112770b081
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400936"
---
# <a name="what-is-language-understanding-luis"></a>什么是语言理解 (LUIS)？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

语言理解 (LUIS) 是一种基于云的 API 服务，可在用户对话的自然语言文本中应用自定义机器学习智能，以便预测整体含义并提炼出相关的详细信息。

LUIS 的客户端应用程序可以是任何传统的应用程序，只要其能够以自然语言与用户通信并完成任务即可。 这些客户端应用程序包括社交媒体应用、聊天机器人以及支持语音的桌面应用程序。

![3 个客户端应用程序使用认知服务语言理解 (LUIS) 的概念图](./media/luis-overview/luis-entry-point.png "3 个客户端应用程序使用认知服务语言理解 (LUIS) 的概念图")

## <a name="use-luis-in-a-chat-bot"></a>在聊天机器人中使用 LUIS

<a name="Accessing-LUIS"></a>

LUIS 应用一旦发布，客户端应用程序即可向 LUIS 自然语言处理终结点 [API][endpoint-apis] 发送话语（文本）并接收结果作为 JSON 响应。 LUIS 的常用客户端应用程序是聊天机器人。


![LUIS 使用聊天机器人以通过自然语言理解 (NLP) 预测用户文本的概念图](./media/luis-overview/LUIS-chat-bot-request-response.svg "LUIS 使用聊天机器人以通过自然语言理解 (NLP) 预测用户文本的概念图")

|步骤|操作|
|:--|:--|
|1|客户端应用程序将用户_话语_（采用自己的词汇的文本）“我想要呼叫 HR 代表”作为 HTTP 请求发送 给 LUIS 终结点。|
|2|LUIS 使你能够创建自定义语言模型，以便将智能添加到你的应用程序。 计算机学习的语言模型采用用户的非结构化输入文本，并以最相关意向 `HRContact` 返回 JSON 格式的响应。 JSON 终结点响应至少包含查询话语和得分最高的意向。 它还可以提取数据，例如“联系人类型”实体。|
|3|客户端应用程序根据 JSON 响应来决定如何处理用户的请求。 这些决策可能包括机器人框架代码中的决策树，以及对其他服务的调用。 |

LUIS 应用提供的智能有助于客户端应用程序进行智能选择。 LUIS 不提供这些选择。

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-understanding-nlu"></a>自然语言理解 (NLU)

[LUIS 以 NLU 的形式提供人工智能 (AI)](artificial-intelligence.md "LUIS 提供人工智能 (AI)")，NLU 是自然语言处理 (NLP) 的一个分支。

LUIS 应用包含一个特定于域的自然语言模型。 可通过预构建的域模型启动 LUIS 应用、构建你自己的模型，还可将预构建的域的各个部分与自己的自定义信息进行混合。

* **预构建的模型** LUIS 具有多个预构建的域模型，它们自带意向、话语和预构建的实体。 即使不使用预构建的模型中的意向和话语，也能使用预构建的实体。 [预构建的域模型](luis-how-to-use-prebuilt-domains.md "预生成域模型")包含适合你的整个设计，是实现 LUIS 快速入门的绝佳方式。

* “自定义模型”LUIS 提供多种方法来识别自己的自定义模型，包括意向和实体。 实体包括机器学习的实体、特定实体或文本实体，以及机器学习的实体和文本实体的组合。

进一步了解 [NLP](artificial-intelligence.md "NLP")，以及 NLU 中特定于 LUIS 的领域。

## <a name="step-1-design-and-build-your-model"></a>步骤 1：设计和生成模型

用名为[意图](luis-concept-intent.md "意向")的用户意图类别设计模型。 每个意向都需要用户 **[话语](luis-concept-utterance.md "言语")** 的示例。 每段言语都可以提供数据，需要通过[机器学习实体](luis-concept-entity-types.md#effective-machine-learned-entities "机器学习实体")来提取这些数据。

|示例用户话语|Intent|提取的数据|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight|西雅图|
|`When does your store open?`|店铺营业时间和位置|开门|
|`Schedule a meeting at 1pm with Bob in Distribution`|安排谈话|下午 1 点，何石|

使用[创作](https://go.microsoft.com/fwlink/?linkid=2092087 "创作") API 和/或 **LUIS 门户**生成模型。 了解如何使用[门户](get-started-portal-build-app.md "portal")和 [SDK 客户端库](azure-sdk-quickstart.md "SDK 客户端库")生成内容。

## <a name="step-2-get-the-query-prediction"></a>步骤 2：获取查询预测

在训练应用模型并将其发布到终结点以后，客户端应用程序（如聊天机器人）会将言语发送到预测[终结点](https://go.microsoft.com/fwlink/?linkid=2092356 "endpoint") API。 API 将模型应用到言语进行分析，并使用 JSON 格式的预测结果进行响应。

JSON 终结点响应至少包含查询话语和得分最高的意向。 它还可以提取数据，例如下面的“联系人类型”实体和整体情绪。

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
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
            "label": "neutral",
            "score": 0.5
        }
    }
}
```

## <a name="step-3-improve-model-prediction"></a>步骤 3：改进模型预测

发布 LUIS 应用并收到真实用户言语后，LUIS 提供终结点言语的[主动学习](luis-concept-review-endpoint-utterances.md "主动学习")以提高预测准确性。 作为开发生命周期中定期维护工作的一部分，请查看这些建议。

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>开发生命周期和工具
LUIS 提供工具、版本控制以及与其他 LUIS 创建者的协作，以便集成到完整的[开发生命周期](luis-concept-app-iteration.md "开发生命周期")。

作为 REST API，语言理解 (LUIS) 可以与任何发送 HTTP 请求的产品、服务或框架配合使用。 LUIS 还为多种顶级编程语言提供客户端库 (SDK)。 详细了解提供的[开发人员资源](developer-reference-resource.md "开发人员资源")。

通过机器人快速轻松地使用 LUIS 的工具：
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS "LUIS CLI") NPM 包以独立命令行工具或导入的形式提供创作和预测。
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen "LUISGen")：LUISGen 是一个用于从导出的 LUIS 模型生成强类型 C# 和 typescript 源代码的工具。
* 启用[调度](https://aka.ms/dispatch-tool "Dispatch")时，可以使用调度程序模型通过父应用使用多个 LUIS 和 QnA Maker 应用。
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown "LUDown") LUDown 是一个命令行工具，可帮助你管理机器人的语言模型。

## <a name="integrate-with-a-bot"></a>与机器人集成

使用 [Azure 机器人程序服务](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0 "Azure 机器人服务")和 [Microsoft Bot Framework](https://dev.botframework.com/ "Microsoft Bot 框架") 来生成和部署聊天机器人程序。 使用图形界面工具 [Composer](https://docs.microsoft.com/composer/ "编辑器") 或为热门机器人场景设计的[工作机器人示例](https://github.com/microsoft/BotBuilder-Samples "工作机器人示例")，进行设计和开发。

## <a name="integrate-with-other-cognitive-services"></a>与其他认知服务集成

与 LUIS 配合使用的其他认知服务：
* [QnA Maker](../QnAMaker/overview/overview.md "QnA Maker") 可将多种类型的文本组合到一个问题答案知识库中。
* [语音服务](../Speech-Service/overview.md "语音服务")可将口述语言请求转化成文本。

LUIS 提供文本分析的功能，作为现有 LUIS 资源的一部分。 此功能包括[情绪分析](luis-how-to-publish-app.md#configuring-publish-settings "情绪分析")和使用预生成 keyPhrase 实体的[关键短语提取](luis-reference-prebuilt-keyphrase.md "关键短语提取")。

## <a name="learn-with-the-quickstarts"></a>通过快速入门学习

使用[门户](get-started-portal-build-app.md "portal")和 [SDK 客户端库](azure-sdk-quickstart.md "SDK 客户端库")的实际操作快速入门来了解 LUIS。


## <a name="next-steps"></a>后续步骤

* 服务和文档的[新增内容](whats-new.md "新增功能")
* 使用[意图](luis-concept-intent.md "意向")和[实体](luis-concept-entity-types.md "实体")[计划应用](luis-how-plan-your-app.md "规划应用")。
* [查询预测终结点](luis-get-started-get-intent-from-browser.md "查询预测终结点")。
* 面向 LUIS 的[开发人员资源](developer-reference-resource.md "开发人员资源")。

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
