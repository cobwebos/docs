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
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: ed0b9450217b06ff145641d9e268ccee28ee49b2
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710594"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>如何通过查看端点最谈话改善 LUIS 应用

查看终结点最谈话进行正确预测的过程称为[活动学习](luis-concept-review-endpoint-utterances.md)。 主动学习捕获终结点查询，并选择不确定的用户终结点最谈话。 您将查看这些最谈话以选择目的并标记这些最谈话的实体。 将这些更改接受到示例最谈话，然后训练和发布。 然后，LUIS 更准确地识别最谈话。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>启用主动学习

若要启用主动学习，必须记录用户查询。 这是通过使用 `log=true` querystring 参数和值调用[终结点查询](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint)来实现的。

## <a name="correct-intent-predictions-to-align-utterances"></a>更正意向预测以使最谈话

각 발화에는 **맞춤 의도** 열에 표시되는 제안된 의도가 포함됩니다.

> [!div class="mx-imgBorder"]
> [![查看 LUIS 不能确定的终结点最谈话](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

如果同意该意向，请选择复选标记。 제안에 동의하지 않는 경우, 맞춤 의도 드롭다운 목록에서 올바른 의도를 선택한 다음, 맞춤 의도 오른쪽에 있는 확인 표시를 선택합니다. 选中复选标记后，查询文本将移到该意向，并从**查看终结点最谈话**列表中删除。

> [!TIP]
> 若要查看并更正 "**查看终结点最谈话**" 列表中的所有示例最谈话中的实体预测，这一点很重要。

## <a name="delete-utterance"></a>발화 삭제

검토 목록에서 각 발화를 삭제할 수 있습니다. 삭제되면 목록에 다시 나타나지 않습니다. 이는 사용자가 엔드포인트에서 동일한 발화를 입력하는 경우에도 적용됩니다.

如果不确定是否应删除查询文本，请将其移到 "无" 目的，或创建新意向（如 `miscellaneous`），然后将查询文本移到该意向。

## <a name="disable-active-learning"></a>禁用活动学习

若要禁用活动学习，请不要记录用户查询。 这是通过使用 `log=false` querystring 参数和值设置[终结点查询](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint)来实现的，或者不使用 querystring 值，因为默认值为 false。

## <a name="next-steps"></a>다음 단계

제안된 발화에 레이블을 지정한 후 성능이 얼마나 향상되는지 테스트하려면 위쪽 패널에서 **테스트**를 선택하여 테스트 콘솔에 액세스할 수 있습니다. 테스트 콘솔을 사용하여 앱을 테스트하는 방법에 대한 자세한 내용은 [앱 학습 및 테스트](luis-interactive-test.md)를 참조하세요.
