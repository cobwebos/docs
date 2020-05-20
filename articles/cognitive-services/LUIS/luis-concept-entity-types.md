---
title: 实体类型 - LUIS
description: 实体在预测运行时从用户查询文本中提取数据。 一个_可选_的辅助目的是通过使用实体作为功能来提高意图或其他实体的预测。
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: a5e4812eab84650401dd19b0f8d7b361a5135dd3
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682170"
---
# <a name="extract-data-with-entities"></a>用实体提取数据

实体在预测运行时从用户查询文本中提取数据。 一个_可选_的辅助目的是通过使用实体作为功能来提高意图或其他实体的预测。

实体有多种类型：

* [机器学习实体](reference-entity-machine-learned-entity.md)-这是主实体。 在使用其他实体之前，应使用此实体类型设计架构。
* 用于作为必需[功能](luis-concept-feature.md)的非计算机学习-精确文本匹配、模式匹配或预生成实体检测
* [Pattern： any](#patternany-entity) -从[模式](reference-entity-pattern-any.md)中提取自由格式的文本，例如书籍标题

机器学习实体提供最广泛的数据提取选择。 非机器学习实体按文本匹配工作，并用作机器学习实体或意向的[必需功能](#design-entities-for-decomposition)。

## <a name="entities-represent-data"></a>实体表示数据

实体是要从言语中提取的数据，例如姓名、日期、产品名称或任何有意义的单词组。 话语可包括多个实体，也可不包含任何实体。 客户端应用程序可能需要数据来执行其任务。 

对于模型中的每个意向，需要在所有训练言语中一致性地标记实体。

 你可以定义自己的实体，也可以使用预生成实体来节省处理 [datetimeV2](luis-reference-prebuilt-datetimev2.md)、[序号](luis-reference-prebuilt-ordinal.md)、[电子邮件](luis-reference-prebuilt-email.md)和[电话号码](luis-reference-prebuilt-phonenumber.md)等为常见概念的时间。

|话语|实体|data|
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
|Send Bob a present|sendSomething|`Bob`, `present`|模型 `Bob` 通过添加预先生成的实体的所需功能来提取 `personName` 。 计算机学习实体已用于提取 `present` 。|
|Send Bob a box of chocolates|sendSomething|`Bob`, `box of chocolates`|`Bob` `box of chocolates` 计算机学习实体已提取两个重要的数据段和。|

## <a name="design-entities-for-decomposition"></a>设计分解实体

利用机器学习实体，你可以设计用于分解的应用架构，将大型概念分解为子实体。

分解设计可使 LUIS 向客户端应用程序返回深度的实体解析。 这样，客户端应用程序便可以专注于业务规则，让 LUIS 来处理数据解析。

计算机学习实体根据通过示例最谈话获知的上下文触发。

[**机器学习实体**](tutorial-machine-learned-entity.md)是顶级提取器。 子实体是机器学习实体的子实体。

## <a name="effective-machine-learned-entities"></a>有效的机器学习实体

有效地构建计算机学习的实体：

* 在此方法中，您的标签应一致。 这包括你在 "**无**" 目的（包括此实体）中提供的最谈话。 否则，模型将无法有效地确定序列。
* 如果有使用子实体的计算机学习实体，请确保实体和子实体的不同订单和变体显示在标记的最谈话中。 标记为示例最谈话应包括所有有效的窗体，并包含显示和不存在的实体，也会在查询文本中重新排序。
* 应避免将实体过度拟合到非常固定的集。 如果模型没有充分通用化，就会发生**过度拟合**，这是机器学习模型中的常见问题。 这意味着应用程序无法充分地处理新数据。 相反，您应该改变标记的示例最谈话，这样应用程序就能够在您提供的有限示例之外进行通用化。 您应该改变不同的子实体，使模型的更改更多，而不是只考虑显示的示例。

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>实体类型

父代的子实体应该是机器学习实体。 子实体可以使用非计算机学习实体作为[功能](luis-concept-feature.md)。

请根据数据的提取方式以及提取后的数据表示方式，来选择实体。

|实体类型|目标|
|--|--|
|[**计算机学习**](tutorial-machine-learned-entity.md)|提取从标记的示例中学习的嵌套的复杂数据。 |
|[**成员列表**](reference-entity-list.md)|用**精确文本匹配**提取的项及其同义词的列表。|
|[**Pattern。 any**](#patternany-entity)|实体在其中查找实体的结尾很难确定，因为实体是自由格式的。 仅在[模式](luis-concept-patterns.md)中可用。|
|[**预生成**](luis-reference-prebuilt-entities.md)|已经过训练，可以提取特定类型的数据，例如 URL 或电子邮件。 其中一些预生成实体是在开源[识别器 - 文本](https://github.com/Microsoft/Recognizers-Text)项目中定义的。 如果你的特定区域性或实体当前不受支持，请通过为项目做贡献来获得支持。|
|[**正则表达式**](reference-entity-regular-expression.md)|使用正则表达式进行**精确文本匹配**。|


## <a name="extraction-versus-resolution"></a>提取与解析

当数据显示在查询文本中时，实体将提取数据。 实体不更改或解析数据。 如果文本是实体的有效值，则实体不会提供任何解决方案。

有多种方法可以将解析引入到提取中，但应注意，这会限制应用不受变化和错误影响的能力。

可以将实体和正则表达式（文本匹配）实体用作子实体的[所需功能](luis-concept-feature.md#required-features)，并将其用作提取的筛选器。 应仔细使用这一点，不要妨碍应用的预测能力。

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

请参阅[教程：使用语言理解（LUIS）中的机器学习实体提取用户查询文本中的结构化数据](tutorial-machine-learned-entity.md)，了解如何使用机器学习实体从查询文本提取结构化数据。

