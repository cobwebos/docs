---
title: 测试 LUIS 应用
titleSuffix: Azure Cognitive Services
description: 进行测试过程中，会向 LUIS 提供示例话语并获取 LUIS 识别出的意向和实体响应。
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: ef63bf68fec592b768986a1329663508443c514b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535842"
---
# <a name="testing-example-utterances-in-luis"></a>在 LUIS 中测试示例话语

进行测试过程中，会向 LUIS 提供示例话语并获取 LUIS 识别出的意向和实体响应。 

可以通过交互方式测试 LUIS，一次查询文本一次，或提供一组最谈话。 测试时，可以将当前活动模型的预测响应与已发布模型的预测响应进行比较。 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>测试中的分数是什么？
请参阅[预测分数](luis-concept-prediction-score.md)概念，详细了解预测分数。

## <a name="interactive-testing"></a>交互式测试
交互式测试在 LUIS 门户的“测试”  面板中完成。 可输入话语，了解意向和实体的识别和打分方式。 在测试面板中，如果 LUIS 根据话语预测的意向和实体不符合预期，则将其作为新话语复制到“意向”页  。 然后为实体标记该话语的各个部分，并训练 LUIS。 

## <a name="batch-testing"></a>批处理测试
若要同时测试多条话语，请参阅[批处理测试](luis-concept-batch-test.md)。

## <a name="endpoint-testing"></a>终结点测试
可使用[终结点](luis-glossary.md#endpoint)进行测试，最多可使用两个版本的应用。 将主要或实时版本的应用设置为“生产”终结点，将另一版本添加到“暂存”终结点   。 通过此方法可获得三个版本的话语：[LUIS](luis-reference-regions.md) 网站“测试”窗格中的当前模型，以及两个不同终结点上的两个版本。 

所有终结点测试均计入使用配额。 

## <a name="do-not-log-tests"></a>不记录测试
如果对终结点进行测试，并且不希望记录话语，请记得使用 `logging=false` 查询字符串配置。

## <a name="where-to-find-utterances"></a>在哪里可以找到话语
LUIS 将记录的所有话语存储在查询日志中，可从 LUIS 门户上的**应用**列表页，以及 LUIS [创作 API](https://go.microsoft.com/fwlink/?linkid=2092087) 下载。 

[LUIS](luis-reference-regions.md) 网站的[“查看终结点话语”](luis-how-to-review-endpoint-utterances.md)页列出了 LUIS 不确定的所有话语****。 

## <a name="remember-to-train"></a>请记住进行训练
请记住在更改模型后[训练](luis-how-to-train.md) LUIS。 在训练应用前在测试中看不到对 LUIS 应用的更改。 

## <a name="best-practices"></a>最佳实践
了解[最佳实践](luis-concept-best-practices.md)。

## <a name="next-steps"></a>后续步骤

* 详细了解[测试](luis-interactive-test.md)话语。
