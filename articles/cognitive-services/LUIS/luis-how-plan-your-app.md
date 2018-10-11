---
title: 计划语言理解 (LUIS) 应用程序
titleSuffix: Azure Cognitive Services
description: 概述相关应用意向和实体，然后在语言理解智能服务 (LUIS) 中创建应用程序计划。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: diberry
ms.openlocfilehash: e8e6c32eca932803acfe0e7854de885e7ab129fc
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2018
ms.locfileid: "48267658"
---
# <a name="plan-your-luis-app"></a>计划 LUIS 应用

计划应用非常重要。 标识你的域，包括与你的应用程序相关的可能意向和实体。  

## <a name="identify-your-domain"></a>标识域
LUIS 应用以特定域的主题为中心。  例如，可能有一个用于预订门票、航班、酒店和租车的旅行应用。 另一应用则用于提供与锻炼、跟踪健身活动和设定目标相关的内容。 标识域可帮助你查找对你的域很重要的单词或短语。

> [!TIP]
> LUIS 提供许多常见场景的[预生成域](luis-how-to-use-prebuilt-domains.md)。
> 检查是否可使用预生成域作为应用的起始点。

## <a name="identify-your-intents"></a>标识意向
考虑一下对应用程序任务极为重要的[意向](luis-concept-intent.md)。 以旅行应用为例，该应用可预订航班并检查用户目的地的天气。 可为这些操作定义“BookFlight”和“GetWeather”意向。 对于具有更多功能的复杂应用，意向也就更多，应仔细进行定义，不能过于细致。 例如，“BookFlight”和“BookHotel”可能是单独的意向，但“BookInternationalFlight”和“BookDomesticFlight”则过于相似。

> [!NOTE]
> 最佳做法是仅使用所需意向来执行应用功能。 如果定义的意向过多，LUIS 将难以对话语进行正确分类。 如果定义的太少，则话语可能太过笼统以致于重叠。

## <a name="create-example-utterances-for-each-intent"></a>为每个意向创建示例陈述
一旦确定了意向，为每个意向创建 10 或 15 个示例陈述。 首先，不要少于此数目或者为每个意向创建许多陈述。 每个陈述应不同于上一陈述。 良好的陈述样本包括总体字数统计、选词、动词时态和标点。 

## <a name="identify-your-entities"></a>标识实体
在示例陈述中，标识要提取的实体。 若要预订航班，则需要一些信息，如目的地、日期、航空公司、机票类别和旅行舱位。 为这些数据类型创建实体，然后在示例陈述中标记[实体](luis-concept-entity-types.md)，因为它们对于实现意向很重要。 

确定要在应用中使用哪些实体后，请记住，有不同类型的实体可用于捕获对象类型间的关系。 [LUIS 中的实体](luis-concept-entity-types.md)提供有关不同类型的详细信息。

### <a name="simple-entity"></a>简单实体
简单实体描述单一概念。

![简单实体](./media/luis-plan-your-app/simple-entity.png)

请参阅[数据提取](luis-concept-data-extraction.md#simple-entity-data)，详细了解如何从终结点 JSON 查询响应提取简单实体。 请尝试[快速入门](luis-quickstart-primary-and-secondary-data.md)，详细了解如何使用简单实体。

### <a name="hierarchical-entity"></a>分层实体
分层实体是一种特殊类型的简单实体；以父子关系的形式定义一个类别及其成员。 关系由陈述中的上下文确定。 分层实体的子实体同样是简单实体。

![分层实体](./media/luis-plan-your-app/hierarchical-entity.png)

请参阅[数据提取](luis-concept-data-extraction.md#hierarchical-entity-data)，详细了解如何从终结点 JSON 查询响应提取分层实体。 请尝试[快速入门](luis-quickstart-intent-and-hier-entity.md)，详细了解如何使用分层实体。

### <a name="composite-entity"></a>复合实体
复合实体由全部实体中的其他实体组成。 复合实体包含各种实体类型。

![复合实体](./media/luis-plan-your-app/composite-entity.png)

请参阅[数据提取](luis-concept-data-extraction.md#composite-entity-data)，详细了解如何从终结点 JSON 查询响应提取复合实体。 请尝试本[教程](luis-tutorial-composite-entity.md)，详细了解如何使用复合实体。

### <a name="prebuilt-entity"></a>预生成实体
LUIS 为常见数据类型（如数字、数据、电子邮件地址和 URL）提供[预生成的实体](luis-prebuilt-entities.md)。 可以在票证顺序中对票证编号使用编号预生成实体。

![编号预生成实体](./media/luis-plan-your-app/number-entity.png)

请参阅[数据提取](luis-concept-data-extraction.md#prebuilt-entity-data)，详细了解如何从终结点 JSON 查询响应提取预生成实体。 

### <a name="list-entity"></a>列表实体 
列表实体是显式指定的值列表。 每个值都包含一个或多个同义词。 在旅行应用中，可选择创建列表实体表示机场名称。

![列表实体](./media/luis-plan-your-app/list-entity.png)

请参阅[数据提取](luis-concept-data-extraction.md#list-entity-data)，详细了解如何从终结点 JSON 查询响应提取列表实体。 请尝试[快速入门](luis-quickstart-intent-and-list-entity.md)，详细了解如何使用列表实体。

### <a name="regular-expression-entity"></a>正则表达式实体
通过正则表达式实体，LUIS 可基于正则表达式从陈述中提取格式正确的数据。

![正则表达式实体](./media/luis-plan-your-app/regex-entity.png)

请参阅[数据提取](luis-concept-data-extraction.md#regular-expression-entity-data)，详细了解如何从终结点 JSON 查询响应提取正则表达式实体。 请尝试[快速入门](luis-quickstart-intents-regex-entity.md)，详细了解如何使用正则表达式实体。

## <a name="next-steps"></a>后续步骤
应用经过培训、发布并获得终结点陈述后，计划通过[主动学习](luis-how-to-review-endoint-utt.md)、[短语列表](luis-concept-feature.md)和[模式](luis-concept-patterns.md)来实现预测改进。 


* 有关如何创建 LUIS 应用的快速演练，请参阅[创建首个语言理解智能服务 (LUIS) 应用](luis-get-started-create-app.md)。
