---
title: 标记实体示例言语
titleSuffix: Azure Cognitive Services
description: 了解如何使用 LUIS 门户的意向详细信息页中的示例言语中的子组件标记机器学习的实体。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: 5499e8362e1107a18b997a6a485e11343c328d0d
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898372"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>在示例言语中标记机器学习的实体

在示例言语中标记实体可为 LUIS 提供示例，其中包含实体的示例，以及实体可以在言语中出现的位置的示例。

## <a name="labeling-machine-learned-entity"></a>标记机器学习实体

不妨使用此短语：`hi, please I want a cheese pizza in 20 minutes`。

1. 选择最左侧的文本，然后选择实体最右侧的文本，接下来选择想要进行标记的实体（在本例中为“完整订单”）。 下图中标记了完成顺序  。

    > [!div class="mx-imgBorder"]
    > ![标记完整的机器学习实体](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. 从弹出窗口中选择实体。 标记的完整的比萨订单实体包括所有被标记的单词（从左到右，英语）。

## <a name="review-labeled-text"></a>查看标记的文本

进行标记后，查看示例言语，并确保选定的一段文本中的选定实体带有下划线。 实线指示文本已被标记。

> [!div class="mx-imgBorder"]
> ![标记的完整机器学习实体](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>确认预测的实体

如果这一段文本被虚线框框起来，并且实体名称位于言语上方，则表示已预测文本，但_尚未标记_。 若要将预测转变为标签，依次选择该言语所在的行和“确认实体预测”  。

> [!div class="mx-imgBorder"]
> ![预测完整的机器学习实体](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

或者，可选择文本上方的实体名称，然后从显示的菜单中选择“确认预测”  。

> [!div class="mx-imgBorder"]
> ![使用菜单预测完整的机器习得实体](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted-menu.png)

## <a name="label-entity-by-painting-with-entity-palette-cursor"></a>通过使用实体调色板光标进行绘制来标记实体

实体调色板提供之前的标记体验的替代方法。 它允许对文本进行画笔处理，使其能够立即使用实体进行标记。

1. 通过选择言语表右上方的荧光笔图标来打开实体调色板。

    > [!div class="mx-imgBorder"]
    > ![机器学习实体的实体调色板](media/label-utterances/example-1-label-machine-learned-entity-palette.png)

2. 选择实体组件。 此操作采用可视方式指示新的光标。 当你在门户中移动时，光标将跟随鼠标。

    > [!div class="mx-imgBorder"]
    > ![机器学习实体的实体调色板](media/label-utterances/example-1-label-machine-learned-entity-palette-menu.png)

3. 在示例言语中，用光标绘制  实体。

    > [!div class="mx-imgBorder"]
    > ![机器学习实体的实体调色板](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="labeling-subcomponents-of-a-machine-learned-entity"></a>标记机器习得实体的子组件

实体中子组件的标记方式与顶级实体的标记方式完全相同。 选择文本时，弹出窗口中可用的实体是相对于文本出现的上下文而言的。 例如，如果你有一个 5 级机器习得实体，并选择标记为第 1 级和第 2 级的文本（通过示例言语下已标记的实体名称表示），则弹出窗口中可用的实体会被限制为第 3 级组件的上下文。 若要使用其他实体标记文本，请选择“标记为其他实体”选项  。

> [!div class="mx-imgBorder"]
> ![机器学习实体的实体调色板](media/label-utterances/example-1-label-machine-learned-entity-subcomponent.png)

仅当父组件也进行标记时，才可对子组件进行标记。

## <a name="labeling-entity-roles"></a>标记实体角色

实体角色使用实体调色板进行标记。

1. 在“意向详细信息”页上，从上下文工具栏中选择“实体调色板”  。
1. 在实体调色板打开后，从实体列表中选择实体。
1. 转到“实体检查器”  ，选择现有角色或创建新角色。
1. 在示例言语文本中，使用实体角色标记文本。

## <a name="unlabel-entities"></a>取消标记实体

若要取消标记实体，请选择文本下方的实体名称，然后选择“取消标记”  。 如果尝试取消标记的实体具有已标记的子组件，则必须先取消标记子组件。

## <a name="editing-labels-using-the-entity-palette"></a>使用实体调色板编辑标签

如果在进行标记时出错，则可使用实体调色板轻松实现快速编辑。 例如，如果某个实体标签错误地多标记了一个字词，且其具有已标记的子组件，则可使用实体调色板来对所需的较短字词范围进行画笔处理。

例如：

1. “披萨类型”子组件跨越“cheese pizza with”，其中包含多余的错误字词“with”

    > [!div class="mx-imgBorder"]
    > ![机器学习实体的实体调色板](media/label-utterances/edit-label-with-palette-1.png)

2. 使用实体调色板选取“披萨类型”并对“cheese pizza”进行画笔处理。 结果就是现在仅“cheese pizza”标记为“披萨类型”。

    > [!div class="mx-imgBorder"]
    > ![机器学习实体的实体调色板](media/label-utterances/edit-label-with-palette-2.png)

## <a name="labels-for-matching-text-entities"></a>用于匹配文本实体的标签

匹配文本实体包括预生成实体、正则表达式实体、列表实体和 pattern.any 实体。 这些实体由 LUIS 自动标记，因此无需用户手动标记它们。

## <a name="entity-prediction-errors"></a>实体预测错误

实体预测错误表示预测的实体与标记的实体不匹配。 这通过言语旁边的警告指示器直观显示。

> [!div class="mx-imgBorder"]
> ![机器学习实体的实体调色板](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>后续步骤

使用[仪表板](luis-how-to-use-dashboard.md)并[查看终结点言语](luis-how-to-review-endpoint-utterances.md)来提高应用的预测质量。