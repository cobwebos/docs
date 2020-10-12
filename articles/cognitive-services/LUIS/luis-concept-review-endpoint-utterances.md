---
title: 评审用户话语 - LUIS
description: 评审终结点话语通过主动学习获取正确的意向和实体。 LUIS 选择它不确定的终结点话语。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 82f228d5e6f801539c549e16faea371782ad4b59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316437"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>通过评审终结点话语启用主动学习的相关概念
主动学习是提高预测准确性的三个策略之一，也是最易于实现的策略。 评审终结点话语通过主动学习获取正确的意向和实体。 LUIS 选择它不确定的终结点话语。

## <a name="what-is-active-learning"></a>什么是主动学习
主动学习是一个两步过程。 首先，LUIS 选择其在应用终结点收到的需要验证的陈述。 第二个步骤由应用程序所有者或协作者执行，用于验证所选的 [最谈话，包括](luis-how-to-review-endpoint-utterances.md)正确的意图和意向中的任何实体。 评审陈述后，再次训练并发布应用。

## <a name="which-utterances-are-on-the-review-list"></a>评审列表上有哪些陈述
首要触发意向分数较低或者两个最高的意向分数过于接近时，LUIS 会将陈述添加到评审列表。

## <a name="single-pool-for-utterances-per-app"></a>每个应用的话语的单个池
“查看终结点话语”列表不会根据版本更改。 要审核的话语只有一个池，不管经常编辑哪个版本的话语，也不管在终结点上发布哪个版本的应用。

在 [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/58b6f32139e2bb139ce823c9) 中，版本名称是必需项，并且必须存在于应用程序中，但不会在该验证之外使用。 审查言语适用于整个应用程序。 如果从一个版本中删除言语，则所有版本都会受影响。

## <a name="where-are-the-utterances-from"></a>陈述来自哪里
终结点言语取自应用程序 HTTP 终结点上的最终用户查询。 如果未发布应用或应用未收到点击，则没有要评审的陈述。 如果未收到针对特定意向或实体的终结点点击，则没有包含它们的陈述需要进行评审。

## <a name="schedule-review-periodically"></a>定期计划评审
不需要每天评审建议的陈述，但应将此纳入 LUIS 的常规维护。

## <a name="delete-review-items-programmatically"></a>以编程方式删除评审项
使用 **[删除未标记话语](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** API。 在删除之前，请通过 **[导出日志文件](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)** 备份这些话语。

## <a name="enable-active-learning"></a>启用主动学习

若要启用主动学习，必须记录用户查询。 这是通过 `log=true` querystring 参数和值调用[终结点查询](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint)实现的。

## <a name="next-steps"></a>后续步骤

* 了解如何[评审](luis-how-to-review-endpoint-utterances.md)终结点陈述
