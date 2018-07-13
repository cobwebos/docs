---
title: Azure 常见问题解答中的语言理解 (LUIS) | Microsoft Docs
description: 获取有关语言理解 (LUIS) 的常见问题的解答
author: v-geberr
manager: kaiqb
services: cognitive-services
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: fd63ffd312e3ac17a6376eb3c9bef8f1978e3935
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333609"
---
# <a name="language-understanding-faq"></a>语言理解常见问题解答

本文包含有关语言理解 (LUIS) 的常见问题的解答。

## <a name="luis-authoring"></a>LUIS 创作

### <a name="what-are-the-luis-best-practices"></a>LUIS 最佳做法是什么？ 
从[创作周期](luis-concept-app-iteration.md)开始，然后阅读[最佳做法](luis-concept-best-practices.md)。 

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>若要开始在 LUIS 中生成应用，最佳方式是什么？

生成应用的最佳方式是使用[增量过程](luis-concept-app-iteration.md)。 

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>若要对应用的意向建模，什么做法较好？ 是应创建较具体的意向还是较宽泛的意向？

选择的意向不应太宽泛，免得互相重叠，也不应太具体，免得 LUIS 难以区分相似的意向。 进行 LUIS 建模时，一项最佳做法是创建差异性的具体意向。

### <a name="is-it-important-to-train-the-none-intent"></a>训练 None 意向是否很重要？

是的，在向其他意向添加更多标签时，不妨使用更多表述训练 **None** 意向。 每次向某个意向添加 10 个标签时，可以向 **None** 添加 1 到 2 个标签，这种比例比较适当。 此比例可以提高 LUIS 的区分能力。

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>如何更正表述中的拼写错误？

