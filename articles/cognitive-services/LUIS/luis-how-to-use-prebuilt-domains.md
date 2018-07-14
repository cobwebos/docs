---
title: 在 Azure 中的 LUIS 应用中使用预生成域 | Microsoft Docs
description: 了解如何在语言理解智能服务 (LUIS) 应用程序中使用预生成域。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 72ca0def8528adae5e46a02fa5bfccd14a3b6ab4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "35366859"
---
# <a name="use-prebuilt-domains-in-luis-apps"></a>在 LUIS 应用中使用预生成域  

语言理解 (LUIS) 提供“预生成域”，预生成域是[意向](luis-how-to-add-intents.md)和[实体](luis-concept-entity-types.md)的预生成集，意向和实体可共同用于客户端应用程序的域或常见类别进行。 预生成域经过预培训，可直接添加到 LUIS 应用中。 将预生成域中的意向和实体添加到应用后即可对其进行完全自定义 - 可使用你的系统中的表述对其进行培训，以使其适用于你的用户。 可直接自定义整个预生成域，或仅从预生成域借用几个意向或实体。 

浏览“预生成域”选项卡，查看可在应用中使用的其他预生成域。 单击域的磁贴以将其添加到应用中，或单击其磁贴中的“了解更多”来查看意向和实体的相关信息。

> [!TIP]
> 可在[预生成域参考](./luis-reference-prebuilt-domains.md)中找到预生成域的完整列表。

![添加预生成域](./media/luis-how-to-prebuilt-domain-entities/add-prebuilt-domain.png)


## <a name="add-a-prebuilt-domain"></a>添加预生成域
在“预生成域”选项卡上，找到 RestaurantReservation 域，然后单击“添加域”。 将预生成域添加到 LUIS 应用后，打开“意向”，然后单击 RestaurantReservation.Reserve 意向。 可看到已提供大量示例表述并已用实体标记。

![RestaurantReservation.Reserve 意向](./media/luis-how-to-prebuilt-domain-entities/prebuilt-domain-restaurant-reservation.png)


## <a name="designing-luis-apps-from-prebuilt-domains"></a>通过预生成域设计 LUIS 应用
在 LUIS 应用中使用预生成域时，可自定义整个预生成域，或只是从其中几个意向和实体开始操作。

## <a name="customizing-an-entire-prebuilt-domain"></a>自定义整个预生成域
预生成域采用通用设计。 它们包含许多意向和实体，你可从中进行选择以根据需要自定义应用。 如果首先自定义整个预生成域，请删除应用不需要的意向和实体。 还可向预生成域已提供的集中添加一些意向或实体。 例如，如果对体育赛事应用使用“事件”预生成域，可对运动队添加实体。 开始向 LUIS [提供表述](luis-how-to-add-example-utterances.md)时，请包含特定于应用的术语。 LUIS 将学习识别它们，并根据应用需求对预生成域的意向和实体进行自定义。 

> [!TIP]
> 组合使用预生成域中的意向和实体时效果最佳。 应尽量结合使用同一个域中的意向和实体。
> * 最佳做法是使用同一个域中的相关意向。 例如，如果要自定义“地点”域中的 `MakeReservation` 意向，则应选择“地点”域中的 `Cancel` 意向，而非“事件”或“实用工具”域中的 Cancel 意向。

## <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>更改预生成域意向的行为
你可能会发现，虽然预生成域中某个意向与你要在 LUIS 应用中使用的意向类似，但你希望它具有不同的行为。 例如，“地点”预生成域提供用于预订餐厅的 `MakeReservation` 意向，但你希望应用使用该意向预订酒店。 在这种情况下，可以通过以下方法修改该意向的行为：向 LUIS 提供有关预订酒店的表述，并使用 `MakeReservation` 意向为其添加标记。这使得 LUIS 可接受重新培训，以识别预订酒店请求中的 `MakeReservation` 意向。

> [!TIP]
> 查看“实用工具”域中的预生成意向，这些意向经过自定义后可用于任何域中。 例如，可向应用添加 `Utilities.Repeat`，然后对其进行培训，使其识别用户可能想在你的应用程序中重复的任何操作。


## <a name="next-step"></a>后续步骤

通过向预生成域中添加更多示例表述来对其进行自定义。

> [!div class="nextstepaction"]
> [添加示例表述](./luis-how-to-add-example-utterances.md)

## <a name="additional-resources"></a>其他资源

有关预生成域的更多详细信息，请参阅[预生成域参考](./luis-reference-prebuilt-domains.md)。
