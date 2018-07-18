---
title: 置信度分数 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 说明置信度分数
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: c97bdb7e57275ebd1893bc28248c4ecc6b35c153
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366287"
---
# <a name="confidence-score"></a>置信度分数

置信度分数指示用户问题与返回的响应之间的匹配程度。

当用户查询与知识库内容匹配时，可以返回多个响应。 响应以置信度分数降序排序顺序返回。

置信度分数介于 0 和 100 之间。

|分数值|置信度|
|--|--|
|100|用户查询和知识库问题完全匹配|
|90|高置信度 - 大多数单词匹配|
|40-60|中等置信度 - 重要的单词匹配|
|10|低置信度 - 重要的单词不匹配|
|0|没有单词匹配|


## <a name="similar-confidence-scores"></a>相似的置信度分数
当多个响应具有相似的置信度分数时，查询很可能过于通用，因此与多个答案匹配的可能性都相同。 尝试更好地构建 QnA，以便每个 QnA 实体都有不同的意向。


## <a name="improving-confidence-scores"></a>提高置信度分数
若要提高对用户查询的特定响应的置信度分数，可以将用户查询添加到知识库，作为该响应的备用问题。
   
## <a name="confidence-score-differences"></a>置信度分数差异
即使内容相同，答案的置信度分数在知识库的测试版和发布版之间也可能有微不足道的差异。 这是因为测试知识库和已发布知识库的内容位于不同的 Azure 搜索索引中。

请参见此处了解[发布](../How-To/publish-knowledge-base.md)操作的工作原理。


## <a name="no-match-found"></a>找不到匹配项
当排名程序找不到良好匹配时，将返回置信度分数 0.0 或“None”，并且默认响应为“在知识库中找不到良好匹配”。 可以在调用终结点的机器人或应用程序代码中重写此默认响应。 或者，也可以在 Azure 中设置重写响应，这将更改在特定 QnA Maker 服务中部署的所有知识库的默认值。

1. 转到 [Azure 门户](http://portal.azure.com)并导航到表示所创建的 QnA Maker 服务的资源组。

2. 单击以打开“应用服务”。

    ![访问应用服务](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. 单击“应用程序设置”，然后将 **DefaultAnswer** 字段编辑为所需的默认响应。 单击“ **保存**”。

    ![更改默认响应](../media/qnamaker-concepts-confidencescore/change-response.png)

4. 重启应用服务

    ![QnA Maker 应用服务重启](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [支持的数据源](./data-sources-supported.md)

## <a name="see-also"></a>另请参阅 

[QnA Maker 概述](../Overview/overview.md)
