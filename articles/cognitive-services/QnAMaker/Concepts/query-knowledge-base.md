---
title: 查询知识库 - QnA 制造商
description: 必须发布知识库。 发布后，使用生成应答 API 在运行时预测终结点查询知识库。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb777aa16fada50811cce1bbf49f28662c62b49b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220717"
---
# <a name="query-the-knowledge-base-for-answers"></a>查询知识库以查找答案

必须发布知识库。 发布后，使用生成应答 API 在运行时预测终结点查询知识库。 该查询包括问题文本和其他设置，以帮助 QnA Maker 选择与答案的最佳匹配。

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker 如何处理用户查询以选择最佳答案

经过训练并[发布的](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base)QnA Maker 知识库在[生成应答 API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)接收来自自动程序或其他客户端应用程序的用户查询。 下图说明了接收用户查询时的过程。

![用户查询的排名模型流程](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Ranker 过程

下表说明了该过程。

|步骤|目的|
|--|--|
|1|客户端应用程序将用户查询发送到[生成应答 API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)。|
|2|QnA Maker 使用语言检测、拼写器和断字符预先处理用户查询。|
|3|此预处理用于更改用户查询以获得最佳搜索结果。|
|4|此更改的查询将发送到 Azure 认知搜索索引，该索引接收`top`结果数。 如果正确答案不在这些结果中，请稍微增加值`top`。 通常，90% 的查询`top`中工作的值为 10。|
|5|QnA Maker 使用句法和基于语义的壮举来确定用户查询与提取的 QnA 结果之间的相似性。|
|6|机器学习的记号模型使用步骤 5 中的不同功能来确定置信度分数和新的排名顺序。|
|7|新结果按排名顺序返回给客户端应用程序。|
|||

使用的功能包括但不限于单词级语义、语料库中的术语级重要性以及深学语义模型，以确定两个文本字符串之间的相似性和相关性。

## <a name="http-request-and-response-with-endpoint"></a>使用终结点进行 HTTP 请求和响应
发布知识库时，该服务将创建一个基于 REST 的 HTTP 终结点，该终结点可以集成到应用程序中，通常是聊天机器人。

### <a name="the-user-query-request-to-generate-an-answer"></a>生成答案的用户查询请求

用户查询是最终用户询问的知识库的问题，例如`How do I add a collaborator to my app?`。 查询通常采用自然语言格式或代表问题的几个关键字，如`help with collaborators`。 查询将从客户端应用程序中的 HTTP 请求发送到知识库。

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

您可以通过设置诸如[分数阈值](./confidence-score.md#choose-a-score-threshold)、[顶部](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)和[严格筛选器](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags)等属性来控制响应。

使用具有[多转功能](../how-to/multiturn-conversation.md)[的对话上下文](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context)来保持对话的优化问题和答案，以找到正确和最终的答案。

### <a name="the-response-from-a-call-to-generate-an-answer"></a>来自呼叫的响应以生成应答

HTTP 响应是根据给定用户查询的最佳匹配从知识库中检索的答案。 响应包括答案和预测分数。 如果您要求多个顶部答案的属性`top`，你会得到多个顶部答案，每个带有分数。

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
> [置信度得分](./confidence-score.md)
