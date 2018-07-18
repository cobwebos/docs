---
title: LUIS 应用的企业概念- Azure | Microsoft Docs
description: 了解大型 LUIS 应用的设计概念。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/05/2018
ms.author: v-geberr
ms.openlocfilehash: 1f501981dd4b45f4d36188ef4c2aaa6cb11881a2
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263747"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>LUIS 应用的企业策略
查看企业应用的设计策略。

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>LUIS 请求超出配额
如果 LUIS 应用请求率超过了允许的[配额率](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)，请将负载分散至多个具有[相同应用定义](#use-multiple-apps-with-same-app-definition)的 LUIS 应用，或创建多个密钥并将这些密钥[分配](#assign-multiple-luis-keys-to-same-app)至该应用。 

### <a name="use-multiple-apps-with-same-app-definition"></a>使用多个具有相同应用定义的应用
导出原始 LUIS 应用，然后将该应用导入回单独的应用。 每个应用都有其自己的应用 ID。 在发布时，请为每个应用创建单独的密钥，而不是对所有应用使用相同的密钥。 均衡所有应用的负载，避免单个应用处于过载状态。 添加 [Application Insights](luis-tutorial-bot-csharp-appinsights.md) 来监视使用情况。 

若要在所有应用之间获取同样的最高意向，请确保第一意向和第二意向之间的差距足够大，不会让 LUIS 混淆，针对陈述中微小的变化在应用间给出不同的结果。 

将单个应用指定为主应用。 建议查看的任何陈述都应添加到主应用，然后移回所有其他应用。 这是应用的一次完整导出，或是将主应用中已标记的陈述加载到子级。 可从 [LUIS][LUIS] 网站或者[单个陈述](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08)或[批量陈述](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)的创作 API 完成加载。 

计划定期（例如每两周一次）[查看终结点陈述](label-suggested-utterances.md)以进行主动学习，然后重新训练并重新发布。 

### <a name="assign-multiple-luis-keys-to-same-app"></a>将多个 LUIS 密钥分配到相同的应用
如果 LUIS 应用接收到的终结点命中数超过了单个密钥的配额，请创建更多密钥并将它们分配到该 LUIS 应用。 创建流量管理器或负载均衡器，管理这些订阅密钥间的终结点查询。 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>单体应用返回错误意向
如果应用要预测多种陈述，请考虑实施[调度模型](#dispatch-tool-and-model)。 分解单体应用可让 LUIS 成功地专注于意向间的检测，而不会在父应用和子应用中的意向间产生混淆。 

计划定期（例如每两周一次）[查看终结点陈述](label-suggested-utterances.md)以进行主动学习，然后重新训练并重新发布。 

## <a name="when-you-need-to-have-more-than-500-intents"></a>需要超过 500 个意向
例如，假设要开发一个包含超过 500 个意向的办公室助手。 如果有 200 个关于计划会议的意向、200 个关于提醒的意向、200 个关于获取同事信息的意向以及 200 个 关于发送电子邮件的意向，则对意向进行分组，每组位于一个应用中，然后创建包含每个意向的顶层应用。 使用[调度工具和体系结构](#dispatch-tool-and-model)来生成顶层应用。 然后将聊天机器人更改为使用级联调用，如[调度教程][dispatcher-application-tutorial]中所示。 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>需要合并多个 LUIS 和 QnA Maker 应用
如果有多个 LUIS 和 QnA Maker 应用需要响应一个机器人，请使用[调度工具](#dispatch-tool-and-model)来生成顶层应用。 然后将聊天机器人更改为使用级联调用，如[调度教程][dispatcher-application-tutorial]中所示。 

## <a name="dispatch-tool-and-model"></a>调度工具和模型
使用 [BotBuilder-tools](https://github.com/Microsoft/botbuilder-tools) 中的[调度][dispatch-tool]命令行工具，将多个 LUIS 和/或 QnA Maker 应用合并至父 LUIS 应用。 采用此方法可以得到一个父域，将所有使用者域和不同的子使用者域都包含在单独的应用中。 

![调度体系结构的概念图](./media/luis-concept-enterprise/dispatch-architecture.png)

父域在 LUIS 中记录为“V Dispatch”应用。 

![带有由调度工具创建的 LUIS 应用的 LUIS 应用列表的屏幕截图](./media/luis-concept-enterprise/dispatch.png)

聊天机器人接收陈述，然后发送至父 LUIS 应用进行预测。 父应用中的最高预测意向决定下一个要调用的子 LUIS 应用。 聊天机器人将该陈述发送至子应用进行更具体的预测。

了解如何从 Bot Builder v4 [dispatcher-application-tutorial][dispatcher-application-tutorial] 创建这个调用层次结构。  

### <a name="intent-limits-in-dispatch-model"></a>调度模型中的意向限制
一个调度应用程序最多可包含 500 个调度资源，相当于 500 个意向。 

## <a name="next-steps"></a>后续步骤

* 了解如何[测试批处理](luis-how-to-batch-test.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch