---
title: 预生成的模型
titleSuffix: Language Understanding - Azure Cognitive Services
description: 预生成模型提供了域、意向、话语和实体。 可以使用预生成域启动你的应用，也可以在以后向你的应用添加相关域。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: 5d2ea9d971eff22ddeed4122c9697ca3096697b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813885"
---
# <a name="prebuilt-domain-intent-and-entity-models"></a>预生成域、意向和实体模型

预生成模型提供了域、意向、话语和实体。 可以使用预生成域启动你的应用，也可以在以后向你的应用添加相关域。 

## <a name="types-of-prebuilt-models"></a>预构建的模型的类型

LUIS 提供了 3 种预构建的模型。 可以随时向你的应用中添加每个模型。 

|模型类型|包括|
|--|--|
|域|意向、话语、实体|
|意向|意向、话语|
|实体|仅实体| 

## <a name="prebuilt-domains"></a>预生成域

语言理解 (LUIS) 提供“预生成域”，预生成域是[意向](luis-how-to-add-intents.md)和[实体](luis-concept-entity-types.md)的预生成集，意向和实体可共同用于客户端应用程序的域或常见类别进行。 

预生成域已经过训练，就绪可添加到你的 LUIS 应用中。 将预生成域中的意向和实体添加到应用后，它们是完全可自定义的。 

如果首先自定义整个预生成域，请删除应用不需要的意向和实体。 还可向预生成域已提供的集中添加一些意向或实体。 例如，如果对体育赛事应用使用“事件”预生成域，可对运动队添加实体。 开始向 LUIS [提供表述](luis-how-to-add-example-utterances.md)时，请包含特定于应用的术语。 LUIS 将学习识别它们，并根据应用需求对预生成域的意向和实体进行自定义。 

> [!TIP]
> 组合使用预生成域中的意向和实体时效果最佳。 应尽量结合使用同一个域中的意向和实体。
> “实用工具”预生成域包含可以进行自定义以用于任何域的意向。 例如，可向应用添加 `Utilities.Repeat`，然后对其进行培训，使其识别用户可能想在你的应用程序中重复的任何操作。 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>更改预生成域意向的行为

你可能会发现，虽然预生成域中某个意向与你要在 LUIS 应用中使用的意向类似，但你希望它具有不同的行为。 例如，“地点”预生成域提供用于预订餐厅的 `MakeReservation` 意向，但你希望应用使用该意向预订酒店。 在这种情况下，可以通过以下方法修改该意向的行为：向 LUIS 提供有关预订酒店的表述，并使用 `MakeReservation` 意向为其添加标记。这使得 LUIS 可接受重新培训，以识别预订酒店请求中的 `MakeReservation` 意向。

可在[预生成域参考](./luis-reference-prebuilt-domains.md)中找到预生成域的完整列表。

## <a name="prebuilt-intents"></a>预生成意向

LUIS 提供了预生成意向及其话语。 可以仅添加意向，而不添加整个域。 添加意向是添加意向及其话语的过程。 意向名称和话语列表都可以修改。  

## <a name="prebuilt-entities"></a>预生成的实体

LUIS 包括一组预生成实体，用于识别常见类型的信息，如日期、时间、数字、度量和货币。 预构建实体支持因 LUIS 应用的区域性而异。 有关 LUIS 支持的预构建实体的完整列表，包括区域性支持，请参阅[预构建实体参考](./luis-reference-prebuilt-entities.md)。

当预构建实体包含在应用程序中时，其预测将包含在发布的应用程序中。 预构建实体的行为是预先训练的，**不能**修改的。 请按照以下步骤了解预构建实体的工作原理：

> [!NOTE]
> **builtin.datetime** 已弃用。 它由 [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md) 取代，后者提供日期和时间范围的识别，并改进了对模糊日期和时间的识别。

## <a name="next-steps"></a>后续步骤

了解如何向你的应用[添加预生成的实体](luis-prebuilt-entities.md)。
