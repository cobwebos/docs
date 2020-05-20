---
title: 术语表 - LUIS
description: 术语表解释使用 LUIS API 服务时可能遇到的术语。
ms.topic: reference
ms.date: 05/08/2020
ms.openlocfilehash: 1513099decc21a7d219bfcb84563619640028550
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681617"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>对包含常见词汇和概念的术语表进行语言理解
语言理解（LUIS）词汇表说明了使用 LUIS 服务时可能会遇到的术语。

## <a name="active-version"></a>活动版本

活动版本是在使用 LUIS 门户对模型进行更改时更新的应用[版本](luis-how-to-manage-versions.md)。 在 LUIS 门户中，如果要对非活动版本的版本进行更改，则需要先将该版本设置为 "活动"。

## <a name="active-learning"></a>主动学习

主动学习是机器学习的一项技术，其中的计算机学习模型用于标识要标记的信息性新示例。 在 LUIS 中，活动学习指的是从终结点流量中添加最谈话，该终结点的当前预测不清楚，无法改善您的模型。 单击 "查看终结点最谈话"，以查看要标记的最谈话。

另请参阅：
* [概念性信息](luis-concept-review-endpoint-utterances.md)
* [教程查看终结点最谈话](luis-tutorial-review-endpoint-utterances.md)
* 如何通过[查看端点最谈话](luis-how-to-review-endpoint-utterances.md)改善 LUIS 应用

## <a name="application-app"></a>应用程序（应用）

在 LUIS 中，应用程序（或应用程序）是在同一数据集上构建的机器学习模型的集合，可配合使用来预测特定方案的意图和实体。 每个应用程序都有一个单独的预测终结点。

如果要构建 HR 机器人，你可能会有一组方法，例如 "计划休假时间"、"查询权益" 和 "更新个人信息"，以及将你分组到单个应用程序中的每个意向的实体。

## <a name="authoring"></a>创作

创作是指使用 LUIS 门户或创作 Api 创建、管理和部署 LUIS 应用程序的功能。

### <a name="authoring-key"></a>创作密钥

