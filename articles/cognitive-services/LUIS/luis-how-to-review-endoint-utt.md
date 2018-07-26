---
title: 使用 LUIS 标记建议的陈述 | Microsoft Docs
description: 使用语言理解 (LUIS) 标记建议的陈述并帮助推动主动机器学习。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/08/2017
ms.author: diberry
ms.openlocfilehash: 5e195b8ef5aeb35b73c22438980fe2b2e3856977
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224545"
---
# <a name="review-endpoint-utterances"></a>评审终结点陈述

主动学习[概念](luis-concept-review-endpoint-utterances.md)是 LUIS 的突破性特征。 一旦 LUIS 有了终结点查询，LUIS 便可使用主动学习提升结果质量。 在主动学习过程中，LUIS 会检查所有终结点陈述，并选择不确定的陈述。 如果标记这些陈述并进行训练和发布，则 LUIS 将可以更准确地识别陈述。 

## <a name="filter-utterances"></a>筛选陈述
1. 通过在“我的应用”页上选择应用名称来打开应用（例如 TravelAgent），然后选择顶栏中的“生成”。

2. 在“提升应用性能”下，选择“评审终结点陈述”。

    ![评审陈述](./media/label-suggested-utterances/review.png)

3. 在“评审终结点陈述”页，在“按意向或实体筛选列表”文本框中进行选择。 此下拉列表的“意向”下包含所有意向，“实体”下包含所有实体。

    ![陈述筛选](./media/label-suggested-utterances/filter.png)

4. 在下拉列表中选择类别（意向或实体）并评审陈述。

    ![意向陈述](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>标记实体
LUIS 将实体标记（字词）替换为突出显示为蓝色的实体名称。 如果陈述具有未标记的实体，请在陈述中对其进行标记。 

1. 选择陈述中的字词。 

2. 从列表选择一个实体。

    ![标记实体](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>统一单个陈述

每个陈述的“统一意向”列中都显示一个建议意向。 

1. 如果同意该建议，则选择复选标记。

    ![保留统一意向](./media/label-suggested-utterances/align-intent-check.png)

2. 如果不同意建议，请从统一意向下拉列表中选择合适的意向，然后选择统一意向右侧的复选标记。 

    ![统一意向](./media/label-suggested-utterances/align-intent.png)

3. 选择复选标记后，该陈述将从列表中移除。 

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