请参阅[必应拼写检查 API V7](luis-tutorial-bing-spellcheck.md) 教程。 LUIS 强制实施必应拼写检查 API V7 施加的限制。 

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>如何以编程方式编辑 LUIS 应用？
若要以编程方式编辑 LUIS 应用，请使用[创作 API](https://aka.ms/luis-authoring-apis)。 请参阅[调用 LUIS 创作 API](./luis-quickstart-node-add-utterance.md) 和[使用 Node.js 以编程方式生成 LUIS 应用](./luis-tutorial-node-import-utterances-csv.md)，通过示例了解如何调用创作 API。 创作 API 要求使用[创作密钥](luis-concept-keys.md#authoring-key)而不是终结点密钥。 编程创作最多允许每个月进行 1,000,000 次调用，每秒处理五个事务。 若要详细了解可与 LUIS 配合使用的密钥，请参阅[管理密钥](./luis-concept-keys.md)。

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>提供了正则表达式匹配的“模式”功能位于何处？
以前的 **“模式”功能**目前已弃用，代之以现在的**[模式](luis-concept-patterns.md)** 功能。 

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>如何使用实体来拉取出正确的数据？ 
请参阅[实体](luis-concept-entity-types.md)和[数据提取](luis-concept-data-extraction.md)。

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>示例表述的变体是否应包括标点？ 
请将不同的变体作为示例表述添加到意向，或者使用[忽略标点的语法](luis-concept-patterns.md#pattern-syntax)添加示例表述的模式。 

## <a name="luis-endpoint"></a>LUIS 终结点

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>为什么 LUIS 向查询添加空格时，会将其添加到单词的周围或中间？
LUIS 根据[区域性](luis-supported-languages.md#tokenization)将表述[标记化](luis-glossary.md#token)。 原始值和标记化值均可用于[数据提取](luis-concept-data-extraction.md#tokenized-entity-returned)。

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>如何创建并分配 LUIS 终结点密钥？
根据[服务](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)级别在 Azure 中[创建终结点密钥](luis-how-to-azure-subscription.md#create-luis-endpoint-key)。 在**[发布](publishapp.md)** 页中[分配密钥](Manage-keys.md#assign-endpoint-key)。 此操作没有相应的 API。 然后，必须更改针对此终结点的 HTTP 请求才能[使用新终结点密钥](luis-concept-keys.md#use-endpoint-key-in-query)。

### <a name="how-do-i-interpret-luis-scores"></a>如何解释 LUIS 分数？ 
系统应该使用最高得分意向，不管其值如何。 例如，分数低于 0.5（不到 50%）不一定意味着 LUIS 的置信度低。 提供更多训练数据可以提高最可能意向的分数。

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>为何在应用的“仪表板”中看不到终结点命中数？
应用的“仪表板”中的终结点总命中数会定期更新，但 Azure 门户中与 LUIS 订阅密钥相关联的指标的更新频率更高。 

如果在“仪表板”中看不到更新的终结点命中数，请登录到 Azure 门户，找到与 LUIS 订阅密钥相关联的资源，然后打开“指标”以选择“总调用数”指标。 如果将订阅密钥用于多个 LUIS 应用，则 Azure 门户中的指标会显示使用该密钥的所有 LUIS 应用进行的调用的聚合数。

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>我的 LUIS 应用昨天还可以正常使用，但今天却出现 403 错误。 我没有更改应用。 如何解决问题？ 
根据下一常见问题解答中的[说明](#how-do-i-create-and-assign-a-luis-endpoint-key)创建一个 LUIS 终结点密钥，然后将其分配给应用。 然后，必须更改针对此终结点的 HTTP 请求才能[使用新终结点密钥](luis-concept-keys.md#use-endpoint-key-in-query)。

### <a name="how-do-i-secure-my-luis-endpoint"></a>如何保护 LUIS 终结点安全？ 
请参阅[保护终结点安全](luis-concept-security.md#securing-the-endpoint)。

## <a name="working-within-luis-limits"></a>在 LUIS 限制内操作

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>LUIS 应用可以支持的最大意向和实体数是多少？
请查看[边界](luis-boundaries.md)参考文档。

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>我希望生成一个 LUIS 应用，其意向数超出最大意向数。 我该怎么办？

请参阅[意向最佳做法](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents)。

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>我希望在 LUIS 中生成一个应用，其实体数超出最大实体数。 我该怎么办？

请参阅[实体最佳做法](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)。

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>短语列表的数目和大小限制是多少？
若要了解[短语列表](./luis-concept-feature.md)的最大长度，请查看[边界](luis-boundaries.md)参考文档。

### <a name="what-are-the-limits-on-example-utterances"></a>示例表述有哪些限制？
请查看[边界](luis-boundaries.md)参考文档。

## <a name="testing-and-training"></a>测试和训练

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>我在批量测试窗格中看到应用中的某些模块出现的某些错误。 如何解决此问题？

这些错误表明标签与模型预测之间存在一些差异。 若要解决此问题，请完成以下任务中的一个或全部两个：
* 添加更多标签，以便 LUIS 提高意向之间的差异性。
* 添加短语列表功能，引入专业领域的词汇，以便 LUIS 加快学习速度。

请参阅[批量测试](luis-tutorial-batch-testing.md)教程。

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>将某个应用导出之后又重新导入到新应用中时（使用新的应用 ID），LUIS 预测分数会有所不同。 为何发生这种情况？ 

请参阅[同一应用的不同副本之间的预测差异](luis-concept-prediction-score.md#differences-with-predictions)。

## <a name="app-publishing"></a>应用发布

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>“向应用添加密钥”窗口中的租户 ID 是什么？
在 Azure 中，租户代表与服务关联的客户或组织。 若要在 Azure 门户的“目录 ID”框中查找租户 ID，请选择“Azure Active Directory” > “管理” > “属性”。

![Azure 门户中的租户 ID](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

### <a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>为何应用的发布页上的订阅密钥数超出分配给应用的数目？ 
每个 LUIS 应用都有创作/初学者密钥。 在 GA 期间创建的 LUIS 订阅密钥会显示在发布页上，不管你是否向应用添加了它们。 这样做是为了方便 GA 迁移。 新的 LUIS 订阅密钥不显示在发布页上。 

## <a name="app-management"></a>应用管理

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>如何转让 LUIS 应用的所有权？
若要将某个 LUIS 应用转让给另一 Azure 订阅，请导出该 LUIS 应用，然后使用新帐户来导入它。 在进行调用的客户端应用程序中更新 LUIS 应用 ID。 新应用返回的 LUIS 分数与原始应用返回的可能稍有不同。 

### <a name="how-do-i-download-a-log-of-user-utterances"></a>如何下载用户表述的日志？
默认情况下，LUIS 应用会记录用户提供的表述。 若要下载用户发送至 LUIS 应用的表述的日志，请转到“我的应用”，单击应用的列表中的省略号 (***...***)。 然后单击“导出终结点日志”。 日志格式化为逗号分隔值 (CSV) 文件。

### <a name="how-can-i-disable-the-logging-of-utterances"></a>如何禁用表述的日志记录？
若要关闭用户表述的日志记录，可以在客户端应用程序用来查询 LUIS 的终结点 URL 中设置 `log=false`。 但是，关闭日志记录会禁用 LUIS 应用提供表述建议或改进基于[主动学习](luis-concept-review-endpoint-utterances.md#what-is-active-learning)的性能的功能。 如果因为数据隐私顾虑而设置 `log=false`，则不能从 LUIS 下载这些用户表述的记录，也不能使用这些表述来改进应用。

日志记录是表述的唯一存储方式。 

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>为什么不需要记录所有的终结点表述？
如果使用日志进行预测分析，请勿在日志中捕获测试性表述。

## <a name="data-management"></a>数据管理

### <a name="can-i-delete-data-from-luis"></a>能否从 LUIS 中删除数据？ 

* 可以随意删除用于训练 LUIS 的示例表述。 如果从 LUIS 应用中删除某个示例表述，则会将其从 LUIS Web 服务中删除，导致其无法导出。
* 可以从 LUIS 在“查看终结点表述”页中建议的用户表述列表中删除表述。 从此列表中删除表述可以防止系统再将其作为建议提出来，但不会将其从日志中删除。
* 如果删除某个帐户，则会删除所有应用及其示例表述和日志。 数据可以保留在服务器上 60 天，然后就会被永久删除。

## <a name="language-and-translation-support"></a>语言和翻译支持 

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>我已经有了某种语言的应用，希望创建一个采用另一种语言的并行应用， 最简单的方法是什么？
1. 导出应用。
2. 将已导出应用的 JSON 文件中标记的表述翻译成目标语言。
3. 可能需要更改意向和实体的名称，或者保留其不变。
4. 最后是导入应用，这样就有了一个采用目标语言的 LUIS 应用。

## <a name="app-notification"></a>应用通知

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>为什么我收到一封电子邮件，说我的配额几乎已用光？
创作/初学者密钥每个月只允许 1000 个终结点查询。 请创建一个 LUIS 订阅密钥（免费或付费），在进行终结点查询时使用该密钥。 如果通过机器人或另一客户端应用程序进行终结点查询，则需在其中更改 LUIS 终结点密钥。 

## <a name="integrating-luis"></a>集成 LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>在 Azure Web 应用机器人订阅过程中创建的 LUIS 应用位于何处？
如果选择一个 LUIS 模板，然后在模板窗格中选择“选择”按钮，则左侧窗格在更改后会包括模板类型，并询问在什么区域创建 LUIS 模板。 不过，Web 应用机器人过程不会创建 LUIS 订阅。

![LUIS 模板 Web 应用机器人区域](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>什么 LUIS 区域支持 Bot Framework 语音启动？
仅中部（美国）实例中的 LUIS 应用支持[语音启动](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)。 

## <a name="luis-service"></a>LUIS 服务 

### <a name="is-luis-available-on-premise-or-in-private-cloud"></a>是否可以在本地或私有云中使用 LUIS？
不是。 

## <a name="changes-to-the-docs"></a>对 Docs 所做的更改

### <a name="where-did-the-tutorials-go"></a>教程放在哪里？ 
以前在“教程”部分的文章现在放在文档的“操作方法”部分。 

|教程|
|--|
|使用 [C#](luis-csharp-tutorial-build-bot-framework-sample.md) 和 [Node.js](luis-nodejs-tutorial-build-bot-framework-sample.md) 将 LUIS 与机器人集成|
|使用 [C#](luis-tutorial-bot-csharp-appinsights.md) 和 [Node.js](luis-tutorial-function-appinsights.md) 向机器人添加 Application Insights|
|使用 [Node.js](luis-tutorial-node-import-utterances-csv.md) 以编程方式生成 LUIS 应用|
|使用[复合实体](luis-tutorial-composite-entity.md)提取分组的数据|
|使用 Node.js 添加[列表实体](luis-tutorial-list-entity.md)以增强实体检测|
|使用[短语列表](luis-tutorial-interchangeable-phrase-list.md)、[模式](luis-tutorial-pattern.md)和[批量测试](luis-tutorial-batch-testing.md)提高预测准确性|
|使用必应拼写检查 API v7 [更正拼写](luis-tutorial-batch-testing.md)

### <a name="at-the-build-2018-conference-i-heard-about-a-language-understanding-feature-or-demo-but-i-dont-remember-what-it-was-called"></a>在 Build 2018 大会上，我了解到一项语言理解功能或演示，但我不记得它的名称了，它叫什么呢？ 

以下功能是在 Build 2018 大会上发布的：

|名称|内容|
|--|--|
|增强功能|[正则表达式](luis-concept-data-extraction.md##regular-expression-entity-data)实体和[关键短语](luis-concept-data-extraction.md#key-phrase-extraction-entity-data)实体
|模式|模式[概念](luis-concept-patterns.md)、[教程](luis-tutorial-pattern.md)、[操作方法](luis-how-to-model-intent-pattern.md)<br>[Patterns.Any](luis-concept-entity-types.md) 实体概念，包括用作例外的[显式列表](luis-concept-patterns.md#explicit-lists)<br>[角色](luis-concept-roles.md)概念|
|集成|[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)集成（集成了[情绪分析](publishapp.md#enable-sentiment-analysis)）<br>[语音](https://docs.microsoft.com/azure/cognitive-services/speech)集成（集成了[语音启动](publishapp.md#enable-speech-priming)和[语音 SDK](https://aka.ms/SpeechSDK)）|
|Dispatch 工具|Dispatch 命令行[工具](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps)是 [BotBuilder-tools](https://github.com/Microsoft/botbuilder-tools) 的一部分，可以将多个 LUIS 应用和 QnA Maker 应用组合成单个 LUIS 应用，以便增强机器人中的意向识别功能

此外还包括其他创作 [API 路由](https://github.com/Microsoft/LUIS-Samples/blob/master/authoring-routes.md)。 

视频： 
* [Azure Friday At Build 2018: Cognitive Services - Language (LUIS)](https://channel9.msdn.com/Shows/Azure-Friday/At-Build-2018-Cognitive-Services-Language-LUIS/player)（Build 2018 中的 Azure Friday：认知服务 - 语言 (LUIS)）
* [Build 2018 AI Show - What’s New with Language Understanding Service](https://channel9.msdn.com/Shows/AI-Show/Whats-New-with-Language-Understanding-Service-LUIS/player)（Build 2018 AI 演示 - 语言理解服务的新增功能）
* [Build 2018 会议 - 机器人智能、语音功能和 NLU 最佳做法](https://channel9.msdn.com/events/Build/2018/BRK3208)
* [Build 2018 - LUIS Updates](https://channel9.msdn.com/events/Build/2018/THR3118/player)（Build 2018 - LUIS 更新）

项目： 
* [Contoso 咖啡店机器人](https://github.com/botbuilderbuild2018/build2018demo)演示 - Github 上的源代码

## <a name="next-steps"></a>后续步骤

若要了解有关 LUIS 的详细信息，请参阅以下资源：
* [标记了 LUIS 的 Stack Overflow 问题](https://stackoverflow.com/questions/tagged/luis)
* [MSDN 语言理解智能服务 (LUIS) 论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
