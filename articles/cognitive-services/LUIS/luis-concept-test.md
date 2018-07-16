---
title: 测试 LUIS 应用 - Azure | Microsoft Docs
description: 使用语言理解 (LUIS) 持续优化应用程序并改进其语言理解能力。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 8c702d2adbadd2736eed05c7580e8aabf69affbf
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266322"
---
# <a name="testing-in-luis"></a>在 LUIS 中进行测试

进行测试过程中，会向 LUIS 提供示例话语并获取 LUIS 识别出的意向和实体响应。 

可以交互方式[测试](interactive-test.md) LUIS，一次提供一条话语，或[一批](luis-concept-batch-test.md)话语。 通过测试，可比较当前[活动](luis-concept-version.md#active-version)模型与已发布的模型。 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>
## <a name="what-is-a-score-in-testing"></a>测试中的分数是什么？
请参阅[预测分数](luis-concept-prediction-score.md)概念，详细了解预测分数。

## <a name="interactive-testing"></a>交互式测试
交互式测试在网站的“测试”面板上进行。 可输入话语，了解意向和实体的识别和打分方式。 在测试窗格中，如果 LUIS 根据话语预测的意向和实体不符合预期，则将其作为新话语复制到“意向”页。 然后标记该话语部分并训练 LUIS。 

## <a name="batch-testing"></a>批处理测试
若要同时测试多条话语，请参阅[批处理测试](luis-concept-batch-test.md)。

## <a name="endpoint-testing"></a>终结点测试
可使用[终结点](luis-glossary.md#endpoint)进行测试，最多可使用两个版本的应用。 将主要或实时版本的应用设置为“生产”终结点，将另一版本添加到“暂存”终结点。 通过此方法可获得三个版本的话语：[LUIS][LUIS] 网站“测试”窗格中的当前模型，以及两个不同终结点上的两个版本。 

所有终结点测试均计入使用配额。 

## <a name="do-not-log-tests"></a>不记录测试
如果对终结点进行测试，并且不希望记录话语，请记得使用 `logging=false` 查询字符串配置。

## <a name="where-to-find-utterances"></a>在哪里可以找到话语
LUIS 将记录的所有话语存储在查询日志中，可在 [LUIS][LUIS] 网站的“应用”列表页，以及 LUIS [创作 API](https://aka.ms/luis-authoring-apis) 上下载。 

[LUIS][LUIS] 网站的[“查看终结点话语”](label-suggested-utterances.md)页列出了 LUIS 不确定的所有话语。 

![查看终结点话语](./media/luis-concept-test/review-endpoint-utterances.png)
 
## <a name="remember-to-train"></a>请记住进行训练
请记住在更改模型后[训练](luis-how-to-train.md) LUIS。 在训练应用前在测试中看不到对 LUIS 应用的更改。 

## <a name="best-practices"></a>最佳实践
了解[最佳实践](luis-concept-best-practices.md)。

## <a name="next-steps"></a>后续步骤

* 详细了解[测试](interactive-test.md)话语。

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions