---
title: 使用 NLP 认知服务充实对话
titleSuffix: Azure Cognitive Services
description: 认知服务提供两种自然语言处理服务，语言理解和 QnA Maker，每个服务都有不同的用途。 了解每个服务的使用时间及其相互补充的方式。
author: diberry
ms.author: diberry
manager: nitinme
ms.topic: conceptual
ms.service: cognitive-services
ms.date: 08/01/2019
ms.openlocfilehash: 32159b37d3d1a8609181d81dc1a73f27177adb85
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818198"
---
# <a name="use-cognitive-services-with-natural-language-processing-nlp-to-enrich-bot-conversations"></a>使用认知服务和自然语言处理（NLP）来丰富机器人对话

认知服务提供两种自然语言处理服务，[语言理解](what-is-luis.md)和[QnA Maker](../qnamaker/overview/overview.md)，每个服务都有不同的用途。 了解每个服务的使用时间及其相互补充的方式。 

自然语言处理（NLP）允许你的客户端应用程序（如聊天机器人）使用自然语言处理你的用户。 用户输入句子或短语。 用户的文本可能具有不良语法、拼写和标点。 认知服务仍可以通过用户句子，返回聊天 bot 需要帮助用户的信息。 

## <a name="cognitive-services-with-nlp"></a>NLP 的认知服务

语言理解（LUIS）和 QnA Maker 提供 NLP。 客户端应用程序提交自然语言文本。 服务获取文本，对其进行处理并返回结果。 

## <a name="when-to-use-each-service"></a>何时使用每个服务

语言理解（LUIS）和 QnA Maker 解决了不同的问题。 LUIS 确定用户的文本用途（称为查询文本），QnA Maker 确定用户文本的答案（称为 "查询"）。 

为了选取正确的服务，您需要了解来自客户端应用程序的用户文本，以及客户端应用程序需要从认知服务获取哪些信息。

如果聊天机器人收到文本 `How do I get to the Human Resources building on the Seattle North campus?`，请使用下面的图表来了解每个服务如何处理文本。

|服务|客户端应用程序确定|
|--|--|
|LUIS|**确定用户**的文本意向-服务不会将问题的答案返回给问题。 例如，此文本归类为匹配 `FindLocation` 意向。<br>|
|QnA Maker|返回自定义知识库中**的问题的答案**。 例如，将此文本确定为 `Get on the #9 bus and get off at Franklin street`的静态文本应答的问题。|
|||

## <a name="when-do-you-use-luis"></a>何时使用 LUIS？ 

当你需要知道查询文本的意图作为聊天机器人中进程的一部分时，请使用 LUIS。 继续使用示例文本 `How do I get to the Human Resources building on the Seattle North campus?`，一旦知道用户的意图是查找位置，则可以将有关查询文本的详细信息（使用实体提取）传递到其他服务（例如运输服务器）以获取答案。 

无需结合 LUIS 和 QnA Maker 来确定目的。 

如果聊天机器人需要基于意向和实体处理文本（使用 LUIS），并查找特定静态文本答案（使用 QnA Maker），则可以将这两个服务组合在一起。

## <a name="when-do-you-use-qna-maker"></a>何时使用 QnA Maker？ 

如果有一个静态知识库，请使用 QnA Maker。 此知识库根据你的需要进行了自定义，你可以通过 Pdf 和 Url 等文档来构建。 

继续使用示例查询文本 `How do I get to the Human Resources building on the Seattle North campus?`，将文本作为查询发送到已发布的 QnA Maker 服务，并获得最佳答案。 

无需合并 LUIS 和 QnA Maker 来确定问题的答案。

如果聊天机器人需要基于意向和实体处理文本（使用 LUIS），并找到答案（使用 QnA Maker），则可以将这两个服务组合在一起。

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>如果知识库未完成，则使用这两项服务

如果你正在构建 QnA Maker 知识库，但知道使用者域正在更改（例如及时信息），则可以合并 LUIS 和 QnA Maker 服务。 这使您可以使用知识库中的信息，还可以使用 LUIS 来确定用户的意图。 客户端应用程序获得意图后，可以从另一个源请求相关信息。 

客户端应用程序需要监视评分的 LUIS 和 QnA Maker 响应。 如果 QnA Maker 的分数低于某些任意阈值，请使用从 LUIS 返回的意向和实体信息将信息传递给第三方服务。

继续使用示例文本 `How do I get to the Human Resources building on the Seattle North campus?`，假设 QnA Maker 返回置信度较低的分数。 使用从 LUIS、`FindLocation` 和任何提取的实体（如 `Human Resources building` 和 `Seattle North campus`）返回的意向，将此信息发送到映射或搜索服务，以获得其他答案。 

您可以向用户提供此第三方的验证答案。 用户获得批准后，可以返回 QnA Maker 来添加信息，以扩大你的知识。 

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>当聊天机器人需要更多信息时，请使用这两项服务

如果聊天机器人需要更多的信息，而不是提供服务，若要继续通过决策树，请在客户端应用程序中使用这两个服务并处理这两个响应。 

使用 Bot framework **[调度 CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** 工具来帮助生成用于处理这两种服务的过程。 此工具将生成一个 LUIS 的顶级应用程序，该应用程序在 LUIS 和 QnA Maker 之间调度为子应用。 

使用 Bot 生成器示例, **使用NLP** [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch)和 [node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch) 实现此类聊天机器人。 

## <a name="best-practices"></a>最佳实践

实现每个服务的最佳实践：

* [LUIS](luis-concept-best-practices.md)最佳实践
* [QnA Maker](../qnamaker/concepts/best-practices.md)最佳实践

## <a name="see-also"></a>另请参阅

* [语言理解 (LUIS)](what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [调度 CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [机器人框架示例](https://github.com/Microsoft/BotBuilder-Samples)
* [Azure 机器人服务](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Azure 机器人模拟器](https://github.com/Microsoft/BotFramework-Emulator)
* [机器人 framework web 聊天](https://github.com/microsoft/BotFramework-WebChat)