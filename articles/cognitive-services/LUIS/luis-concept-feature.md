---
title: 结合使用 LUIS 和机器学习特征
description: 将特征添加到语言模型，提供有关如何识别需要标记或分类的输入的提示。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 08ab71375171d4bb4167c725bc7118bec2e1ebfa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371981"
---
# <a name="machine-learning-features"></a>机器学习特征

在机器学习中，特征是系统观察到的以及习得的数据的判别特征或属性。 **  

机器学习特征为 LUIS 提供了重要提示，指示在何处查找可判别概念的事项。 它们是 LUIS 可以使用的提示，但并不是硬规则。 LUIS 将这些提示与标签结合使用来查找数据。

特征可以描述为一个函数，如 f(x) = y。 在示例言语中，特征会告诉你在何处寻找判别特征。 使用此信息来帮助创建架构。

## <a name="types-of-features"></a>特征类型

功能是架构设计的必需部分。 LUIS 支持同时使用短语列表和模型作为特征：

* 短语列表特征
* 以模型（意向或实体）作为特征

## <a name="find-features-in-your-example-utterances"></a>查找示例言语中的特征

由于 LUIS 是基于语言的应用程序，因此特征是基于文本的。 选择指示要判别的特征的文本。 对于 LUIS，最小单位是标记。 对于英语，标记是一组连续的字母和数字，不包含空格或标点。

由于空格和标点不是标记，因此请专注于可用作特征的文本线索。 请记住单词的变体也包括在内，例如：

* 复数形式
* 动词时态
* 缩写
* 拼写和拼写错误

确定文本是否因可判别特征而必须：

* 匹配精确的单词或短语：考虑将正则表达式实体或列表实体作为特征添加到实体或意向。
* 匹配熟知的概念，例如日期、时间或人名：使用预生成的实体作为实体或意向的特征。
* 随时间推移不断通过新示例学习：使用部分概念示例的短语列表作为实体或意向的特征。

## <a name="create-a-phrase-list-for-a-concept"></a>为概念创建短语列表

短语列表是描述概念的单词或短语列表。 短语列表作为不区分大小写的匹配项应用于标记级别。

