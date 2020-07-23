---
title: 有关生成 LUIS 应用的最佳做法
description: 学习最佳做法以从 LUIS 应用的模型中获取最佳结果。
ms.topic: conceptual
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: 9c22256f6fac3647108b7078b774338d7f22d29a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683757"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>有关生成语言理解 (LUIS) 应用的最佳做法
使用应用创作过程生成 LUIS 应用：

* 生成语言模型（意向和实体）
* 添加几个训练言语示例（每个意向 15 到 30 个）
* 发布到终结点
* 从终结点进行测试

[发布](luis-how-to-publish-app.md)应用后，使用开发生命周期从终结点添加功能、发布和测试。 下一个创作周期不要从添加更多示例言语开始，否则 LUIS 无法根据实际用户言语学习你的模型。

在当前的示例言语与终结点言语集返回可信的较高预测评分之前，请不要展开言语。 使用[主动学习](luis-concept-review-endpoint-utterances.md)提高评分。




## <a name="do-and-dont"></a>注意事项
下面的列表包含 LUIS 应用的最佳做法：

|要求事项|禁止事项|
|--|--|
|[规划架构](#do-plan-your-schema)|[无计划生成和发布](#dont-publish-too-quickly)|
|[应定义不同的意向](#do-define-distinct-intents)<br>[向意向添加功能](#do-add-features-to-intents)<br>
[使用计算机学习的实体](#do-use-machine-learned-entities) |[将许多话语示例添加到意向](#dont-add-many-example-utterances-to-intents)<br>[使用少量或简单的实体](#dont-use-few-or-simple-entities) |
|[每个意向需采用合适的详细程度](#do-find-sweet-spot-for-intents)|[将 LUIS 用作培训平台](#dont-use-luis-as-a-training-platform)|
|[利用版本以迭代方式生成应用](#do-build-your-app-iteratively-with-versions)<br>[生成模型分解的实体](#do-build-for-model-decomposition)|[添加许多相同格式的话语示例，忽略其他格式](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[在后续迭代中添加模式](#do-add-patterns-in-later-iterations)|[混淆意向和实体的定义](#dont-mix-the-definition-of-intents-and-entities)|
|[跨所有意向来平衡话语](#balance-your-utterances-across-all-intents)，None 意向除外。<br>[将示例言语添加到 None 意向](#do-add-example-utterances-to-none-intent)|[使用所有可能的值创建短语列表](#dont-create-phrase-lists-with-all-the-possible-values)|
|[利用主动学习的建议功能](#do-leverage-the-suggest-feature-for-active-learning)|[添加的模式过多](#dont-add-many-patterns)|
|[通过批处理测试监视应用的性能](#do-monitor-the-performance-of-your-app)|[使用添加的每个话语示例进行训练和发布](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-plan-your-schema"></a>规划架构

开始构建应用的架构之前，应确定计划使用此应用的内容和位置。 您的计划越彻底和具体，应用就越好。

* 调查目标用户
* 定义端到端角色以表示你的应用程序-语音、头像、问题处理（主动、被动）
* 标识用户交互（文本和语音），通道用于处理现有解决方案或为此应用创建新的解决方案
* 端到端用户旅程
    * 此应用应如何操作？ * 其工作原理的优先级是什么？
    * 主要用例有哪些？
* 收集数据-[了解](data-collection.md)收集和准备数据

## <a name="do-define-distinct-intents"></a>应定义不同的意向
确保每个意向的词汇特定于该意向，而不会与其他意向的词汇重叠。 例如，如果要创建一款处理行程安排（例如航班和酒店）的应用，可以选择将这些主题领域视作彼此独立的意向或视为同一意向，其中包含话语中特定数据的实体。

如果两个意向的词汇相同，请合并意向并使用实体。

请考虑以下话语示例：

|示例陈述|
|--|
|预订航班|
|预订酒店|

`Book a flight` 和 `Book a hotel` 使用相同的词汇 `book a `。 此格式相同，因此它应该是同一意向，只是使用不同的词语（`flight` 和 `hotel`）作为提取的实体。

## <a name="do-add-features-to-intents"></a>向意向添加功能

功能描述了意图的概念。 功能可以是对该意向非常重要的单词的短语列表，也可以是对该意向非常重要的实体。

## <a name="do-find-sweet-spot-for-intents"></a>请找到意向的平衡点
使用 LUIS 中的预测数据来判定意向是否存在重叠的情况。 重叠的意向会困扰 LUIS。 结果是评分最高的意向会与另一个意向非常接近。 由于 LUIS 不会在每次训练的数据中使用完全相同的路径，所以重叠意向可能会在训练中排到第一或第二的位置。 各意向的话语分数应相互拉开差距以避免出现上述翻转情况。 更好地区分意向可以使得每次训练都得出预期的最高分意向。

## <a name="do-use-machine-learned-entities"></a>使用计算机学习的实体

计算机学习的实体针对你的应用进行定制，并要求标记为成功。 如果未使用计算机学习的实体，可能是因为使用了错误的工具。

计算机学习的实体可以使用其他实体作为功能。 这些其他实体可以是自定义实体（如正则表达式实体或列表实体），也可以使用预生成实体作为功能。

了解[有效的机器学习实体](luis-concept-entity-types.md#effective-machine-learned-entities)。

<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>使用版本以迭代方式生成应用

每个创作周期应该在一个新[版本](luis-concept-version.md)内进行，从现有版本进行克隆。

## <a name="do-build-for-model-decomposition"></a>为模型分解生成

模型分解的一个典型流程是：

* 基于客户端应用的用户意向创建**意向**
* 基于实际用户输入添加 15-30 个示例言语
* 标记示例言语中的顶层数据概念
* 将数据概念分解为子实体
* 向子实体添加功能
* 向意向添加功能

创建意向并添加示例言语后，以下示例描述实体分解。

首先标识要在言语中提取的整个数据概念。 这是您的机器学习实体。 然后将短语分解成各个组成部分。 这包括识别子实体和功能。

例如，如果想要提取地址，则可以调用顶级机器学习实体 `Address` 。 在创建地址时，标识其某些子实体，如街道地址、城市、省/市/自治区和邮政编码。

继续通过以下方式分解这些元素：
* 作为正则表达式实体添加邮政编码的必需功能。
* 将街道地址分解到各个部分：
    * 具有编号的预生成实体的所需功能的**街道号**。
    * **街道名称**。
    * 具有列表实体所需功能的**街道类型**，其中包括 "公路"、"圆形"、"道路" 和 "路线" 等词。

可以使用 V3 创作 API 进行模型分解。

## <a name="do-add-patterns-in-later-iterations"></a>在后续的迭代中添加模式

在添加[模式](luis-concept-patterns.md)之前，应该了解应用的行为方式，因为模式比示例言语的权重更大，会影响置信度。

了解应用的行为方式后，添加要应用于应用的模式。 不需要在每个[迭代](luis-concept-app-iteration.md)中添加模式。

在设计模型之初就添加它们并没有什么坏处，但如果在使用言语测试模型之后再添加，则可以更容易地看出每个模式如何改变模型。

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>跨所有意向平衡言语

为了使 LUIS 预测准确，每个意向（None 意向除外）中示例话语的数量必须相同（相对说来）。

如果一个意向有 100 个示例话语，另一个意向有 20 个示例话语，则 100 个话语的意向的预测准确率会更高。

## <a name="do-add-example-utterances-to-none-intent"></a>务必将话语示例添加至“None”意向

此意向是回退意向，指示应用程序以外的所有内容。 针对 LUIS 应用其余部分的每 10 个话语示例，向“None”意向中添加一个话语示例。

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>应利用主动学习的建议功能

定期使用[主动学习](luis-how-to-review-endpoint-utterances.md)的“查看终结点话语”功能，而不是将更多话语示例添加到意向****。 因为应用会不断接收终结点话语，所以此列表会不断变化。

## <a name="do-monitor-the-performance-of-your-app"></a>应监视应用的性能

使用[批量测试](luis-concept-batch-test.md)集监视预测准确性。

保留一个独立的言语集，不将其用作[示例言语](luis-concept-utterance.md)或终结点言语。 针对测试集不断改进应用。 调整测试集以反映真实的用户话语。 使用此测试集来评估每次迭代的或每个版本的应用。

## <a name="dont-publish-too-quickly"></a>发布速度太快

如果你的应用程序发布速度过快，无需进行[适当的规划](#do-plan-your-schema)，则可能会导致一些问题，例如：

* 在实际方案中，你的应用程序将无法正常运行。
* 架构（意向和实体）不合适，如果在架构后开发了客户端应用逻辑，则可能需要从头开始重新编写。 这会导致意外的延迟，并为正在处理的项目带来额外的费用。
* 添加到模型中的最谈话可能会导致难以调试和确定的示例查询文本集。 它还会在您提交到某个架构后消除歧义。

## <a name="dont-add-many-example-utterances-to-intents"></a>请勿将许多话语示例添加到意向

发布应用后，仅在开发生命周期过程中添加主动学习中的言语。 如果话语太过相似，请添加模式。

## <a name="dont-use-few-or-simple-entities"></a>不要使用少量或简单的实体

实体是为数据提取和预测而生成的。 这一点非常重要，因为每个意向都具有用于描述数据的机器学习实体。 这可以帮助 LUIS 预测意向，即使客户端应用程序不需要使用提取的实体。

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

为预订航班的机器人创建一个“BookFlight”意向****。 请勿为每条航线或每个目的地都创建一个意向。 将这些数据用作[实体](luis-concept-entity-types.md)，并在话语示例中进行标记。

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>请勿使用所有可能的值创建短语列表

提供[短语列表](luis-concept-feature.md)中的几个示例，而不是每个单词或短语。 LUIS 会对上下文进行一般化并将其纳入考虑。

## <a name="dont-add-many-patterns"></a>请勿添加许多模式

请勿添加过多[模式](luis-concept-patterns.md)。 LUIS 旨在通过少量示例快速学习。 请勿在不必要的情况下重载系统。

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>请勿使用添加的每个话语示例进行训练和发布

在进行训练和发布之前添加 10 或 15 个话语。 这样可以了解对预测准确性的影响。 添加单个话语可能不会对分数产生明显影响。

## <a name="next-steps"></a>后续步骤

* 了解如何在 LUIS 应用中[规划应用](luis-how-plan-your-app.md)。
