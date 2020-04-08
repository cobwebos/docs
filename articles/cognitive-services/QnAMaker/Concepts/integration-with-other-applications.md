---
title: 与其他应用集成 - QnA 制造商
description: QnA Maker 集成到客户端应用程序（如聊天机器人）以及其他自然语言处理服务（如语言理解 （LUIS）） 中。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: c1edbfb6badfb73ce08a99709da0f8bfb61b7dc3
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804181"
---
# <a name="design-knowledge-base-for-client-applications"></a>为客户应用程序设计知识库

QnA Maker 集成到客户端应用程序（如聊天机器人）以及其他自然语言处理服务（如语言理解 （LUIS）） 中。

## <a name="integration-with-a-conversational-client"></a>与对话客户端集成

QnA Maker 与会话客户端应用程序（如[微软机器人框架](https://dev.botframework.com/)）集成。 发送到 QnA Maker 的文本不需要清理或转换。 QnA Maker 接受自然语言并返回最佳答案。

## <a name="create-a-bot-without-writing-any-code"></a>创建自动程序而不编写任何代码

发布知识库后，通过选择"**创建自动程序"** 按钮，从 **"发布"** 页创建自动程序。 使用[自动程序教程](../Quickstarts/create-publish-knowledge-base.md)了解选择按钮后会发生什么情况。

## <a name="providing-multi-turn-conversations"></a>提供多转对话

如果答案是多转 QnA 对的一部分，则自动客户端提供来自知识库中的最佳答案，并提供后续提示。 [了解如何将](../how-to/multiturn-conversation.md)多转对话问题和答案集添加到您的知识库。

## <a name="natural-language-processing"></a>自然语言处理

虽然 QnA Maker 处理使用自然语言处理的问题，但它也可以使用一个更大的系统的一部分来回答来自多个知识库的问题。 您可以将 QnA Maker 与另一个认知服务（语言理解 （LUIS） 相结合，在到达特定知识库之前提供自然语言处理。 详细了解何时以及如何一起使用 LUIS[和 QnA 制造商](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json)。

## <a name="next-steps"></a>后续步骤

了解 QnA 制造商的开发周期[概念](development-lifecycle-knowledge-base.md)。