---
title: 使用模型进行设计 - LUIS
description: 语言理解提供多种类型的模型。 某些模型可以通过多种方式使用。
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: bbb1f0d43b2a3fd2e8a2dff2201a09622ecaf977
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683933"
---
# <a name="design-with-intent-and-entity-models"></a>使用意向和实体模型进行设计

语言理解提供了两种类型的模型，用于定义应用架构。 应用架构确定从新用户查询文本预测获得的信息。

应用架构是使用[计算机教学](#authoring-uses-machine-teaching)创建的模型生成的：
* 用户[最谈话分类](#intents-classify-utterances)
* [实体](#entities-extract-data)从查询文本中提取数据

## <a name="authoring-uses-machine-teaching"></a>创作使用机器教学

使用 LUIS 的机器教学方法，可以轻松地向计算机讲授概念。 了解_机器学习_不需要使用 LUIS。 相反，你作为老师，通过提供概念的示例并说明如何使用其他相关概念来建模概念，将概念传达给 LUIS。 作为教师，你还可以通过识别和修复预测错误，以交互方式改进 LUIS 的模型。

<a name="v3-authoring-model-decomposition"></a>

## <a name="intents-classify-utterances"></a>意向将言语分类

意向将示例言语分类，以便向 LUIS 传授意向。 意向中的示例言语用作言语的积极示例。 相同的这些言语用作所有其他意向中的消极示例。

假设某个应用需要确定用户的预订意向，另一个应用需要客户的交货地址。 此应用具有两个意向：`OrderBook` 和 `ShippingLocation`。

以下言语是 `OrderBook` 意向的**积极示例**，以及 `ShippingLocation` 和 `None` 意向的**消极示例**：

`Buy the top-rated book on bot architecture.`

## <a name="entities-extract-data"></a>实体提取数据

实体表示要从言语中提取的数据单位。 机器学习实体是包含子实体的顶级实体，也是机器学习实体。

机器学习实体的一个示例是为飞机票据提供订单。 从概念上讲，机票预测是包含许多较小数据单位（例如日期、时间、座位数、座位类型（头等舱或经济舱）、出发地、目的地和餐饮选项）的单笔交易。

## <a name="intents-versus-entities"></a>意向与实体

意向是整个言语的所需结果，而实体是从言语中提取的数据片段。__ 通常，目的是与客户端应用程序应执行的操作相关联。 实体是执行此操作所需的信息。 从编程的角度讲，意向会触发方法调用，而实体将用作该方法调用的参数。

以下言语肯定包含意向，同时可能包含实体：____

`Buy an airline ticket from Seattle to Cairo`

以下言语包含单个意向：

* 购买机票

以下言语可能包含多个实体：__

* Seattle（出发地）和 Cairo（目的地）的地点
* 数量为一张机票

## <a name="entity-model-decomposition"></a>实体模型分解

LUIS 支持使用创作 Api 进行_模型分解_，并将概念分解为更小的部分。 这样，你便可以生成自己的模型，并有把握地构造和预测各个组成部分。

模型分解包括以下组成部分：

* [调色](#intents-classify-utterances)
    * [功能](#features)
* [机器学习实体](reference-entity-machine-learned-entity.md)
    * 子实体（也是机器学习实体）
        * [功能](#features)
            * [短语列表](luis-concept-feature.md)
            * [非计算机学习实体](luis-concept-feature.md)，如[正则表达式](reference-entity-regular-expression.md)、[列表](reference-entity-list.md)和预生成[实体](luis-reference-prebuilt-entities.md)

<a name="entities-extract-data"></a>
<a name="machine-learned-entities"></a>

## <a name="features"></a>功能

[功能](luis-concept-feature.md)是系统所遵循的数据的特征或属性。 机器学习功能为 LUIS 提供了有关在何处查找将区分概念的事项的重要提示。 它们是 LUIS 可以使用而不是硬规则的提示。 这些提示与标签结合使用来查找数据。

## <a name="patterns"></a>模式

当多个最谈话非常相似时，[模式](luis-concept-patterns.md)旨在提高准确性。 使用模式可在不提供更多话语的情况下获得更高的意向准确度。

## <a name="extending-the-app-at-runtime"></a>在运行时扩展应用程序

应用的架构（模型和功能）经过训练并发布到预测终结点。 您可以将[新信息](schema-change-prediction-runtime.md)以及用户的查询文本传递到预测终结点以增加预测。

## <a name="next-steps"></a>后续步骤

* 了解[意向](luis-concept-intent.md)和[实体](luis-concept-entity-types.md)。
* 了解有关[功能](luis-concept-feature.md)的详细信息