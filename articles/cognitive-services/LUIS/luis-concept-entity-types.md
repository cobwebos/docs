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
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 6ee156efb5512c92d86ba05513b6a2b91df4eae8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "79221025"
---
# <a name="entities-and-their-purpose-in-luis"></a>实体及其在 LUIS 中的用途

实体的主要用途是使客户端应用程序能够以可预测的方式提取数据。 一个可选的辅助用途是通过描述符促进意向或其他实体的预测。 

有两种类型的实体：

* 机器学习 - 从上下文学习
* 非机器学习 - 供预生成实体用于精确文本匹配、模式匹配或检测

机器学习实体提供最广泛的数据提取选项。 非机器学习实体通过文本匹配来运行，可以单独使用，也可以用作机器学习实体中的[约束](#design-entities-for-decomposition)。

## <a name="entities-represent-data"></a>实体表示数据

实体是要从言语中提取的数据，例如姓名、日期、产品名称或任何有意义的单词组。 话语可包括多个实体，也可不包含任何实体。 客户端应用程序可能需要数据来执行其任务。 

对于模型中的每个意向，需要在所有训练言语中一致性地标记实体。

 你可以定义自己的实体，也可以使用预生成实体来节省处理 [datetimeV2](luis-reference-prebuilt-datetimev2.md)、[序号](luis-reference-prebuilt-ordinal.md)、[电子邮件](luis-reference-prebuilt-email.md)和[电话号码](luis-reference-prebuilt-phonenumber.md)等为常见概念的时间。

|话语|实体|data|
|--|--|--|
|购买 3 张到纽约的机票|预生成的数字<br>Location.Destination|3<br>纽约|
|购买 3 月 5 日从纽约到伦敦的机票|Location.Origin<br>Location.Destination<br>预生成的 datetimeV2|纽约<br>London<br>2018 年 3 月 5 日|

### <a name="entities-are-optional"></a>实体是可选的

意向是必需的，而实体是可选的。 无需为应用中的每个概念创建实体，只需为客户端应用程序操作时所需的概念创建实体。

如果言语不包含客户端应用程序所需的数据，则无需添加实体。 以后随着应用程序的开发以及确定新的数据需求，可以在 LUIS 模型中添加相应的实体。

## <a name="entity-compared-to-intent"></a>实体与意向

实体表示要提取的言语中的数据概念。

言语可以选择性地包含实体。 相比之下，言语意向的预测是必需的，表示整个言语。  LUIS 要求在意向中包含示例言语。

考虑以下4 段言语：

|话语|预测的意向|提取的实体|说明|
|--|--|--|--|
|帮助|help|-|没有要提取的内容。|
|Send something|sendSomething|-|没有要提取的内容。 模型尚未训练，不会在此上下文中提取 `something`，并且没有任何接收方。|
|Send Bob a present|sendSomething|`Bob`、`present`|已使用提取了名字 [ 的 ](luis-reference-prebuilt-person.md)personName`Bob` 预生成实体训练模型。 已使用机器学习实体提取 `present`。|
|Send Bob a box of chocolates|sendSomething|`Bob`、`box of chocolates`|实体已提取两个重要数据片段 `Bob` 和 `box of chocolates`。|

## <a name="design-entities-for-decomposition"></a>设计分解实体

良好的实体设计是将机器学习实体用作顶级实体。 这样，就可以不断地对实体设计进行更改，并选择性地结合**约束**和**描述符**使用**子组件**（子实体），将顶级实体分解成客户端应用程序所需的部件。

分解设计可使 LUIS 向客户端应用程序返回深度的实体解析。 这样，客户端应用程序便可以专注于业务规则，让 LUIS 来处理数据解析。

### <a name="machine-learned-entities-are-primary-data-collections"></a>机器学习实体是主要数据集合

[**计算机学习的实体**](tutorial-machine-learned-entity.md)是顶级数据单元。 子组件是机器学习实体的子实体。

机器学习实体根据通过训练言语习得的上下文触发。 **约束**是应用于机器学习实体的可选规则，这些规则根据 [List](reference-entity-list.md) 或 [Regex](reference-entity-regular-expression.md) 等非机器学习实体的精确文本匹配定义进一步约束触发。 例如，`size` 机器学习实体可以包含 `sizeList` 列表实体的约束，用于将 `size` 实体约束为仅当遇到包含在 `sizeList` 实体中的值时才触发。

[**描述符**](luis-concept-feature.md)是应用的功能，用于提高预测的单词或短语的相关性。 它们被称为*描述符*，因为它们用于*描述*意向或实体。 描述符描述了数据的特征或特性，例如 LUIS 观察和学习的重要字词或短语。

在 LUIS 应用中创建短语列表特征时，默认会全局启用该特征，并均匀地在所有意向和实体之间应用该特征。 但是，如果将短语列表作为机器学习实体（或模型）的描述符（特征）应用，则其应用范围将缩减为仅限该模型，而不再用于其他所有模型。** 使用短语列表作为模型的描述符有助于分解，因为这对它应用到的模型的准确度有帮助。

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
|[**计算机学习**](tutorial-machine-learned-entity.md)|机器学习实体从话语的上下文学习。 实体的父分组，不考虑实体类型。 这使得示例话语中的位置差异变得显著。 |
|[**列出**](reference-entity-list.md)|用**精确文本匹配**提取的项及其同义词的列表。|
|[**Pattern。 any**](reference-entity-pattern-any.md)|难以确定末尾部分的实体。 |
|[**预生成**](luis-reference-prebuilt-entities.md)|已经过训练，可以提取特定类型的数据，例如 URL 或电子邮件。 其中一些预生成实体是在开源[识别器 - 文本](https://github.com/Microsoft/Recognizers-Text)项目中定义的。 如果你的特定区域性或实体当前不受支持，请通过为项目做贡献来获得支持。|
|[**Regular Expression**](reference-entity-regular-expression.md)|使用正则表达式进行**精确文本匹配**。|

## <a name="extracting-contextually-related-data"></a>提取上下文相关的数据

言语可以包含两个或更多个实体，其中的数据含义基于言语内部的上下文。 例如，预订航班的言语包含两个位置：出发地和目的地。

`Book a flight from Seattle to Cairo`

需要提取 `location` 实体的两个示例。 客户端应用程序需要知道每个实体的位置类型才能完成购票过程。

可通过两种方法提取上下文相关的数据：

 * `location` 实体是机器学习实体，它使用两个子组件实体来捕获 `origin` 和 `destination`（首选）
 * `location` 实体使用 `origin` 和 `destination` 这两个**角色**

多个实体可以在一个言语中存在，如果使用这些实体的上下文没有意义，则无需使用分解或角色即可提取这些实体。 例如，如果言语 `I want to travel to Seattle, Cairo, and London.` 包含位置列表，该列表中的每个项没有其他含义。

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>使用机器学习实体的子组件实体来定义上下文

您可以使用[**机器学习的实体**](tutorial-machine-learned-entity.md)提取描述预订航班操作的数据，然后将顶级实体分解为客户端应用程序所需的单独部件。

在此示例中，顶级实体 `Book a flight from Seattle to Cairo` 可以是 `travelAction`，标记为提取 `flight from Seattle to Cairo`。 然后，创建名为 `origin` 和 `destination` 的两个子组件实体，两者已应用 `geographyV2` 预生成实体的约束。 在训练言语中，相应地对 `origin` 和 `destination` 进行标记。

### <a name="using-entity-role-to-define-context"></a>使用实体角色来定义上下文

角色是实体的命名别名，基于言语内部的上下文。 角色可与任何预生成的或自定义的实体类型配合使用，并可在示例言语和模式中使用。 在此示例中，`location` 实体需要 `origin` 和 `destination` 这两个角色，并且需要在示例言语中标记这两个角色。

如果 LUIS 找到 `location` 但无法确定角色，则仍会返回位置实体。 客户端应用程序需要跟进问题，以确定用户所指的位置类型。


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>如果所需实体数超过最大实体数

如果需要提高限制，请联系支持人员。 为此，请收集有关系统的详细信息，转到 [LUIS](luis-reference-regions.md#luis-website) 网站，然后选择“支持”****。 如果所持 Azure 订阅包含支持服务，请与 [Azure 技术支持](https://azure.microsoft.com/support/options/)联系。

## <a name="entity-prediction-status"></a>实体预测状态

当示例言语中的实体的实体预测不同于所选实体时，LUIS 门户会显示此状态。 这种不同的评分是根据当前已训练的模型给出的。

## <a name="next-steps"></a>后续步骤

了解关于优良[话语](luis-concept-utterance.md)的概念。

请参阅[添加实体](luis-how-to-add-entities.md)，详细了解如何将实体添加到 LUIS 应用。

请参阅[教程：从用户查询文本中提取结构化数据和语言理解（LUIS）中的机器学习的实体](tutorial-machine-learned-entity.md)，了解如何使用机器学习的实体从查询文本提取结构化数据。
 
