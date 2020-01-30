---
title: 查询知识库-QnA Maker
description: 必须发布知识库。 发布后，将使用 generateAnswer API 在运行时预测终结点上查询知识库。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb777aa16fada50811cce1bbf49f28662c62b49b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843305"
---
# <a name="query-the-knowledge-base-for-answers"></a>查询知识库以获取答案

必须发布知识库。 发布后，将使用 generateAnswer API 在运行时预测终结点上查询知识库。 查询包含问题文本和其他设置，以帮助 QnA Maker 选择最佳匹配项。

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker 如何处理用户查询以选择最佳答案

训练和[发布](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base)的 QnA Maker 知识库通过[GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)从机器人或其他客户端应用程序接收用户查询。 下图说明了收到用户查询时的过程。

![用户查询的排名模型过程](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Ranker 进程

下表对此过程进行了说明。

|步骤|用途|
|--|--|
|第|客户端应用程序将用户查询发送到[GENERATEANSWER API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)。|
|2|QnA Maker 通过语言检测、spellers 和断字符来预处理用户查询。|
|3|此预处理用于更改最佳搜索结果的用户查询。|
|4|此更改的查询将发送到 Azure 认知搜索索引，该索引接收 `top` 的结果数。 如果这些结果中没有正确答案，请将 `top` 的值略微增大。 通常，在90% 的查询中，`top` 的值为10。|
|5|QnA Maker 使用语法和语义特征化来确定用户查询与提取的 QnA 结果之间的相似性。|
|6|机器学习的 ranker 模型使用步骤5中的不同功能来确定置信度和新的排名顺序。|
|7|新结果将按顺序返回到客户端应用程序。|
|||

使用的功能包括但不限于 word 级语义、语料库中的术语级别重要性，以及深入了解的语义模型，以确定两个文本字符串之间的相似性和相关性。

## <a name="http-request-and-response-with-endpoint"></a>带有终结点的 HTTP 请求和响应
发布知识库时，该服务会创建一个基于 REST 的 HTTP 终结点，该终结点可以集成到应用程序中，通常是一个聊天机器人。

### <a name="the-user-query-request-to-generate-an-answer"></a>用于生成答案的用户查询请求

用户查询是最终用户要求使用知识库的问题，如 `How do I add a collaborator to my app?`。 查询通常采用自然语言格式或表示问题的几个关键字，如 `help with collaborators`。 查询将从客户端应用程序中的 HTTP 请求发送到知识库。

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```

可以通过设置[scoreThreshold](./confidence-score.md#choose-a-score-threshold)、 [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)和[strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags)等属性来控制响应。

使用[会话上下文](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context)和[多轮功能](../how-to/multiturn-conversation.md)，使对话进入优化问题和答案，找到正确且最终的答案。

### <a name="the-response-from-a-call-to-generate-an-answer"></a>来自调用的响应生成答案

HTTP 响应是基于给定用户查询的最佳匹配项从知识库中检索到的答案。 响应包括答案和预测分数。 如果您要求使用 `top` 属性的多个 top 答案，则会获得多个顶级答案，其中每个都有一个分数。

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [置信度分数](./confidence-score.md)
