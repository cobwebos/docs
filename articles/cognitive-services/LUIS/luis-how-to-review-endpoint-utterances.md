---
title: 评审用户话语 - LUIS
titleSuffix: Azure Cognitive Services
description: 评审主动学习捕获的言语，以选择意向并标记真实言语的实体；接受更改、训练并发布。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: 9777e4f9b2205d2f6cdf6158b035e2a18403d69f
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541452"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>如何通过评审终结点言语来改进 LUIS 应用

评审终结点言语以进行正确预测的过程称为[主动学习](luis-concept-review-endpoint-utterances.md)。 主动学习捕获终结点查询并选择用户不确定的终结点话语。 评审这些言语以选择意向并标记这些真实言语的实体。 将这些更改接受到示例言语中，然后对其进行训练并将其发布。 然后 LUIS 更准确地识别话语。

## <a name="enable-active-learning"></a>启用主动学习

若要启用主动学习，必须记录用户查询。 这是通过 `log=true` querystring 参数和值调用[终结点查询](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint)实现的。

使用 LUIS 门户构造正确的终结点查询。

1. 登录到 [LUIS 门户](https://www.luis.ai)，选择“订阅”和“创作资源”以查看分配给该创作资源的应用。
1. 在“我的应用”页上选择应用名称以打开应用。
1. 转到“管理”部分，然后选择“Azure 资源”。
1. 对于分配的预测资源，选择“更改查询参数”。

    > [!div class="mx-imgBorder"]
    > ![屏幕截图显示 "更改查询参数" 链接。](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. 切换“保存日志”，然后选择“完成”进行保存。

    > [!div class="mx-imgBorder"]
    > ![使用 LUIS 门户保存日志，这是主动学习所必需的。](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     此操作通过添加 `log=true` 查询字符串参数来更改示例 URL。 对运行时终结点进行预测查询时，请复制并使用更改后的示例查询 URL。

## <a name="correct-intent-predictions-to-align-utterances"></a>纠正意向预测以匹配言语

每个陈述的“统一意向”列中都显示一个建议意向****。

> [!div class="mx-imgBorder"]
> [![查看 LUIS 无法确定的终结点言语](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

如果同意该意向，请选择复选标记。 如果不同意建议，请从统一意向下拉列表中选择合适的意向，然后选择统一意向右侧的复选标记。 在复选标记上进行选择后，言语将移至该意向并从“评审终结点言语”列表中删除。

> [!TIP]
> 若要查看并纠正来自“评审终结点言语”列表中的所有示例言语的实体预测，请务必转到意向详细信息页。

## <a name="delete-utterance"></a>删除陈述

可删除评审列表中的每个陈述。 删除后，该陈述将不再出现在列表中。 即使用户从终结点中输入同一陈述，结果也是如此。

如果不确定是否应删除言语，请将其移至 None 意向，或创建新的意向（例如 `miscellaneous`），并将言语移至该意向。

## <a name="disable-active-learning"></a>禁用主动学习

若要禁用主动学习，请不要记录用户查询。 这可以通过使用 `log=false` 查询字符串参数和值设置[终结点查询](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint)，或不使用查询字符串值（因为默认值是 false）来实现。

## <a name="next-steps"></a>后续步骤

若要测试标记建议陈述后的性能提升情况，可通过选择顶部面板中的“测试”访问测试控制台****。 有关如何使用测试控制台测试应用的说明，请参阅[训练和测试应用](luis-interactive-test.md)。
