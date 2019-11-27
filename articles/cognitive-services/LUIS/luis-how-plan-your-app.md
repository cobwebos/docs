---
title: 规划应用-LUIS
titleSuffix: Azure Cognitive Services
description: 概述相关应用意向和实体，然后在语言理解智能服务 (LUIS) 中创建应用程序计划。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 6a155f4c43da03ccdc40d289742918973aa6da7b
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326775"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>使用 subject 域和数据提取计划 LUIS 应用架构

LUIS 应用架构包含与你的使用者[域](luis-glossary.md#domain)相关的[意向](luis-glossary.md#intent)和[实体](luis-glossary.md#entity)。 用于对用户[最谈话](luis-glossary.md#utterance)进行分类的方法和实体从用户最谈话中提取数据。

## <a name="identify-your-domain"></a>标识域

LUIS 应用中心围绕一个使用者域。 例如，你可能有一个旅行应用，用于处理入场券、航班、宾馆和租赁汽车的预订。 另一应用则用于提供与锻炼、跟踪健身活动和设定目标相关的内容。 标识域可帮助你查找与域相关的字词或短语。

> [!TIP]
> LUIS 提供许多常见场景的[预生成域](luis-how-to-use-prebuilt-domains.md)。 检查是否可使用预生成域作为应用的起始点。

## <a name="identify-your-intents"></a>标识意向

考虑对应用程序任务[重要的方法](luis-concept-intent.md)。

以旅行应用为例，该应用可预订航班并检查用户目的地的天气。 可以定义这些操作的 `BookFlight` 和 `GetWeather` 意向。

在具有更多函数的更复杂的应用程序中，你有更多意图，你应该仔细定义这些方法，使意向不太具体。 例如，`BookFlight` 和 `BookHotel` 可能需要不同的方法，但 `BookInternationalFlight` 和 `BookDomesticFlight` 可能会过于类似。

> [!NOTE]
> 最佳做法是仅使用所需意向来执行应用功能。 如果定义的意向过多，LUIS 将难以对话语进行正确分类。 如果定义太少，它们可能是重叠的。

如果不需要确定总体用户意图，请将所有示例用户最谈话添加到 `None` 意向。 如果你的应用程序需要更多的方法，稍后可以创建它们。

## <a name="create-example-utterances-for-each-intent"></a>为每个意向创建示例陈述

首先，应避免为每个意向创建太多的最谈话。 确定意图后，请创建每个意向15到30个最谈话示例。 每个查询文本应与前面提供的最谈话不同。 最谈话中的一个很好的优点包括字数统计、字词选择、动词时态和标点。

有关详细信息，请参阅[了解适用于 LUIS 应用的最谈话](luis-concept-utterance.md)。

## <a name="identify-your-entities"></a>标识实体

在示例陈述中，标识要提取的实体。 若要预订航班，则需要“目的地”、“日期”、“航空公司”、“机票类别”和“旅行舱位”等信息。 为这些数据类型创建实体，然后将示例中的[实体](luis-concept-entity-types.md)标记为最谈话。 实体对于实现意向非常重要。

在确定要在应用程序中使用的实体时，请记住，存在不同类型的实体来捕获对象类型之间的关系。 [LUIS 中的实体](luis-concept-entity-types.md)提供有关不同类型的详细信息。

> [!TIP]
> LUIS 为常见的对话用户方案提供[预先构建的实体](luis-prebuilt-entities.md)。 请考虑使用预构建的实体作为应用程序开发的起点。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [学习 LUIS 开发 lifecylce](luis-concept-app-iteration.md)

