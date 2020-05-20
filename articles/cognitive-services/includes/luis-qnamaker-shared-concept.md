---
title: include 文件
description: include 文件
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/16/2020
ms.author: diberry
ms.openlocfilehash: 1c3631b4a2964c5e3a8d8267d1934a5822966342
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673435"
---
认知服务提供两种自然语言处理服务，[语言理解](../luis/what-is-luis.md)和[QnA Maker](../qnamaker/overview/overview.md)，每个服务都有不同的用途。 本文介绍每个服务的使用时机，以及两者如何相辅相成。

自然语言处理 (NLP) 可让客户端应用程序（例如聊天机器人）使用自然语言来与用户交互。 用户输入句子或短语。 用户输入的文本可能存在语法、拼写和标点符号不当的情况。 认知服务仍可以处理用户的句子，并返回聊天机器人为用户提供帮助所需的信息。

## <a name="cognitive-services-with-nlp"></a>包含 NLP 的认知服务

语言理解 (LUIS) 和 QnA Maker 提供 NLP。 客户端应用程序提交自然语言文本。 服务提取文本，对其进行处理，然后返回结果。

## <a name="when-to-use-each-service"></a>每个服务的使用时机

语言理解 (LUIS) 和 QnA Maker 解决不同的问题。 LUIS 确定用户文本（称为言语）的意向，QnA Maker 确定用户文本（称为查询）的答案。

若要选择正确的服务，需要理解来自客户端应用程序的用户文本，以及客户端应用程序需要从认知服务获取哪些信息。

如果聊天机器人收到文本 `How do I get to the Human Resources building on the Seattle North campus?`，请使用以下图表来理解每个服务如何处理该文本。

|服务|客户端应用程序确定|
|--|--|
|LUIS|**确定文本中的用户意图** - 服务不会返回问题的答案。 例如，此文本分类为与 `FindLocation` 意向匹配。<br>|
|QnA Maker|从自定义知识库**返回问题的答案**。 例如，此文本已确定为一个问题，其答案是静态文本 `Get on the #9 bus and get off at Franklin street`。|
|||

> [!div class="mx-imgBorder"]
> ![信息图确定何时使用 LUIS 以及何时使用 QnA Maker](./luis-qna-maker-together-decision.png)

## <a name="when-do-you-use-luis"></a>何时使用 LUIS？

如果聊天机器人在处理过程中需要知道言语的意图，请使用 LUIS。 沿用示例文本 `How do I get to the Human Resources building on the Seattle North campus?`。知道用户的意图是查找某个地点后，可将有关言语的详细信息（使用实体提取）传递给另一服务（例如交通服务器）来获取答案。

无需结合使用 LUIS 和 QnA Maker 即可确定意向。

如果聊天机器人需要根据意图和实体处理文本（使用 LUIS），并需要查找具体的静态文本答案（使用 QnA Maker），则你可以对言语结合使用这两个服务。

## <a name="when-do-you-use-qna-maker"></a>何时使用 QnA Maker？

如果已有一个静态的答案知识库，请使用 QnA Maker。 此知识库是根据需要自定义的，其内容是使用 PDF 和 URL 等文档生成的。

沿用示例言语 `How do I get to the Human Resources building on the Seattle North campus?`。将该文本作为查询发送到已发布的 QnA Maker 服务，然后接收最佳答案。

无需结合使用 LUIS 和 QnA Maker 即可确定问题的答案。

如果聊天机器人需要根据意图和实体处理文本（使用 LUIS），并需要查找答案（使用 QnA Maker），则你可以对言语结合使用这两个服务。

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>如果知识库不完整，请使用这两个服务

如果你正在生成 QnA Maker 知识库，但知道主题域会发生更改（例如，需要及时提供信息），则可以结合使用 LUIS 和 QnA Maker 服务。 这样就可以使用知识库中的信息，同时还可以使用 LUIS 来确定用户的意图。 客户端应用程序获得意图后，可以从另一个源请求相关信息。

客户端应用程序需要监视 LUIS 和 QnA Maker 响应中的评分。 如果 QnA Maker 的评分低于某个任意阈值，请使用 LUIS 返回的意向和实体信息并将其传递给第三方服务。

沿用示例文本 `How do I get to the Human Resources building on the Seattle North campus?`。假设 QnA Maker 返回了较低的置信度评分。 使用 LUIS 返回的意向、`FindLocation` 和提取的任何实体（例如 `Human Resources building` 和 `Seattle North campus`），将此信息发送到地图或搜索服务以获取其他答案。

可将此第三方答案提供给用户进行验证。 获得的用户批准后，可以返回 QnA Maker 来添加信息，以扩充知识。

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>当聊天机器人需要更多信息时，请使用这两个服务

如果聊天机器人所需的信息超过了服务能够提供的信息，请在客户端应用程序中使用这两个服务并处理两者的响应，这样才能在决策树中递进。

借助 Bot Framework **[Dispatch CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** 工具生成一个流程来处理这两个服务。 此工具将为意向生成一个顶层 LUIS 应用，用于调度充当子应用的 LUIS 和 QnA Maker。 [详细了解](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=cs)如何与 LUIS、QnA Maker 和机器人框架集成。

使用以 [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) 或 [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch) 编写的机器人生成器示例**具有调度功能的 NLP** 可以实现此类聊天机器人。

## <a name="best-practices"></a>最佳实践

实施适用于每个服务的最佳做法：

* [LUIS](../luis/luis-concept-best-practices.md) 最佳做法
* [QnA Maker](../qnamaker/concepts/best-practices.md)最佳实践

## <a name="see-also"></a>另请参阅

* [语言理解 (LUIS)](../luis/what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [调度 CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [机器人框架示例](https://github.com/Microsoft/BotBuilder-Samples)
* [Azure 机器人服务](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Azure 机器人仿真器](https://github.com/Microsoft/BotFramework-Emulator)
* [Bot Framework 网上聊天](https://github.com/microsoft/BotFramework-WebChat)