---
title: 了解 LUIS 返回的预测分数 - Azure | Microsoft Docs
description: 了解预测分数在 LUIS 中的含义
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: cee7243531857f07dec2e968352ffb54aef16bf1
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224580"
---
# <a name="prediction-score"></a>预测分数
预测分数表示 LUIS 对预测结果的置信度。 

预测分数通常在零 (0) 到一 (1) 之间。 例如，一个置信度很高的 LUIS 分数可以是 0.99。 置信度低的分数可以是 0.01。 

|分数值|置信度|
|--|--|
|1|明确匹配|
|0.99|高置信度|
|0.01|低置信度|
|0|明确匹配失败|

如果某个话语的得分为低置信度，LUIS 会在 [LUIS](luis-reference-regions.md) 网页“意向”页面上突出显示该话语，并以红色边框标记识别到的意向。 

![分数差异](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>得分最高的意向
每个话语预测都会返回一个评分最高的意向。 这是预测分数的数值比较。 最高两个分数的差距可能很小。 LUIS 不会指明这种分差，只会返回分数。  

如果担心高分之间过于接近，则应返回所有意向的分数。 可将话语添加到两个意向，指示它们在字词选择和顺序上的不同，或者可使用 LUIS 调用应用程序（例如聊天机器人），以编程方式选择如何处理分数最高的两个意向。 

## <a name="return-prediction-score-for-all-intents"></a>返回所有意向的预测分数
测试或终结点结果可以包括所有意向。 此配置是使用 `verbose=true` 查询字符串名称/值对在[终结点](https://aka.ms/v1-endpoint-api-docs)上设置的。 

## <a name="review-intents-with-similar-scores"></a>查看分数相近的意向
查看所有意向的分数可很好地验证不仅识别到了话语的正确意向，且该意向的分数始终明显高于识别到的下一个意向。 

如果多个意向的预测分数相近，LUIS 可基于话语的上下文在这些意向之间进行切换。 若要解决此问题，请继续将上下文差异更大的话语添加到每个意向。   

## <a name="e-exponent-notation"></a>E（指数）表示法

预测分数可采用指数表示法，显示超过 0 到 1 这个范围的值，例如 `9.910309E-07`。 此分数指示的是非常小的数。

|E 表示法分数 |实际分数|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="differences-with-predictions"></a>预测差异
如果在不同应用中训练同样的模型，但分数不同，这是因为在训练过程中存在随机性因素。 其次，如果话语的多个意向重叠，则意味着相同话语中评分最高的意向可能会因训练而发生变化。

如果聊天机器人需要一个特定的 LUIS 分数来指示意向的置信度，则请改用最高两个意向之间的分差。 这样可更灵活地应对训练过程中的变化。 

## <a name="punctuation"></a>标点
标点是 LUIS 中单独的标记。 在末尾包含句号的话语与不是两个单独话语并可能得到两种不同预测的话语。 请确保模型在[示例话语](luis-concept-utterance.md)（有标点和没有标点）或在更容易使用特殊语法忽略标点的 [patterns}(luis-concept-patterns.md) 中处理标点：`I am applying for the {Job} position[.]`

## <a name="next-steps"></a>后续步骤

请参阅[添加实体](luis-how-to-add-entities.md)，详细了解如何将实体添加到 LUIS 应用。