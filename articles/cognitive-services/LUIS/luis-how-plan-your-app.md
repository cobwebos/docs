---
title: Plan your app - LUIS
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
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Plan your LUIS app schema with subject domain and data extraction

A LUIS app schema contains [intents](luis-glossary.md#intent) and [entities](luis-glossary.md#entity) relevant to your subject [domain](luis-glossary.md#domain). The intents classify user [utterances](luis-glossary.md#utterance), and the entities extract data from the user utterances.

## <a name="identify-your-domain"></a>标识域

A LUIS app is centered around a subject domain. For example, you may have a travel app that handles booking of tickets, flights, hotels, and rental cars. 另一应用则用于提供与锻炼、跟踪健身活动和设定目标相关的内容。 Identifying the domain helps you find words or phrases that are relevant to your domain.

> [!TIP]
> LUIS 提供许多常见场景的[预生成域](luis-how-to-use-prebuilt-domains.md)。 检查是否可使用预生成域作为应用的起始点。

## <a name="identify-your-intents"></a>标识意向

Think about the [intents](luis-concept-intent.md) that are important to your application's task.

以旅行应用为例，该应用可预订航班并检查用户目的地的天气。 You can define the `BookFlight` and `GetWeather` intents for these actions.

In a more complex app with more functions, you have more intents, and you should define them carefully so the intents aren't too specific. For example, `BookFlight` and `BookHotel` may need to be separate intents, but `BookInternationalFlight` and `BookDomesticFlight` may be too similar.

> [!NOTE]
> 最佳做法是仅使用所需意向来执行应用功能。 如果定义的意向过多，LUIS 将难以对话语进行正确分类。 If you define too few, they may be so general that they overlap.

If you don't need to identify overall user intention, add all the example user utterances to the `None` intent. If your app grows into needing more intents, you can create them later.

## <a name="create-example-utterances-for-each-intent"></a>为每个意向创建示例陈述

To begin with, avoid creating too many utterances for each intent. Once you have determined the intents, create 15 to 30 example utterances per intent. Each utterance should be different from the previously provided utterances. A good variety in utterances include overall word count, word choice, verb tense, and punctuation.

For more information, see [understanding good utterances for LUIS apps](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>标识实体

在示例陈述中，标识要提取的实体。 To book a flight, you need information like the destination, date, airline, ticket category, and travel class. Create entities for these data types and then mark the [entities](luis-concept-entity-types.md) in the example utterances. Entities are important for accomplishing an intent.

When determining which entities to use in your app, keep in mind that there are different types of entities for capturing relationships between object types. [LUIS 中的实体](luis-concept-entity-types.md)提供有关不同类型的详细信息。

> [!TIP]
> LUIS offers [prebuilt entities](luis-prebuilt-entities.md) for common, conversational user scenarios. Consider using prebuilt entities as a starting point for your application development.

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Learning the LUIS development lifecylce](luis-concept-app-iteration.md)

