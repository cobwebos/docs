---
title: 实体类型
titleSuffix: Language Understanding - Azure Cognitive Services
description: 实体从话语中提取数据。 实体类型为你提供可预测的数据提取。 有两种类型的实体：机器学习的和非机器学习的。 请务必知道你在话语中使用的是哪种类型的实体。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: a5a3ba8c25107317e7c47ee358f9a6ebe7d4556f
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479117"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>实体类型及其在 LUIS 中的目的

实体从话语中提取数据。 实体类型为你提供可预测的数据提取。 有两种类型的实体：机器学习的和非机器学习的。 请务必知道你在话语中使用的是哪种类型的实体。 

## <a name="entity-compared-to-intent"></a>实体与意向

实体表示要提取的话语中的字词或短语。 话语可包括多个实体，也可不包含任何实体。 客户端应用程序可能需要此实体来执行其任务，或者使用它作为指南来确定提供给用户的多个选项。 

实体：

* 表示一种类，包含相似对象（位置、事项、人员、事件或概念）的集合。 
* 介绍与意向相关的信息


例如，新闻搜索应用可能包括“主题”、“源”、“关键字”和“发布日期”等实体，这些是用于搜索新闻的关键数据。 在旅行预订应用中，“位置”、“日期”、“航空公司”、“舱位等级”和“机票”均为航班预订（与“Bookflight”意向相关）的关键信息。

相比之下，意向表示有关整个话语的预测。 

## <a name="entities-help-with-data-extraction-only"></a>实体仅有助于提取数据

只能出于提取实体的目的标记实体，实体对于意向预测没有任何帮助。

## <a name="entities-represent-data"></a>实体表示数据

实体是要从话语中提取的数据。 可以是姓名、日期、产品名称或任何词组。 

|话语|实体|Data|
|--|--|--|
|购买 3 张到纽约的机票|预生成的数字<br>Location.Destination|3<br>New York|
|购买 3 月 5 日从纽约到伦敦的机票|Location.Origin<br>Location.Destination<br>预生成的 datetimeV2|New York<br>伦敦<br>2018 年 3 月 5 日|

## <a name="entities-are-optional-but-highly-recommended"></a>实体是可选的（但强烈建议使用）

意向是必需的，而实体是可选的。 无需为应用中的每个概念创建实体，只需为客户端应用程序操作时所需的概念创建实体。 

如果话语不含机器人继续操作所需的详细信息，则无需添加这些信息。 随着应用的成熟，可在稍后添加信息。 

如果不确定如何使用这些信息，可添加一些常见的预生成的实体，如 [datetimeV2](luis-reference-prebuilt-datetimev2.md)、[序号](luis-reference-prebuilt-ordinal.md)、[电子邮件](luis-reference-prebuilt-email.md)和[电话号码](luis-reference-prebuilt-phonenumber.md)。

## <a name="label-for-word-meaning"></a>字词含义的标签

如果选词或字词排列方式相同，但含义并不相同，请勿将其标记为实体。 

以下话语中，`fair` 一词为同形异义词。 该词虽拼写相同但含义不同：

|话语|
|--|
|今年夏天西雅图地区会举办什么样的乡村集市？|
|西雅图评审的当前评级公平吗？|

如果希望事件实体查找所有事件数据，请标记第一个话语中的 `fair` 一词，而不是第二个话语。

## <a name="entities-are-shared-across-intents"></a>实体在意向之间共享

实体在意向之间共享。 它们不属于任一意向。 意向和实体可在语义上相关联，但这并非一种排它性的关系。

在“为我预订到巴黎的机票”这一话语中，“巴黎”是引用位置的实体。 通过识别用户话语中提到的实体，LUIS 可帮助客户端应用程序选择特定的操作来实现用户的意向。

## <a name="mark-entities-in-none-intent"></a>在 None 意向中标记实体

在可能的情况下，所有意向（包括 **None** 意向）应包含标记的实体。 这可帮助 LUIS 深入了解实体在话语中的位置，以及实体周围的字词。 

## <a name="entity-status-for-predictions"></a>预测的实体状态

当示例话语中的实体不同于标记的实体，或者与另一实体过于接近，因此不够明确时，LUIS 门户会告知这一点。 它会在示例话语中插入一条红色的下划线。 

