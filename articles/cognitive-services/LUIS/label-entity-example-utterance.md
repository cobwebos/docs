---
title: 标记实体示例言语
description: 了解如何使用 LUIS 门户的意向详细信息页中的示例查询文本中的子组件在计算机学习实体中进行标记。
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: 0181057bd693280223806e9b5b7cd8c7f7345f79
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683778"
---
# <a name="label-machine-learning-entity-in-an-example-utterance"></a>在示例查询文本中标记计算机学习实体

在示例言语中标记实体可为 LUIS 提供示例，其中包含实体的示例，以及实体可以在言语中出现的位置的示例。

## <a name="label-example-utterances-from-the-intent-detail-page"></a>从意向详细信息页最谈话的标签示例

1. 登录到[LUIS 门户](https://www.luis.ai)，选择**订阅**和**创作资源**，查看分配给该创作资源的应用。
1. 通过在 **"我的应用**" 页上选择应用程序的名称来打开应用。
1. 选择具有要为其添加实体的最谈话示例的目的。
1. 选择要添加标签的文本，然后选择实体。

## <a name="label-with-the-entity-palette-visible"></a>显示实体调色板的标签

在规划了包含实体的架构后，在标记时保持**实体调色板**可见。 **实体调色板**提醒您计划提取的实体。

若要访问**实体调色板**，请在 " **@** 示例查询文本" 列表上方的上下文工具栏中选择符号。

> [!div class="mx-imgBorder"]
> ![意向详细信息页上的实体调色板的屏幕截图。](media/label-utterances/entity-palette-from-tool-bar.png)

## <a name="label-entity-from-in-place-menu"></a>从就地菜单中标记实体

请考虑示例查询文本， `hi, please I want a cheese pizza in 20 minutes` 。

1. 选择最左侧的文本，然后选择实体的最右侧文本，然后从就地菜单中选择要用标签标记的实体。

    > [!div class="mx-imgBorder"]
    > ![标记完整计算机-学习实体](media/label-utterances/label-steps-in-place-menu.png)


## <a name="label-entity-from-entity-palette"></a>实体调色板中的标签实体

实体调色板提供之前的标记体验的替代方法。 它允许对文本进行画笔处理，使其能够立即使用实体进行标记。

1. 通过选择 **@** 查询文本表右上方的符号，打开实体调色板。

2. 从调色板中选择要标记的实体。 此操作采用可视方式指示新的光标。 当你在 LUIS 门户中移动时，光标将跟随鼠标。

3. 在示例言语中，用光标绘制__ 实体。

    > [!div class="mx-imgBorder"]
    > ![机器学习实体的实体调色板](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="adding-entity-as-a-feature-from-the-entity-palette"></a>将实体作为功能添加到实体调色板

实体调色板的下半部分允许您向当前选定的实体添加功能。 可以从 "现有实体和短语列表" 中进行选择，也可以创建新的短语列表。

> [!div class="mx-imgBorder"]
> ![实体调色板作为功能的实体调色板的屏幕截图](media/label-utterances/entity-palette-entity-as-a-feature.png)

## <a name="labeling-entity-roles"></a>标记实体角色

实体角色使用**实体调色板**进行标记。

1. 在“意向详细信息”页上，从上下文工具栏中选择“实体调色板”****。
1. 在实体调色板打开后，从实体列表中选择实体。
1. 在实体列表下方，选择现有角色。
1. 在示例言语文本中，使用实体角色标记文本。

## <a name="review-labeled-text"></a>查看标记的文本

进行标记后，查看示例言语，并确保选定的一段文本中的选定实体带有下划线。 实线指示文本已被标记。

> [!div class="mx-imgBorder"]
> ![标记为完整的计算机学习实体](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>确认预测的实体

如果在文本范围内有一个虚线框，则表示文本已预测但_尚未标记_。 若要将预测转换为标签，请选择 "查询文本" 行，然后从上下文工具栏中选择 "**确认实体**"。

## <a name="relabeling-over-existing-entities"></a>在现有实体上重新标记

如果重新标记已标记的文本，则 LUIS 可以拆分或合并现有标签。

## <a name="labeling-for-punctuation"></a>为标点符号标记

不需要为标点符号添加标签。 使用[应用程序设置](luis-reference-application-settings.md) _word 窗体_来控制标点是否影响查询文本预测。

## <a name="unlabel-entities"></a>取消标记实体

> [!NOTE]
> 仅可标记计算机的实体。

若要 unlabel 某个实体，请选择该实体，并从就地菜单中选择 " **unlabel** "。

> [!div class="mx-imgBorder"]
> ![显示 unlabeling 实体的屏幕截图](media/label-utterances/unlabel-entity-using-in-place-menu.png)

## <a name="automatic-labeling-for-parent-and-child-entities"></a>父实体和子实体的自动标记

如果您要为父实体添加标签，则会标记可根据当前训练版本预测的任何子实体。

如果为子实体标记，则父项将自动标记。

## <a name="automatic-labeling-for-non-machine-learned-entities"></a>非计算机学习实体的自动标记

非计算机学习实体包括预生成的实体、正则表达式实体、列表实体和模式。任何实体。 这些实体由 LUIS 自动标记，因此无需用户手动标记它们。

## <a name="entity-prediction-errors"></a>实体预测错误

实体预测错误表示预测的实体与标记的实体不匹配。 这通过言语旁边的警告指示器直观显示。

> [!div class="mx-imgBorder"]
> ![机器学习实体的实体调色板](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>后续步骤

使用[仪表板](luis-how-to-use-dashboard.md)并[查看终结点言语](luis-how-to-review-endpoint-utterances.md)来提高应用的预测质量。