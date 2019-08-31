---
title: 知识库 ID - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 知识库包含一组问题/答案 (QnA) 对和与每个 QnA 对关联的可选元数据。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 78b04a06b71590a79180d1ae367e7d059e1b84a3
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195237"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>QnA Maker 知识库是什么？

QnA Maker 知识库包含一组问题/答案 (QnA) 对和与每个 QnA 对关联的可选元数据。

## <a name="key-knowledge-base-concepts"></a>重要知识库概念

* **问题** - 问题包含最能代表用户查询的文本。 
* **答案** - 答案是当用户查询与关联的问题匹配时返回的响应。  
* **元数据** - 元数据是与 QnA 对关联的标记，以键值对形式表示。 元数据标记用于筛选 QnA 对并限制对其执行查询匹配的集。

单个 QnA（由数字 QnA ID 表示）包含一个问题的多个变体（备用问题），这些变体全部映射到一个回答。 此外, 每个此类对都可以有多个与之关联的元数据字段: 一个键和一个值。

![QnA Maker 知识库](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>知识库内容格式

向某一知识库引入丰富的内容时，QnA Maker 会尝试将内容转换为 markdown。 阅读[此](https://aka.ms/qnamaker-docs-markdown-support)博客以了解大多数聊天客户端可理解的 markdown 格式。

元数据字段包含由冒号分隔的键值对 **(Product:Shredder)** 。 键和值都必须是纯文本。 元数据密钥不得包含任何空格。 元数据仅支持每个键一个值。

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker 如何处理用户查询以选择最佳答案

训练和[发布](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base)的 QnA Maker 知识库通过[GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)从机器人或其他客户端应用程序接收用户查询。 下图说明了收到用户查询时的过程。

![用户查询的排名过程](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Ranker 进程

下表对此过程进行了说明:

|步骤|用途|
|--|--|
|1|客户端应用程序将用户查询发送到[GENERATEANSWER API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)。|
|2|Qna Maker 通过语言检测、spellers 和断字符来预处理用户查询。|
|3|此预处理用于更改用户查询以获得最佳搜索结果。|
|4|此更改的查询将发送到 Azure 搜索索引, 并`top`收到结果数。 如果这些结果中没有正确答案, 请将值`top`略微增大。 通常, 90% 的查询`top`中的值为10。|
|5|QnA Maker 会应用高级特征化, 以确定获取用户查询的 Azure 搜索结果的正确性。 |
|6|训练的 ranker 模型使用步骤5中的功能分数对 Azure 搜索结果进行排序。|
|7|新结果将按顺序返回到客户端应用程序。|
|||

使用的功能包括但不限于 word 级语义、语料库中的术语级别重要性, 以及深入了解的语义模型, 以确定两个文本字符串之间的相似性和相关性。

## <a name="http-request-and-response-with-endpoint"></a>带有终结点的 HTTP 请求和响应
发布知识库时, 该服务会创建一个基于 REST 的 HTTP**终结点, 该终结点**可以集成到应用程序中, 通常是一个聊天机器人。 

### <a name="the-user-query-request-to-generate-an-answer"></a>用于生成答案的用户查询请求

**用户查询**是最终用户要求使用知识库的问题, 例如`How do I add a collaborator to my app?`。 查询通常采用自然语言格式或表示问题的几个关键字, 如`help with collaborators`。 查询将从客户端应用程序中的 HTTP**请求**发送到你的知识。

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

可以通过设置[scoreThreshold](./confidence-score.md#choose-a-score-threshold)、 [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)和[strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags)等属性来控制响应。

使用[会话上下文](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context)和[多轮功能](../how-to/multiturn-conversation.md), 使对话进入优化问题和答案, 找到正确且最终的答案。

### <a name="the-response-from-a-call-to-generate-answer"></a>调用生成答案的响应

HTTP**响应**是基于给定用户查询的最佳匹配项从知识库中检索到的答案。 响应包括答案和预测分数。 如果您要求使用`top`属性, 则可以获得多个顶级答案, 其中每个都有一个分数。 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

### <a name="test-and-production-knowledge-base"></a>测试和生产知识库
知识库是通过 QnA Maker 创建、维护和使用的问题和答案的存储库。 每个 QnA Maker 层均可用于多个知识库。

知识库有两种状态 -“测试”和“已发布”。 

**测试知识库**是要编辑、保存和测试的版本, 以确保响应的准确性和完整性。 对测试知识库所做的更改不会影响应用程序/聊天机器人的最终用户。 测试知识库`test`在 HTTP 请求中称为。 

**已发布的知识库**是在聊天机器人/应用程序中使用的版本。 发布知识库的操作会将测试知识库的内容置于知识库的已发布版本中。 由于已发布的知识库是应用程序通过终结点使用的版本，因此应注意确保内容正确且经过良好测试。 已发布的知识库`prod`在 HTTP 请求中称为。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [知识库的开发生命周期](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>请参阅

[QnA Maker 概述](../Overview/overview.md)

创建和编辑知识库: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.Net SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

生成的答案: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.Net SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
