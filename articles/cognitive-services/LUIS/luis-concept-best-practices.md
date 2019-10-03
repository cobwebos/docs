---
title: 最佳做法-LUIS
titleSuffix: Azure Cognitive Services
description: 学习 LUIS 最佳做法以从 LUIS 应用的模型中获取最佳结果。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 91ff99f674439580d369aad1490ded85d39d377c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382879"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>使用认知服务生成语言理解应用的最佳做法
使用应用创作过程构建你的 LUIS 应用： 

* 生成语言模型
* 添加几个训练话语示例（每个意向 10 到 15 个）
* 发布 
* 从终结点进行测试 
* 添加特征

[发布](luis-how-to-publish-app.md)应用后，请使用创作周期来添加功能、发布和测试终结点。 请勿通过添加更多话语示例来启动下一个创作周期。 这无法让 LUIS 通过真实的用户话语来学习相关模型。 

为了让 LUIS 高效学习，请勿在当前示例话语与终结点话语配对组返回较高的可信度和预测分数之前展开话语。 使用[主动学习](luis-concept-review-endpoint-utterances.md)、[模式](luis-concept-patterns.md)和[短语列表](luis-concept-feature.md)来提高分数。 

## <a name="do-and-dont"></a>注意事项
下面的列表包含 LUIS 应用的最佳做法：

