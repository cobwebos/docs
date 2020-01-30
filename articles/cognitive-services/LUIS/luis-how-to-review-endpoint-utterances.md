---
title: 查看用户最谈话-LUIS
titleSuffix: Azure Cognitive Services
description: 查看活动学习捕获的最谈话，以选择目的并标记最谈话的实体。接受更改、定型和发布。
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
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775119"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>如何通过查看端点最谈话改善 LUIS 应用

查看终结点最谈话进行正确预测的过程称为[活动学习](luis-concept-review-endpoint-utterances.md)。 主动学习捕获终结点查询，并选择不确定的用户终结点最谈话。 您将查看这些最谈话以选择目的并标记这些最谈话的实体。 将这些更改接受到示例最谈话，然后训练和发布。 然后，LUIS 更准确地识别最谈话。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>启用主动学习

若要启用主动学习，必须记录用户查询。 这是通过使用 `log=true` querystring 参数和值调用[终结点查询](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint)来实现的。

使用 LUIS 门户构造正确的终结点查询。

1. 在[预览版 LUIS 门户](https://preview.luis.ai/)中，从应用列表中选择应用。
1. 请在 "**管理**" 部分，选择 " **Azure 资源**"。
1. 对于分配的预测资源，请选择 "**更改查询参数**"。

    > [!div class="mx-imgBorder"]
    > ![使用 LUIS 门户来保存活动学习所需的日志。](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. 切换**保存日志**，然后选择 "**完成**"。

    > [!div class="mx-imgBorder"]
    > ![使用 LUIS 门户来保存活动学习所需的日志。](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     此操作通过添加 `log=true` querystring 参数更改示例 URL。 对运行时终结点进行预测查询时，复制并使用更改后的示例查询 URL。

## <a name="correct-intent-predictions-to-align-utterances"></a>更正意向预测以使最谈话

每个陈述的“统一意向”列中都显示一个建议意向。

> [!div class="mx-imgBorder"]
> [![查看 LUIS 不能确定的终结点最谈话](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

如果同意该意向，请选择复选标记。 如果不同意建议，请从统一意向下拉列表中选择合适的意向，然后选择统一意向右侧的复选标记。 选中复选标记后，查询文本将移到该意向，并从**查看终结点最谈话**列表中删除。

> [!TIP]
> 若要查看并更正 "**查看终结点最谈话**" 列表中的所有示例最谈话中的实体预测，这一点很重要。

## <a name="delete-utterance"></a>删除陈述

可删除评审列表中的每个陈述。 删除后，该陈述将不再出现在列表中。 即使用户从终结点中输入同一陈述，结果也是如此。

如果不确定是否应删除查询文本，请将其移到 "无" 目的，或创建新意向（如 `miscellaneous`），然后将查询文本移到该意向。

## <a name="disable-active-learning"></a>禁用活动学习

若要禁用活动学习，请不要记录用户查询。 这是通过使用 `log=false` querystring 参数和值设置[终结点查询](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint)来实现的，或者不使用 querystring 值，因为默认值为 false。

## <a name="next-steps"></a>后续步骤

若要测试标记建议陈述后的性能提升情况，可通过选择顶部面板中的“测试”访问测试控制台。 有关如何使用测试控制台测试应用的说明，请参阅[训练和测试应用](luis-interactive-test.md)。