有关详细信息，请参阅[实体状态预测](luis-how-to-add-example-utterances.md#entity-status-predictions)。 

## <a name="types-of-entities"></a>实体类型

LUIS 提供许多类型的实体。 请根据数据的提取方式以及提取后的数据表示方式，来选择实体。

可以使用机器学习提取实体，这样，LUIS 就可以持续学习该实体在话语中的显示方式。 可以通过对文本或正则表达式进行完全匹配，在不使用机器学习的情况下提取实体。 可以使用混合实现提取模式中的实体。 

提取实体后，可将实体数据表示为单个信息单元，或者将它与其他实体相结合，构成客户端应用程序可以使用的信息单元。

|机器学习|可以标记|教程|示例<br>响应|实体类型|用途|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**复合**](#composite-entity)|实体的分组，不考虑实体类型。|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**列表**](#list-entity)|使用文本完全匹配法提取的项列表及其同义词。|
|混合||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Pattern.any**](#patternany-entity)|难以确定末尾部分的实体。|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**预生成**](#prebuilt-entity)|已经过训练，可以提取各种类型的数据。|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**正则表达式**](#regular-expression-entity)|使用正则表达式来匹配文本。|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**简单**](#simple-entity)|包含单词或短语中的单个概念。|

只需在示例话语中标记机器学习实体。 机器学习到的实体最适合通过[终结点查询](luis-concept-test.md#endpoint-testing)和[查看终结点话语](luis-how-to-review-endoint-utt.md)进行测试。 

需要在[模式](luis-how-to-model-intent-pattern.md)模板示例而不是意向用户示例中标记 Pattern.any 实体。 

混合实体使用实体检测方法的组合。

## <a name="machine-learned-entities-use-context"></a>计算机学习的实体使用上下文

计算机学习的实体从查询文本中的上下文学习。 这会使最谈话的位置变体变得很重要。 

## <a name="non-machine-learned-entities-dont-use-context"></a>非计算机获知实体不使用上下文

当对实体进行匹配时, 以下非计算机学习实体不会将查询文本上下文纳入考虑范围: 

* [预生成实体](#prebuilt-entity)
* [Regex 实体](#regular-expression-entity)
* [列表实体](#list-entity) 

这些实体不需要标签或定型模型。 添加或配置实体后, 将提取实体。 其缺点在于, 这些实体可以是 overmatched 的, 如果已考虑到上下文, 则不会进行匹配。 

这种情况下, 经常在新模型上列出实体。 您可以使用 list 实体生成和测试模型, 但当您发布模型并从终结点接收查询时, 由于缺少上下文, 因此会 overmatching 模型。 

如果要匹配单词或短语并将上下文纳入考虑范围, 可以使用两个选项。 第一种方式是使用与短语列表配对的简单实体。 短语列表将不用于匹配, 但会帮助发出相对相似的字词 (可互换列表)。 如果必须完全匹配而不是短语列表的变体, 请使用具有角色的列表实体, 如下所述。

### <a name="context-with-non-machine-learned-entities"></a>具有非计算机学习实体的上下文

如果希望查询文本的上下文对非计算机学习的实体而言很重要, 则应使用[角色](luis-concept-roles.md)。

如果你有一个非计算机学习实体, 如[预生成实体](#prebuilt-entity)、 [regex](#regular-expression-entity)实体或[列表](#list-entity)实体 (在你想要的实例之外进行匹配), 请考虑创建一个具有两个角色的实体。 一个角色将捕获你要查找的内容, 一个角色将捕获你未查找的内容。 这两个版本都需要标记为示例最谈话。  

## <a name="composite-entity"></a>复合实体

[复合实体](reference-entity-composite.md)由其他实体 (例如预生成实体、简单、正则表达式和列表实体) 组成。 各种单独的实体构成整个实体。 

## <a name="list-entity"></a>列表实体

[列表实体](reference-entity-list.md)表示固定的、封闭的相关单词集及其同义词。 LUIS 不会为列表实体发现更多值。 使用“建议”功能根据当前列表查看有关新词的建议。 如果存在多个具有相同值的列表实体，则终结点查询中会返回其中每个实体。 

## <a name="patternany-entity"></a>Pattern.any 实体

[Pattern。 any](reference-entity-pattern-any.md)是仅在模式的模板查询文本中使用的可变长度占位符, 用于标记实体开始和结束的位置。  

## <a name="prebuilt-entity"></a>预生成实体

预生成实体是表示电子邮件、URL 和电话号码等常见概念的内置类型。 预生成的实体名称已保留。 添加到应用程序的[所有预生成实体](luis-prebuilt-entities.md)会返回到终结点查询中（如果在话语中发现了这些实体）。 

在以下情况下，非常适合使用该实体：

* 数据与语言区域性预生成实体支持的常见用例相匹配。 

随时可以添加和删除预生成实体。

![编号预生成实体](./media/luis-concept-entities/number-entity.png)

[教程](luis-tutorial-prebuilt-intents-entities.md)<br>
[实体的 JSON 响应示例](luis-concept-data-extraction.md#prebuilt-entity-data)

其中一些预生成实体是在开源[识别器 - 文本](https://github.com/Microsoft/Recognizers-Text)项目中定义的。 如果你的特定区域性或实体当前不受支持，请通过为项目做贡献来获得支持。 

### <a name="troubleshooting-prebuilt-entities"></a>排查预生成实体问题

在 LUIS 门户中，如果标记了预生成实体而非你的自定义实体，则可以使用多种方法来纠正此问题。

“始终”会返回添加到应用的预生成实体，即使话语应当提取同一文本的自定义实体。 

#### <a name="change-tagged-entity-in-example-utterance"></a>更改示例话语中标记的实体

如果预生成实体具有与自定义实体相同的文本或标记，请选择示例话语中的文本并更改标记的话语。 

如果为预生成实体标记了比自定义实体多的文本或标记，则可以使用多种方法来纠正此问题：

* [删除示例话语](#remove-example-utterance-to-fix-tagging)方法
* [删除预生成实体](#remove-prebuilt-entity-to-fix-tagging)方法

#### <a name="remove-example-utterance-to-fix-tagging"></a>删除示例话语来纠正标记 

你的第一选择是删除示例话语。 

1. 删除示例话语。
1. 重新训练应用。 
1. 仅将是实体的字词或短语（标记为预生成实体）作为完整的示例话语添加回来。 字词或短语仍然标记为预生成实体。 
1. 在“意向”页面上的示例话语中选择实体，更改为你的自定义实体并重新训练。 这应当会阻止 LUIS 将此确切文本在使用该文本的任何示例话语中标记为预生成实体。 
1. 将整个原始示例话语添加回意向。 应当会继续标记自定义实体而非预生成实体。 如果未标记自定义实体，则你需要在话语中添加该文本的更多示例。

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>删除预生成实体来纠正标记

1. 从应用中删除预生成实体。 
1. 在“意向”页面上，在示例话语中标记自定义实体。
1. 将应用定型。
1. 将预生成实体添加回应用并训练应用。 此纠正假定预生成实体不是复合实体的一部分。

## <a name="regular-expression-entity"></a>正则表达式实体 

[正则表达式实体](reference-entity-regular-expression.md)基于您提供的正则表达式模式提取实体。

## <a name="simple-entity"></a>简单实体

[简单实体](reference-entity-simple.md)是一种机器学习值。 它可以是一个单词或短语。
## <a name="entity-limits"></a>实体限制

查看[限制](luis-boundaries.md#model-boundaries)，了解可添加到模型中的每种类型的实体的数量。

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>如果所需实体数超过最大实体数 

可能需要将复合实体与实体角色结合使用。

复合实体表示整体的一部分。 例如，名为 PlaneTicketOrder 的复合实体可能具有子实体 Airline、Destination、DepartureCity、DepartureDate 和 PlaneTicketClass。

LUIS 还提供非机器学习的列表实体类型，可让 LUIS 应用指定固定的值列表。 请参阅 [LUIS 边界](luis-boundaries.md)参考内容，查看列表实体类型的限制。 

如果已考虑这些实体，但所需仍超过限制，请与支持部门联系。 为此，请收集有关系统的详细信息，转到 [LUIS](luis-reference-regions.md#luis-website) 网站，然后选择“支持”。 如果所持 Azure 订阅包含支持服务，请与 [Azure 技术支持](https://azure.microsoft.com/support/options/)联系。 

## <a name="next-steps"></a>后续步骤

了解关于优良[话语](luis-concept-utterance.md)的概念。 

请参阅[添加实体](luis-how-to-add-entities.md)，了解如何将实体添加到 LUIS 应用的详细信息。
