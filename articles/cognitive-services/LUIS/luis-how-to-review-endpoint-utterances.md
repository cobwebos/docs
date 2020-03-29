---
title: 评审用户话语 - LUIS
titleSuffix: Azure Cognitive Services
description: 查看通过主动学习捕获的话语，以选择意图并为阅读世界陈述标记实体;接受更改、培训和发布。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.openlocfilehash: 95b7c7446a47fafd26d00b0da4d880786340fcd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219848"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>如何通过查看终结点陈述来改进 LUIS 应用

查看端点陈述以进行正确预测的过程称为["活动学习](luis-concept-review-endpoint-utterances.md)"。 主动学习捕获终结点查询并选择用户不确定的终结点话语。 你审核这些话语以选择意向并标记这些真实话语的实体。 在示例话语中接受这些更改，然后对其进行训练和发布。 然后 LUIS 更准确地识别话语。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>启用主动学习

要启用活动学习，必须记录用户查询。 这是通过使用查询字符串参数和值调用[终结点查询](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint)来实现的`log=true`。

使用 LUIS 门户构造正确的终结点查询。

1. 在[预览 LUIS 门户](https://preview.luis.ai/)中，从应用列表中选择你的应用。
1. 转到 **"管理**"部分，然后选择**Azure 资源**。
1. 对于分配的预测资源，选择 **"更改查询参数**"。

    > [!div class="mx-imgBorder"]
    > ![使用 LUIS 门户保存日志，这是活动学习所必需的。](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. 切换 **"保存"日志**，然后通过选择 **"完成"** 进行保存。

    > [!div class="mx-imgBorder"]
    > ![使用 LUIS 门户保存日志，这是活动学习所必需的。](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     此操作通过添加 `log=true` 查询字符串参数来更改示例 URL。 对运行时终结点进行预测查询时，请复制并使用更改后的示例查询 URL。

## <a name="correct-intent-predictions-to-align-utterances"></a>更正意图预测以对齐陈述

每个陈述的“统一意向”列中都显示一个建议意向****。

> [!div class="mx-imgBorder"]
> [![查看 LUIS 不确定的终结点陈述](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

如果您同意该意图，请选择复选标记。 如果不同意建议，请从统一意向下拉列表中选择合适的意向，然后选择统一意向右侧的复选标记。 在复选标记上选择后，陈述将移动到意图，并从 **"查看终结点陈述"** 列表中删除。

> [!TIP]
> 请务必转到"意向详细信息"页，以查看和更正 **"查看终结点内容**"列表中所有示例陈述中的实体预测。

## <a name="delete-utterance"></a>删除陈述

可删除评审列表中的每个陈述。 删除后，该陈述将不再出现在列表中。 即使用户从终结点中输入同一陈述，结果也是如此。

如果您不确定是否应删除陈述，请将其移动到"无"意图，或创建一个新意图，例如`miscellaneous`，并将陈述移动到该意图。

## <a name="disable-active-learning"></a>禁用主动学习

若要禁用主动学习，请不要记录用户查询。 这是通过使用`log=false`查询字符串参数和值设置[终结点查询](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint)来实现的，或者不使用查询字符串值，因为默认值为 false。

## <a name="next-steps"></a>后续步骤

若要测试标记建议陈述后的性能提升情况，可通过选择顶部面板中的“测试”访问测试控制台****。 有关如何使用测试控制台测试应用的说明，请参阅[训练和测试应用](luis-interactive-test.md)。
