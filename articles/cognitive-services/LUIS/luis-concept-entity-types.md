---
title: 实体类型 - LUIS
description: 实体可以在预测运行时从用户言语中提取数据。 一个可选的辅助用途是通过用作特征的实体促进意向或其他实体的预测。__
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398d18642052726af4d4920443bad515ec0b5bef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316556"
---
# <a name="extract-data-with-entities"></a>通过实体提取数据

实体可以在预测运行时从用户言语中提取数据。 一个可选的辅助用途是通过用作特征的实体促进意向或其他实体的预测。__

有多种类型的实体：

* [机器学习实体](reference-entity-machine-learned-entity.md) - 这是主要实体。 在使用其他实体之前，应使用此实体类型设计架构。
* 用作必要[特征](luis-concept-feature.md)的非机器学习实体 - 由预生成实体用于精确文本匹配、模式匹配或检测
* [Pattern.any](#patternany-entity) - 从[模式](reference-entity-pattern-any.md)中提取自由格式的文本，例如书籍标题

机器学习实体提供最广泛的数据提取选项。 非机器学习实体通过文本匹配发挥作用，充当机器学习实体或意图的[必要特征](#design-entities-for-decomposition)。

## <a name="entities-represent-data"></a>实体表示数据

实体是要从言语中提取的数据，例如姓名、日期、产品名称或任何有意义的单词组。 话语可包括多个实体，也可不包含任何实体。 客户端应用程序可能需要数据来执行其任务。__

对于模型中的每个意向，需要在所有训练言语中一致性地标记实体。

 你可以定义自己的实体，也可以使用预生成实体来节省处理 [datetimeV2](luis-reference-prebuilt-datetimev2.md)、[序号](luis-reference-prebuilt-ordinal.md)、[电子邮件](luis-reference-prebuilt-email.md)和[电话号码](luis-reference-prebuilt-phonenumber.md)等为常见概念的时间。

|话语|实体|数据|
|--|--|--|
|购买 3 张到纽约的机票|预生成的数字<br>目标|3<br>纽约|


### <a name="entities-are-optional-but-recommended"></a>实体是可选的（但建议使用）

[意向](luis-concept-intent.md)是必需的，而实体是可选的。 无需为应用中的每个概念创建实体，只需为符合以下条件的概念创建实体：客户端应用程序需要其相关数据或实体充当另一实体或意向的提示或信号。

以后随着应用程序的开发以及确定新的数据需求，可以在 LUIS 模型中添加相应的实体。

<a name="entity-compared-to-intent"></a>

## <a name="entity-represents-data-extraction"></a>实体表示数据提取

实体表示言语中的数据概念。__ 意向对整个言语分类。__

请考虑以下四个言语：

|话语|预测的意向|提取的实体|说明|
|--|--|--|--|
|帮助|help|-|没有要提取的内容。|
|Send something|sendSomething|-|没有要提取的内容。 在此上下文中，模型没有可用于提取 `something` 的必要特征，且未指定接收方。|
|Send Bob a present|sendSomething|`Bob`, `present`|模型通过添加预生成实体 `personName` 的必要特征来提取 `Bob`。 已使用机器学习实体提取 `present`。|
|Send Bob a box of chocolates|sendSomething|`Bob`, `box of chocolates`|机器学习实体已提取两个重要数据片段：`Bob` 和 `box of chocolates`。|

## <a name="label-entities-in-all-intents"></a>标记所有意向中的实体

实体提取数据而不考虑预测的意向。 请确保标记所有意向中的所有示例言语。__ `None` 意向缺少实体标记会导致混淆，即使其他意向有多得多的训练言语，也是如此。

## <a name="design-entities-for-decomposition"></a>设计分解实体

借助机器学习实体，可以设计用于分解的应用架构，将一个大的概念分解成子实体。

分解设计可使 LUIS 向客户端应用程序返回深度的实体解析。 这样，客户端应用程序便可以专注于业务规则，让 LUIS 来处理数据解析。

机器学习实体根据通过示例言语习得的上下文触发。

[机器学习实体](tutorial-machine-learned-entity.md)是顶级提取程序。 子实体是机器学习实体的子实体。

## <a name="effective-machine-learned-entities"></a>有效的机器学习实体

若要有效构建机器学习实体，必须符合以下条件：

* 标记应在意向之间应保持一致。 这甚至包括你在 **None** 意向中提供的包含此实体的言语。 否则，模型将无法有效地确定序列。
* 如果有包含子实体的机器学习实体，请确保实体和子实体的不同顺序和变体显示在标记的言语中。 标记的示例言语应包括所有有效的形式，并包括显示的、缺失的以及在言语中重新排序的实体。
* 应避免将实体过度拟合到极固定的集。 当模型未充分通用化时，会发生过度拟合****，这是机器学习模型中的常见问题。 这意味着应用并非很适合处理新数据。 因此，你应该使标记的示例言语多样化，从而使应用能够在你提供的有限示例之外通用化。 你应该让不同的子实体有足够的差异性，使模型更多地考虑相关概念，而不是只考虑显示的示例。

## <a name="effective-prebuilt-entities"></a>有效的预生成实体

若要生成可提取常见数据（例如由[预生成实体](luis-reference-prebuilt-entities.md)提供的数据）的有效实体，建议采用以下流程。

通过将自己的数据作为特征引入到实体中来改进数据的提取。 这样，你数据中的所有附加标签都会了解人名在应用程序中存在的上下文。

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>实体类型

父代的子实体应是机器学习实体。 子实体可以使用一个非机器学习实体作为[特征](luis-concept-feature.md)。

请根据数据的提取方式以及提取后的数据表示方式，来选择实体。

|实体类型|目的|
|--|--|
|[**机器学习**](tutorial-machine-learned-entity.md)|从标记的示例中提取嵌套的、复杂的数据。 |
|[**列表**](reference-entity-list.md)|使用**精确文本匹配**提取的项列表及其同义词。|
|[**Pattern.any**](#patternany-entity)|由于属于自由格式而难以确定末尾部分的实体。 仅在[模式](luis-concept-patterns.md)中可用。|
|[**预生成**](luis-reference-prebuilt-entities.md)|已经过训练，可以提取特定类型的数据，例如 URL 或电子邮件。 其中一些预生成实体是在开源[识别器 - 文本](https://github.com/Microsoft/Recognizers-Text)项目中定义的。 如果你的特定区域性或实体当前不受支持，请通过为项目做贡献来获得支持。|
|[**正则表达式**](reference-entity-regular-expression.md)|使用正则表达式进行**精确文本匹配**。|


## <a name="extraction-versus-resolution"></a>提取与解析

当数据显示在言语中时，实体会提取数据。 实体不更改或解析数据。 实体不会对文本是否为实体的有效值提供任何解析。

可以通过多种方法将解析引入到提取中，但应注意，这会限制应用防范变异和错误的能力。

可以将列表实体和正则表达式（文本匹配）实体用作子实体的[必要特征](luis-concept-feature.md#required-features)，让子实体充当所提取内容的筛选器。 应谨慎使用此功能，不要妨碍应用的预测功能。

## <a name="extracting-contextually-related-data"></a>提取上下文相关的数据

言语可以包含两个或更多个实体，其中的数据含义基于言语内部的上下文。 例如，一个用于预订航班的言语包含两个地理位置：出发地和目的地。

`Book a flight from Seattle to Cairo`

这两个位置的提取方式需使客户端应用程序可以知道每个位置的类型，以便完成购票过程。

若要提取源和目标，请创建两个子实体作为订票机器学习实体的一部分。 对于每个子实体，请创建一个使用 geographyV2 的必要特征。

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>使用必要特征来约束实体

详细了解[必要特征](luis-concept-feature.md)

## <a name="patternany-entity"></a>Pattern.any 实体

Pattern.any 仅在[模式](luis-concept-patterns.md)中可用。

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>超过应用的实体限制

如果需要提高[限制](luis-limits.md#model-limits)，请联系支持人员。 为此，请收集有关系统的详细信息，转到 [LUIS](luis-reference-regions.md#luis-website) 网站，然后选择“支持”****。 如果所持 Azure 订阅包含支持服务，请与 [Azure 技术支持](https://azure.microsoft.com/support/options/)联系。

## <a name="entity-prediction-status-and-errors"></a>实体预测状态和错误

当实体的实体预测不同于你为示例言语选择的实体时，LUIS 门户会显示此状态。 这种不同的评分是根据当前已训练的模型给出的。 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="当实体的实体预测不同于你为示例言语选择的实体时，LUIS 门户会显示此状态。":::

其中文本在示例查询文本内突出显示，示例查询文本行向右显示错误指示器，显示为红色三角形。 

使用此信息可使用以下一项或多项解决实体错误：
* 突出显示的文本为网文。 修复、查看、更正和重新训练。 
* 为实体创建一个有助于确定实体概念的[特征](luis-concept-feature.md)
* 添加更多[示例言语](luis-concept-utterance.md)并使用实体进行标记
* 对于在预测终结点上收到的任何言语，[查看有效的学习建议](luis-concept-review-endpoint-utterances.md)，以便确定实体的概念。

## <a name="next-steps"></a>后续步骤

了解关于优良[话语](luis-concept-utterance.md)的概念。

请参阅[添加实体](luis-how-to-add-entities.md)，详细了解如何将实体添加到 LUIS 应用。

请参阅[教程：在语言理解 (LUIS) 中使用机器学习实体从用户言语中提取结构化数据](tutorial-machine-learned-entity.md)，学习如何使用机器学习实体从言语中提取结构化数据。

