---
title: 发布知识库
titleSuffix: QnA Maker - Azure Cognitive Services
description: 发布知识库是将知识库作为问答终结点提供的最后一步。 发布知识库时，知识库的 QnA 内容将从测试索引转移到 Azure 搜索中的生产索引。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/11/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 585e5f8f955a405b08bef42588ab5caff71c7bba
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55865907"
---
# <a name="publish-a-knowledge-base-using-the-qna-maker-portal"></a>使用 QnA Maker 门户发布知识库

发布知识库是将知识库作为客户端应用程序问答终结点提供的最后一步。 

发布知识库时，知识库的问答内容将从测试索引转移到 Azure 搜索中的生产索引。

![发布生产测试索引](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="publish-a-knowledge-base"></a>发布知识库

1. 更改完知识库后，请选择顶部导航栏中的“发布”。 发布的知识库数量不得超过分配给 Azure 搜索的数量。 

    ![发布知识库](../media/qnamaker-how-to-publish-kb/publish.png)

2. 再次选择“发布”，查看可在应用程序或机器人代码中使用的终结点详细信息。

    ![已成功发布的知识库](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="clean-up-resources"></a>清理资源

使用完知识库后，在 QnA Maker 门户中将其删除。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [获取有关知识库的分析](./get-analytics-knowledge-base.md)
