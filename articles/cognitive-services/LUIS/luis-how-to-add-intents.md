---
title: 添加方法-LUIS
titleSuffix: Azure Cognitive Services
description: 向 LUIS 应用添加意向，识别具有相同意向的问题或命令组。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: eb90a902b8f7fe8b37b81c2825cbdfc25ef5dc0d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932891"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>添加意向以确定用户的话语意向

向 LUIS 应用添加[意向](luis-concept-intent.md)，识别具有相同意向的问题或命令组。 

可以依次转到顶部导航栏的“生成”部分和左侧面板的“意向”来管理意向。 

## <a name="add-intent"></a>添加意向

1. 在“意向”页上，选择“创建新意向”。

1. 在“创建新意向”对话框中，输入意向名称 `GetEmployeeInformation`，然后单击“完成”。

    ![添加意向](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>添加示例话语

示例表述是用户问题或命令的文本示例。 若要传授语言理解 (LUIS)，需将示例话语添加到意向。

1. 在“GetEmployeeInformation”意向详细信息页上的意向名称下的文本框中，输入预期用户会输入的相关话语，例如 `Does John Smith work in Seattle?`，然后按 Enter。
 
    ![意向详细信息页的屏幕截图，其中突出显示了表述](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS 会将所有话语转换为小写，并在连字符等标记的两侧添加空格。

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>意向预测错误 

意向中的示例话语可能会在示例话语当前表现出的意向和在训练过程中确定的预测意向之间存在意向预测错误。 

若要找出并修复话语预测错误，请将“筛选器”选项的“评估”选项“不正确”和“不清楚”与“视图”选项“详细视图”配合使用。 

![若要找出并修复话语预测错误，请使用“筛选器”选项。](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

应用筛选器和视图以后，如果示例话语出现错误，则示例话语列表会显示话语和问题。

![![应用筛选器和视图以后，如果示例话语出现错误，则示例话语列表会显示话语和问题。](./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

每一行会显示当前训练的示例话语预测分数、最接近的对手的分数，以及这两个分数之间的差异。 

### <a name="fixing-intents"></a>修复意向

若要了解如何修复意向预测错误，请使用[摘要仪表板](luis-how-to-use-dashboard.md)。 摘要仪表板提供对活动版本的上一次训练的分析，并提供排名靠前的建议，用于修复模型。  

## <a name="add-a-custom-entity"></a>添加自定义实体

将话语添加到意向后，可以选择话语内部的文本来创建自定义实体。 通过自定义实体可以标记要提取的文本，以及正确的意向。 

请参阅[将实体添加到话语](luis-how-to-add-example-utterances.md)以了解更多信息。

## <a name="entity-prediction-discrepancy-errors"></a>实体预测偏差错误 

带红色下划线的实体表示存在[实体预测偏差](luis-how-to-add-example-utterances.md#entity-status-predictions)。 由于这是第一个出现的实体，LUIS 并未获得足够的示例，因此无法十分确信已使用正确的实体标记了此文本。 训练应用时，这种偏差将会消除。 

![“意向详细信息”页的屏幕截图，其中以蓝色突出显示了自定义实体名称](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

文本以蓝色突出显示，表示它是一个实体。  

## <a name="add-a-prebuilt-entity"></a>添加预构建实体

有关信息，请参阅[预生成实体](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app)。

## <a name="using-the-contextual-toolbar"></a>使用上下文工具栏

通过选中话语左侧的框在列表中选择一个或多个示例话语后，使用话语列表上方的工具栏可以执行以下操作：

* 重新分配意向：将话语移到不同的意向
* 删除话语
* 实体筛选器：仅显示包含已筛选实体的话语
* 全部显示/仅显示错误：显示存在预测错误的话语，或显示所有话语
* 实体/标记视图：显示包含实体名称的实体视图，或显示话语的原始文本
* 放大镜：搜索包含特定文本的话语

## <a name="working-with-an-individual-utterance"></a>使用单个话语

可以通过话语右侧的省略号菜单针对单个话语执行以下操作：

* 编辑：更改话语的文本
* 删除：从意向中删除话语。 如果仍想保留该话语，则更好的方法是将其移到 **None** 意向。 
* 添加模式：使用模式可以提取常用话语以及标记可替换的文本和可忽略的文本，从而减少在意向中提供更多话语的需要。 

使用“已标记的意向”列可以更改话语的意向。

## <a name="train-your-app-after-changing-model-with-intents"></a>在对模型进行意向更改后对应用进行培训

添加、编辑或删除意向后，请[训练](luis-how-to-train.md)并[发布](luis-how-to-publish-app.md)应用，以便将更改应用到终结点查询。 

## <a name="next-steps"></a>后续步骤

详细了解如何添加包含实体的[示例话语](luis-how-to-add-example-utterances.md)。 
