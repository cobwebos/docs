---
title: 实体类型 - LUIS
description: 实体在预测运行时从用户查询文本中提取数据。 一个_可选_的辅助目的是通过使用实体作为功能来提高意图或其他实体的预测。
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 9d8afd5a660b3af5556256835486e984d7d657bc
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585634"
---
# <a name="extract-data-with-entities"></a>用实体提取数据

实体在预测运行时从用户查询文本中提取数据。 一个_可选_的辅助目的是通过使用实体作为功能来提高意图或其他实体的预测。

实体有多种类型：

* [机器学习实体](reference-entity-machine-learned-entity.md)
* 非计算机学习用作必需[功能](luis-concept-feature.md)-用于精确文本匹配、模式匹配或通过预生成实体检测
* [Pattern： any](#patternany-entity) -从[模式](reference-entity-pattern-any.md)中提取自由格式的文本，例如书籍标题

机器学习实体提供最广泛的数据提取选项。 非计算机学习实体按文本匹配工作，并用作计算机学习的实体或意向的[必需功能](#design-entities-for-decomposition)。

## <a name="entities-represent-data"></a>实体表示数据

实体是要从言语中提取的数据，例如姓名、日期、产品名称或任何有意义的单词组。 话语可包括多个实体，也可不包含任何实体。 客户端应用程序可能需要数据来执行其任务。__

对于模型中的每个意向，需要在所有训练言语中一致性地标记实体。

 你可以定义自己的实体，也可以使用预生成实体来节省处理 [datetimeV2](luis-reference-prebuilt-datetimev2.md)、[序号](luis-reference-prebuilt-ordinal.md)、[电子邮件](luis-reference-prebuilt-email.md)和[电话号码](luis-reference-prebuilt-phonenumber.md)等为常见概念的时间。

|话语|实体|数据|
|--|--|--|
|购买 3 张到纽约的机票|预生成的数字<br>目标|3<br>纽约|


### <a name="entities-are-optional-but-recommended"></a>实体是可选的，但建议使用

虽然需要[意向](luis-concept-intent.md)，但实体是可选的。 您无需为应用中的每个概念都创建实体，而只适用于客户端应用程序需要数据或实体充当另一实体或意向的提示或信号的实体。

以后随着应用程序的开发以及确定新的数据需求，可以在 LUIS 模型中添加相应的实体。

## <a name="entity-compared-to-intent"></a>实体与意向

实体表示_查询文本中_的数据概念。 目的是将_整个查询文本_分类。

请考虑以下四个最谈话：

|话语|预测的意向|提取的实体|说明|
|--|--|--|--|
|帮助|帮助|-|没有要提取的内容。|
|Send something|sendSomething|-|没有要提取的内容。 该模型没有要 `something` 在此上下文中提取的必需功能，并且没有指定任何接收方。|
|Send Bob a present|sendSomething|`Bob`, `present`|模型 `Bob` 通过添加预先生成的实体的所需功能来提取 `personName` 。 已使用机器学习实体提取 `present`。|
|Send Bob a box of chocolates|sendSomething|`Bob`, `box of chocolates`|`Bob` `box of chocolates` 已通过机器学习的实体提取了两项重要的数据和。|

## <a name="design-entities-for-decomposition"></a>设计分解实体

利用机器学习的实体，你可以设计用于分解的应用架构，将大型概念分解为子实体。

分解设计可使 LUIS 向客户端应用程序返回深度的实体解析。 这样，客户端应用程序便可以专注于业务规则，让 LUIS 来处理数据解析。

计算机获知的实体基于通过示例最谈话获知的上下文触发。

[**计算机学习的实体**](tutorial-machine-learned-entity.md)是顶级提取器。 子实体是计算机学习实体的子实体。

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>实体类型

父代的子实体应该是计算机学习的实体。 子实体可以使用非计算机学习实体作为[功能](luis-concept-feature.md)。

请根据数据的提取方式以及提取后的数据表示方式，来选择实体。

|实体类型|目标|
|--|--|
|[**计算机学习**](tutorial-machine-learned-entity.md)|提取从标记的示例中学习的嵌套的复杂数据。 |
|[**成员列表**](reference-entity-list.md)|用**精确文本匹配**提取的项及其同义词的列表。|
|[**Pattern。 any**](#patternany-entity)|实体在其中查找实体的结尾很难确定，因为实体是自由格式的。 仅在[模式](luis-concept-patterns.md)中可用。|
|[**预生成**](luis-reference-prebuilt-entities.md)|已经过训练，可以提取特定类型的数据，例如 URL 或电子邮件。 其中一些预生成实体是在开源[识别器 - 文本](https://github.com/Microsoft/Recognizers-Text)项目中定义的。 如果你的特定区域性或实体当前不受支持，请通过为项目做贡献来获得支持。|
|[**正则表达式**](reference-entity-regular-expression.md)|使用正则表达式进行**精确文本匹配**。|

## <a name="extracting-contextually-related-data"></a>提取上下文相关的数据

言语可以包含两个或更多个实体，其中的数据含义基于言语内部的上下文。 例如，预订航班具有两个地理位置（源和目标）的查询文本。

`Book a flight from Seattle to Cairo`

需要提取两个位置，使客户端应用程序知道每个位置的类型才能完成票证购买。

若要提取源和目标，请创建两个子实体作为票据订单机器学习实体的一部分。 对于每个子实体，请创建使用 geographyV2 的必需功能。

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>使用所需功能来约束实体

了解有关[所需功能](luis-concept-feature.md)的详细信息

## <a name="patternany-entity"></a>Pattern.any 实体

模式。 any 仅在[模式](luis-concept-patterns.md)中可用。

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>超过实体的应用限制

如果你需要超过此[限制](luis-limits.md#model-limits)，请联系支持人员。 为此，请收集有关系统的详细信息，转到 [LUIS](luis-reference-regions.md#luis-website) 网站，然后选择“支持”****。 如果所持 Azure 订阅包含支持服务，请与 [Azure 技术支持](https://azure.microsoft.com/support/options/)联系。

## <a name="entity-prediction-status"></a>实体预测状态

LUIS 门户显示实体与你为示例查询文本选择的实体具有不同的实体预测的时间。 这种不同的评分是根据当前已训练的模型给出的。 使用此信息可以使用以下一项或多项解决定型错误：
* 为实体创建[功能](luis-concept-feature.md)以帮助标识实体的概念
* 添加具有实体的更多[示例最谈话](luis-concept-utterance.md)和标签
* 查看预测终结点上收到的任何最谈话的[活动学习建议](luis-concept-review-endpoint-utterances.md)，这些建议可帮助标识实体的概念。

## <a name="next-steps"></a>后续步骤

了解关于优良[话语](luis-concept-utterance.md)的概念。

请参阅[添加实体](luis-how-to-add-entities.md)，详细了解如何将实体添加到 LUIS 应用。

请参阅[教程：从用户查询文本中提取结构化数据和语言理解（LUIS）中的机器学习的实体](tutorial-machine-learned-entity.md)，了解如何使用机器学习的实体从查询文本提取结构化数据。

