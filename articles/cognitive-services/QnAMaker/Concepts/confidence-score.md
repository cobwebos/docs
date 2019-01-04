---
title: 置信度分数 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 此置信度分数指明了答案是给定用户查询的正确匹配答案的置信度。
services: cognitive-services
author: tulasim88
manager: pchoudh
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 09/27/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 8fe4ecfe241422c03a2de8d0634ef1e1395aa050
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081313"
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
上表指明了大多数知识库上应该会出现的分数。 不过，由于每个知识库都不同，它们具有不同类型的字词、意向和目标，因此建议测试并选择最适合自己的阈值。 适用于大多数知识库的默认建议阈值为 50。

选择阈值时，请务必平衡“准确度”和“覆盖率”，并根据自己的需求来调整阈值。

- 如果“准确度”（或精准率）对方案更为重要，请提高阈值。 这样，每次返回的答案的置信度都会更高，且更有可能就是用户所要找的答案。 在这种情况下，最终可能会导致更多问题没有答案。 例如，如果将阈值设置为 70，可能会错过一些含糊不清的示例（例如，“什么是保存和定型？”）。

- 如果“覆盖率”（或召回率）更为重要，且希望尽可能多地回答问题（即使答案与用户问题仅部分相关，也不例外），请降低阈值。 也就是说，可能会更多出现以下情况：答案并未回答用户实际查询，而是提供了其他一些相关答案。 例如，如果将阈值设置为 30，可能不会有非常相关的答案，如上面示例对“我可以在哪里编辑知识库？”等查询的回答

> [!NOTE]
> 较新版本的 QnA Maker 包括对评分逻辑的改进，并可能影响你的阈值。 每次更新服务时，请务必测试阈值并在必要时调整阈值。 可以在[此处](https://www.qnamaker.ai/UserSettings)查看 QnA 服务版本，并在[此处](../How-To/troubleshooting-runtime.md)了解如何获取最新更新。

## <a name="improve-confidence-scores"></a>提高置信度分数
若要提高对用户查询的特定响应的置信度分数，可以将用户查询添加到知识库，作为该响应的备用问题。


## <a name="similar-confidence-scores"></a>相似的置信度分数
当多个响应具有相似的置信度分数时，查询很可能过于通用，因此与多个答案匹配的可能性都相同。 尝试更好地构建 QnA，以便每个 QnA 实体都有不同的意向。


## <a name="confidence-score-differences"></a>置信度分数差异
即使内容相同，答案的置信度分数在知识库的测试版和发布版之间也可能有微不足道的差异。 这是因为测试知识库和已发布知识库的内容位于不同的 Azure 搜索索引中。
请参见此处了解[发布](../How-To/publish-knowledge-base.md)操作的工作原理。


## <a name="no-match-found"></a>找不到匹配项
当排名程序找不到良好匹配时，将返回置信度分数 0.0 或“None”，并且默认响应为“在知识库中找不到良好匹配”。 可以在调用终结点的机器人或应用程序代码中重写此默认响应。 或者，也可以在 Azure 中设置重写响应，这将更改在特定 QnA Maker 服务中部署的所有知识库的默认值。

### <a name="change-default-answer"></a>更改默认答案

1. 转到 [Azure 门户](https://portal.azure.com)并导航到表示所创建的 QnA Maker 服务的资源组。

2. 单击以打开“应用服务”。

    ![在 Azure 门户中访问 QnA Maker 的应用服务](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. 单击“应用程序设置”，然后将 **DefaultAnswer** 字段编辑为所需的默认响应。 单击“ **保存**”。

    ![选择“应用程序设置”，然后编辑 QnA Maker 的 DefaultAnswer](../media/qnamaker-concepts-confidencescore/change-response.png)

4. 重启应用服务

    ![更改 DefaultAnswer 后，重启 QnA Maker 应用服务](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [支持的数据源](./data-sources-supported.md)
## <a name="see-also"></a>另请参阅 
[QnA Maker 概述](../Overview/overview.md)
