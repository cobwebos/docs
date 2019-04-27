---
title: 预测评分
titleSuffix: Language Understanding - Azure Cognitive Services
description: 预测得分指示 LUIS API 服务具有的预测结果的置信度基于用户语句。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 383ce4c4248f7e21f745f503c74a29cb613983e2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813990"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>预测评分指示意向和实体的预测准确性

预测得分指示 LUIS 具有用于预测结果的置信度基于用户语句。

预测分数在零 (0) 到一 (1) 之间。 例如，一个置信度很高的 LUIS 分数可以是 0.99。 置信度低的分数可以是 0.01。 

|分数值|置信度|
|--|--|
|第|明确匹配|
|0.99|高置信度|
|0.01|低置信度|
|0|明确匹配失败|

如果某个话语的得分为低置信度，LUIS 会在 [LUIS](luis-reference-regions.md) 网页“意向”页面上突出显示该话语，并以红色边框标记识别到的意向。

![分数差异](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>得分最高的意向

每个话语预测都会返回一个评分最高的意向。 此预测是预测分数的数值比较。 前 2 个分数可以具有区别很小。 LUIS 并不指示这种距离以外返回的最高分。  

## <a name="return-prediction-score-for-all-intents"></a>返回所有意向的预测分数

测试或终结点结果可以包括所有意向。 此配置是使用 `verbose=true` 查询字符串名称/值对在[终结点](https://aka.ms/v1-endpoint-api-docs)上设置的。

## <a name="review-intents-with-similar-scores"></a>查看分数相近的意向

查看所有意向的分数可很好地验证不仅识别到了话语的正确意向，且该意向的分数始终明显高于识别到的下一个意向。

如果多个意向的预测分数相近，LUIS 可基于话语的上下文在这些意向之间进行切换。 若要解决这种情况下，继续将语音样本添加到具有更广泛的上下文的差异的每个意向或客户端应用程序，例如聊天机器人，做出有关如何处理 2 的顶部意图以编程方式选择。

2 个意向，进行过-密切评分，可能会由于非确定性培训反转。 最高分可能会变为第二高分，第二高分可能会变为最高分。 为了防止这种情况下，将添加到每个该查询文本的 word 选择和用于区分开来 2 意图的上下文的前两个方式的示例查询文本。 这两个意图应该具有相同数量的示例话语。 防止由于训练而造成反转的一个间隔经验法则是，让分数有 15% 的差值。

你可以关闭的非确定性的培训[的所有数据定型](luis-how-to-train.md#train-with-all-data)。

## <a name="differences-with-predictions-between-different-training-sessions"></a>预测不同的训练会话之间的差异

当在不同应用中，相同的模型进行定型和评分不相同时，这种差异是因为没有非确定性培训 （随机性的元素）。 其次，如果话语的多个意向重叠，则意味着相同话语中评分最高的意向可能会因训练而发生变化。

如果聊天机器人需要特定的 LUIS 分数，以指示意向的置信度，则应使用前两个方法之间的分数差异。 这种情况下提供了灵活性的培训中的变体。

## <a name="e-exponent-notation"></a>E（指数）表示法

预测分数可采用指数表示法，显示超过 0 到 1 这个范围的值，例如 `9.910309E-07`。 此分数指示的是非常小的数。

|E 表示法分数 |实际分数|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>标点

标点是 LUIS 中单独的标记。 包含与查询文本不包含在结束一段的末尾句点语音样本是两个单独的语音样本，可能会收到两个不同的预测。 请确保模型在[示例话语](luis-concept-utterance.md)（有标点和没有标点）或在更容易使用特殊语法忽略标点的[模式](luis-concept-patterns.md)中处理标点：`I am applying for the {Job} position[.]`

## <a name="next-steps"></a>后续步骤

请参阅[添加实体](luis-how-to-add-entities.md)，详细了解如何将实体添加到 LUIS 应用。
