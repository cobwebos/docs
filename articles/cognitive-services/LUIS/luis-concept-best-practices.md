---
title: 了解 LUIS 最佳做法- Azure | Microsoft Docs
description: 学习 LUIS 最佳做法以获取最佳结果。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: 6c4e4c7acb5083e758aa7c6b94e2464a85ae8e9e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951101"
---
# <a name="best-practices"></a>最佳实践
使用应用创作进程生成 LUIS 应用。 

* 生成语言模型
* 添加几个训练话语示例（每个意向 10 到 15 个）
* 发布 
* 从终结点进行测试 
* 添加特征

[发布](luis-how-to-publish-app.md)应用后，请采用后列创作周期：添加特征、发布和从终结点进行测试。 请勿通过添加更多话语示例来启动下一个创作周期。 这无法让 LUIS 通过真实的用户话语来学习相关模型。 

为了让 LUIS 高效学习，请勿在当前示例话语与终结点话语配对组返回较高的可信度和预测分数之前展开话语。 使用主动学习、[模式](luis-concept-patterns.md)和[短语列表](luis-concept-feature.md)来提高分数。 

## <a name="do-and-dont"></a>注意事项
下面的列表包含 LUIS 应用的最佳做法：

|操作|不要|
|--|--|
|[应定义不同的意向](#do-define-distinct-intents) |[将许多话语示例添加到意向](#dont-add-many-example-utterances-to-intents) |
|[每个意向需采用合适的详细程度](#do-find-sweet-spot-for-intents)|[将 LUIS 用作培训平台](#dont-use-luis-as-a-training-platform)|
|[以迭代方式生成应用](#do-build-the-app-iteratively)|[添加许多相同格式的话语示例，忽略其他格式](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[在后续的迭代中添加短语列表和模式](#do-add-phrase-lists-and-patterns-in-later-iterations)|[混淆意向和实体的定义](#dont-mix-the-definition-of-intents-and-entities)|
|[将话语示例添加到“None”意向](#do-add-example-utterances-to-none-intent)|[使用所有可能的值创建短语列表](#dont-create-phrase-lists-with-all-the-possible-values)|
|[利用主动学习的建议功能](#do-leverage-the-suggest-feature-for-active-learning)|[添加过多模式](#dont-add-many-patterns)|
|[监视应用的性能](#do-monitor-the-performance-of-your-app)|[使用添加的每个话语示例进行训练和发布](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>应定义不同的意向
确保每个意向的词汇特定于该意向，而不会与其他意向的词汇重叠。 例如，如果要创建一款处理行程安排（例如航班和酒店）的应用，可以选择将这些视作彼此独立的意向或视为同一意向，其中包含话语中特定数据的实体。

如果两个意向的词汇相同，请合并意向并使用实体。 

请考虑以下话语示例：

```
Book a flight
Book a hotel
```

“预订航班”和“预订酒店”使用了同样的词汇“预订”。 这就是重叠的情况，所以它应为同一意向，只是分别带有提取了“航班”和“酒店”两个不同词语的实体。 

## <a name="do-find-sweet-spot-for-intents"></a>请找到意向的平衡点
使用 LUIS 中的预测数据来判定意向是否存在重叠的情况。 重叠的意向会困扰 LUIS。 结果是评分最高的意向会与另一个意向非常接近。 由于 LUIS 不会在每次训练的数据中使用完全相同的路径，所以重叠意向可能会在训练中排到第一或第二的位置。 各意向的话语分数应相互拉开差距以避免出现上述情况。 更好地区分意向可以使得每次训练都得出预期的最高分意向。 
 
## <a name="do-build-the-app-iteratively"></a>以迭代方式生成应用
保留一个独立的盲测测试集，不将其用作[话语示例](luis-concept-utterance.md)或终结点话语。 针对测试集不断改进应用。 调整测试集以反映真实的用户话语。 使用此盲测测试集来评估每次迭代。 

开发人员应备有三个数据集。 第一个是用于构建模型的话语示例集。 第二个数据集用于在终结点测试模型。 第三个是[批处理测试](luis-how-to-batch-test.md)中使用的盲测数据集。 最后一个数据集不用于训练应用程序，也不在终结点上发送。  

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>应在后续的迭代中添加短语列表和模式
[短语列表](luis-concept-feature.md)可用于定义包含与应用域有关的字词的字典。 从几个字词开始创建短语列表，然后使用推荐功能，让 LUIS 了解更多词汇表中的字词。 请勿将所有字词都添加到词汇表中，因为短语列表不是完全匹配的。 

终结点中真实用户的话语之间非常类似，可能会体现出字词选择和位置放置的模式。 [模式](luis-concept-patterns.md)功能会同时考虑此字词选择和位置放置以及正则表达式，以提升预测的准确性。 模式中的正则表达式允许在忽略字词和标点的情况下依然与模式匹配。 

在模式中为标点使用可选语法，以便忽略标点。

请勿在应用接收到终结点请求之前应用这些做法，因为这会导致置信度出现偏差。  

## <a name="do-add-example-utterances-to-none-intent"></a>务必将话语示例添加至“None”意向
这是回退意向，指示应用程序以外的所有内容。 针对 LUIS 应用其余部分的每 10 个话语示例，向“None”意向中添加一个话语示例。

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>应利用主动学习的建议功能
定期使用[主动学习](luis-how-to-review-endoint-utt.md)的“查看终结点话语”功能，而不是将更多话语示例添加到意向。 因为应用会不断接收终结点话语，所以此列表会不断变化。

## <a name="do-monitor-the-performance-of-your-app"></a>应监视应用的性能
使用测试集监视预测准确性。 

## <a name="dont-add-many-example-utterances-to-intents"></a>请勿将许多话语示例添加到意向
发布应用后，仅在迭代过程中添加主动学习中的话语。 如果话语太过相似，请添加模式。 

## <a name="dont-use-luis-as-a-training-platform"></a>请勿将 LUIS 用作培训平台
LUIS 特定于语言模型的域。 但并不意味着将其用作常规的培训平台。 

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

## <a name="next-steps"></a>后续步骤

* 了解如何在 LUIS 应用中[规划应用](luis-how-plan-your-app.md)。