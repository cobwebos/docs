---
title: 预测评分 - LUIS
description: 预测分数表示 LUIS API 服务基于用户话语对于预测结果的置信度。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d836273e61752ff208133466016ce7c6ff9c28fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316454"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>预测评分指示意向和实体的预测准确性

预测分数表示 LUIS 对用户话语预测结果的置信度。

预测分数在零 (0) 到一 (1) 之间。 例如，一个置信度很高的 LUIS 分数可以是 0.99。 置信度低的分数可以是 0.01。

|分数值|置信度|
|--|--|
|1|明确匹配|
|0.99|高置信度|
|0.01|低置信度|
|0|明确匹配失败|

## <a name="top-scoring-intent"></a>得分最高的意向

每个话语预测都会返回一个评分最高的意向。 此预测是对预测分数的数值比较。

## <a name="proximity-of-scores-to-each-other"></a>分数彼此接近

最高 2 个分数的差距可能很小。 LUIS 不会指明这种分差，只会返回最高分数。

## <a name="return-prediction-score-for-all-intents"></a>返回所有意向的预测分数

测试或终结点结果可以包括所有意向。 此配置是使用正确的 querystring 名称/值对在终结点上设置的。

|预测 API|Querystring 名称|
|--|--|
|V3|`show-all-intents=true`|
|V2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>查看分数相近的意向

审查所有意向的分数可很好地验证不仅识别到了话语的正确意向，而且该意向的分数始终明显高于识别到话语的下一个意向。

如果多个意向的预测分数相近，LUIS 可基于话语的上下文在这些意向之间进行切换。 若要解决这种情况，可以继续为每个意向添加具有更广泛上下文差异的话语，或者让客户端应用程序（例如聊天机器人）通过编程选择如何处理前两个意向。

分数太接近的两个意向可能会由于**非确定性训练**而反转。 最高分可能会变为第二高分，第二高分可能会变为最高分。 为了防止此情况，请向该话语的前两个意向添加示例话语，并在示例话语中包含单词选择和用于区分两个意向的上下文。 这两个意图应该具有相同数量的示例话语。 防止由于训练而造成反转的一个间隔经验法则是，让分数有 15% 的差值。

可以通过[使用所有数据进行训练](luis-how-to-train.md#train-with-all-data)来关闭**非确定性训练**。

## <a name="differences-with-predictions-between-different-training-sessions"></a>不同训练会话之间的预测差异

如果在不同的应用中训练相同的模型，但分数不同，这种差异是因为存在**非确定性训练**（一种随机性因素）。 其次，如果话语的多个意向重叠，则意味着相同话语中评分最高的意向可能会因训练而发生变化。

如果聊天机器人需要一个特定的 LUIS 分数来指示意向的置信度，则应使用前两个意向之间的分差。 这种情况可更灵活地应对训练过程中的变化。

可以通过[使用所有数据进行训练](luis-how-to-train.md#train-with-all-data)来关闭**非确定性训练**。

## <a name="e-exponent-notation"></a>E（指数）表示法

预测分数可采用指数表示法，显示超过 0 到 1 这个范围的值，例如 `9.910309E-07`。 此分数指示的是非常小的数  。

|E 表示法分数 |实际分数|
|--|--|
|9.910309E-07|.0000009910309|

<a name="punctuation"></a>

## <a name="application-settings"></a>应用程序设置

使用 [应用程序设置](luis-reference-application-settings.md) 来控制音调符号和标点如何影响预测分数。

## <a name="next-steps"></a>后续步骤

请参阅[添加实体](luis-how-to-add-entities.md)，详细了解如何将实体添加到 LUIS 应用。
