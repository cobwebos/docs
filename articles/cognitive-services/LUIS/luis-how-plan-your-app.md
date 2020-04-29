---
title: 规划应用 - LUIS
description: 概述相关应用意向和实体，然后在语言理解智能服务 (LUIS) 中创建应用程序计划。
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: dfed27a05973a2ea2e9a97eaa1c233b847b33d87
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81382311"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>使用主题域和数据提取规划 LUIS 应用架构

LUIS 应用架构包含与主题 [域](luis-glossary.md#intent)相关的[意向](luis-glossary.md#entity)和[实体](luis-glossary.md#domain)。 意向对用户[言语](luis-glossary.md#utterance)分类，实体从用户言语中提取数据。

## <a name="identify-your-domain"></a>标识域

LUIS 应用以主题域为中心。 例如，可能有一个用于预订门票、航班、酒店和租车的旅行应用。 另一应用则用于提供与锻炼、跟踪健身活动和设定目标相关的内容。 标识域可帮助你查找与你的域相关的单词或短语。

> [!TIP]
> LUIS 提供许多常见场景的[预生成域](luis-how-to-use-prebuilt-domains.md)。 检查是否可使用预生成域作为应用的起始点。

## <a name="identify-your-intents"></a>标识意向

考虑一下对应用程序的任务极为重要的[意向](luis-concept-intent.md)。

以旅行应用为例，该应用可预订航班并检查用户目的地的天气。 可为这些操作定义 `BookFlight` 和 `GetWeather` 意向。

对于具有更多功能的复杂应用，意向也就更多，应仔细进行定义，使意向不要太具体。 例如，`BookFlight` 和 `BookHotel` 可能是单独的意向，但 `BookInternationalFlight` 和 `BookDomesticFlight` 则过于相似。

> [!NOTE]
> 最佳做法是仅使用所需意向来执行应用功能。 如果定义的意向过多，LUIS 将难以对话语进行正确分类。 如果定义的太少，则言语可能太过笼统以致于重叠。

如果不需标识整个用户意向，请将所有示例性的用户言语添加到 `None` 意向。 如果应用需要更多意向，可以在以后创建它们。

## <a name="create-example-utterances-for-each-intent"></a>为每个意向创建示例陈述

首先，避免为每个意向创建太多言语。 确定了意向后，为每个意向创建 15 到 30 个示例言语。 每个言语应不同于前面提供的言语。 最谈话中的一个很好的优点包括字数统计、字词选择、动词时态和[标点](luis-reference-application-settings.md#punctuation-normalization)。

有关详细信息，请参阅[了解适用于 LUIS 应用的言语](luis-concept-utterance.md)。

## <a name="identify-your-entities"></a>标识实体

在示例陈述中，标识要提取的实体。 若要预订航班，则需要“目的地”、“日期”、“航空公司”、“机票类别”和“旅行舱位”等信息。 为这些数据类型创建实体，然后在示例言语中标记[实体](luis-concept-entity-types.md)。 实体对于实现意向很重要。

确定要在应用中使用哪些实体后，请记住，有不同类型的实体可用于捕获对象类型间的关系。 [LUIS 中的实体](luis-concept-entity-types.md)提供有关不同类型的详细信息。

> [!TIP]
> LUIS 为常见的对话用户方案提供[预先构建的实体](luis-prebuilt-entities.md)。 考虑从使用预生成的实体着手，方便应用程序开发。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 LUIS 开发生命周期](luis-concept-app-iteration.md)

