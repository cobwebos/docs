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
ms.date: 11/08/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 66a3350dee60772ce706af8995179dcd8c485b64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "73904325"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>添加意向以确定用户的话语意向

向 LUIS 应用添加[意向](luis-concept-intent.md)，识别具有相同意向的问题或命令组。 

可以依次转到顶部导航栏的“生成”部分和左侧面板的“意向”来管理意向。******** 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-intent"></a>添加意向

1. 在[LUIS 预览门户](https://preview.luis.ai)中，选择 **"生成"** 以查看意图。 
1. 在 **"意向"** 页上，选择 **"创建**"。
1. 在 **"创建新意向"** 对话框中，输入意向名称，例如`ModifyOrder`，然后选择 **"完成**"。

    > [!div class="mx-imgBorder"]
    > ![添加意图](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    意图需要示例陈述。

## <a name="add-an-example-utterance"></a>添加示例话语

示例表述是用户问题或命令的文本示例。 要教导语言理解 （LUIS） 何时预测此意图，您需要向意图添加示例陈述。 LUIS 需要 15 到 30 个示例陈述来开始理解意图。 请不要批量添加示例言语。 每个陈述都应仔细选择，因为它与意图中已有的示例如何不同。 

1. 在意向详细信息页上，输入您希望用户提供的相关陈述，例如`Deliver a large cheese pizza`在意向名称下方的文本框中，然后按 Enter。
 
    > [!div class="mx-imgBorder"]
    > ![意向详细信息页的屏幕截图，其中突出显示了表述](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS 将所有陈述转换为小写，并在[代号](luis-language-support.md#tokenization)（如连字符）周围添加空格。

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>意向预测错误 

意图中的示例陈述在示例陈述当前的意图与训练期间确定的意图之间可能存在意图预测错误。 

要查找陈述预测错误并修复它们，请使用"不正确和不明确"的 **"筛选器"** 选项与 **"详细视图**"的 **"视图"** 选项结合使用。 

![若要找出并修复话语预测错误，请使用“筛选器”选项。](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

应用筛选器和视图以后，如果示例话语出现错误，则示例话语列表会显示话语和问题。

> [!div class="mx-imgBorder"]
> ![![应用筛选器和视图以后，如果示例话语出现错误，则示例话语列表会显示话语和问题。](./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

每一行会显示当前训练的示例话语预测分数、最接近的对手的分数，以及这两个分数之间的差异。 

### <a name="fixing-intents"></a>修复意向

若要了解如何修复意向预测错误，请使用[摘要仪表板](luis-how-to-use-dashboard.md)。 摘要仪表板提供对活动版本的上一次训练的分析，并提供排名靠前的建议，用于修复模型。  

## <a name="using-the-contextual-toolbar"></a>使用上下文工具栏

上下文工具栏提供其他操作：

* 编辑或删除示例陈述
* 将示例陈述重新分配给其他意图
* 筛选器和视图：仅显示包含筛选实体或查看可选详细信息的表述
* 搜索示例陈述

## <a name="train-your-app-after-changing-model-with-intents"></a>在对模型进行意向更改后对应用进行培训

添加、编辑或删除意向后，请[训练](luis-how-to-train.md)并[发布](luis-how-to-publish-app.md)应用，以便将更改应用到终结点查询。 每一次更换后都不要训练。 一组更改后进行培训。 

## <a name="next-steps"></a>后续步骤

详细了解如何添加包含实体的[示例话语](luis-how-to-add-example-utterances.md)。 
