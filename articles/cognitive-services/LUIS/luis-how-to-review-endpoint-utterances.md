---
title: 评审用户话语 - LUIS
titleSuffix: Azure Cognitive Services
description: 查看活动学习捕获的最谈话，以选择目的并标记最谈话的实体。接受更改、定型和发布。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4a77ac26076fc1b1e4e94ee24dafb28a0e88c858
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669374"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>如何在 LUIS 门户中审核终结点话语以进行主动学习

[主动学习](luis-concept-review-endpoint-utterances.md)捕获终结点查询并选择用户不确定的终结点话语。 你审核这些话语以选择意向并标记这些真实话语的实体。 在示例话语中接受这些更改，然后对其进行训练和发布。 然后 LUIS 更准确地识别话语。

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="enable-active-learning"></a>启用主动学习

若要启用主动学习，请记录用户查询。 这是通过使用 [ querystring 参数和值设置](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint)终结点查询`log=true`来实现的。

## <a name="disable-active-learning"></a>禁用主动学习

若要禁用主动学习，请不要记录用户查询。 这是通过使用 [ querystring 参数和值设置](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint)终结点查询`log=false`来实现的。

## <a name="filter-utterances"></a>筛选陈述

1. 通过在“我的应用”页上选择应用名称来打开应用（例如 TravelAgent），然后选择顶栏中的“生成”。

1. 在“提升应用性能”下，选择“评审终结点陈述”。

1. 在“评审终结点陈述”页，在“按意向或实体筛选列表”文本框中进行选择。 此下拉列表的“意向”下包含所有意向，“实体”下包含所有实体。

    ![陈述筛选](./media/label-suggested-utterances/filter.png)

1. 在下拉列表中选择类别（意向或实体）并评审陈述。

    ![意向陈述](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>标记实体
LUIS 将实体标记（字词）替换为突出显示为蓝色的实体名称。 如果陈述具有未标记的实体，请在陈述中对其进行标记。 

1. 选择陈述中的字词。 

1. 从列表选择一个实体。

    ![标记实体](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>统一单个陈述

每个陈述的“统一意向”列中都显示一个建议意向。 

1. 如果同意该建议，则选择复选标记。

    ![保留统一意向](./media/label-suggested-utterances/align-intent-check.png)

1. 如果不同意建议，请从统一意向下拉列表中选择合适的意向，然后选择统一意向右侧的复选标记。 

    ![统一意向](./media/label-suggested-utterances/align-intent.png)

1. 选择复选标记后，该陈述将从列表中移除。 

## <a name="align-several-utterances"></a>统一多个陈述

若要统一多个陈述，请勾选陈述左侧的框，然后选择“添加所选”按钮。 

![统一多个陈述](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>验证统一意向

要想验证已将陈述统一为具有正确的意向，可转到“意向”页，选择意向名称，然后评审陈述。 “评审终结点陈述”中的陈述将出现在列表中。

## <a name="delete-utterance"></a>删除陈述

可删除评审列表中的每个陈述。 删除后，该陈述将不再出现在列表中。 即使用户从终结点中输入同一陈述，结果也是如此。 

如果不确定是否应删除陈述，请将其移动到 None 意向，或创建一个新意向（例如“其他”），然后将陈述移入此意向。 

## <a name="delete-several-utterances"></a>删除多个陈述

若要删除多个陈述，请选择各项，然后选择“添加所选”按钮右侧的垃圾桶。

![删除多个陈述](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>后续步骤

若要测试标记建议陈述后的性能提升情况，可通过选择顶部面板中的“测试”访问测试控制台。 有关如何使用测试控制台测试应用的说明，请参阅[训练和测试应用](luis-interactive-test.md)。
