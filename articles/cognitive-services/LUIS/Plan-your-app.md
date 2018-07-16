---
title: 计划 LUIS 应用程序 | Microsoft Docs
description: 概述相关应用意向和实体，然后在语言理解智能服务 (LUIS) 中创建应用程序计划。
services: cognitive-services
author: DeniseMak
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 7aec5d5b90ac7145ce9f337ec74c590b4b88c6b1
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266356"
---
# <a name="plan-your-luis-app"></a>计划 LUIS 应用

开始在 LUIS 中创建应用前，请务必计划应用。 准备与应用特定域主题相关的可能的意向和实体的大纲或架构。  

## <a name="identify-your-domain"></a>标识域
LUIS 应用以特定域的主题为中心。  例如，可能有一个用于预订门票、航班、酒店和租车的旅行应用。 另一应用则用于提供与锻炼、跟踪健身活动和设定目标相关的内容。 

> [!TIP]
> LUIS 提供许多常见场景的[预生成域](luis-how-to-use-prebuilt-domains.md)。
> 检查是否可使用预生成域作为应用的起始点。

## <a name="identify-your-intents"></a>标识意向
考虑一下对应用程序任务极为重要的[意向](luis-concept-intent.md)。 以旅行应用为例，该应用可预订航班并检查用户目的地的天气。 可为这些操作定义“BookFlight”和“GetWeather”意向。 对于具有更多功能的复杂应用，意向也就更多，应仔细进行定义，不能过于细致。 例如，“BookFlight”和“BookHotel”可能是单独的意向，但“BookInternationalFlight”和“BookDomesticFlight”则过于相似。

> [!NOTE]
> 最佳做法是仅使用所需意向来执行应用功能。 如果定义的意向过多，LUIS 将难以对话语进行正确分类。 如果定义的太少，则话语可能太过笼统以致于重叠。


## <a name="identify-your-entities"></a>标识实体
若要预订航班，则需要一些信息，如目的地、日期、航空公司、机票类别和旅行舱位。 可将这些信息添加为[实体](luis-concept-entity-types.md)，因为它们对于完成意向非常重要。 

确定要在应用中使用哪些实体后，请记住，有不同类型的实体可用于捕获对象类型间的关系。 [LUIS 中的实体](luis-concept-entity-types.md)提供有关不同类型的详细信息。

### <a name="simple-entity"></a>简单实体
简单实体描述单一概念。

![简单实体](./media/luis-plan-your-app/simple-entity.png)

请参阅[数据提取](luis-concept-data-extraction.md#simple-entity-data)，详细了解如何从终结点 JSON 查询响应提取简单实体。 请尝试简单实体[快速入门](luis-quickstart-primary-and-secondary-data.md)，详细了解如何使用简单实体。

### <a name="hierarchical-entity"></a>分层实体
分层实体是一种特殊类型的简单实体；以父子关系的形式定义一个类别及其成员。

![分层实体](./media/luis-plan-your-app/hierarchical-entity.png)

请参阅[数据提取](luis-concept-data-extraction.md#hierarchical-entity-data)，详细了解如何从终结点 JSON 查询响应提取分层实体。 请尝试分层实体[快速入门](luis-quickstart-intent-and-hier-entity.md)，详细了解如何使用分层实体。

### <a name="composite-entity"></a>复合实体
复合实体由全部实体中的其他实体组成。 

![复合实体](./media/luis-plan-your-app/composite-entity.png)

请参阅[数据提取](luis-concept-data-extraction.md#composite-entity-data)，详细了解如何从终结点 JSON 查询响应提取复合实体。 请尝试复合实体[教程](luis-tutorial-composite-entity.md)，详细了解如何使用复合实体。

### <a name="prebuilt-entity"></a>预生成实体
LUIS 为诸如 `Number` 这样的常见类型提供[预生成实体](Pre-builtEntities.md)，可用于票证顺序中的票证编号。

![编号预生成实体](./media/luis-plan-your-app/number-entity.png)

请参阅[数据提取](luis-concept-data-extraction.md#prebuilt-entity-data)，详细了解如何从终结点 JSON 查询响应提取正则表达式实体。 

### <a name="list-entity"></a>列表实体 
列表实体是显式指定的值列表。 每个值都包含一个或多个同义词。 在旅行应用中，可选择创建列表实体表示机场名称。

![列表实体](./media/luis-plan-your-app/list-entity.png)

请参阅[数据提取](luis-concept-data-extraction.md#list-entity-data)，详细了解如何从终结点 JSON 查询响应提取列表实体。 请尝试[快速入门](luis-quickstart-intent-and-list-entity.md)，详细了解如何使用列表实体。

### <a name="regular-expression-entity"></a>正则表达式实体
通过正则表达式实体，LUIS 可基于正则表达式从话语中提取数据。

![正则表达式实体](./media/luis-plan-your-app/regex-entity.png)

请参阅[数据提取](luis-concept-data-extraction.md#regular-expression-entity-data)，详细了解如何从终结点 JSON 查询响应提取正则表达式实体。 请尝试[快速入门](luis-quickstart-intents-regex-entity.md)，详细了解如何使用正则表达式实体。

## <a name="after-getting-endpoint-utterances"></a>获取终结点话语后
应用获得终结点话语后，计划通过[主动学习](label-suggested-utterances.md)、[短语列表](luis-concept-feature.md)和[模式](luis-concept-patterns.md)来实现预测改进。 

### <a name="patternany-entity"></a>Pattern.any 实体
Patterns.any 是一种长度可变的占位符，仅在[模式的](luis-concept-patterns.md)模板话语中使用，用于标记实体的起始和结束位置。 模板话语遵照[正确语法](luis-concept-patterns.md#pattern-syntax)来标识实体和可忽略的文本。


## <a name="next-steps"></a>后续步骤
* 有关如何创建 LUIS 应用的快速演练，请参阅[创建首个语言理解智能服务 (LUIS) 应用][luis-get-started-create-app]。

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app