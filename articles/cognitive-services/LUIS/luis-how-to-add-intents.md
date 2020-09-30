---
title: 添加意向 - LUIS
titleSuffix: Azure Cognitive Services
description: 向 LUIS 应用添加意向，识别具有相同意向的问题或命令组。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.service: cognitive-services
ms.openlocfilehash: c2802f1b41ed2842e12c808a8c380ebd646ffa03
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540925"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>添加意向以确定用户的话语意向

向 LUIS 应用添加[意向](luis-concept-intent.md)，识别具有相同意向的问题或命令组。

在 LUIS 门户中，可以依次转到顶部导航栏的“生成”部分和左侧面板的“意向”来管理意向**** ****。

## <a name="add-an-intent-to-your-app"></a>向应用程序添加意向

1. 登录到 [LUIS 门户](https://www.luis.ai)，选择“订阅”**** 和“创作资源”**** 以查看分配给该创作资源的应用。
1. 在“我的应用”页上选择应用名称以打开应用****。
1. 在“意向”页上，选择“+ 创建”**** ****。
1. 在“创建新意向”对话框中，输入意向名称（例如 `ModifyOrder`），然后选择“完成”**** ****。

    > [!div class="mx-imgBorder"]
    > ![添加意向](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    意向需要[示例言语](luis-concept-utterance.md)，以便在已发布的预测终结点上预测言语。

## <a name="add-an-example-utterance"></a>添加示例话语

示例表述是用户问题或命令的文本示例。 若要传授语言理解 (LUIS) 何时预测这种意向，需将示例言语添加到意向。 LUIS 需要 15 到 30 个示例言语才能开始了解意向。 请不要批量添加示例言语。 应当慎重选择每个言语，应考虑它与意向中已有的言语有何不同之处。

1. 在意向详细页中，在意向名称下的文本框中输入期望用户输入的相关言语（例如 `Deliver a large cheese pizza`），然后按 Enter。

    > [!div class="mx-imgBorder"]
    > ![意向详细信息页的屏幕截图，其中突出显示了言语](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png)

    LUIS 会将所有言语转换为小写，并在连字符等[标记](luis-language-support.md#tokenization)的两侧添加空格。

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>意向预测错误

当未使用受过训练的应用来预测意向的言语时，将确定为一个意向预测错误。

1. 若要找出并解决言语预测错误，请使用“不正确”或“不明确”这两个“筛选器”选项****。

    > [!div class="mx-imgBorder"]
    > ![若要找出并修复言语预测错误，请使用“筛选器”选项。](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

1. 若要在“意向详细信息”页面上显示分数值，请从“查看”选项菜单中选择“显示详细意向分数”**** ****。

    应用筛选器和视图以后，如果示例话语出现错误，则示例话语列表会显示话语和问题。

每一行会显示当前训练的示例话语预测分数、最接近的对手的分数，以及这两个分数之间的差异。

### <a name="fixing-intents"></a>修复意向

若要了解如何修复意向预测错误，请使用[摘要仪表板](luis-how-to-use-dashboard.md)。 摘要仪表板提供对活动版本的上一次训练的分析，并提供排名靠前的建议，用于修复模型。

## <a name="using-the-contextual-toolbar"></a>使用上下文工具栏

上下文工具栏提供其他操作：

* 编辑或删除示例言语
* 将示例言语重新分配给其他意向
* 筛选和查看：仅显示包含已筛选实体的言语或查看可选详细信息
* 搜索示例言语

## <a name="train-your-app-after-changing-model-with-intents"></a>在对模型进行意向更改后对应用进行培训

添加、编辑或删除意向后，请[训练](luis-how-to-train.md)并[发布](luis-how-to-publish-app.md)应用，以便将更改应用到终结点查询。 不要在每次更改后进行训练。 在经过一系列更改后进行训练。

## <a name="next-steps"></a>后续步骤

详细了解如何添加包含实体的[示例话语](luis-how-to-add-example-utterances.md)。
