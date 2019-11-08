---
title: 知识库生命周期 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 在反复的模型变更、表述示例、发布以及从终结点查询收集信息等周期中，会取得最佳的学习成效。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 1cb5af13bdd309c762337e64ecde8538afc756b0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794840"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>QnA Maker 中的知识库生命周期
QnA Maker 在反复的模型变更、表述示例、发布以及从终结点查询收集信息等周期中，会取得最佳的学习成效。 

![创作周期](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>创建 QnA Maker 知识库
QnA Maker 知识库 (KB) 终结点基于知识库的内容为用户查询提供匹配度最高的答案。 创建知识库是向问题、答案和相关元数据设置内容存储库的一次性操作。 可以通过提取预先存在的内容（例如常见问题解答页面、产品手册或结构化问-答对）创建知识库。 了解如何[创建知识库](../quickstarts/create-publish-knowledge-base.md)。

## <a name="testing-and-updating-the-knowledge-base"></a>测试和更新知识库

知识库只要填充了内容（无论是以编辑方式填充还是通过自动提取填充），就可以用于测试。 可以通过 "**测试**" 面板在 QnA Maker 门户中完成交互式测试，方法是输入常见的用户查询，并验证返回的响应是否正确，以及是否有足够的置信度得分。 

* **若要修复置信度较低的评分**：添加替代问题。 
* **如果查询错误地返回[默认响应](confidence-score.md#change-default-answer)** ：将新答案添加到正确的问题。 

测试更新这一紧凑周期会持续至得到满意的结果为止。 了解如何[测试知识库](../How-To/test-knowledge-base.md)。

对于大型 Kb，请对[GENERATEANSWER API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api)使用自动测试，`isTest` 并将查询 `test` 知识库而不是已发布的知识库。 

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>发布知识库
完成知识库的测试后，即可发布此知识库。 发布将已测试知识库的最新版本推送到表示**已发布**知识库的专用 Azure 认知搜索索引。 还会创建一个终结点，可在应用程序或聊天机器人中调用此终结点。

这样一来，对知识库的测试版本进行的任何更改都不会影响可能已进入生产应用程序的已发布版本。

所有这些知识库都可以单独作为测试目标。 使用 Api，可以在 generateAnswer 调用中将知识库的测试版本定位到 `isTest` body 属性。

了解如何[发布知识库](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)。

## <a name="monitor-usage"></a>监视使用情况
若要能记录服务的聊天日志，需要在[创建 QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)时启用 Application Insights。

可以获取服务使用情况的各种分析信息。 了解如何使用 Application Insights 获取 [QnA Maker 服务的分析](../How-To/get-analytics-knowledge-base.md)。

根据从分析中得出的结论，适当[更新知识库](../How-To/edit-knowledge-base.md)。

## <a name="version-control-of-a-knowledge-base"></a>知识库的版本控制

QnA Maker 未提供版本控制。 需要从 "**设置**" 页导出知识库，并使用自己的方法和工具。

从 "**设置**" 页将知识库导出到 TSV 或 XLS 格式。 

需要返回到特定版本时，需要从本地系统导入该文件。 在 "**设置**" 页中，导入 TSV 或 XLS 文件。 这会将知识库中当前存在的问题和答案替换为导入的文件的内容。   

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [置信度分数](./confidence-score.md)