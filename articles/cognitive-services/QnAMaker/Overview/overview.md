---
title: QnA Maker 服务是什么？
description: QnA Maker 是一个基于云的 NLP 服务，它可以轻松地基于数据创建自然对话层。 使用该服务可以基于自定义的信息知识库 (KB)，针对任何给定的自然语言输入查找最适当的回答。
ms.topic: overview
ms.date: 05/26/2020
ms.openlocfilehash: 5962ab2294a19c600beb00816394d96059bd4108
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873725"
---
# <a name="what-is-the-qna-maker-service"></a>QnA Maker 服务是什么？

[!INCLUDE [TLS 1.2 enforcement](../../../../includes/cognitive-services-tls-announcement.md)]

QnA Maker 是一个基于云的自然语言处理 (NLP) 服务，它可以轻松地基于数据创建自然对话层。 使用该服务可以基于自定义的信息知识库 (KB)，针对任何给定的自然语言输入查找最适当的回答。

QnA Maker 的客户端应用程序是能够以自然语言与用户通信并回答问题的任何传统应用程序。 这些客户端应用程序包括社交媒体应用、聊天机器人以及支持语音的桌面应用程序。

## <a name="when-to-use-qna-maker"></a>何时使用 QnA Maker

* **包含静态信息时** - 如果回答知识库中包含静态信息，可使用 QnA Maker。 此知识库是根据你的需要自定义的，其内容是使用 [PDF 和 URL](../concepts/content-types.md) 等文档生成的。
* **想要对某个请求、问题或命令提供相同的回答时** - 如果不同的用户提交相同的问题，则返回相同的回答。
* **想要基于元信息筛选静态信息时** - 添加[元数据](../how-to/metadata-generateanswer-usage.md)标记，以提供与客户端应用程序的用户和信息相关的附加筛选选项。 常见的元数据信息包括[聊天内容](../how-to/chit-chat-knowledge-base.md)、内容类型或格式、内容目的和内容新鲜度。
* **想要管理包含静态信息的机器人聊天时** - 知识库提取用户的聊天文本或命令，并予以回答。 如果回答是预先确定的聊天流（在知识库中使用[多轮次上下文](../how-to/multiturn-conversation.md)表示）的一部分，则机器人可以轻松提供此流。

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>在聊天机器人中使用 QnA Maker 知识库

发布 QnA Maker 知识库后，客户端应用程序会将问题发送到知识库终结点，并接收 JSON 响应形式的结果。 QnA Maker 的常用客户端应用程序是聊天机器人。

![向机器人提问并从知识库内容获取回答](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|步骤|操作|
|:--|:--|
|1|客户端应用程序将用户的问题（用他们自己的语言表达的文本）“如何以编程方式更新我的知识库？” 发送到你的知识库终结点。|
|2|QnA Maker 使用经过训练的知识库提供正确的回答，并提供可用于具体化搜索以获得最佳回答的任何后续提示。 QnA Maker 返回 JSON 格式的响应。|
|3|客户端应用程序使用 JSON 响应在如何继续聊天方面做出决策。 这些决策可能包括显示最相关的回答，以及提供更多选项用于优化搜索以获得最佳回答。 |
|||

## <a name="what-is-a-knowledge-base"></a>知识库是什么？

QnA Maker [将内容导入](../concepts/knowledge-base.md)问题和回答集知识库。 导入过程提取有关结构化和半结构化内容部分之间的关系的信息，以暗示问题与回答集之间的关系。 可编辑这些问题与回答集，或添加新集。

问题与回答集的内容包括：
* 问题的所有替代形式
* 用于在搜索期间筛选回答选择的元数据标记
* 用于继续优化搜索的跟进提示

![带有元数据的示例问题和回答](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

发布知识库之后，客户端应用程序会将用户的问题发送到你的终结点。 QnA Maker 服务将处理问题，并以最佳回答做出响应。

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>在不使用代码的情况下创建、管理和发布到机器人

QnA Maker 门户提供完整的知识库创作体验。 可将文档以其当前格式导入到知识库。 这些文档（例如 FAQ、产品手册、电子表格或网页）将转换为问题和回答集。 系统将分析每个集以提供后续提示并将其连接到其他集。 最终的 _markdown_ 格式支持丰富的表示形式，包括图像和链接。

编辑知识库后，无需编写任何代码即可将知识库发布到正常工作的 [Azure Web 应用机器人](https://azure.microsoft.com/services/bot-service/)。 在 [Azure 门户](https://portal.azure.com)中测试机器人，或下载并继续进行开发。

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>搜索质量和排名提供尽可能最佳的回答

QnA Maker 的系统采用分层排名方法。 数据存储在 Azure 搜索（同样充当第一个排名层）中。 然后，来自 Azure 搜索的最相关结果将通过 QnA Maker 的 NLP 重新排名模型传递，以生成最终结果和置信度评分。

## <a name="qna-maker-improves-the-conversation-process"></a>QnA Maker 改善了聊天过程

QnA Maker 提供多轮次提示和主动学习来帮助你改善基本的问题和回答集。

**多轮次提示**使你有机会连接问题和回答对。 客户端应用程序可通过此连接提供最相关的回答，并提供更多问题来具体化搜索以获得最终回答。

知识库收到已发布终结点中用户的问题后，QnA Maker 将对这些实际问题应用**主动学习**，以建议对知识库做出哪些更改来提高质量。

## <a name="development-lifecycle"></a>开发生命周期

QnA Maker 提供可集成到整个开发生命周期的创作、训练、发布和协作权限。

> [!div class="mx-imgBorder"]
> ![开发周期的概念图](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="how-do-i-start"></a>如何开始？

**步骤 1**：在 [Azure 门户](https://portal.azure.com)中创建一个 QnA Maker 资源。

**步骤 2**：在 [QnA Maker](https://www.qnamaker.ai) 门户中创建一个知识库。 添加用于创建知识库的[文件和 URL](../concepts/content-types.md)。

**步骤 3**：发布知识库，并使用 [cURL 或 Postman](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) 通过自定义终结点进行测试。

**步骤 4**：通过客户端应用程序，以编程方式调用知识库的终结点。 客户端应用程序处理 JSON 响应，以向用户显示最佳回答。

## <a name="next-steps"></a>后续步骤
QnA Maker 提供生成、管理和部署自定义知识库所需的全部功能。

> [!div class="nextstepaction"]
> [查看最新更改](../whats-new.md)