[创作密钥](luis-concept-keys.md)用于创作应用程序。 不用于生产级别的终结点查询。 有关详细信息，请参阅[密钥限制](luis-limits.md#key-limits)。

### <a name="authoring-resource"></a>创作资源

你的 LUIS[创作资源](luis-concept-keys.md#azure-resources-for-luis)是可通过 Azure 获取的可管理项。 资源是对 Azure 服务的关联创作、培训和发布功能的访问权限。 资源包括访问关联的 Azure 服务所需的身份验证、授权和安全信息。

创作资源具有的 Azure "种类" `LUIS-Authoring` 。

## <a name="batch-test"></a>批处理测试

批处理测试是指使用用户最谈话的一致且已知测试集验证当前 LUIS 应用的模型。 批处理测试在 [JSON 格式化文件](luis-concept-batch-test.md#batch-file-format)中定义。

另请参阅：
* [概念](luis-concept-batch-test.md)
* [如何](luis-how-to-batch-test.md)运行批处理测试
* [教程](luis-tutorial-batch-testing.md)-创建和运行批测试

### <a name="f-measure"></a>F 度量值

在批处理测试中，它是有关测试准确度的一个度量值。

### <a name="false-negative-fn"></a>假负（FN）

在批处理测试中，数据点表示某些话语，其中，应用错误地预测了目标意向/实体的缺失。

### <a name="false-positive-fp"></a>假正（FP）

在批处理测试中，数据点表示某些话语，其中，应用错误地预测了目标意向/实体的存在。

### <a name="precision"></a>精度
在批处理测试中，精准率（也称为正预测值）是相关话语在检索到的话语中所占的比例。

动物批处理测试的一个示例是预测的羊数除以动物总数（羊和非羊等）。

### <a name="recall"></a>召回率

在批处理测试中，召回率（也称为敏感度）是 LUIS 进行通用化的能力。

动物批处理测试的一个示例是预测的羊数除以可用的总羊数。

### <a name="true-negative-tn"></a>实报 (TN)

如果应用正确预测无匹配项，则为 true。 在批处理测试中，当您的应用程序对未使用该意向或实体进行标记的示例预测意向或实体时，会出现 true 负数。

### <a name="true-positive-tp"></a>实报 (TP)

True （TP）在应用正确预测匹配项时是真正的正值。 在批处理测试中，如果应用程序为使用该意向或实体标记的示例预测意向或实体，则会出现 true。

## <a name="classifier"></a>分类器

分类器是一种机器学习的模型，可预测输入适合的类别或类。

[目的](#intent)是分类器的一个示例。

## <a name="collaborator"></a>协作者

协作者在概念上与[参与者](#contributor)相同。 当所有者将协作者的电子邮件地址添加到不受基于角色的访问（RBAC）控制的应用时，将授予协作者访问权限。 如果你仍在使用协作者，则应迁移你的 LUIS 帐户，并使用 LUIS 创作资源来使用 RBAC 管理参与者。

## <a name="contributor"></a>参与者

参与者不是应用的[所有者](#owner)，但具有用于添加、编辑和删除意向、实体、最谈话的相同权限。 参与者为 LUIS 应用提供基于角色的访问（RBAC）。

另请参阅：
* [如何](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource)添加参与者

## <a name="descriptor"></a>描述符

描述符是以前用于机器学习[功能](#features)的术语。

## <a name="domain"></a>域

在 LUIS 上下文中，域是一个知识领域。 你的域特定于你的方案。 不同域使用在域上下文中有意义的特定语言和术语。 例如，如果您正在构建一个用于播放音乐的应用程序，则您的应用程序将具有特定于音乐的术语和语言–如 "song、曲目、唱片集、歌词、b 方、艺术家" 等词。 有关域的示例，请参阅[预构建域](#prebuilt-domain)。

## <a name="endpoint"></a>终结点

### <a name="authoring-endpoint"></a>创作终结点

LUIS 创作终结点 URL 是你创作、培训和发布你的应用程序的位置。 终结点 URL 包含已发布应用的区域或自定义子域以及应用 ID。

详细了解如何通过[开发人员参考](developer-reference-resource.md#rest-endpoints)以编程方式创作应用

### <a name="prediction-endpoint"></a>预测终结点

LUIS 预测终结点 URL 是在创作和发布[LUIS 应用](#application-app)后提交 LUIS 查询的位置。 终结点 URL 包含已发布应用的区域或自定义子域以及应用 ID。 可以在应用的 " **[Azure 资源](luis-how-to-azure-subscription.md)**" 页上找到终结点，也可以从[获取应用信息](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37)API 获取终结点 URL。

您可以使用 LUIS 预测密钥授权访问预测终结点。

## <a name="entity"></a>实体

[实体](luis-concept-entity-types.md)是最谈话中的单词，用于描述用于满足或确定目的的信息。 如果你的实体很复杂，并且你希望模型标识特定部分，则可以将模型分解为子实体。 例如，您可能希望模型预测某个地址，同时还需要子实体街道、city、state 和 zipcode。 实体还可以用作模型的功能。 来自 LUIS 应用的响应将包含预测意向和所有实体。

### <a name="entity-extractor"></a>实体提取器

实体提取器有时只是众所周知的，因为提取器是 LUIS 用于预测实体的机器学习模型的类型。

### <a name="entity-schema"></a>实体架构

实体架构是为具有子实体的计算机学习实体定义的结构。 预测终结点返回在该架构中定义的所有已提取实体和子实体。

### <a name="entitys-subentity"></a>实体的子实体

子实体是机器学习实体的子实体。

### <a name="non-machine-learning-entity"></a>非计算机学习实体

使用文本匹配来提取数据的实体：
* 列表实体
* 正则表达式实体

### <a name="list-entity"></a>列表实体

[列表实体](reference-entity-list.md)表示固定的、已关闭的相关单词集及其同义词。 与已加工的已学习实体不同，列出实体完全匹配。

如果列表中的某个单词包含在列表中，则将预测该实体。 例如，如果您有一个名为 "size" 的列表实体，并且在该列表中有 "small，medium，大型" 字样，则将对所有最谈话预测 size 实体，而不管上下文如何，都将使用单词 "small"、"medium" 或 "大"。

### <a name="regular-expression"></a>正则表达式

[正则表达式实体](reference-entity-regular-expression.md)表示正则表达式。 与所进行的已学习实体不同，正则表达式实体完全匹配。
### <a name="prebuilt-entity"></a>预生成实体

请参阅预先生成的模型条目以获取[预生成实体](#prebuilt-entity)

## <a name="features"></a>功能

在机器学习中，功能是帮助模型识别特定概念的一种特性。 LUIS 可以使用，但不能使用硬规则。

此术语也称为**[机器学习功能](luis-concept-feature.md)**。

这些提示与标签结合使用，以了解如何预测新数据。 LUIS 支持词组列表和使用其他模型作为功能。

### <a name="required-feature"></a>必需的功能

必需的功能是一种约束 LUIS 模型输出的方法。 当实体的功能标记为 "必需" 时，该功能必须存在于要预测的实体的示例中，而不考虑计算机学习的模型预测的内容。

假设有一个示例，其中有一个预先生成的功能，该功能在菜单顺序机器人的 "数量" 实体上标记为 "必需"。 当你的机器人看到时 `I want a bajillion large pizzas?` ，bajillion 不会被预测为数量，而不考虑它出现的上下文。 Bajillion 不是有效的数字，并且不会被数字预生成实体预测。

## <a name="intent"></a>Intent

[意向](luis-concept-intent.md)代表用户要执行的任务或操作。 这是在用户输入中表示的目的或目标，如预订航班或支付帐单。 在 LUIS 中，作为一个整体的查询文本归类为意向，而查询文本的部分作为实体提取

## <a name="labeling-examples"></a>标记示例

标记或标记是将正示例或负示例与模型关联的过程。

### <a name="labeling-for-intents"></a>用于意向的标签
在 LUIS 中，应用中的意向是互斥的。 这意味着，当你将查询文本添加到某个意向时，它将被视为该意向的_正_示例和所有其他意图的_负_示例。 负示例不应与 "无" 意向混淆，这表示应用范围之外的最谈话。

### <a name="labeling-for-entities"></a>实体标签
在 LUIS 中，可以在意向的示例查询文本中[标记](label-entity-example-utterance.md)一个词或短语，其中实体为_正_示例。 标记显示了该查询文本应预测的内容。 标记的最谈话用于训练目的。

## <a name="luis-app"></a>LUIS 应用

请参阅[应用程序的定义（应用）](#application-app)。

## <a name="model"></a>“模型”

（机器学习）模型是一种对输入数据进行预测的函数。 在 LUIS 中，我们以 "模型" 的形式引用意向分类器和实体提取器，并将一组以 "应用" 形式进行定型、发布和查询的模型作为一个整体来引用。

## <a name="normalized-value"></a>规范化值

向[列表](#list-entity)实体添加值。 其中每个值都可以包含一个或多个同义词的列表。 仅在响应中返回规范化值。

## <a name="overfitting"></a>过度拟合

当模型关注特定的示例并且无法很好地通用化时，会发生过度拟合。

## <a name="owner"></a>所有者

每个应用都有一个所有者，即创建应用的人。 所有者管理 Azure 门户中的应用程序的权限。

## <a name="phrase-list"></a>短语列表

[短语列表](luis-concept-feature.md)是一种特定类型的机器学习功能，其中包括属于同一类的一组值（词或短语），必须按类似方式处理（例如，城市或产品的名称）。

## <a name="prebuilt-model"></a>预构建模型

[预构建的模型](luis-concept-prebuilt-model.md)是一个目的、实体或二者的集合，以及标记的示例。 可以将这些常见的预生成模型添加到应用，以减少应用所需的模型开发工作。

### <a name="prebuilt-domain"></a>预生成域

预生成域是为特定域配置的 LUIS 应用，例如家庭自动化 (HomeAutomation) 或餐厅订位 (RestaurantReservation)。 已为此域配置意向、话语和实体。

### <a name="prebuilt-entity"></a>预生成实体

预生成实体是 LUIS 为常用信息类型提供的实体（例如数字、URL 和电子邮件）。 这些是基于公共数据创建的。 您可以选择将预生成的实体添加为独立实体，或添加为实体的功能

### <a name="prebuilt-intent"></a>预建意向

预先构建的意图是一种意向 LUIS，它为常见类型的信息提供，并附带其自己标记的示例最谈话。

## <a name="prediction"></a>预测

预测是对使用新数据（用户查询文本）的 Azure LUIS 预测服务的 REST 请求，并将定型和发布的应用程序应用于该数据，以确定找到了哪些意图和实体。

### <a name="prediction-key"></a>预测密钥

[预测密钥](luis-concept-keys.md)（以前称为订阅密钥）是与在 Azure 中创建的 LUIS 服务关联的密钥，该服务授权你使用预测终结点。

此密钥不是创作密钥。 如果有一个预测终结点密钥，则它应用于任何终结点请求，而不是创作密钥。 你可以在 LUIS 网站中 "Azure 资源" 页底部的终结点 URL 内查看当前的预测密钥。 它是订阅密钥名称/值对的值。

### <a name="prediction-resource"></a>预测资源

LUIS 预测资源是可通过 Azure 获取的可管理项。 资源是对 Azure 服务的关联预测的访问权限。 该资源包含预测。

预测资源具有的 Azure "种类" `LUIS` 。

### <a name="prediction-score"></a>预测分数

[分数](luis-concept-prediction-score.md)是0和1之间的数字，它是一种衡量系统如何相信特定输入查询文本与特定意向相匹配的数字。 接近1的分数表示系统对其输出非常有把握，分数越接近0，表示系统确信输入与特定输出不匹配。 中间的分数表示系统不能确定如何做出决定。

例如，使用一个模型来确定某些客户文本是否包含食物订单。 它可能为 "我想要定购一个咖啡" （系统非常确信这是订单），分数为0（即 "我的团队在过去的某个时间赢得游戏）" 的分数（系统非常确信这不是订单）。 对于 "我们有一些茶"，它的分数可能为0.5，而不确定这是不是订单。

## <a name="programmatic-key"></a>编程密钥

已重命名为[创作密钥](#authoring-key)。

## <a name="publish"></a>发布

[发布](luis-how-to-publish-app.md)是指使 LUIS 活动版本在过渡或生产[终结点](#endpoint)上可用。

## <a name="quota"></a>Quota

LUIS 配额是 Azure 订阅层的限制。 可同时通过每秒请求数（HTTP 状态 429）和每月请求总数（HTTP 状态 403）来限制 LUIS 配额。

## <a name="schema"></a>架构

您的架构包含您的子实体和实体。 最初计划该架构，然后在一段时间内循环访问。 该架构不包括应用程序设置、功能或示例最谈话。

## <a name="sentiment-analysis"></a>情绪分析
情绪分析提供[文本分析](../text-analytics/overview.md)所提供的话语的正值或负值。

## <a name="speech-priming"></a>语音启动

语音填充改进了在你的方案中经常用于[语音服务](../speech-service/overview.md)的字词和短语识别。 对于启用了语音填充的应用程序，通过为此特定应用程序创建自定义语音模型，可使用所有标记为的示例来提高语音识别的准确性。 例如，你想要确保当用户显示 "Move knight" 时，它不会被解释为 "移动晚间"。 LUIS 应用应包含“knight”被标记为实体的示例。

## <a name="starter-key"></a>初学者密钥

首次开始使用 LUIS 时要使用的免费密钥。

## <a name="synonyms"></a>同义词

在 LUIS 中，可以创建一个规范化值，每个[实体](reference-entity-list.md)都有一个同义词列表。 例如，如果创建的大小实体的值为小、中、大和超大。 可以为每个值创建同义词，如下所示：

|Nomalized 值| 同义词|
|--|--|
|小| 这只是一个8盎司|
|中| 常规，12盎司|
|大| 大，16盎司|
|Xtra 大型| 最大的1盎司|

当在输入中出现任何同义词时，该模型将返回实体的规范化值。

## <a name="test"></a>测试

[测试](luis-concept-test.md)LUIS 应用意味着查看模型预测。

## <a name="timezone-offset"></a>时区偏移

终结点包含[timezoneOffset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)。 这是要从预生成的实体 datetimeV2 删除或向其添加的分钟数。 例如，如果话语为“现在几点了？”，则返回的 datetimeV2 是发出客户端请求时的当前时间。 如果客户端请求来自聊天机器人或其他不同于聊天机器人的用户的应用程序，则应传入机器人与该用户之间的时间偏差量。

请参阅[更改预生成的 datetimeV2 实体的时区](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity)。

## <a name="token"></a>令牌
[标记](luis-language-support.md#tokenization)是 LUIS 可以识别的最小文本单位。 这种不同的语言略有不同。

对于**英语**，标记是字母和数字的连续跨度（无空格或标点）。 空格不是标记。

|文字|令牌计数|说明|
|--|--|--|
|`Dog`|1|不带标点或空格的单个单词。|
|`RMT33W`|1|记录定位器号。 它可能包含数字和字母，但没有任何标点。|
|`425-555-5555`|5|电话号码。 每个标点符号都是一个标记，因此将 `425-555-5555` 是5个标记：<br>`425`<br>`-`<br>`555`<br>`-`<br>`5555` |
|`https://luis.ai`|7|`https`<br>`:`<br>`/`<br>`/`<br>`luis`<br>`.`<br>`ai`<br>|

## <a name="train"></a>定型

[训练](luis-how-to-train.md)是在上一次培训后，对活动版本的任何更改进行 LUIS 的过程。

### <a name="training-data"></a>训练数据

定型数据是训练模型所需的信息集。 这包括架构、标记为最谈话、功能和应用程序设置。

### <a name="training-errors"></a>定型错误

定型错误是对与标签不符的定型数据的预测。

## <a name="utterance"></a>话语

[查询文本](luis-concept-utterance.md)是会话中句子的简短文本表示形式的用户输入。 这是一种自然语言短语，如 "将图书2入场券给西雅图的第二个星期二"。 示例最谈话添加到定型模型，模型在运行时在新查询文本上预测

## <a name="version"></a>版本

LUIS[版本](luis-how-to-manage-versions.md)是与 LUIS 应用 ID 和已发布终结点关联的 LUIS 应用程序的特定实例。 每个 LUIS 应用至少有一个版本。
