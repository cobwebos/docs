---
title: 迭代应用设计-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 在反复的模型变更、陈述示例、发布以及从终结点查询收集信息等周期中，会取得最佳的学习成效。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: 4356d9e1cd3d6f1a924603f7405d612814d35859
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256934"
---
# <a name="authoring-cycle-for-your-luis-app"></a>LUIS 应用的创作周期
LUIS 在反复的模型变更、陈述示例、发布以及从终结点查询收集信息等周期中，会取得最佳的学习成效。 

![创作周期](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>生成 LUIS 模型
模型的目的在于弄清楚用户的要求是什么（意图或意向）以及问题的哪些部分提供有助于确定答案的详细信息（实体）。 

模型需要特定于应用域，以此确定相关的字词、短语以及典型的字词顺序。 

模型需要意向，并且_应具有_实体。 

## <a name="add-training-examples"></a>添加训练示例
LUIS 需要意向的示例陈述。 这些示例需要有足够的字词选择和字词顺序方面的变化，才能确定陈述的意向。 每个示例陈述都需要将任何所需的数据标记为实体。 

通过将陈述分配到 None 意向，指示 LUIS 忽略与应用域不相关的陈述。 任何不需要从陈述中提取出来的字词或短语都不需要进行标记。 要忽略的字词或短语均无标记。 

## <a name="train-and-publish-the-app"></a>训练并发布应用
每个意向中有15到30个不同的最谈话, 并将所需实体标记为 " [训练](luis-how-to-train.md) [发布](luis-how-to-publish-app.md)"。 使用发布成功通知中的链接获取终结点。 请确保创建并发布应用，使其在所需的[终结点区域](luis-reference-regions.md)中可用。 

## <a name="https-prediction-endpoint-testing"></a>HTTPS 预测终结点测试
可以从 HTTPS 预测终结点测试 LUIS 应用。 通过预测终结点进行测试，LUIS 可以选择具有低置信度的任何[最谈话。](luis-how-to-review-endpoint-utterances.md)  

## <a name="recycle"></a>再循环

结束一个创作周期后可以重新开始。 开始[审阅预测终结点最谈话](luis-how-to-review-endpoint-utterances.md)LUIS 标记为低置信度。 检查这些陈述的意向和实体。 评审陈述后，评审列表应为空。  

考虑将当前版本[克隆](luis-concept-version.md#clone-a-version)到新版本，然后在新版本中开始创作更改。 

## <a name="batch-testing"></a>批处理测试

[批处理测试](luis-concept-batch-test.md)是一种查看 LUIS 评分的示例最谈话数的方法。 这些示例应该是初次用于 LUIS，并应正确标有想要 LUIS 查找的意向和实体。 测试结果表明 LUIS 在这组陈述上的表现如何。 

## <a name="next-steps"></a>后续步骤

了解有关[协作](luis-concept-keys.md)的概念。
