---
title: 规划您的应用程序-QnA Maker
description: 规划 QnA Maker 应用需要了解 QnA Maker 的工作方式，并与其他 Azure 服务以及一些知识库概念进行交互。
ms.topic: conceptual
ms.date: 07/2/2020
ms.openlocfilehash: d19ec51aec7e71b6f040a03543f72af3aed09556
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85875702"
---
# <a name="plan-your-qna-maker-app"></a>规划 QnA Maker 应用

规划 QnA Maker 应用需要了解 QnA Maker 的工作方式，并与其他 Azure 服务以及一些知识库概念进行交互。

## <a name="azure-resources"></a>Azure 资源

使用 QnA Maker 创建的每个[Azure 资源](azure-resources.md#resource-purposes)都有特定目的。 由于每个资源都有其自己的用途、限制和[定价层](azure-resources.md#pricing-tier-considerations)，因此在规划过程中，必须了解这些资源的作用。

|资源|目标|
|--|--|
| [QnA Maker](azure-resources.md#qna-maker-resource)资源|创作和查询预测|
| [认知搜索](azure-resources.md#cognitive-search-resource)资源|数据存储和搜索|
| [应用服务资源和应用计划服务](azure-resources.md#app-service-and-app-service-plan)资源|查询预测终结点|
| [Application Insights](azure-resources.md#application-insights)资源|查询预测遥测|

### <a name="resource-planning"></a>资源规划

学习创作和查询预测时，可以使用免费层， `F0` 每个资源的工作方式都是创作和查询预测体验。 当你迁移到生产、实时、方案时，请重新评估你的资源选择。

#### <a name="qna-maker-resource"></a>QnA Maker 资源

单个 QnA Maker 资源可以托管多个知识库。 知识库的数目由认知搜索定价层支持的索引数量决定。 详细了解[索引与知识库之间的关系](azure-resources.md#index-usage)。

#### <a name="knowledge-base-size-and-throughput"></a>知识库大小和吞吐量

当你计划生成真实的应用时，请规划资源的大小，了解知识库的大小以及所需的查询预测请求。

知识库大小由以下各内容控制：
* [认知搜索资源](../../../search/search-limits-quotas-capacity.md)定价层限制
* [QnA Maker 限制](../limits.md)

知识库查询预测请求由 Web 应用计划和 web 应用控制。 若要规划定价层，请参阅[推荐的设置](azure-resources.md#recommended-settings)。

### <a name="resource-sharing"></a>资源共享

如果已使用其中一些资源，可以考虑共享资源。 尽管[可以共享](azure-resources.md#share-services-with-qna-maker)某些资源，但这是一种高级方案。

在同一 QnA Maker 资源中创建的所有知识库共享同一**测试**查询预测终结点。

### <a name="understanding-impact-of-resource-selection"></a>了解资源选择的影响

适当的资源选择意味着您的知识库会成功回答查询预测。

如果您的知识库不能正常工作，则通常是不正确的资源管理问题。

不正确的资源选择需要进行调查来确定[需要更改的资源](azure-resources.md#when-to-change-a-pricing-tier)。

## <a name="knowledge-bases"></a>知识库

知识库直接与 QnA Maker 资源关联，并保存了用于回答查询预测请求的问题和答案（QnA）对。

### <a name="language-considerations"></a>语言注意事项

在 QnA Maker 资源上创建的第一个知识库为资源设置了语言。 一个 QnA Maker 资源只能有一种语言。

按语言构造 QnA Maker 资源，或使用[转换器](../../translator/translator-info-overview.md)将查询从另一种语言更改为知识库的语言，然后将查询发送到查询预测终结点。

### <a name="ingesting-data-sources"></a>引入数据源

用于创建知识库的引入[数据源](knowledge-base.md)可以是以下项之一：

* 公共 URL
* 私有 SharePoint URL
* 文件

引入过程会将[支持的内容类型](content-types.md)转换为 markdown。 对*答案*的所有进一步编辑都是通过 markdown 来完成的。 创建知识库后，可以通过[丰富的文本创作](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer)在 QnA Maker 门户中编辑[QnA 对](question-answer-set.md)。

### <a name="data-format-considerations"></a>数据格式注意事项

由于 QnA 对的最终格式是 markdown，因此理解[markdown 支持](../reference-markdown-format.md)是很重要的。

必须从公共 URL 提供链接图像，才能在 QnA Maker 门户的 "测试" 窗格中显示，还可以在任何客户端应用程序的 "测试" 窗格中显示，因为 QnA Maker 不提供内容身份验证（包括图像）。

### <a name="bot-personality"></a>机器人个性

使用[chit-聊天](../how-to/chit-chat-knowledge-base.md)将 bot 个性添加到知识库。 这项预配的答案是在某些对话音中提供的，例如*专业*和*友好*的。 此 chit 是一种对话集，它具有用于添加、编辑和删除的总体控制。

如果机器人连接到知识库，则建议使用 bot 个性。 如果要连接到多个服务，其中一项是你的知识库，则可以选择继续在知识库中使用 chit-聊天，但应查看 bot 服务如何进行交互，了解这是否是正确的体系结构设计以供你使用。

### <a name="conversation-flow-with-a-knowledge-base"></a>包含知识库的会话流

会话流通常以用户的贺词开头，例如 `Hi` 或 `Hello` 。 您的知识库可以使用一般答案来回答，如 `Hi, how can I help you` ，还可以提供后续提示以继续进行对话。

你应在设计会话流时考虑循环，使用户知道如何使用你的机器人，而不会被会话中的机器人弃用。 [跟进提示](../how-to/multiturn-conversation.md)提供 QnA 对之间的链接，这允许会话流。

### <a name="authoring-with-collaborators"></a>用合作者创作

协作者可以是共享知识库应用程序的完整开发堆栈的其他开发人员，也可以仅编写该知识库。

知识库创作支持在 Azure 门户中应用的多个[基于角色的访问权限](../how-to/collaborate-knowledge-base.md)，以限制合作者的能力范围。

## <a name="integration-with-client-applications"></a>与客户端应用程序集成

与[客户端应用程序](integration-with-other-applications.md)的集成意味着将查询发送到预测运行时终结点。 使用 SDK 或基于 REST 的请求将查询发送到你的 QnA Maker 的 web 应用终结点。

为了正确验证客户端请求，客户端应用程序必须发送正确的凭据和知识库 ID。 如果使用的是 Azure 机器人服务，请在 Azure 门户中将设置配置为机器人配置的一部分。

### <a name="conversation-flow-in-a-client-application"></a>客户端应用程序中的会话流

[客户端应用程序](integration-with-other-applications.md)中的会话流（如 Azure 机器人）可能需要在与知识库交互前后使用功能。

如果客户端应用程序支持会话流，则提供替代方法来处理后续提示或 chit-chit，及早设计这些信息，并确保正确处理客户端应用程序中使用的查询。

### <a name="dispatching-between-qna-maker-and-language-understanding-luis"></a>QnA Maker 和语言理解（LUIS）之间的调度

客户端应用程序可以提供多种功能，其中只有一个功能由知识库应答。 其他功能仍然需要了解对话文本，并从中提取含义。

常见的客户端应用程序体系结构是同时使用 QnA Maker 和[语言理解（LUIS）](../../LUIS/what-is-luis.md) 。 LUIS 为任何查询提供文本分类和提取，包括其他服务，同时 QnA Maker 提供您的知识库的答案。

在[共享的体系结构](../choose-natural-language-processing-service.md)中，这两个服务之间的调度是通过 Bot Framework 提供的[调度](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)工具来完成的。

### <a name="active-learning-from-a-client-application"></a>来自客户端应用程序的活动学习

QnA Maker 使用_活动学习_，通过向答案建议替代问题来改善您的知识库。 客户端应用程序负责此[活动学习](active-learning-suggestions.md)。 客户端应用程序通过会话提示，可以确定从知识库返回的答案不是用户查找的答案，并确定更好的答案。 客户端应用程序需要将[该信息发送回知识库](active-learning-suggestions.md#how-you-give-explicit-feedback-with-the-train-api)以提高预测质量。

### <a name="providing-a-default-answer"></a>提供默认答案

如果知识库找不到答案，将返回_默认的答案_。 此答案可在 QnA Maker 门户、"**设置**" 页或[api](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body)中进行配置。

此默认答案不同于 Azure 机器人默认答案。 在配置设置过程中，将在 Azure 门户中为 bot 配置 Azure 机器人默认答案，在未满足分数阈值时返回。

## <a name="prediction"></a>预测

预测是来自您的知识库的响应，其中包含的信息比答案更多。 若要获取查询预测响应，请使用[GENEATEANSWER API](query-knowledge-base.md)。

### <a name="prediction-score-fluctuations"></a>预测分数波动

分数可以根据多个因素变化：

* 为响应[GenerateAnswer](query-knowledge-base.md) with property 而请求的答案数 `top`
* 各种可用的替代问题
* 筛选元数据
* 查询发送到 `test` 或 `production` 知识库

有[两阶段答案排名](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer)：
* 认知搜索优先排名-为了获得从认知搜索返回的答案，_允许的回答_数量必须足够高，以便认知搜索返回最佳答案，以便它们可以传入 QnA Maker ranker
* QnA Maker 秒排名-应用特征化和机器学习来确定最佳答案。

### <a name="service-updates"></a>服务更新

服务更新通过应用[最新的运行时更新](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)来自动管理。

### <a name="scaling-throughput-and-resiliency"></a>缩放、吞吐量和复原

缩放、吞吐量和复原能力取决于[Azure 资源](../how-to/set-up-qnamaker-service-azure.md)、其定价层和任何围绕[流量管理器](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager)的体系结构。

### <a name="analytics-with-application-insights"></a>分析与 Application Insights

对知识库的所有查询都存储在 Application Insights 中。 使用[最常见的查询](../how-to/get-analytics-knowledge-base.md)来了解指标。

## <a name="development-lifecycle"></a>开发生命周期

知识库的[开发生命周期](development-lifecycle-knowledge-base.md)是：编辑、测试和发布您的知识库。

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>QnA Maker 对的知识库开发

应根据客户端应用程序的使用情况设计和开发[QnA 对](question-answer-set.md)。

每个对可以包含：
* 元数据-查询时可筛选。 这样，便可以用有关数据的源、内容、格式和用途的附加信息来标记 QnA 对。
* 跟进提示-通过知识库确定路径，使用户到达正确的答案。
* 替换问题-备选问题对于允许搜索与来自各种形式的问题的答案匹配至关重要。 [活动学习建议](active-learning-suggestions.md)变成了替代问题。

### <a name="devops-development"></a>DevOps 开发

若要开发要插入到 DevOps 管道中的知识库，需要在[批处理测试](../quickstarts/batch-testing.md)过程中隔离此知识库。

知识库与 QnA Maker 资源上的所有其他知识库共享认知搜索的索引。 虽然知识库是按分区隔离的，但在与已发布的知识库进行比较时，共享索引可能会导致分数不同。

若要在和知识库上具有_相同_的 `test` 分数 `production` ，请将 QnA Maker 资源隔离到一个知识库中。 在此体系结构中，资源只需生存到隔离批处理测试的长度。

## <a name="next-step"></a>后续步骤

* [Azure 资源](../how-to/set-up-qnamaker-service-azure.md)
* [问答集](question-answer-set.md)