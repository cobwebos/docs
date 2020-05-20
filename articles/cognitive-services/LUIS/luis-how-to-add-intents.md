---
title: 添加意向 - LUIS
titleSuffix: Azure Cognitive Services
description: 向 LUIS 应用添加意向，识别具有相同意向的问题或命令组。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: f282bb0a80226afeb314aac4e8669634327f81d2
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654048"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>添加意向以确定用户的话语意向

向 LUIS 应用添加[意向](luis-concept-intent.md)，识别具有相同意向的问题或命令组。

在 LUIS 门户中，从顶部导航**栏的 "****生成**" 部分管理意向，然后从左侧面板的 "管理"。

## <a name="add-an-intent-to-your-app"></a>向应用程序添加意向

1. 登录到[LUIS 门户](https://www.luis.ai)，选择**订阅**和**创作资源**，查看分配给该创作资源的应用。
1. 通过在 **"我的应用**" 页上选择应用程序的名称来打开应用。
1. 在 "**意向**" 页上，选择 " **+ 创建**"。
1. 在 "**新建意向**" 对话框中，输入意向名称，例如 `ModifyOrder` ，并选择 "**完成**"。

    > [!div class="mx-imgBorder"]
    > ![添加意向](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    目的需要[最谈话示例](luis-concept-utterance.md)，以便在已发布的预测终结点预测最谈话。

## <a name="add-an-example-utterance"></a>添加示例话语

示例表述是用户问题或命令的文本示例。 若要教授语言理解（LUIS）预测这一目的，需要将示例最谈话添加到意向。 LUIS 需要在15到30个示例最谈话的范围内开始了解意向。 请不要批量添加示例言语。 应仔细选择每个查询文本，使其与已在意向中的示例不同。

1. 在 "意向详细信息" 页上，输入你期望用户使用的相关查询文本，例如， `Deliver a large cheese pizza` 在 "意向名称" 下的文本框中，然后按 enter。

    > [!div class="mx-imgBorder"]
    > ![意向详细信息页的屏幕截图，其中突出显示了表述](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png)

    LUIS 将所有最谈话都转换为小写，并在[标记](luis-language-support.md#tokenization)之间添加空格，例如连字符。

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>意向预测错误

如果未使用训练的应用进行预测，则会确定意向预测错误查询文本。

1. 若要查找并修复查询文本预测错误，请使用不正确和不明确的**筛选器**选项。

    > [!div class="mx-imgBorder"]
    > ![若要找出并修复话语预测错误，请使用“筛选器”选项。](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

1. 若要在 "目的详细信息" 页上显示分数值，请从 "**查看**选项" 菜单中选择 "**显示详细信息意向评分**"。

    应用筛选器和视图以后，如果示例话语出现错误，则示例话语列表会显示话语和问题。

每一行会显示当前训练的示例话语预测分数、最接近的对手的分数，以及这两个分数之间的差异。

### <a name="fixing-intents"></a>修复意向

若要了解如何修复意向预测错误，请使用[摘要仪表板](luis-how-to-use-dashboard.md)。 摘要仪表板提供对活动版本的上一次训练的分析，并提供排名靠前的建议，用于修复模型。

## <a name="using-the-contextual-toolbar"></a>使用上下文工具栏

上下文工具栏提供了其他操作：

* 编辑或删除示例查询文本
* 将示例查询文本重新分配给其他意图
* 筛选器和视图：仅显示包含已筛选实体的最谈话或查看可选详细信息
* 搜索示例最谈话

## <a name="train-your-app-after-changing-model-with-intents"></a>在对模型进行意向更改后对应用进行培训

添加、编辑或删除意向后，请[训练](luis-how-to-train.md)并[发布](luis-how-to-publish-app.md)应用，以便将更改应用到终结点查询。 不要在每次更改后进行定型。 在一组更改之后训练。

## <a name="next-steps"></a>后续步骤

详细了解如何添加包含实体的[示例话语](luis-how-to-add-example-utterances.md)。
