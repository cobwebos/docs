---
title: 如何发布知识库 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 如何发布知识库
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: e9dbeacfb0df98c6b8f084c263690c05fe966cdc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366252"
---
# <a name="publish-a-knowledge-base"></a>发布知识库

发布知识库是将知识库作为问答终结点提供的最后一步。 

发布知识库时，知识库的 QnA 内容将从测试索引转移到 Azure 搜索中的生产索引。

![发布生产测试索引](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

## <a name="publish-a-knowledge-base"></a>发布知识库

1. 更改完知识库后，请选择顶部导航栏中的“发布”。 发布的知识库数量不得超过分配给 Azure 搜索的数量。 

    ![发布知识库](../media/qnamaker-how-to-publish-kb/publish.png)

2. 再次选择“发布”，查看可在应用程序或机器人代码中使用的终结点详细信息。

    ![发布知识库](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [获取有关知识库的分析](./get-analytics-knowledge-base.md)
