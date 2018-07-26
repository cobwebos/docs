---
title: 训练 LUIS 应用 - Azure | Microsoft Docs
description: 使用语言理解 (LUIS) 来训练模型。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: diberry
ms.openlocfilehash: e947df20141b0b9870f318f410488aea23bafcf5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223178"
---
# <a name="train-your-luis-app"></a>训练 LUIS 应用

训练是向语言理解 (LUIS) 应用进行教学以提高其自然语言理解能力的过程。 对模型进行更新（例如添加、编辑、标记或删除实体、意向或陈述）后，请对 LUIS 应用进行训练。 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

对应用进行训练和[测试](luis-concept-test.md)是一个迭代过程。 训练 LUIS 应用后，采用示例陈述来对应用进行测试，查看是否能准确地识别意向和实体。 如果未能准确识别，请对 LUIS 应用进行更新，然后重新进行训练和测试。 

## <a name="train-your-app"></a>训练用户
若要开始迭代过程，首先需要将 LUIS 应用训练至少一次。 在训练之前，请确保每个意向具有至少一个陈述。

1. 在“我的应用”页面上选择应用名称以访问应用。 

2. 在应用中，在顶部的面板中选择“训练”。 

    ![“训练”按钮](./media/luis-how-to-train/train-button.png)

3. 训练完成后，浏览器顶部会显示一个绿色的通知栏。

    ![“训练和测试应用”页面](./media/luis-how-to-train/train-success.png)

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>如果应用中有一个或多个意向未包含陈述示例，则无法训练应用。 请为所有意向添加陈述。 有关详细信息，请参阅[添加陈述示例](luis-how-to-add-example-utterances.md)。

## <a name="next-steps"></a>后续步骤

* [使用 LUIS 标记建议的陈述](luis-how-to-review-endoint-utt.md) 
* [使用相关功能来改进 LUIS 应用的性能](luis-how-to-add-features.md) 