|操作|不要|
|--|--|
|[应定义不同的意向](#do-define-distinct-intents) |[将许多话语示例添加到意向](#dont-add-many-example-utterances-to-intents) |
|[每个意向需采用合适的详细程度](#do-find-sweet-spot-for-intents)|[将 LUIS 用作培训平台](#dont-use-luis-as-a-training-platform)|
|[以迭代方式生成应用](#do-build-the-app-iteratively)|[添加许多相同格式的话语示例，忽略其他格式](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[在后续的迭代中添加短语列表和模式](#do-add-phrase-lists-and-patterns-in-later-iterations)|[混淆意向和实体的定义](#dont-mix-the-definition-of-intents-and-entities)|
|[跨所有意向来平衡话语](#balance-your-utterances-across-all-intents)，None 意向除外。<br>[将话语示例添加到“None”意向](#do-add-example-utterances-to-none-intent)|[使用所有可能的值创建短语列表](#dont-create-phrase-lists-with-all-the-possible-values)|
|[利用主动学习的建议功能](#do-leverage-the-suggest-feature-for-active-learning)|[添加的模式过多](#dont-add-many-patterns)|
|[监视应用的性能](#do-monitor-the-performance-of-your-app)|[使用添加的每个话语示例进行训练和发布](#dont-train-and-publish-with-every-single-example-utterance)|
|[将版本用于每个应用迭代](#do-use-versions-for-each-app-iteration)||

## <a name="do-define-distinct-intents"></a>应定义不同的意向
确保每个意向的词汇特定于该意向，而不会与其他意向的词汇重叠。 例如，如果要创建一款处理行程安排（例如航班和酒店）的应用，可以选择将这些主题领域视作彼此独立的意向或视为同一意向，其中包含话语中特定数据的实体。

如果两个意向的词汇相同，请合并意向并使用实体。 

请考虑以下话语示例：

|示例陈述|
|--|
|预订航班|
|预订酒店|

“预订航班”和“预订酒店”使用了同样的词汇“预订”。 此格式相同，所以它应该是同一意向，只是使用不同的词语（“航班”和“酒店”）作为提取的实体。 

更多相关信息：
* 概念：[关于 LUIS 应用中的意向的概念](luis-concept-intent.md)
* 教程：[生成 LUIS 应用，以确定用户意向](luis-quickstart-intents-only.md)
* 如何：[添加意向以确定用户的话语意向](luis-how-to-add-intents.md)


## <a name="do-find-sweet-spot-for-intents"></a>请找到意向的平衡点
使用 LUIS 中的预测数据来判定意向是否存在重叠的情况。 重叠的意向会困扰 LUIS。 结果是评分最高的意向会与另一个意向非常接近。 由于 LUIS 不会在每次训练的数据中使用完全相同的路径，所以重叠意向可能会在训练中排到第一或第二的位置。 各意向的话语分数应相互拉开差距以避免出现上述翻转情况。 更好地区分意向可以使得每次训练都得出预期的最高分意向。 
 
## <a name="do-build-the-app-iteratively"></a>以迭代方式生成应用
保留一个独立的话语集，不将其用作[话语示例](luis-concept-utterance.md)或终结点话语。 针对测试集不断改进应用。 调整测试集以反映真实的用户话语。 使用此测试集来评估每次迭代的或每个版本的应用。 

开发人员应备有三个数据集。 第一个是用于构建模型的话语示例集。 第二个数据集用于在终结点测试模型。 第三个是[批处理测试](luis-how-to-batch-test.md)中使用的盲测数据集。 最后一个数据集不用于训练应用程序，也不在终结点上发送。  

更多相关信息：
* 概念：[LUIS 应用的创作周期](luis-concept-app-iteration.md)

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>应在后续的迭代中添加短语列表和模式

最佳做法是在测试应用之前不要应用这些做法。 在添加[短语列表](luis-concept-feature.md)和[模式](luis-concept-patterns.md)之前，你应了解应用的行为方式，因为这些功能的加权比示例最谈话更高，并将会歪曲置信度。 

在了解没有这些项时你的应用的行为方式后，在这些功能适用于你的应用时再添加每个功能。 你不需要在每次[迭代](luis-concept-app-iteration.md)中都添加这些功能，也不需要在每个版本中都更改这些功能。 

在设计模型之初就添加它们并没有什么坏处，但如果在使用言语测试模型之后再添加，则可以更容易地看出每个功能如何改变结果。 

最佳做法是通过[终结点](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint)进行测试，以便获得[主动学习](luis-concept-review-endpoint-utterances.md)的额外好处。 [交互式测试窗格](luis-interactive-test.md)也是有效的测试方法。 
 

### <a name="phrase-lists"></a>短语列表

[短语列表](luis-concept-feature.md)可用于定义包含与应用域有关的字词的字典。 从几个字词开始创建短语列表，然后使用推荐功能，让 LUIS 了解词汇表中更多特定于应用的字词。 短语列表通过增强与对应用而言有意义的字词或短语关联的信号，改进了意向检测和实体分类。 

请勿将所有字词都添加到词汇表中，因为短语列表不是完全匹配的。 

更多相关信息：
* 概念：[LUIS 应用中的短语列表特征](luis-concept-feature.md)
* 操作说明：[使用短语列表来增强字词列表的信号](luis-how-to-add-features.md)

### <a name="patterns"></a>模式

终结点中真实用户的话语之间非常类似，可能会体现出字词选择和位置放置的模式。 [模式](luis-concept-patterns.md)功能会同时考虑此字词选择和位置放置以及正则表达式，以提升预测的准确性。 模式中的正则表达式允许在忽略字词和标点的情况下依然与模式匹配。 

在模式中为标点使用[可选语法](luis-concept-patterns.md)，以便忽略标点。 使用[显式列表](luis-concept-patterns.md#explicit-lists)来弥补 pattern.any 语法问题。 

更多相关信息：
* 概念：[模式可提高预测准确性](luis-concept-patterns.md)
* 操作说明：[如何添加模式以提高预测准确性](luis-how-to-model-intent-pattern.md)

## <a name="balance-your-utterances-across-all-intents"></a>跨所有意向来平衡话语

为了使 LUIS 预测准确，每个意向（None 意向除外）中示例话语的数量必须相同（相对说来）。 

如果一个意向有 100 个示例话语，另一个意向有 20 个示例话语，则 100 个话语的意向的预测准确率会更高。  

## <a name="do-add-example-utterances-to-none-intent"></a>务必将话语示例添加至“None”意向

此意向是回退意向，指示应用程序以外的所有内容。 针对 LUIS 应用其余部分的每 10 个话语示例，向“None”意向中添加一个话语示例。

更多相关信息：
* 概念：[了解哪些良好的话语适用于你的 LUIS 应用](luis-concept-utterance.md)

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>应利用主动学习的建议功能

定期使用[主动学习](luis-how-to-review-endpoint-utterances.md)的“查看终结点话语”功能，而不是将更多话语示例添加到意向。 因为应用会不断接收终结点话语，所以此列表会不断变化。

更多相关信息：
* 概念：[通过评审终结点话语启用主动学习的相关概念](luis-concept-review-endpoint-utterances.md)
* 教程：[教程：通过查看终结点话语来修复不确定的预测](luis-tutorial-review-endpoint-utterances.md)
* 操作说明：[如何评审 LUIS 门户中的终结点陈述](luis-how-to-review-endpoint-utterances.md)

## <a name="do-monitor-the-performance-of-your-app"></a>应监视应用的性能

使用[批量测试](luis-concept-batch-test.md)集监视预测准确性。 

## <a name="dont-add-many-example-utterances-to-intents"></a>请勿将许多话语示例添加到意向

发布应用后，仅在迭代过程中添加主动学习中的话语。 如果话语太过相似，请添加模式。 

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

为执行航班预订操作的聊天机器人创建一个“BookFlight”意向。 请勿为每条航线或每个目的地都创建一个意向。 将这些数据用作[实体](luis-concept-entity-types.md)，并在话语示例中进行标记。 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>请勿使用所有可能的值创建短语列表

在[短语列表](luis-concept-feature.md)中提供几个示例，但无需包含所有字词。 LUIS 会对上下文进行一般化并将其纳入考虑。 

## <a name="dont-add-many-patterns"></a>请勿添加许多模式

请勿添加过多[模式](luis-concept-patterns.md)。 LUIS 旨在通过少量示例快速学习。 请勿在不必要的情况下重载系统。

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>请勿使用添加的每个话语示例进行训练和发布

在进行训练和发布之前添加 10 或 15 个话语。 这样可以了解对预测准确性的影响。 添加单个话语可能不会对分数产生明显影响。 

## <a name="do-use-versions-for-each-app-iteration"></a>将版本用于每个应用迭代

每个创作周期应该在一个新[版本](luis-concept-version.md)内进行，从现有版本进行克隆。 LUIS 没有版本限制。 版本名称用作 API 路由的一部分，因此必须选取 URL 中允许的字符，不得超出版本的 10 字符计数限制。 制定版本名称策略，使版本保持有序状态。 

更多相关信息：
* 概念：[了解如何以及何时使用某个 LUIS 版本](luis-concept-version.md)
* 操作说明：[使用各个版本进行编辑和测试，而不会影响暂存应用或生产应用](luis-how-to-manage-versions.md)


## <a name="next-steps"></a>后续步骤

* 了解如何在 LUIS 应用中[规划应用](luis-how-plan-your-app.md)。
