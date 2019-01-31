---
title: 知识库生命周期 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 在反复的模型变更、表述示例、发布以及从终结点查询收集信息等周期中，会取得最佳的学习成效。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: c728946582ec2d0423c1e984b5ff0463f59da466
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207337"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>QnA Maker 中的知识库生命周期
QnA Maker 在反复的模型变更、表述示例、发布以及从终结点查询收集信息等周期中，会取得最佳的学习成效。 

![创作周期](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>创建 QnA Maker 知识库
QnA Maker 知识库 (KB) 终结点基于知识库的内容为用户查询提供匹配度最高的答案。 创建知识库是向问题、答案和相关元数据设置内容存储库的一次性操作。 可以通过提取预先存在的内容（例如常见问题解答页面、产品手册或结构化问-答对）创建知识库。 了解如何[创建知识库](../How-To/create-knowledge-base.md)。

## <a name="testing-and-updating-the-knowledge-base"></a>测试和更新知识库
知识库只要填充了内容（无论是以编辑方式填充还是通过自动提取填充），就可以用于测试。 若要通过“测试”面板进行测试，请输入常见的用户查询，然后验证是否返回了预期的响应并具备足够的置信度分数。 可以添加替代问题来改善置信度分数较低的情况。 还可以在查询返回默认响应“未在知识库中找到匹配内容”时，添加新的答案。 测试更新这一紧凑周期会持续至得到满意的结果为止。 了解如何[测试知识库](../How-To/test-knowledge-base.md)。

对于大型知识库，可通过 generateAnswer API 自动进行测试工作。 

## <a name="publish-the-knowledge-base"></a>发布知识库
完成知识库的测试后，即可发布此知识库。 发布操作会将经过测试的知识库的最新版本推送至代表“已发布”知识库的专用 Azure 搜索索引。 还会创建一个终结点，可在应用程序或聊天机器人中调用此终结点。

这样一来，对知识库的测试版本进行的任何更改都不会影响可能已进入生产应用程序的已发布版本。

所有这些知识库都可以单独作为测试目标。 使用 API，可以在 generateAnswer 调用中使用 `isTest=true` 标记，将知识库的测试版本作为目标。

了解如何[发布知识库](../How-To/publish-knowledge-base.md)。

## <a name="monitor-usage"></a>监视使用情况
若要能记录服务的聊天日志，需要在[创建 QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)时启用 Application Insights。

可以获取服务使用情况的各种分析信息。 了解如何使用 Application Insights 获取 [QnA Maker 服务的分析](../How-To/get-analytics-knowledge-base.md)。

根据从分析中得出的结论，适当[更新知识库](../How-To/edit-knowledge-base.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [置信度分数](./confidence-score.md)

## <a name="see-also"></a>另请参阅 

[知识库](./knowledge-base.md)
[QnA Maker 概述](../Overview/overview.md)
