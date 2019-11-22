---
title: 模型设计-LUIS
titleSuffix: Azure Cognitive Services
description: 语言理解提供多种类型的模型。 某些模型可以用多种方式使用。
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280867"
---
# <a name="design-with-intent-and-entity-models"></a>设计意向模型和实体模型 

语言理解提供多种类型的模型。 某些模型可以用多种方式使用。 

## <a name="v3-authoring-uses-machine-teaching"></a>V3 创作使用机器教学

LUIS 使用户可以轻松地向计算机讲授概念。 然后，该计算机可以生成可用于对智能应用程序进行智能应用程序的模型（功能，如分类器和提取器的概念）。 尽管 LUIS 由机器学习提供支持，但不需要了解机器学习。 相反，machine 老师会显示概念的正面和负面示例，并说明如何使用其他相关概念对概念进行建模，从而将概念传达给 LUIS。 教师还可以通过识别和修复预测错误，以交互方式改进 LUIS 的模型。 

## <a name="v3-authoring-model-decomposition"></a>V3 创作模型分解

LUIS 支持通过 V3 创作 Api 进行_模型分解_，并将模型分解为更小的部分。 这样一来，您就可以在构造和预测各个部分的方式上自信地构建模型。

模型分解包含以下部分：

* [调色](#intents-classify-utterances)
    * 功能提供的[描述符](#descriptors-are-features)
* [计算机学习的实体](#machine-learned-entities)
    * [子组件](#entity-subcomponents-help-extract-data)（也是机器学习的实体）
        * 功能提供的[描述符](#descriptors-are-features) 
        * 非计算机获知的实体（如正则表达式和列表）提供的[约束](#constraints-are-text-rules)

## <a name="v2-authoring-models"></a>V2 创作模型

LUIS 支持包含 V2 创作 Api 的复合实体。 这会提供类似的模型分解，但与 V3 模型分解不同。 建议的模型体系结构将移到 V3 创作 Api 中的模型分解。 

## <a name="intents-classify-utterances"></a>对最谈话进行分类

目的是对示例最谈话进行分类，以教授 LUIS 的意图。 目的中的示例最谈话用作查询文本的正示例。 在所有其他方面，相同的最谈话用作负的示例。

请考虑一个应用程序，该应用程序需要确定用户对书籍的意图以及需要客户发运地址的应用程序。 此应用有两种方法： `OrderBook` 和 `ShippingLocation`。

下面的查询文本是 `OrderBook` 意向的**正示例**和 `ShippingLocation` 和 `None` 意向的**消极示例**： 

`Buy the top-rated book on bot architecture.`

设计良好的方法（及其示例最谈话）的结果是一项高意向预测。 

## <a name="entities-extract-data"></a>实体提取数据

实体表示你要从查询文本中提取的数据单元。 

### <a name="machine-learned-entities"></a>计算机学习的实体

计算机学习的实体是包含子组件（也是机器学习的实体）的顶级实体。 

**使用机器学习的实体**：

* 当客户端应用程序需要子组件时
* 帮助机器学习算法分解实体

每个子组件都可以：

* 子
* 约束（正则表达式实体或列表实体）
* 描述符（功能，如短语列表） 

机器学习实体的一个示例是为飞机票据提供订单。 从概念上讲，这是包含很多小型数据单元的单个事务，例如日期、时间、座位数量、座位类型（如第一类或教练、来源位置、目的地位置和餐费选项）。


### <a name="entity-subcomponents-help-extract-data"></a>实体子组件有助于提取数据

子组件是计算机获知的父实体内的计算机学习的子实体。 

**使用子组件可以**：

* 分解计算机学习的实体（父实体）的各个部分。

下面表示了一个机器学习的实体，其中包含所有这些单独的数据段：

* TravelOrder （机器学习的实体）
    * DateTime （预生成的 datetimeV2）
    * 位置（计算机学习的实体）
        * 源（通过上下文找到的角色，如 `from`）
        * 目标（通过上下文找到的角色，如 `to`）
    * 席位（机器学习的实体）
        * 数量（预建编号）
        * 质量（带有短语列表描述符的计算机学习的实体）
    * 餐费（具有作为食品选项的列表实体约束的计算机学习的实体）

其中的某些数据（例如，源位置和目标位置）应从查询文本的上下文中获知，可能会用 `from` 和 `to`这样的措辞。 可以用精确的字符串匹配项（`Vegan`）或预生成的实体（geographyV2 的 `Seattle` 和 `Cairo`）来提取数据的其他部分。 

您可以通过所选的模型以及配置数据的方式来设计如何匹配和提取数据。

### <a name="constraints-are-text-rules"></a>约束是文本规则

约束是文本匹配规则，由非计算机学习的实体（如正则表达式实体或列表实体）提供。 约束在预测时间应用，以限制预测并提供客户端应用程序所需的实体解析。 在创作子组件时定义这些规则。 

**使用约束**：
* 知道要提取的确切文本。

约束包括：

* [正则表达式](reference-entity-regular-expression.md)实体
* [列出](reference-entity-list.md)实体 
* [预](luis-reference-prebuilt-entities.md)生成实体

继续使用飞机票据的示例，机场代码可以位于列表实体中，以精确地匹配文本。 

对于机场列表，西雅图的列表项是城市名称，`Seattle`，西雅图的同义词包括西雅图的机场代码以及周围的城镇和城市：

|`Seattle` 列出实体同义词|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

如果只希望识别机场代码的3个字母代码，请使用正则表达式作为约束。 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>意向与实体

目的是_整个_查询文本的所需结果，而实体是从查询文本提取的数据片段。 通常，方法与客户端应用程序应采用的操作相关联，实体是执行此操作所需的信息。 从编程的角度来看，意图会触发方法调用，实体将用作该方法调用的参数。

此查询文本_必须_具有意向，并且_可能_具有实体：

`Buy an airline ticket from Seattle to Cairo`

此查询文本有一个意向：

* 购买飞机票据

此查询文本_可能_具有多个实体：

* 西雅图（源）和 Cairo （目标）的位置
* 单个票证的数量

## <a name="descriptors-are-features"></a>描述符是功能

描述符是在定型时间应用于模型的一种功能，其中包括短语列表和实体。 

若要执行以下操作，**请使用描述符**：

* 提高由描述符标识的单词和短语的重要性
* 让 LUIS 建议新的文本或短语，以建议用于说明符
* 修复定型数据上的错误

## <a name="next-steps"></a>后续步骤

* 了解[意向](luis-concept-intent.md)和[实体](luis-concept-entity-types.md)。 