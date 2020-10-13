---
title: 预生成模型 - LUIS
titleSuffix: Azure Cognitive Services
description: 预生成模型提供了域、意向、话语和实体。 可以使用预生成域启动你的应用，也可以在以后向你的应用添加相关域。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 32755ea9dccd2a7d4a0e727c7a006491d5ea3521
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535877"
---
# <a name="prebuilt-models"></a>预生成的模型

预生成模型提供了域、意向、话语和实体。 可以使用预生成模型启动应用，也可以稍后将相关模型添加到应用。 

## <a name="types-of-prebuilt-models"></a>预构建的模型的类型

LUIS 提供了三种类型的预生成模型。 可以随时向你的应用中添加每个模型。 

|模型类型|包括|
|--|--|
|[域](luis-reference-prebuilt-domains.md)|意向、话语、实体|
|意向|意向、话语|
|[实体](luis-reference-prebuilt-entities.md)|仅实体| 

## <a name="prebuilt-domains"></a>预生成域

语言理解 (LUIS) 提供预先训练的域，这些*域*是用于域或常见类别的客户端应用程序的预训练[模型和](luis-how-to-add-intents.md)[实体](luis-concept-entity-types.md)。 

预生成域已经过训练，就绪可添加到你的 LUIS 应用中。 将预生成域中的意向和实体添加到应用后，它们是完全可自定义的。 

> [!TIP]
> 组合使用预生成域中的意向和实体时效果最佳。 应尽量结合使用同一个域中的意向和实体。
> “实用工具”预生成域包含可以进行自定义以用于任何域的意向。 例如，可向应用添加 `Utilities.Repeat`，然后对其进行培训，使其识别用户可能想在你的应用程序中重复的任何操作。 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>更改预生成域意向的行为

你可能会发现，虽然预生成域中某个意向与你要在 LUIS 应用中使用的意向类似，但你希望它具有不同的行为。 例如，“地点”预生成域提供用于预订餐厅的 `MakeReservation` 意向，但你希望应用使用该意向预订酒店。 在这种情况下，可以通过在预订酒店的相关意向中添加示例话语来修改该意向的行为，然后重新训练应用。 

可在[预生成域参考](./luis-reference-prebuilt-domains.md)中找到预生成域的完整列表。

## <a name="prebuilt-intents"></a>预生成意向

LUIS 为其每个预生成域提供预生成意向及其话语。 可以仅添加意向，而不添加整个域。 添加意向是将意向及其话语添加到应用的过程。 意向名称和话语列表都可以修改。  

## <a name="prebuilt-entities"></a>预生成的实体

LUIS 包括一组预生成实体，用于识别常见类型的信息，如日期、时间、数字、度量和货币。 预构建实体支持因 LUIS 应用的区域性而异。 有关 LUIS 支持的预构建实体的完整列表，包括区域性支持，请参阅[预构建实体参考](./luis-reference-prebuilt-entities.md)。

当预构建实体包含在应用程序中时，其预测将包含在发布的应用程序中。 预构建实体的行为是预先训练的，**不能**修改的。 

> [!NOTE]
> **builtin.datetime** 已弃用。 它由 [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md) 取代，后者提供日期和时间范围的识别，并改进了对模糊日期和时间的识别。

## <a name="next-steps"></a>后续步骤

了解如何向你的应用[添加预生成的实体](luis-prebuilt-entities.md)。
