---
title: 模式有助于预测 - LUIS
titleSuffix: Azure Cognitive Services
description: 使用模式可在不提供更多话语的情况下获得更高的意向准确度。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 6d8088f537c4148f780c5f250eda3dcd5198f67f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683920"
---
# <a name="patterns-improve-prediction-accuracy"></a>模式可提高预测的准确性
模式旨在多条话语非常类似的情况下提升准确性。  使用模式可在不提供更多话语的情况下获得更高的意向准确度。

## <a name="patterns-solve-low-intent-confidence"></a>模式解决了意向置信度低的问题
设想一个人力资源应用，它在组织结构图上报告某员工的相关信息。 如果具有员工的姓名和组织关系，LUIS 就能反馈所涉及的员工。 设想员工叫做孔西明，经理叫做柏隼，下属员工有翁捷生、康霓和尹锋。

![组织结构图](./media/luis-concept-patterns/org-chart.png)

|陈述|预测的意向|意向得分|
|--|--|--|
|孔西明的下属员工有谁？|获取组织结构组|.30|
|谁归孔西明管辖？|获取组织结构组|.30|

如果应用具有 10 到 20 条句子长度不同、字词顺序不同，甚至用词不同（“下属员工”、“经理”和“报告”的近义词）的话语，则 LUIS 返回的置信度得分可能较低。 创建一个模式来帮助 LUIS 理解字词顺序的重要性。

模式可处理以下情况：

* 意向得分较低
* 正确的意向得分不是最高得分但太接近最高得分。

## <a name="patterns-are-not-a-guarantee-of-intent"></a>模式不保证提取意向
模式使用多种预测技术。 在模式中设置模板话语的意向并不保证得出意向预测，但表示很可能得出。

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learning-entity-detection"></a>模式不改进机器学习实体检测

模式的主要用途是帮助预测意向和角色。 _pattern.any_ 实体用于提取自由格式实体。 当模式使用实体时，模式不会帮助检测计算机学习实体。

若将多个话语合并到单个模式，则无法提升实体预测。 若要触发简单实体，需要添加话语或使用列表实体，否则模式将不会触发。

## <a name="patterns-use-entity-roles"></a>模式使用实体角色
如果模式中的两个或多个实体具有相关的上下文，模式会使用实体[角色](luis-concept-roles.md)来提取关于实体的上下文信息。

## <a name="prediction-scores-with-and-without-patterns"></a>使用和不使用模式的情况下的预测得分
假定有足够多的实例话语，则 LUIS 不使用模式即能提高预测的置信度。 无需提供尽量多的话语，模式就能增加置信度得分。

## <a name="pattern-matching"></a>模式匹配
模式的匹配顺序是先检测模式内的实体，再验证模式的其余字词和字词顺序。 要匹配模式，需要在模式中包含实体。 模式在标记级别应用，而不在字符级别应用。

## <a name="pattern-only-apps"></a>只有模式的应用
你可以构建其中的意向没有示例话语的应用，只要每个意向具有模式即可。 对于仅限模式的应用程序，该模式不应包含计算机学习实体，因为它们需要示例最谈话。

## <a name="patternany-entity"></a>Pattern.any 实体

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

## <a name="best-practices"></a>最佳做法
了解[最佳实践](luis-concept-best-practices.md)。

## <a name="pattern-syntax"></a>模式语法

从[模式语法参考](reference-pattern-syntax.md)了解模式语法。

## <a name="next-steps"></a>后续步骤

详细了解模式：

* [如何添加模式](luis-how-to-model-intent-pattern.md)
* [如何添加模式。任何实体](luis-how-to-add-entities.md#add-a-patternany-entity)
* [模式语法](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [在本教程中学习如何实现模式](luis-tutorial-pattern.md)