添加短语列表时，可以将特征设置为[全局](#global-features)。 全局特征适用于整个应用。

### <a name="when-to-use-a-phrase-list"></a>何时使用短语列表

如果需要 LUIS 应用来归纳和识别概念的新项，请使用短语列表。 短语列表类似于域特定词汇。 它们提高了意向和实体的理解质量。

### <a name="how-to-use-a-phrase-list"></a>如何使用短语列表

使用短语列表时，LUIS 会考虑上下文并归纳内容，以识别类似的但不完全匹配文本的项。 请遵循以下步骤使用短语列表：

1. 开始使用机器学习实体：
    1. 添加示例言语。
    1. 使用机器学习实体进行标记。
1. 添加短语列表：
    1. 添加具有相似含义的单词。 不要添加每个可能的单词或短语。 应该每次添加几个单词或短语。 然后重新训练并发布。
    1. 查看并添加建议的单词。

### <a name="a-typical-scenario-for-a-phrase-list"></a>短语列表的典型方案

短语列表的典型方案是强化与特定想法相关的单词。

医学术语是很好的示例，这些词可能需要短语列表来提高其显著性。 这些术语可以具有特定的物理、化学、治疗或抽象含义。 如果没有短语列表，LUIS 将不知道这些术语对你的主题领域很重要。

若要提取医学术语：

1. 创建示例言语并在这些言语中标记医学术语。
2. 通过主题领域中的术语示例创建短语列表。 此短语列表应包含你标记的实际术语以及描述相同概念的其他术语。
3. 将短语列表添加到用于提取短语列表中使用的概念的实体或子实体中。 最常见的方案是机器学习实体的组件（子级）。 如果短语列表应该应用于所有意向或实体，请将短语列表标记为全局短语列表。 enabledForAllModels 标志控制 API 中的此模型范围。

### <a name="token-matches-for-a-phrase-list"></a>短语列表的标记匹配

短语列表始终应用于标记级别。 下表显示具有 Ann 一词的短语列表如何以同样的顺序应用于相同字符的变体。


| Ann 的标记变体 | 找到标记时的短语列表匹配 |
|--------------------------|---------------------------------------|
| **ANN**<br>**aNN**<br>           | 是 - 标记是 Ann                  |
| **Ann's**                    | 是 - 标记是 Ann                  |
| **Anne**                     | 否 - 标记是 Anne                  |

<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>作为特征的模型可帮助另一模型

你可将模型（意向或实体）作为特征添加到另一个模型（意向或实体）。 通过将现有意向或实体添加为特征，你将添加一个已有标记示例的定义明确的概念。

添加模型作为特征时，可以将特征设置为：
* **[必需](#required-features)** 。 若要从预测终结点返回模型，必须找到所需特征。
* **[全局](#global-features)** 。 全局特征适用于整个应用。

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>何时将实体用作意向特征

如果该实体的检测对于意向非常重要，请将实体作为特征添加到意向。

例如，如果意向是预订航班（如 BookFlight），实体是机票信息（例如座位数量、出发地和目的地），则查找机票信息实体应会将重要权重添加到 BookFlight 意向的预测 。

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>何时将实体用作另一个实体的特征

如果实体 (A) 的检测对于另一个实体 (B) 的预测非常重要，则应将实体 (A) 作为特征添加到实体 (B)。

例如，如果“邮寄地址”实体包含在“街道地址”子实体中，则查找“街道地址”子实体会将较大权重添加到“邮寄地址”实体的预测。

* 邮寄地址（机器学习实体）：

    * 街道编号（子实体）
    * 街道地址（子实体）
    * 城市（子实体）
    * 州或省/自治区/直辖市（子实体）
    * 国家/地区（子实体）
    * 邮政编码（子实体）

## <a name="nested-subentities-with-features"></a>带特征的嵌套子实体

机器学习子实体指示向父实体呈现了一个概念。 父实体可以是另一个子实体或顶层实体。 子实体的值充当其父实体的特征。

子实体可以同时包含短语列表和模型（另一实体）作为特征。

当子实体包含短语列表时，它会增加概念的词汇，但不会将任何信息添加到预测的 JSON 响应。

当子实体具有另一实体的特征时，JSON 响应将包含该另一实体的提取数据。


## <a name="required-features"></a>所需功能

若要将模型从预测终结点返回，必须找到所需特征。 如果你知道传入数据必须与特征匹配，请使用所需特征。

如果言语文本与所需特征不匹配，则不会提取该文本。

所需特征使用非机器学习实体：

* 正则表达式实体
* 列表实体
* 预生成实体

如果你确信将在数据中发现模型，请将特征设置为“所需”。 如果未发现模型，则所需特征不会返回任何内容。

仍以邮寄地址为例：

邮寄地址（机器学习实体）

 * 街道编号（子实体）
 * 街道地址（子实体）
 * 街道名称（子实体）
 * 城市（子实体）
 * 州或省/自治区/直辖市（子实体）
 * 国家/地区（子实体）
 * 邮政编码（子实体）

### <a name="required-feature-using-prebuilt-entities"></a>使用预生成实体的所需特征

城市、省/市/自治区和国家/地区通常是一组封闭列表，这意味着它们不会随着时间的推移而变化。 这些实体可能具有相关的建议特征，可以将这些特征标记为“所需”。 这意味着，如果找不到具有所需特征的实体，则不会返回整个邮寄地址。

如果言语中有城市、省/市/自治区或国家/地区信息，但它们的所在位置不在 LUIS 的预期范畴内，或是一些不在该范畴内的俚语，该怎么办？ 如果要提供一些后期处理来帮助解析该实体，由于该实体在 LUIS 中的置信度评分较低，请勿将该特征标记为“所需”。

邮寄地址的所需特征的另一示例是，将街道编号设置为所需的[预生成](luis-reference-prebuilt-entities.md)编号。 这允许用户输入“1 Microsoft Way”或“One Microsoft Way”。 对于街道编号子实体，两者都解析为数字“1”。

### <a name="required-feature-using-list-entities"></a>使用列表实体的所需特征

[列表实体](reference-entity-list.md)用作规范名称及其同义词的列表。 作为所需特征，如果言语既不包括规范名称也不包括同义词，则不会将该实体作为预测终结点的一部分返回。

假设你的公司仅向部分国家/地区发货。 你可以创建一个列表实体，其中包含多种供客户参考国家/地区的方式。 如果 LUIS 在言语的文本中找不到完全匹配项，则不会在预测中返回（具有列表实体的所需特征）实体。

|规范名称|同义词|
|--|--|
|美国|美国<br>U.S.A<br>US<br>美国<br>0|

客户端应用程序（如聊天机器人）可以询问后续问题来提供帮助。 这可帮助客户了解国家/地区选择是有限制的和必需的。

### <a name="required-feature-using-regular-expression-entities"></a>使用正则表达式实体的所需特征

充当所需特征的[正则表达式实体](reference-entity-regular-expression.md)可提供丰富的文本匹配功能。

在邮寄地址示例中，可以创建一个正则表达式来捕获国家/地区邮政编码的语法规则。

## <a name="global-features"></a>全局特征

尽管最常见的用法是将特征应用到特定模型，但也可以将特征配置为全局特征，将其应用到整个应用程序。

全局特征最常见的用途是向应用添加其他词汇。 例如，如果你的客户使用的是主要语言，但希望能够在同一言语中使用其他语言，则你可添加一项特征来包含辅助语言的单词。

由于用户希望跨任何意向或实体使用辅助语言，因此请将辅助语言中的单词添加到短语列表。 将短语列表配置为全局特征。

## <a name="combine-features-for-added-benefit"></a>合并功能以增加权益

可使用多个特征来描述特征或概念。 常见的配对使用：

* 短语列表功能：可以使用多个短语列表作为同一模型的功能。
* 作为功能的模型： [预生成实体](luis-reference-prebuilt-entities.md)、 [正则表达式实体](reference-entity-regular-expression.md)、 [列表实体](reference-entity-list.md)。 

### <a name="example-ticket-booking-entity-features-for-a-travel-app"></a>示例：旅游应用的票据预订实体功能  

作为基本示例，请考虑使用航班预订 _意向_ 和票据预订 _实体_来预订航班的应用。 票据预订实体将捕获信息以在预订系统中预订飞机票据。 

票据书籍的机器学习实体提供两个子实体来捕获源和目标。 需要将这些功能添加到每个子实体，而不是顶级实体。

:::image type="content" source="media/luis-concept-features/ticket-booking-entity.png" alt-text="Ticketbooking 实体架构":::

票据预订实体是一种机器学习实体，其中包括 _源_ 和 _目标_子实体。 这些子实体都表示地理位置。 为了帮助提取位置，并区分 _源_ 和 _目标_，每个子实体都应有功能。

|类型|源子实体 |目标子实体|
|--|--|--|
|作为特征的模型|[geographyV2](luis-reference-prebuilt-geographyv2.md?tabs=V3) 预生成实体|[geographyV2](luis-reference-prebuilt-geographyv2.md?tabs=V3) 预生成实体|
|短语列表|**原词**： `start at` 、 `begin from` 、 `leave`|**目标关键字**： `to` 、 `arrive` 、 `land at` 、 `go` 、 `going` 、 `stay` 、 `heading`|
|短语列表|机场代码-源和目标的相同列表|机场代码-源和目标的相同列表|
|短语列表|机场名称-与源和目标相同的列表|机场代码-源和目标的相同列表|

如果预计用户使用机场代码和机场名称，则 LUIS 应包含使用这两种类型短语的短语列表。 使用在聊天机器人中输入的文本，机场代码可能更常见，而机场名称可能更常见，如启用语音的聊天机器人。

仅为模型而不是短语列表返回这些功能的匹配详细信息，因为在预测 JSON 中仅返回模型。

#### <a name="ticket-booking-labeling-in-the-intent"></a>票证预订标签意向

创建机器学习实体后，需要将最谈话示例添加到意向，并为父实体和所有子实体添加标签。

对于票证预订示例，请将最谈话中的示例与 `TicketBooking` 实体和文本中的任何子实体一起标记。

:::image type="content" source="media/luis-concept-features/intent-example-utterances-machine-learning-entity.png" alt-text="Ticketbooking 实体架构":::

### <a name="example-pizza-ordering-app"></a>示例：比萨饼定购应用

对于第二个示例，请考虑一个适用于比萨饼餐馆的应用，该餐厅接收比萨饼订单，包括某人所订购的比萨饼类型的详细信息。 应尽可能提取每个比萨饼的详细信息，以便完成订单处理。

本示例中的机器学习实体更复杂，其中包含嵌套的子实体、短语列表、预生成的实体和自定义实体。

:::image type="content" source="media/luis-concept-features/pizza-order-entity.png" alt-text="Ticketbooking 实体架构":::

此示例在子实体级别使用功能，并使用子实体级别的子项。 功能的哪种类型的短语列表或模型是实体设计的重要组成部分。

尽管子实体可以有多个短语列表作为有助于检测实体的功能，但每个子实体都只有一个模型作为功能。 在此 [比萨饼应用](https://github.com/Azure/pizza_luis_bot/blob/master/CognitiveModels/MicrosoftPizza.json)中，这些模型主要是列表。

:::image type="content" source="media/luis-concept-features/intent-example-utterances-machine-learning-entity-pizza.png" alt-text="Ticketbooking 实体架构":::

标记正确的示例最谈话以显示实体嵌套方式的方式显示。 


## <a name="best-practices"></a>最佳实践

了解[最佳实践](luis-concept-best-practices.md)。

## <a name="next-steps"></a>后续步骤

* 在预测运行时[扩展](schema-change-prediction-runtime.md)应用模型。
* 请参阅[添加特征](luis-how-to-add-features.md)，详细了解如何将特征添加到 LUIS 应用。
