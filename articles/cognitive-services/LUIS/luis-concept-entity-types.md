---
title: 实体类型-LUIS
titleSuffix: Azure Cognitive Services
description: 实体从查询文本中提取数据。 实体类型使你可以预测的数据提取。 有两种类型的实体：机器学习和非计算机学习。 了解要在最谈话中使用的实体类型很重要。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 6ee156efb5512c92d86ba05513b6a2b91df4eae8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221025"
---
# <a name="entities-and-their-purpose-in-luis"></a>实体及其在 LUIS 中的用途

实体的主要目的是使客户端应用程序可预测的数据提取。 _可选_的辅助目的是通过描述符提高意图或其他实体的预测。

有两种类型的实体：

* 计算机-从上下文
* 非计算机学习-用于精确文本匹配、模式匹配或通过预生成实体检测

计算机学习的实体提供最广泛的数据提取选择。 非计算机学习实体按文本匹配工作，可以单独使用，也可以作为对计算机获知实体的[约束](#design-entities-for-decomposition)。

## <a name="entities-represent-data"></a>实体表示数据

实体是要从查询文本中提取的数据，例如名称、日期、产品名称或任何重要的单词组。 话语可包括多个实体，也可不包含任何实体。 客户端应用程序_可能_需要数据来执行其任务。

对于模型中的每个意向，实体都需要在所有定型最谈话中一致地标记。

 您可以定义自己的实体，也可以使用预构建的实体来节省常见概念（如[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[序号](luis-reference-prebuilt-ordinal.md)、[电子邮件](luis-reference-prebuilt-email.md)和[电话号码](luis-reference-prebuilt-phonenumber.md)）的时间。

|话语|实体|data|
|--|--|--|
|购买 3 张到纽约的机票|预生成的数字<br>Location.Destination|3<br>纽约|
|购买 3 月 5 日从纽约到伦敦的机票|Location.Origin<br>Location.Destination<br>预生成的 datetimeV2|纽约<br>London<br>2018 年 3 月 5 日|

### <a name="entities-are-optional"></a>实体是可选的

意向是必需的，而实体是可选的。 无需为应用中的每个概念创建实体，只需为客户端应用程序操作时所需的概念创建实体。

如果最谈话没有客户端应用程序所需的数据，则无需添加实体。 随着应用程序的开发和识别数据的新需要，稍后可以在 LUIS 模型中添加相应的实体。

## <a name="entity-compared-to-intent"></a>实体与意向

实体表示要提取的查询文本中的数据概念。

查询文本可以选择包含实体。 相比之下，查询文本的意图预测是_必需_的，它表示整个查询文本。 LUIS 需要一个意向中包含的示例最谈话。

请考虑以下4最谈话：

|话语|预测的意向|提取的实体|说明|
|--|--|--|--|
|帮助|help|-|没有要提取的内容。|
|发送内容|sendSomething|-|没有要提取的内容。 尚未训练该模型来提取此上下文中 `something`，并且不存在任何接收方。|
|发送小明|sendSomething|`Bob`、`present`|该模型已通过[personName](luis-reference-prebuilt-person.md)预先生成的实体进行定型，该实体已提取 `Bob`的名称。 已使用计算机学习的实体提取 `present`。|
|向 Bob 发送甜蜜的框|sendSomething|`Bob`、`box of chocolates`|两个重要的数据段（`Bob` 和 `box of chocolates`）已由实体提取。|

## <a name="design-entities-for-decomposition"></a>为分解设计实体

这是一种良好的实体设计，使顶级实体成为计算机学习的实体。 这允许在一段时间内对你的实体设计进行更改**subcomponents** ，并可选择使用**约束**和**描述符**将顶级实体分解为客户端应用程序所需的部件。

针对分解进行设计使 LUIS 可以向客户端应用程序返回更深层的实体解析。 这样，客户端应用程序便可以专注于业务规则并将数据解析 LUIS。

### <a name="machine-learned-entities-are-primary-data-collections"></a>计算机学习的实体是主数据集合

[**计算机学习的实体**](tutorial-machine-learned-entity.md)是顶级数据单元。 子组件是计算机学习实体的子实体。

计算机获知的实体基于通过定型最谈话获知的上下文触发。 **约束**是适用于机器学习的实体的可选规则，这些规则基于未获知计算机的实体（如[列表](reference-entity-list.md)或[Regex](reference-entity-regular-expression.md)）的精确文本匹配定义进一步约束触发。 例如，`size` 机器学习的实体可以具有 `sizeList` 列表实体的约束，该限制仅在遇到 `sizeList` 实体内包含的值时才会触发 `size` 实体。

[**描述符**](luis-concept-feature.md)是应用的功能，用于提高预测的单词或短语的相关性。 它们被称为*描述符*，因为它们用于*描述*意向或实体。 描述符描述了数据的特征或特性，例如 LUIS 观察和学习的重要字词或短语。

当你在 LUIS 应用程序中创建短语列表功能时，默认情况下它将在全局范围内启用并在所有意向和实体之间平均应用。 但是，如果您将短语列表作为计算机学习的实体（或*模型*）的描述符（功能）应用，则其作用域将减少为仅应用于该模型，而不再与所有其他模型一起使用。 使用短语列表作为模型的描述符有助于分解应用到的模型的准确性。

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>实体类型

请根据数据的提取方式以及提取后的数据表示方式，来选择实体。

|实体类型|目的|
|--|--|
|[**计算机学习**](tutorial-machine-learned-entity.md)|计算机学习的实体从查询文本中的上下文学习。 实体的父分组，与实体类型无关。 这会使最谈话的位置变体变得很重要。 |
|[**列表**](reference-entity-list.md)|用**精确文本匹配**提取的项及其同义词的列表。|
|[**Pattern.any**](reference-entity-pattern-any.md)|难以确定末尾部分的实体。 |
|[**预生成**](luis-reference-prebuilt-entities.md)|已经过培训，可以提取特定类型的数据，例如 URL 或电子邮件。 其中一些预生成实体是在开源[识别器 - 文本](https://github.com/Microsoft/Recognizers-Text)项目中定义的。 如果你的特定区域性或实体当前不受支持，请通过为项目做贡献来获得支持。|
|[**正则表达式**](reference-entity-regular-expression.md)|使用正则表达式**精确匹配文本**。|

## <a name="extracting-contextually-related-data"></a>提取根据上下文相关数据

查询文本可能包含两个或更多实体，其中的数据含义基于查询文本内的上下文。 例如，预订航班具有两个地点（源和目标）的查询文本。

`Book a flight from Seattle to Cairo`

需要提取 `location` 实体的两个示例。 客户端应用程序需要知道每个的位置类型才能完成票证购买。

可以通过两种方法来提取与根据上下文相关的数据：

 * `location` 实体是计算机学习的实体，并使用两个子组件实体来捕获 `origin` 和 `destination` （首选）
 * `location` 实体使用 `origin` 和的两个**角色**`destination`

查询文本中可以有多个实体，如果使用它的上下文没有任何意义，则无需使用分解或角色即可提取。 例如，如果查询文本包含位置列表，`I want to travel to Seattle, Cairo, and London.`，则这是一个列表，其中的每个项没有其他含义。

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>使用机器学习的实体的子组件实体来定义上下文

您可以使用[**机器学习的实体**](tutorial-machine-learned-entity.md)提取描述预订航班操作的数据，然后将顶级实体分解为客户端应用程序所需的单独部件。

在此示例中，`Book a flight from Seattle to Cairo`可以 `travelAction` 顶级实体，并将其标记为 `flight from Seattle to Cairo`提取。 然后，将创建两个子组件实体（称为 `origin` 和 `destination`），并将约束应用于预生成的 `geographyV2` 实体。 在定型最谈话中，`origin` 和 `destination` 都进行了相应的标记。

### <a name="using-entity-role-to-define-context"></a>使用实体角色定义上下文

角色是基于查询文本中的上下文的实体的命名别名。 角色可与任何预生成的或自定义的实体类型配合使用，并可在示例言语和模式中使用。 在此示例中，`location` 实体需要2个角色的 `origin` 和 `destination`，并且两者都需要在最谈话示例中进行标记。

如果 LUIS 找到 `location` 但无法确定该角色，则仍将返回 location 实体。 客户端应用程序需要跟进问题，以确定用户所代表的位置类型。


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>如果所需实体数超过最大实体数

如果你需要超过此限制，请联系支持人员。 为此，请收集有关系统的详细信息，转到 [LUIS](luis-reference-regions.md#luis-website) 网站，然后选择“支持”。 如果所持 Azure 订阅包含支持服务，请与 [Azure 技术支持](https://azure.microsoft.com/support/options/)联系。

## <a name="entity-prediction-status"></a>实体预测状态

LUIS 门户显示了当实体（在示例中为查询文本）具有不同于所选实体的实体预测时。 这种不同分数基于当前训练的模型。

## <a name="next-steps"></a>后续步骤

了解关于优良[话语](luis-concept-utterance.md)的概念。

请参阅[添加实体](luis-how-to-add-entities.md)，详细了解如何将实体添加到 LUIS 应用。

请参阅[教程：从用户查询文本中提取结构化数据和语言理解（LUIS）中的机器学习的实体](tutorial-machine-learned-entity.md)，了解如何使用机器学习的实体从查询文本提取结构化数据。
 
