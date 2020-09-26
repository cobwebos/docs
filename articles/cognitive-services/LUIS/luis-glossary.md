---
title: 术语表 - LUIS
description: 术语表解释使用 LUIS API 服务时可能遇到的术语。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/08/2020
ms.openlocfilehash: 0f17e489cabb047ca2e9f0ad7406c34cd292e556
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91309483"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>对包含常见词汇和概念的术语表进行语言理解
语言理解 (LUIS) 术语表解释使用 LUIS 服务时可能遇到的术语。

## <a name="active-version"></a>活动版本

活动版本是应用的[版本](luis-how-to-manage-versions.md)，当你使用 LUIS 门户对模型进行更改时，该版本就会更新。 在 LUIS 门户中，如果想对非活动版本进行更改，需先将其设为活动版本。

## <a name="active-learning"></a>主动学习

主动学习是一种机器学习方法，通过该方法，机器学习的模型被用来识别要标记的信息性新示例。 在 LUIS 中，主动学习是指从当前预测尚不清楚的终结点流量中添加言语，以改进模型。 单击“查看终结点言语”，以查看要标记的言语。

另请参阅：
* [概念性信息](luis-concept-review-endpoint-utterances.md)
* [教程：审核终结点言语](luis-tutorial-review-endpoint-utterances.md)
* 如何通过[审核终结点言语](luis-how-to-review-endpoint-utterances.md)改进 LUIS 应用

## <a name="application-app"></a>应用程序（应用）

在 LUIS 中，应用程序（或应用）是针对同一数据集构建的机器学习的模型集合，可协同工作以预测特定场景的意向和实体。 每个应用程序都有一个单独的预测终结点。

如果要构建 HR 机器人，则你可能有一组意向（例如“计划休假时间”、“查询福利”和“更新个人信息”）以及分组到单个应用程序的每个意向的实体。

## <a name="authoring"></a>创作

创作是使用 LUIS 门户或创作 API 创建、管理和部署 LUIS 应用的能力。

### <a name="authoring-key"></a>创作密钥

