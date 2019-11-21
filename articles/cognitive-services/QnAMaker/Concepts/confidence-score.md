---
title: 置信度分数 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 此置信度分数指明了答案是给定用户查询的正确匹配答案的置信度。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: e2f7136ea7b973386eeb746a74ad09fadb490e83
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229103"
---
# <a name="confidence-score-of-a-qna-maker-knowledge-base"></a>QnA Maker 知识库的置信度分数
如果用户查询的匹配依据为知识库，QnA Maker 会返回相关答案和置信度分数。 此分数指明了答案是给定用户查询的正确匹配答案的置信度。 

置信度分数是介于 0 和 100 之间的数字。 100 分表明很可能是完全匹配，而 0 分则表明找不到匹配答案。 分数越高，答案的置信度就越大。 对于给定查询，可能会返回多个答案。 在这种情况下，答案按置信度分数降序顺序返回。

在下面的示例中，可以看到一个 QnA 实体包含 2 个问题。 


![示例 QnA 对](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

对于上面的示例，不同类型用户查询的置信度分数介于如下示例分数范围内：


![排名程序分数范围](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


下表指明了与给定分数关联的典型置信度。

|分数值|分数含义|示例查询|
|--|--|--|
|90 - 100|与用户查询和知识库问题几乎完全匹配|“我的更改并未在发布后的知识库中更新”|
|> 70|高置信度 - 通常是能够完全回答用户查询的合理答案|“我已发布我的知识库，但它并未更新”|
|50 - 70|中等置信度 - 通常是应该能回答用户查询的主要意向的较合理答案|“我是否应在发布知识库前保存我的更新？”|
|30 - 50|低置信度 - 通常是部分回答用户意向的相关答案|“保存和定型有何作用？”|
|< 30|极低置信度 - 通常未回答用户查询，但有一些匹配字词或短语 |“我可以在哪里将同义词添加到我的知识库”|
|0|无匹配，因此未返回任何答案。|“服务费用是多少”|

## <a name="choose-a-score-threshold"></a>选择分数阈值
上表指明了大多数知识库上应该会出现的分数。 However, since every KB is different, and has different types of words, intents, and goals- we recommend you test and choose the threshold that best works for you. By default the threshold is set to 0, so that all possible answers are returned. The recommended threshold that should work for most KBs, is **50**.

选择阈值时，请务必平衡“准确度”和“覆盖率”，并根据自己的需求来调整阈值。

- 如果“准确度”（或精准率）对方案更为重要，请提高阈值。 这样，每次返回的答案的置信度都会更高，且更有可能就是用户所要找的答案。 在这种情况下，最终可能会导致更多问题没有答案。 例如，如果将阈值设置为 70，可能会错过一些含糊不清的示例（例如，“什么是保存和定型？”）。

- 如果“覆盖率”（或召回率）更为重要，且希望尽可能多地回答问题（即使答案与用户问题仅部分相关，也不例外），请降低阈值。 也就是说，可能会更多出现以下情况：答案并未回答用户实际查询，而是提供了其他一些相关答案。 *For example:* if you make the threshold **30**, you might give answers for queries like "Where can I edit my KB?"

> [!NOTE]
> 较新版本的 QnA Maker 包括对评分逻辑的改进，并可能影响你的阈值。 每次更新服务时，请务必测试阈值并在必要时调整阈值。 可以在[此处](https://www.qnamaker.ai/UserSettings)查看 QnA 服务版本，并在[此处](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)了解如何获取最新更新。

## <a name="set-threshold"></a>Set threshold 

Set the threshold score as a property of the [GenerateAnswer API JSON body](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). This means you set it for each call to GenerateAnswer. 

From the bot framework, set the score as part of the options object with [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) or [Node.js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>提高置信度分数
若要提高对用户查询的特定响应的置信度分数，可以将用户查询添加到知识库，作为该响应的备用问题。 还可以使用区分大小写的[字变更](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace)向知识库中的关键字添加同义词。


## <a name="similar-confidence-scores"></a>相似的置信度分数
当多个响应具有相似的置信度分数时，查询很可能过于通用，因此与多个答案匹配的可能性都相同。 尝试更好地构建 QnA，以便每个 QnA 实体都有不同的意向。


## <a name="confidence-score-differences-between-test-and-production"></a>Confidence score differences between test and production
即使内容相同，答案的置信度分数在知识库的测试版和发布版之间也可能有微不足道的差异。 This is because the content of the test and the published knowledge base are located in different Azure Cognitive Search indexes. 

The test index holds all the QnA pairs of your knowledge bases. When querying the test index, the query applies to the entire index then results are restricted to the partition for that specific knowledge base. If the test query results are negatively impacting your ability to validate the knowledge base, you can:
* organize your knowledge base using one of the following:
    * 1 resource restricted to 1 KB: restrict your single QnA resource (and the resulting Azure Cognitive Search test index) to a single knowledge base. 
    * 2 resources - 1 for test, 1 for production: have two QnA Maker resources, using one for testing (with its own test and  production indexes) and one for product (also having its own test and production indexes)
* and, always use the same parameters, such as **[top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)** when querying both your test and production knowledge base

发布知识库时，知识库的问答内容将从测试索引转移到 Azure 搜索中的生产索引。 请参阅[发布](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)操作的工作原理。

If you have a knowledge base in different regions, each region uses its own Azure Cognitive Search index. 因为使用的索引不同，所以得分并不完全相同。 


## <a name="no-match-found"></a>找不到匹配项
当排名程序找不到良好匹配时，将返回置信度分数 0.0 或“None”，并且默认响应为“在知识库中找不到良好匹配”。 You can override this [default response](#change-default-answer) in the bot or application code calling the endpoint. 或者，也可以在 Azure 中设置重写响应，这将更改在特定 QnA Maker 服务中部署的所有知识库的默认值。

## <a name="change-default-answer"></a>更改默认答案

1. 转到 [Azure 门户](https://portal.azure.com)并导航到表示所创建的 QnA Maker 服务的资源组。

2. 单击以打开“应用服务”。

    ![在 Azure 门户中访问 QnA Maker 的应用服务](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. 单击“应用程序设置”，然后将 **DefaultAnswer** 字段编辑为所需的默认响应。 单击“保存”。

    ![选择“应用程序设置”，然后编辑 QnA Maker 的 DefaultAnswer](../media/qnamaker-concepts-confidencescore/change-response.png)

4. 重启应用服务

    ![更改 DefaultAnswer 后，重启 QnA Maker 应用服务](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [支持的数据源](./data-sources-supported.md)

