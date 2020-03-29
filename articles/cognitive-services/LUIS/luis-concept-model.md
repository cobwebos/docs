---
title: 使用模型进行设计 - LUIS
titleSuffix: Azure Cognitive Services
description: 语言理解提供多种类型的模型。 某些模型可以通过多种方式使用。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d721ceb25b3ce2408563a0bed16457d05affe7b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219991"
---
# <a name="design-with-intent-and-entity-models"></a>使用意向和实体模型进行设计 

语言理解提供多种类型的模型。 某些模型可以通过多种方式使用。 

## <a name="v3-authoring-uses-machine-teaching"></a>V3 Authoring 使用机器教学

LUIS 可让用户轻松地向机器传授概念。 然后，机器可以生成模型（分类器和提取器等概念的功能近似性），用于为智能应用程序赋能。 尽管 LUIS 由机器学习赋能，但使用 LUIS 却不必要熟悉机器学习。 机器教学器通过显示概念的积极和消极示例，并解释如何使用其他相关概念来为某个概念建模，将概念传达给 LUIS。 教学器还能以交互方式改善 LUIS 的模型，因为它可以识别和修复预测错误。 

## <a name="v3-authoring-model-decomposition"></a>V3 Authoring 模型分解

LUIS 支持使用 V3 创作 API 的模型分解，以便将模型分解成较小组成部分。__ 这样，你便可以生成自己的模型，并有把握地构造和预测各个组成部分。

模型分解包括以下组成部分：

* [意图](#intents-classify-utterances)
    * 特征提供的[描述符](#descriptors-are-features)
* [机器学习实体](#machine-learned-entities)
    * [子组件](#entity-subcomponents-help-extract-data)（也是机器学习实体）
        * 特征提供的[描述符](#descriptors-are-features) 
        * 正则表达式和列表等非机器学习实体提供的[约束](#constraints-are-text-rules)

## <a name="v2-authoring-models"></a>V2 Authoring 模型

LUIS 支持使用 V2 创作 API 的复合实体。 此功能提供类似的模型分解，但不同于 V3 模型分解。 建议的模型体系结构是迁移到 V3 创作 API 中的模型分解。 

## <a name="intents-classify-utterances"></a>意向将言语分类

意向将示例言语分类，以便向 LUIS 传授意向。 意向中的示例言语用作言语的积极示例。 相同的这些言语用作所有其他意向中的消极示例。

假设某个应用需要确定用户的预订意向，另一个应用需要客户的交货地址。 此应用具有两个意向：`OrderBook` 和 `ShippingLocation`。

以下言语是 `OrderBook` 意向的**积极示例**，以及 `ShippingLocation` 和 `None` 意向的**消极示例**： 

`Buy the top-rated book on bot architecture.`

合理设计意向及其示例言语可以提高意向预测准确度。 

## <a name="entities-extract-data"></a>实体提取数据

实体表示要从言语中提取的数据单位。 

### <a name="machine-learned-entities"></a>机器学习实体

机器学习实体是包含子组件（也是机器学习实体）的顶级实体。 

**使用机器学习实体**：

* 当客户端应用程序需要子组件时
* 帮助机器学习算法分解实体

每个子组件可以包含：

* 子组件
* 约束（正则表达式实体或列表实体）
* 描述符（短语列表等特征） 

机票预订就是机器学习实体的一个例子。 从概念上讲，机票预测是包含许多较小数据单位（例如日期、时间、座位数、座位类型（头等舱或经济舱）、出发地、目的地和餐饮选项）的单笔交易。


### <a name="entity-subcomponents-help-extract-data"></a>实体子组件帮助提取数据

子组件是机器学习父实体中的机器学习子实体。 

**使用子组件可以**：

* 分解机器学习实体（父实体）的各个组成部分。

下面是包含所有这些独立数据片段的机器学习实体：

* TravelOrder（机器学习实体）
    * DateTime（预生成的 datetimeV2）
    * Location（机器学习实体）
        * Origin（通过上下文找到的角色，例如 `from`）
        * Destination（通过上下文找到的角色，例如 `to`）
    * Seating（机器学习实体）
        * Quantity（预生成的数字）
        * Quality（机器学习实体，包含短语列表的描述符）
    * Meals（机器学习实体，包含食品选项列表实体的约束）

其中的某些数据（例如，出发地和目的地）应从言语的上下文（也许是类似于 `from` 和 `to` 的单词）中习得。 其他数据部分可以通过精确字符串匹配 (`Vegan`) 或预生成的实体（`Seattle` 和 `Cairo` 的 geographyV2）来提取。 

应该设计好由哪些所选模型匹配和提取数据，以及如何配置这些模型。

### <a name="constraints-are-text-rules"></a>约束是文本规则

约束是正则表达式实体或列表实体等非机器学习实体提供的文本匹配规则。 约束在预测时应用，以限制预测并提供客户端应用程序所需的实体解析。 请在创作子组件时定义这些规则。 

**使用约束**：
* 知道要提取的确切文本时。

约束包括：

* [正则表达式](reference-entity-regular-expression.md)实体
* [列表](reference-entity-list.md)实体 
* [预构建](luis-reference-prebuilt-entities.md)实体

沿用机票预订的示例，机场代码可能在列表实体中，需要对其进行精确文本匹配。 

对于机场列表，Seattle 的列表项是城市名称 `Seattle`，Seattle 的同义词包括 Seattle 的机场代码以及周边乡镇和城市：

|`Seattle` 列表实体同义词|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

如果只想识别机场代码的 3 字母代码，请使用正则表达式作为约束。 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>意向与实体

意向是整个言语的所需结果，而实体是从言语中提取的数据片段。__ 通常，意向与客户端应用程序应执行的操作相关联，而实体是执行此操作所需的信息。 从编程的角度讲，意向会触发方法调用，而实体将用作该方法调用的参数。

以下言语肯定包含意向，同时可能包含实体：____

`Buy an airline ticket from Seattle to Cairo`

以下言语包含单个意向：

* 购买机票

以下言语可能包含多个实体：__

* Seattle（出发地）和 Cairo（目的地）的地点
* 数量为一张机票

## <a name="descriptors-are-features"></a>描述符是特征

描述符是在训练时应用到模型的特征，其中包含短语列表和实体。 

**有以下需要时，请使用描述符**：

* 提升描述符标识的单词和短语的重要性
* 让 LUIS 建议新的文本或短语，以便在描述符方面提供建议
* 修复训练数据的错误

## <a name="next-steps"></a>后续步骤

* 了解[意向](luis-concept-intent.md)和[实体](luis-concept-entity-types.md)。 