使用[创作密钥](luis-how-to-azure-subscription.md)创作应用。 不用于生产级别的终结点查询。 有关详细信息，请参阅[密钥限制](luis-limits.md#key-limits)。

### <a name="authoring-resource"></a>创作资源

LUIS [创作资源](luis-how-to-azure-subscription.md#azure-resources-for-luis)是可通过 Azure 获取的可管理项。 资源是你对 Azure 服务的相关创作、训练和发布功能的访问权限。 资源包括访问关联的 Azure 服务所需的身份验证、授权和安全信息。

创作资源的 Azure 类型为 `LUIS-Authoring`。

## <a name="batch-test"></a>批处理测试

批处理测试是使用一致且已知的用户话语测试集验证当前 LUIS 应用模型的功能。 批处理测试在 [JSON 格式化文件](luis-concept-batch-test.md#batch-file-format)中定义。

另请参阅：
* [概念](luis-concept-batch-test.md)
* [操作说明](luis-how-to-batch-test.md) - 运行批处理测试
* [教程](luis-tutorial-batch-testing.md) - 创建和运行批处理测试

### <a name="f-measure"></a>F 度量值

在批处理测试中，它是有关测试准确度的一个度量值。

### <a name="false-negative-fn"></a>假负 (FN)

在批处理测试中，数据点表示某些话语，其中，应用错误地预测了目标意向/实体的缺失。

### <a name="false-positive-fp"></a>假正 (FP)

在批处理测试中，数据点表示某些话语，其中，应用错误地预测了目标意向/实体的存在。

### <a name="precision"></a>精度
在批处理测试中，精准率（也称为正预测值）是相关话语在检索到的话语中所占的比例。

动物批处理测试的一项示例是预测的绵羊数除以动物总数（不管是不是绵羊，都是一样的）。

### <a name="recall"></a>Recall

在批处理测试中，召回率（也称为敏感度）是 LUIS 进行通用化的能力。

动物批处理测试的一项示例是预测的绵羊数除以可用的绵羊总数。

### <a name="true-negative-tn"></a>实报 (TN)

如果应用正确地预测为无匹配项，则为真负。 在批处理测试中，若应用确实针对某个示例预测了一个意图或实体，而该示例却并没有使用该意图或实体进行标记，真负就会出现。

### <a name="true-positive-tp"></a>实报 (TP)

如果应用正确地预测了匹配项，则为真正。 在批处理测试中，若应用针对某个示例预测了一个意图或实体，且该示例已使用该意图或实体进行了标记，真正就会出现。

## <a name="classifier"></a>分类器

分类器是一种机器学习的模型，可预测输入适合的类别或类。

[意向](#intent)是分类器的一个示例。

## <a name="collaborator"></a>协作者

从概念上讲，协作者与[参与者](#contributor)等同。 当所有者将协作者的电子邮件地址添加到不受 Azure 基于角色的访问控制 (Azure RBAC) 的应用时，将授予协作者访问权限。 如果你仍在使用协作者，则应迁移 LUIS 帐户，并使用 LUIS 创作资源以通过 RBAC 管理参与者。

## <a name="contributor"></a>参与者

参与者不是应用程序的[所有者](#owner)，但具有相同权限，可以添加、编辑和删除意向、实体和言语。 参与者为 LUIS 应用提供 Azure RBAC)  (Azure 基于角色的访问控制。

另请参阅：
* [操作说明](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource) - 添加参与者

## <a name="descriptor"></a>描述符

描述符是以前用于机器学习[功能](#features)的术语。

## <a name="domain"></a>Domain

在 LUIS 上下文中，域是一个知识领域。 你的域特定于你的方案。 不同的域使用在该域的上下文中具有意义的特定语言和术语。 例如，如果您正在构建一个用于播放音乐的应用程序，则您的应用程序将具有特定于音乐的术语和语言–如 "song、曲目、唱片集、歌词、b 方、艺术家" 等词。 有关域的示例，请参阅[预生成域](#prebuilt-domain)。

## <a name="endpoint"></a>终结点

### <a name="authoring-endpoint"></a>创作终结点

LUIS 创作终结点 URL 是你创作、训练和发布应用的位置。 终结点 URL 包含所发布的应用的区域或自定义子域以及应用 ID。

若要详细了解如何以编程方式创作应用，请参阅[开发人员参考](developer-reference-resource.md#rest-endpoints)

### <a name="prediction-endpoint"></a>预测终结点

LUIS 预测终结点 URL 是在创作并发布 [LUIS 应用](#application-app)后提交 LUIS 查询的位置。 终结点 URL 包含所发布的应用的区域或自定义子域以及应用 ID。 可以在应用的 [Azure 资源](luis-how-to-azure-subscription.md)页上找到终结点，也可以从[获取应用信息](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API 获取终结点 URL。

可以使用 LUIS 预测密钥授权访问预测终结点。

## <a name="entity"></a>实体

[实体](luis-concept-entity-types.md)是言语中的字词，描述用于实现或识别意向的信息。 如果实体很复杂，并且你希望模型标识特定的部分，可将模型分解为子实体。 例如，你可能希望模型预测某个地址，但同时预测街道、城市、州和邮政编码等子实体。 实体还可以用作模型的功能。 来自 LUIS 应用的响应将包含所预测的意向和所有实体。

### <a name="entity-extractor"></a>实体提取程序

实体提取程序（有时仅简称为提取器）是 LUIS 用来预测实体的机器学习模型的类型。

### <a name="entity-schema"></a>实体架构

实体架构是你为具有子实体的机器学习实体定义的结构。 预测终结点返回在该架构中定义的所有已提取实体和子实体。

### <a name="entitys-subentity"></a>实体的子实体

子实体是机器学习实体的子实体。

### <a name="non-machine-learning-entity"></a>非计算机学习实体

使用文本匹配来提取数据的实体：
* 列表实体
* 正则表达式实体

### <a name="list-entity"></a>列表实体

[列表实体](reference-entity-list.md)表示一组固定、封闭的相关字词及其同义词。 与机器学习实体不同，列表实体是精确匹配项。

如果列表实体中的某个字词包含在列表中，则可预测到该实体。 例如，如果你有一个名为“大小”的列表实体，并且列表中包含字词“小、中、大”，则无论上下文如何，均可预测使用字词“小”、“中”或“大”的所有言语的大小实体。

### <a name="regular-expression"></a>正则表达式

[正则表达式实体](reference-entity-regular-expression.md)表示正则表达式。 与机器学习实体不同，正则表达式实体是精确匹配项。
### <a name="prebuilt-entity"></a>预生成实体

请参阅[预生成实体](#prebuilt-entity)的预生成模型条目

## <a name="features"></a>功能

在机器学习中，功能是帮助模型识别特定概念的一种特性。 这是 LUIS 可以使用的一项提示，但不是硬性规则。

此术语也称为[机器学习功能](luis-concept-feature.md)。

这些提示与标签结合使用，以了解如何预测新数据。 LUIS 支持词组列表和使用其他模型作为功能。

### <a name="required-feature"></a>必需功能

必需功能是一种约束 LUIS 模型输出的方法。 将实体的某项功能被标记为必需时，无论机器学习模型预测什么，该功能都必须存在于要预测的实体示例中。

假设有一个示例，其中你有一个预生成数字功能，且你已将其在点菜机器人的数量实体上标记为必需。 当机器人看到 `I want a bajillion large pizzas?` 时，不管 bajillion 出现在什么上下文中，都不会被预测为一个数量。 Bajillion 不是有效的数字，数字预生成实体无法预测到它。

## <a name="intent"></a>Intent

[意向](luis-concept-intent.md)表示用户想执行的任务或操作。 系指用户输入的内容中所表达的目的或目标，比如预订航班或支付帐单。 在 LUIS 中，整体上将言语分类为意向，但是将部分言语提取为实体

## <a name="labeling-examples"></a>标签示例

标签或标记是将正示例或负示例与模型关联的过程。

### <a name="labeling-for-intents"></a>意向标签
在 LUIS 中，应用中的意向是互斥的。 这意味着，当你向意向添加言语时，它被认为是该意向的正示例，而对所有其他意向来说它则是负示例 。 负示例不应与“无”意向混淆，后者表示超出应用范围的言语。

### <a name="labeling-for-entities"></a>实体标签
在 LUIS 中，你使用一个实体作为正示例来[标记](label-entity-example-utterance.md)意向示例言语中的字词或短语。 标记显示了意向，即应预测该言语的哪些内容。 带标记的言语用于训练意向。

## <a name="luis-app"></a>LUIS 应用

请参阅[应用程序（应用）](#application-app)的定义。

## <a name="model"></a>模型

（机器学习）模型是一种对输入数据进行预测的函数。 在 LUIS 中，我们将意向分类程序和实体提取程序统称为“模型”，而将经过训练、发布和查询的模型集合统称为“应用”。

## <a name="normalized-value"></a>规范化值

向[列表](#list-entity)实体添加值。 其中每个值都可以包含一个或多个同义词的列表。 仅在响应中返回规范化值。

## <a name="overfitting"></a>过度拟合

若模型只关注特定示例而无法很好地实现通用，就会发生过度拟合。

## <a name="owner"></a>所有者

每个应用都有一个所有者，即创建应用的人。 所有者管理 Azure 门户中的应用程序的权限。

## <a name="phrase-list"></a>短语列表

[短语列表](luis-concept-feature.md)是一种特定类型的机器学习功能，包括一组值（字词或短语），它们属于同一个类，并且必须以同样的方式处理它们（例如城市或产品名称）。

## <a name="prebuilt-model"></a>预生成模型

[预生成模型](luis-concept-prebuilt-model.md)是意向、实体或二者的集合，以及带标记的示例。 可以将这些常见的预生成模型添加到应用中，以减少应用所需的模型开发工作。

### <a name="prebuilt-domain"></a>预生成域

预生成域是为特定域配置的 LUIS 应用，例如家庭自动化 (HomeAutomation) 或餐厅订位 (RestaurantReservation)。 已为此域配置意向、话语和实体。

### <a name="prebuilt-entity"></a>预生成实体

预生成实体是 LUIS 为常用信息类型提供的实体（例如数字、URL 和电子邮件）。 这些是基于公共数据创建的。 可以选择将预生成实体添加为独立实体，或添加为实体的功能

### <a name="prebuilt-intent"></a>预生成意向

预生成意向是 LUIS 为常见类型的信息提供的意向，并带有自己的已标记示例言语。

## <a name="prediction"></a>预测

预测是对 Azure LUIS 预测服务的 REST 请求，它接收新数据（用户言语），并将经过训练和发布的应用程序应用于该数据，以确定找到了哪些意向和实体。

### <a name="prediction-key"></a>预测密钥

[预测密钥](luis-how-to-azure-subscription.md)（以前称为订阅密钥）是与你在 Azure 中创建的 LUIS 服务相关联的密钥，用于授权你对预测终结点的使用。

此密钥不是创作密钥。 如果你有预测终结点密钥，则应为所有终结点请求使用该密钥，而非创作密钥。 可以在 LUIS 网站的 Azure 资源页底部的终结点 URL 内看到当前的预测密钥。 它是 subscription-key 名称/值对的值。

### <a name="prediction-resource"></a>预测资源

LUIS 预测资源是可通过 Azure 获取的可管理项。 资源是你对 Azure 服务的相关预测的访问权限。 该资源包含预测。

预测资源的 Azure 类型为 `LUIS`。

### <a name="prediction-score"></a>预测分数

[分数](luis-concept-prediction-score.md)为介于 0 和 1 之间的数字，这是一个度量值，表示系统对于特定输入言语与特定意向匹配的置信度。 接近 1 的分数表示系统对其输出非常有信心，接近 0 的分数表示系统确信输入与特定输出不匹配。 分数居中意味着系统对如何做出决定非常不确定。

以一个模型为例，该模型用于标识某些客户文本是否包含食品订单。 对于“我想点一杯咖啡”，分数可能为 1（系统非常确信这是一个订单），而对于“我队昨晚赢得了比赛”，分数为 0（系统非常确信这不是一个订单）。 对于“我们喝点茶吧”，分数可能为 0.5（不确定这是不是订单）。

## <a name="programmatic-key"></a>编程密钥

已重命名为[创作密钥](#authoring-key)。

## <a name="publish"></a>发布

[发布](luis-how-to-publish-app.md)指在暂存或生产[终结点](#endpoint)上提供一个 LUIS 活动版本。

## <a name="quota"></a>Quota

LUIS 配额是 Azure 订阅层的限制。 可同时通过每秒请求数（HTTP 状态 429）和每月请求总数（HTTP 状态 403）来限制 LUIS 配额。

## <a name="schema"></a>架构

架构包括意向和实体以及子实体。 架构是最初计划好的，之后可随时间的推移进行迭代。 架构不包括应用设置、功能或示例言语。

## <a name="sentiment-analysis"></a>情绪分析
情绪分析提供[文本分析](../text-analytics/overview.md)所提供的话语的正值或负值。

## <a name="speech-priming"></a>语音启动

语音启动使用[语音服务](../speech-service/overview.md)改进了对场景中常用的口语和短语的识别。 对于启用了语音启动的应用程序，通过为该特定应用程序创建自定义语音模型，可使用所有 LUIS 标记的示例来提高语音识别的准确性。 例如，在国际象棋比赛中，你希望确保当用户说“Move knight”时，它不会被解释为“Move night”。 LUIS 应用应包含“knight”被标记为实体的示例。

## <a name="starter-key"></a>初学者密钥

首次开始使用 LUIS 时要使用的免费密钥。

## <a name="synonyms"></a>同义词

在 LUIS [列表实体](reference-entity-list.md)中，可以创建一个规范化值，每个规范化值可以包含一个同义词列表。 例如，如果你创建一个大小实体，其规范化值有小、中、大和超大。 可以为每个值创建同义词，如下所示：

|规范化值| 同义词|
|--|--|
|小型| 小，8 盎司|
|中型| 常规，12 盎司|
|大型| 大，16 盎司|
|特大| 最大，24 盎司|

当输入中出现任何这些同义词时，该模型将返回实体的相应规范化值。

## <a name="test"></a>测试

[测试](luis-concept-test.md) LUIS 应用意味着查看模型预测。

## <a name="timezone-offset"></a>时区偏移

该终结点包含 [timezoneOffset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)。 这是要从预生成的实体 datetimeV2 删除或向其添加的分钟数。 例如，如果话语为“现在几点了？”，则返回的 datetimeV2 是发出客户端请求时的当前时间。 如果客户端请求来自聊天机器人或其他不同于聊天机器人的用户的应用程序，则应传入机器人与该用户之间的时间偏差量。

请参阅[更改预生成的 datetimeV2 实体的时区](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity)。

## <a name="token"></a>标记
[标记](luis-language-support.md#tokenization)是 LUIS 可识别的最小文本单位。 这在不同语言之间略有不同。

对于英语，标记是字母和数字的连续跨度（无空格或标点符号）。 空格不是标记。

|短语|令牌计数|说明|
|--|--|--|
|`Dog`|1|不带标点或空格的单个词。|
|`RMT33W`|1|记录定位符编号。 它可能包含数字和字母，但没有任何标点。|
|`425-555-5555`|5|电话号码。 每个标点符号都是一个标记，因此 `425-555-5555` 为 5 个标记：<br>`425`<br>`-`<br>`555`<br>`-`<br>`5555` |
|`https://luis.ai`|7|`https`<br>`:`<br>`/`<br>`/`<br>`luis`<br>`.`<br>`ai`<br>|

## <a name="train"></a>定型

[训练](luis-how-to-train.md)指让 LUIS 了解并习得自上次训练之后对活动版本所做的更改的过程。

### <a name="training-data"></a>训练数据

训练数据是训练模型所需的信息集。 这包括架构、标记的言语、功能和应用程序设置。

### <a name="training-errors"></a>训练错误

训练错误是指对训练数据的预测与标签不匹配。

## <a name="utterance"></a>话语

[言语](luis-concept-utterance.md)是用户输入，它是对话中一个句子的简短文本代表。 言语是一条自然语言短语，例如“订 2 张下周二到西雅图的票”。 通过添加实例言语来训练模型，模型在运行时对新言语进行预测

## <a name="version"></a>版本

LUIS [版本](luis-how-to-manage-versions.md)是与 LUIS 应用程序 ID 和已发布终结点关联的 LUIS 应用程序的特定实例。 每个 LUIS 应用至少有一个版本。
