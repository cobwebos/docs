---
title: 实体类型 - LUIS
titleSuffix: Azure Cognitive Services
description: 实体从话语中提取数据。 实体类型为你提供可预测的数据提取。 有两种类型的实体：机器学习的和非机器学习的。 请务必知道你在话语中使用的是哪种类型的实体。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9dc26e50e1c0f43e816e422f0fee91a246ea04a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487601"
---
# <a name="entities-and-their-purpose-in-luis"></a>实体及其在 LUIS 中的用途

实体的主要目的是使客户端应用程序可预测的数据提取。 一个_可选_的辅助目的是通过描述符来提升对意向的预测。 

有两种类型的实体： 

* 计算机-从上下文
* 非计算机学习-精确文本匹配

始终从计算机学习的实体开始，因为它提供了最广泛的数据提取选择。

## <a name="entity-compared-to-intent"></a>实体与意向

实体表示要提取的查询文本中的数据概念。 

请考虑以下3最谈话：

|陈述|提取的数据|说明|
|--|--|--|
|`Help`|-|没有要提取的内容。|
|`Send Bob a present`|Bob，提供|Bob 对于完成任务一定很重要。 现可提供足够的信息，或者机器人可能需要使用跟进问题来阐明现有内容。|
|`Send Bob a box of chocolates.`|数据的两个重要部分（Bob 和甜蜜）对于完成用户的请求十分重要。|

话语可包括多个实体，也可不包含任何实体。 客户端应用程序_可能_需要实体来执行其任务。 

相比之下，查询文本的意图预测是_必需_的，它表示整个查询文本。 LUIS 需要一个意向中包含的示例最谈话。 如果对客户端应用程序而言，查询文本的主要目的并不重要，请将所有最谈话添加到 "无" 目的。 

如果你在应用程序生命周期中发现，你会想要最谈话，可以轻松地执行此操作。 这可以是在创作时组织最谈话，也可以在客户端应用程序中使用预测的意图。 

不需要在客户端应用程序中使用预测意向，但它作为预测终结点响应的一部分返回。

## <a name="entities-represent-data"></a>实体表示数据

实体是要从话语中提取的数据。 可以是姓名、日期、产品名称或任何词组。 

|陈述|实体|数据|
|--|--|--|
|购买 3 张到纽约的机票|预生成的数字<br>Location.Destination|3<br>纽约|
|购买 3 月 5 日从纽约到伦敦的机票|Location.Origin<br>Location.Destination<br>预生成的 datetimeV2|纽约<br>伦敦<br>2018 年 3 月 5 日|

## <a name="entities-are-optional-but-highly-recommended"></a>实体是可选的（但强烈建议使用）

意向是必需的，而实体是可选的。 无需为应用中的每个概念创建实体，只需为客户端应用程序操作时所需的概念创建实体。 

如果话语不含机器人继续操作所需的详细信息，则无需添加这些信息。 随着应用的成熟，可在稍后添加信息。 

如果不确定如何使用这些信息，可添加一些常见的预生成的实体，如 [datetimeV2](luis-reference-prebuilt-datetimev2.md)、[序号](luis-reference-prebuilt-ordinal.md)、[电子邮件](luis-reference-prebuilt-email.md)和[电话号码](luis-reference-prebuilt-phonenumber.md)。

## <a name="design-entities-for-decomposition"></a>为分解设计实体

使用机器学习的实体开始进行实体设计。 这样一来，就可以轻松地随着时间的推移设计实体的增长和变化。 添加具有**约束**和**描述符**的**子组件**（子实体）以完成实体设计。 

针对分解进行设计使 LUIS 可以向客户端应用程序返回更深层的实体解析。 这样，客户端应用程序便可以专注于业务规则并将数据解析 LUIS。

### <a name="machine-learned-entities-are-primary-data-collections"></a>计算机学习的实体是主数据集合

计算机学习的实体是顶级数据单元。 子组件是计算机学习实体的子实体。 

**约束**是精确的文本匹配实体，它们应用规则来标识和提取数据。 **描述符**是应用的功能，用于提高预测的单词或短语的相关性。

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
|[**计算机学习**](#composite-entity)|实体的父分组，与实体类型无关。 机器学习实体从话语的上下文学习。 这使得示例话语中的位置差异变得显著。 |
|[**列表**](#list-entity)|用**精确文本匹配**提取的项及其同义词的列表。|
|[**Pattern.any**](#patternany-entity)|难以确定末尾部分的实体。 |
|[**预生成**](#prebuilt-entity)|已经过培训，可以提取特定类型的数据，例如 URL 或电子邮件。 其中一些预生成实体是在开源[识别器 - 文本](https://github.com/Microsoft/Recognizers-Text)项目中定义的。 如果你的特定区域性或实体当前不受支持，请通过为项目做贡献来获得支持。|
|[**正则表达式**](#regular-expression-entity)|使用正则表达式**精确匹配文本**。|

### <a name="entity-role-defines-context"></a>实体角色定义上下文

实体的角色是基于查询文本内上下文的命名别名。 例如，预订航班具有两个地点（源和目标）的查询文本。

`Book a flight from Seattle to Cairo`

需要提取 `location` 实体的两个示例。 客户端应用程序需要知道每个的位置类型才能完成票证购买。 `location` 实体需要两个角色的 `origin` 和 `destination`，并且两者都需要在最谈话示例中进行标记。 

如果 LUIS 找到 `location` 但无法确定该角色，则仍将返回 location 实体。 客户端应用程序需要跟进问题，以确定用户所代表的位置类型。 

多个实体可以存在于一个言语中，无需使用角色即可提取这些实体。 如果句子的上下文指示实体值，则应使用角色。

如果言语 `I want to travel to Seattle, Cairo, and London.` 包含位置列表，该列表中的每个项没有其他含义。 

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>如果所需实体数超过最大实体数 

如果你需要超过此限制，请联系支持人员。 为此，请收集有关系统的详细信息，转到 [LUIS](luis-reference-regions.md#luis-website) 网站，然后选择“支持”。 如果 Azure 订阅包含支持服务，请与 [Azure 技术支持](https://azure.microsoft.com/support/options/)联系。 

## <a name="entity-prediction-status"></a>实体预测状态

LUIS 门户显示了当实体（在示例中为查询文本）具有不同于所选实体的实体预测时。 这种不同分数基于当前训练的模型。 

## <a name="next-steps"></a>后续步骤

了解关于优良[话语](luis-concept-utterance.md)的概念。 

请参阅[添加实体](luis-how-to-add-entities.md)，详细了解如何将实体添加到 LUIS 应用。
