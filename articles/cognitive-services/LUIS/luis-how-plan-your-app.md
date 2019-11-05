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
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b5e5df111b81cb60b6d194be190421bdb5ce2683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467697"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>使用 subject 域和数据提取计划 LUIS 应用架构

LUIS 应用架构包含与你的使用者域相关的意向和实体。 用于对用户最谈话进行分类的方法和实体从用户最谈话中提取数据。 

## <a name="identify-your-domain"></a>标识域

LUIS 应用以特定域的主题为中心。  例如，可能有一个用于预订门票、航班、酒店和租车的旅行应用。 另一应用则用于提供与锻炼、跟踪健身活动和设定目标相关的内容。 标识域可帮助你查找对你的域很重要的单词或短语。

> [!TIP]
> LUIS 提供许多常见场景的[预生成域](luis-how-to-use-prebuilt-domains.md)。
> 检查是否可使用预生成域作为应用的起始点。

## <a name="identify-your-intents"></a>标识意向

考虑一下对应用程序任务极为重要的[意向](luis-concept-intent.md)。 

以旅行应用为例，该应用可预订航班并检查用户目的地的天气。 可以定义这些操作的 `BookFlight` 和 `GetWeather` 意向。 

在具有更多函数的更复杂的应用程序中，你有更多意图，你应该仔细定义这些方法，使意向不太具体。 例如，`BookFlight` 和 `BookHotel` 可能需要不同的方法，但 `BookInternationalFlight` 和 `BookDomesticFlight` 可能会过于类似。

> [!NOTE]
> 最佳做法是仅使用所需意向来执行应用功能。 如果定义的意向过多，LUIS 将难以对话语进行正确分类。 如果定义太少，它们可能是重叠的。

如果不需要确定总体用户意图，请将所有示例用户最谈话添加到无意向。 如果你的应用程序需要更多的方法，稍后可以创建它们。 

## <a name="create-example-utterances-for-each-intent"></a>为每个意向创建示例陈述

确定了意向后，为每个意向创建 15 到 30 个示例话语。 首先，不要少于此数目或者为每个意向创建太多话语。 每个陈述应不同于上一陈述。 良好的陈述样本包括总体字数统计、选词、动词时态和标点。 

有关详细信息，请查看[话语](luis-concept-utterance.md)。

## <a name="identify-your-entities"></a>标识实体

在示例陈述中，标识要提取的实体。 若要预订航班，则需要“目的地”、“日期”、“航空公司”、“机票类别”和“旅行舱位”等信息。 为这些数据类型创建实体，然后在示例话语中标记[实体](luis-concept-entity-types.md)，因为它们对于实现意向很重要。 

确定要在应用中使用哪些实体后，请记住，有不同类型的实体可用于捕获对象类型间的关系。 [LUIS 中的实体](luis-concept-entity-types.md)提供有关不同类型的详细信息。

## <a name="next-steps"></a>后续步骤

了解典型[开发周期](luis-concept-app-iteration.md)。  