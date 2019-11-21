---
title: 标记实体示例言语
titleSuffix: Azure Cognitive Services
description: 了解如何使用 LUIS 门户的意向详细信息页中的示例言语中的子组件标记机器学习的实体。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 87b7375b2da0effbc18fff6a7e9d67383c93a403
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134118"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>在示例言语中标记机器学习的实体

在示例言语中标记实体将显示 LUIS，其中包含实体的示例，以及实体可以出现在言语中的位置。 

## <a name="labeling-machine-learned-entity"></a>标记机器学习实体

不妨使用此短语：`hi, please I want a cheese pizza in 20 minutes`。 

1. 选择最左侧的文本，然后选择实体的最右侧文本。 下图中标记了完成顺序  。

    > [!div class="mx-imgBorder"]
    > ![标记完整的机器学习实体](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. 从弹出窗口中选择实体。 标记的完整的比萨订单实体包括所有被标记的单词（从左到右，英语）。 

> [!TIP]
> 相对于文本出现的上下文弹出窗口中可用的实体。 例如，如果你有一个 5 级机器学习实体，并且你选择的是第 3 级文本（通过示例言语下标记的实体名称表示），则弹出窗口中可用的实体会被限制为第 3 级别子组件的上下文（第 4 级别子组件）。 

## <a name="review-labeled-text"></a>查看标记的文本

标记后，查看示例言语。 LUIS 将当前模型应用于标记后的示例言语。 实线指示文本已被标记。 

> [!div class="mx-imgBorder"]
> ![标记的完整机器学习实体](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="when-to-train"></a>何时训练

如果当前模型应支持标记实体，但示例言语继续将文本显示为“已预测”但未标记，则训练你的应用。  

## <a name="confirm-predicted-entity"></a>确认预测的实体

如果视觉对象指示器位于言语上方，则指示已预测文本但尚未进行标记  。 若要将预测转变为标签，依次选择该言语和“确认实体预测”  。

> [!div class="mx-imgBorder"]
> ![预测完整的机器学习实体](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

## <a name="label-subcomponent-entity-by-painting-with-entity-palette-cursor"></a>通过使用实体调色板光标进行绘制来标记子组件实体

1. 为了纠正预测（显示在示例言语上方的实体），请打开实体调色板。 

    > [!div class="mx-imgBorder"]
    > ![机器学习实体的实体调色板](media/label-utterances/pizza-entity-palette-with-pizza-type-selected.png)

1. 选择实体子组件。 此操作采用可视方式指示新的光标。 当你在门户中移动时，光标将跟随鼠标。 

    > [!div class="mx-imgBorder"]
    > ![机器学习实体的实体调色板](media/label-utterances/pizza-type-entity-palette-cursor.png)

1. 在示例言语中，用光标绘制  实体。 

    > [!div class="mx-imgBorder"]
    > ![机器学习实体的实体调色板](media/label-utterances/paint-subcomponent-with-entity-palette-cursor.png)

## <a name="labeling-matching-text-entities-to-a-machine-learned-entity"></a>将匹配文本实体标记为机器学习的实体

匹配文本实体包括预生成实体、正则表达式实体和列表实体。 创建或编辑机器学习实体时，可以将这些添加到机器学习实体，作为对子组件的约束。 

**添加这些约束后，无需在示例言语中标记匹配文本。**

## <a name="entity-prediction-errors"></a>实体预测错误

实体预测错误显示警告指示器。 这表明预测的实体与标记的实体不匹配。 

> [!div class="mx-imgBorder"]
> ![机器学习实体的实体调色板](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>后续步骤

使用[仪表板](luis-how-to-use-dashboard.md)并[查看终结点言语](luis-how-to-review-endpoint-utterances.md)来提高应用的预测质量。
