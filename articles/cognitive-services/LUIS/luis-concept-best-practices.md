---
title: 生成 LUIS 应用的最佳做法
titleSuffix: Azure Cognitive Services
description: 了解从 LUIS 应用的模型获得最佳结果的最佳实践。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b4be79338db71ad83204fae971da0b77885a8070
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280930"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>构建语言理解（LUIS）应用的最佳做法
使用应用创作过程生成 LUIS 应用： 

* 生成语言模型（意向和实体）
* 添加一些定型示例最谈话（每个意向15-30）
* 发布到终结点
* 从终结点进行测试 

[发布](luis-how-to-publish-app.md)应用后，使用开发生命周期从终结点添加功能、发布和测试。 不要通过添加更多示例最谈话开始下一个创作周期，因为这不允许 LUIS 了解实际的用户最谈话模型。 

请勿展开最谈话，直到两个示例和终结点最谈话的当前集合返回 "自信，高预测分数"。 使用[活动学习](luis-concept-review-endpoint-utterances.md)提高分数。 




## <a name="do-and-dont"></a>注意事项
下面的列表包含 LUIS 应用的最佳做法：

|操作|不要|
|--|--|
|[应定义不同的意向](#do-define-distinct-intents)<br>[将描述符添加到意向](#do-add-descriptors-to-intents) |[将许多话语示例添加到意向](#dont-add-many-example-utterances-to-intents)<br>[使用少量或简单的实体](#dont-use-few-or-simple-entities) |
|[每个意向需采用合适的详细程度](#do-find-sweet-spot-for-intents)|[将 LUIS 用作培训平台](#dont-use-luis-as-a-training-platform)|
|[利用版本以迭代方式生成应用](#do-build-your-app-iteratively-with-versions)<br>[生成模型分解的实体](#do-build-for-model-decomposition)|[添加许多相同格式的话语示例，忽略其他格式](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[在后续迭代中添加模式](#do-add-patterns-in-later-iterations)|[混淆意向和实体的定义](#dont-mix-the-definition-of-intents-and-entities)|
|[跨所有意向来平衡话语](#balance-your-utterances-across-all-intents)，None 意向除外。<br>[将话语示例添加到“None”意向](#do-add-example-utterances-to-none-intent)|[创建具有所有可能值的描述符](#dont-create-descriptors-with-all-the-possible-values)|
|[利用主动学习的建议功能](#do-leverage-the-suggest-feature-for-active-learning)|[添加的模式过多](#dont-add-many-patterns)|
|[通过批处理测试监视应用的性能](#do-monitor-the-performance-of-your-app)|[使用添加的每个话语示例进行训练和发布](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>应定义不同的意向
确保每个意向的词汇特定于该意向，而不会与其他意向的词汇重叠。 例如，如果要创建一款处理行程安排（例如航班和酒店）的应用，可以选择将这些主题领域视作彼此独立的意向或视为同一意向，其中包含话语中特定数据的实体。

如果两个意向的词汇相同，请合并意向并使用实体。 

请考虑以下话语示例：

|示例陈述|
|--|
|预订航班|
|预订酒店|

`Book a flight` 和 `Book a hotel` 使用相同的 `book a `词汇。 这种格式是相同的，因此它应与 `flight` 的不同词语和 `hotel` 提取的实体相同。 

## <a name="do-add-descriptors-to-intents"></a>确实要将描述符添加到意向

描述符帮助描述功能的用途。 描述符可以是对该意向非常重要的单词的短语列表，也可以是对该意向非常重要的实体。 

## <a name="do-find-sweet-spot-for-intents"></a>请找到意向的平衡点
使用 LUIS 中的预测数据来判定意向是否存在重叠的情况。 重叠的意向会困扰 LUIS。 结果是评分最高的意向会与另一个意向非常接近。 由于 LUIS 不会在每次训练的数据中使用完全相同的路径，所以重叠意向可能会在训练中排到第一或第二的位置。 各意向的话语分数应相互拉开差距以避免出现上述翻转情况。 更好地区分意向可以使得每次训练都得出预期的最高分意向。 
 
<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>利用版本以迭代方式生成应用

每个创作周期应该在一个新[版本](luis-concept-version.md)内进行，从现有版本进行克隆。 

## <a name="do-build-for-model-decomposition"></a>模型分解的生成

模型分解具有典型的处理过程：

* 基于客户端应用的用户意向创建**意向**
* 基于实际用户输入添加15-30 示例最谈话
* 标签示例查询文本中的顶层数据概念
* 将数据概念分解为子组件
* 向子组件添加描述符（功能）
* 将描述符（功能）添加到意向 

创建意向并添加示例最谈话后，下面的示例说明实体分解。 

首先，确定要在查询文本中提取的完整数据概念。 这是您的机器学习的实体。 然后将该短语分解为各个部分。 这包括标识子组件（作为实体）以及描述符和约束。 

例如，如果想要提取地址，则可 `Address`调用顶级计算机的实体。 创建地址时，标识其部分子组件，如街道地址、城市、省/市/自治区和邮政编码。 

继续分解这些元素，方法是将邮政编码**约束**为正则表达式。 将街道地址分解为街道号码的各个部分（使用预建号码）、街道名称和街道类型。 街道类型可以用**描述符**列表进行描述，如 "公路"、"圆形"、"道路" 和 "通道"。

V3 创作 API 允许模型分解。 

## <a name="do-add-patterns-in-later-iterations"></a>在后续迭代中添加模式

在添加[模式](luis-concept-patterns.md)之前，你应了解应用的行为方式，因为模式的加权比示例最谈话更高，并将会扭曲置信度。 

了解应用的行为方式后，请添加应用于应用的模式。 不需要在每次[迭代](luis-concept-app-iteration.md)时都添加它们。 

将它们添加到模型设计的开始位置并不会有任何坏处，但在通过最谈话对模型进行测试后，可以更轻松地查看每个模式更改模型的方式。 
 
<!--

### Phrase lists

[Phrase lists](luis-concept-feature.md) allow you to define dictionaries of words related to your app domain. Seed your phrase list with a few words then use the suggest feature so LUIS knows about more words in the vocabulary specific to your app. A Phrase List improves intent detection and entity classification by boosting the signal associated with words or phrases that are significant to your app. 

Don't add every word to the vocabulary since the phrase list isn't an exact match. 

For more information:
* Concept: [Phrase list features in your LUIS app](luis-concept-feature.md)
* How-to: [Use phrase lists to boost signal of word list](luis-how-to-add-features.md)



### Patterns

Real user utterances from the endpoint, very similar to each other, may reveal patterns of word choice and placement. The [pattern](luis-concept-patterns.md) feature takes this word choice and placement along with regular expressions to improve your prediction accuracy. A regular expression in the pattern allows for words and punctuation you intend to ignore while still matching the pattern. 

Use pattern's [optional syntax](luis-concept-patterns.md) for punctuation so punctuation can be ignored. Use the [explicit list](luis-concept-patterns.md#explicit-lists) to compensate for pattern.any syntax issues. 

For more information:
* Concept: [Patterns improve prediction accuracy](luis-concept-patterns.md)
* How-to: [How to add Patterns to improve prediction accuracy](luis-how-to-model-intent-pattern.md)
-->

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>跨所有意图平衡最谈话

为了使 LUIS 预测准确，每个意向（None 意向除外）中示例话语的数量必须相同（相对说来）。 

如果一个意向有 100 个示例话语，另一个意向有 20 个示例话语，则 100 个话语的意向的预测准确率会更高。  

## <a name="do-add-example-utterances-to-none-intent"></a>务必将话语示例添加至“None”意向

此方法是一种回退意向，表示应用程序以外的所有内容。 针对 LUIS 应用其余部分的每 10 个话语示例，向“None”意向中添加一个话语示例。

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>应利用主动学习的建议功能

定期使用[主动学习](luis-how-to-review-endpoint-utterances.md)的“查看终结点话语”功能，而不是将更多话语示例添加到意向。 因为应用会不断接收终结点话语，所以此列表会不断变化。

## <a name="do-monitor-the-performance-of-your-app"></a>应监视应用的性能

使用[批量测试](luis-concept-batch-test.md)集监视预测准确性。 

保留一组不同的最谈话，不能用作[示例最谈话](luis-concept-utterance.md)或 endpoint 最谈话。 针对测试集不断改进应用。 调整测试集以反映真实的用户话语。 使用此测试集来评估每次迭代的或每个版本的应用。 

## <a name="dont-add-many-example-utterances-to-intents"></a>请勿将许多话语示例添加到意向

应用发布后，在开发生命周期过程中只从活动学习中添加最谈话。 如果话语太过相似，请添加模式。 

## <a name="dont-use-few-or-simple-entities"></a>不要使用少量或简单的实体

实体是为进行数据提取和预测而构建的。 这一点非常重要，因为每个意向都具有计算机学习的实体，这些实体描述了目的中的数据。 这有助于 LUIS 预测意向，即使客户端应用程序不需要使用提取的实体也是如此。 

## <a name="dont-use-luis-as-a-training-platform"></a>请勿将 LUIS 用作培训平台

LUIS 特定于语言模型的域。 但并不意味着将其用作常规自然语言训练平台。 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>请勿添加许多相同格式的话语示例，而忽略其他格式

LUIS 会预期一个意向的话语会存在变体。 在总体意思相同的情况下，话语形式可能会有所不同。 其差异可能涉及话语长度、字词选择和字词位置等方面。 

|请勿使用相同的格式|务必使用不同的格式|
|--|--|
|购买一张到西雅图的票<br>购买一张到巴黎的票<br>购买一张到奥兰多的票|购买 1 张到西雅图的票<br>预定下周一到巴黎的夜间定期航班的两个座位<br>我要预订 3 张到奥兰多的票，去度春假|

第二列使用了不同的动词（购买、预订、预定）、不同的数量（一、两、3）和不同的字词排序，但表达的是相同的意向，就是购买旅行的机票。 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>请勿混淆意向和实体的定义

为聊天机器人将执行的任何操作创建一个意向。 将实体用作实现操作的参数。 

对于将向航班提供书籍的机器人，请创建**BookFlight**意向。 请勿为每条航线或每个目的地都创建一个意向。 将这些数据用作[实体](luis-concept-entity-types.md)，并在话语示例中进行标记。 

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>不要创建具有所有可能值的描述符

提供描述符[短语列表](luis-concept-feature.md)中的几个示例，而不是每个单词。 LUIS 会对上下文进行一般化并将其纳入考虑。 

## <a name="dont-add-many-patterns"></a>请勿添加许多模式

请勿添加过多[模式](luis-concept-patterns.md)。 LUIS 旨在通过少量示例快速学习。 请勿在不必要的情况下重载系统。

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>请勿使用添加的每个话语示例进行训练和发布

在进行训练和发布之前添加 10 或 15 个话语。 这样可以了解对预测准确性的影响。 添加单个话语可能不会对分数产生明显影响。 

## <a name="next-steps"></a>后续步骤

* 了解如何在 LUIS 应用中[规划应用](luis-how-plan-your-app.md)。
