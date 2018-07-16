---
title: 了解 LUIS 迭代应用设计 - Azure | Microsoft Docs
description: 为获得最佳数据提取，LUIS 应用需要设计迭代来训练 LUIS。
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/12/2018
ms.author: v-geberr
ms.openlocfilehash: b7f8dd46dc8289322726934f330761b0f1ab94bd
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265931"
---
# <a name="authoring-cycle"></a>创作周期
LUIS 在反复的模型变更、陈述示例、发布以及从终结点查询收集信息等周期中，会取得最佳的学习成效。 

![创作周期](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>生成 LUIS 模型
模型的目的在于弄清楚用户的要求是什么（意图或意向）以及问题的哪些部分提供有助于确定答案的详细信息（实体）。 

模型需要特定于应用域，以此确定相关的字词、短语以及典型的字词顺序。 

模型包含意向和实体。 

## <a name="add-training-examples"></a>添加训练示例
LUIS 需要意向的示例陈述。 这些示例需要有足够的字词选择和字词顺序方面的变化，才能确定陈述的意向。 每个示例陈述都需要将任何所需的数据标记为实体。 

通过将陈述分配到 None 意向，指示 LUIS 忽略与应用域不相关的陈述。 任何不需要从陈述中提取出来的字词或短语都不需要进行标记。 要忽略的字词或短语均无标记。 
<!--
## Not just yet
Do not add features such as a [phrase list](luis-concept-feature.md) feature in your first cycle. Phrase lists are phrases that would be specific to your app's subject area.  
-->
## <a name="train-and-publish-the-app"></a>训练并发布应用
每个意向具有 10 到 15 个不同的陈述并且所需实体被标记后，训练 LUIS，然后发布以获取终结点。 确保创建应用并发布应用，以便它在需要的[终结点区域](luis-reference-regions.md)中可用。 

## <a name="https-endpoint-testing"></a>HTTPS 终结点测试
可以从[发布](publishapp.md)页面上列出的 HTTPS 终结点测试 LUIS 应用。 从该终结点测试可使 LUIS 选择对可信度低的表述进行评审。  

## <a name="recycle"></a>再循环
结束一个创作周期后可以重新开始。 从评审被 LUIS 标为低可信度的终结点陈述开始。 检查这些陈述的意向和实体。 评审陈述后，评审列表应为空。  

## <a name="batch-testing"></a>批处理测试
批处理测试是一种查看 LUIS 对多少个示例陈述评分的方法。 这些示例应该是初次用于 LUIS，并应正确标有想要 LUIS 查找的意向和实体。 测试结果表明 LUIS 在这组陈述上的表现如何。 

## <a name="next-steps"></a>后续步骤

了解有关[协作](luis-concept-collaborator.md)的概念。

[luis-reference-prebuilt-domains]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-domains