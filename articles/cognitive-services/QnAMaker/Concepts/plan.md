---
title: 规划您的应用程序-QnA Maker
description: 了解如何计划 QnA Maker 应用。 了解 QnA Maker 的工作原理，并与其他 Azure 服务和一些知识库概念交互。
ms.topic: conceptual
ms.date: 07/2/2020
ms.openlocfilehash: 19499aceed96155fa42c78865b1d673a3830f5cc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87054210"
---
# <a name="plan-your-qna-maker-app"></a>规划 QnA Maker 应用

若要计划 QnA Maker 应用，需要了解 QnA Maker 的工作方式，并与其他 Azure 服务交互。 您还应了解知识库的概念。

## <a name="azure-resources"></a>Azure 资源

使用 QnA Maker 创建的每个[Azure 资源](azure-resources.md#resource-purposes)都有特定目的。 每个资源都有其自己的用途、限制和[定价层](azure-resources.md#pricing-tier-considerations)。 了解这些资源的功能非常重要，这样就可以在规划过程中使用该知识。

| 资源 | 目的 |
|--|--|
| [QnA Maker](azure-resources.md#qna-maker-resource)资源 | 创作和查询预测 |
| [认知搜索](azure-resources.md#cognitive-search-resource)资源 | 数据存储和搜索 |
| [应用服务资源和应用计划服务](azure-resources.md#app-service-and-app-service-plan)资源 | 查询预测终结点 |
| [Application Insights](azure-resources.md#application-insights)资源 | 查询预测遥测 |

### <a name="resource-planning"></a>资源规划

每个资源的免费层都 `F0` 有效，并可提供创作和查询预测体验。 您可以使用此层来了解创作和查询预测。 转到生产或实时方案时，请重新评估资源选择。

#### <a name="qna-maker-resource"></a>QnA Maker 资源

单个 QnA Maker 资源可以托管多个知识库。 知识库的数目由认知搜索定价层支持的索引数量决定。 详细了解[索引与知识库之间的关系](azure-resources.md#index-usage)。

#### <a name="knowledge-base-size-and-throughput"></a>知识库大小和吞吐量

构建真实应用时，为您的知识库和预期的查询预测请求计划足够的资源。

知识库大小由以下各内容控制：
* [认知搜索资源](../../../search/search-limits-quotas-capacity.md)定价层限制
* [QnA Maker 限制](../limits.md)

知识库查询预测请求由 web 应用计划和 web 应用控制。 若要规划定价层，请参阅[推荐的设置](azure-resources.md#recommended-settings)。

### <a name="resource-sharing"></a>资源共享

如果已使用其中一些资源，可以考虑共享资源。 查看[可以共享](azure-resources.md#share-services-with-qna-maker)哪些资源，了解资源共享是一种高级方案。

在同一 QnA Maker 资源中创建的所有知识库共享同一**测试**查询预测终结点。

### <a name="understand-the-impact-of-resource-selection"></a>了解资源选择的影响

适当的资源选择意味着您的知识库会成功回答查询预测。

如果知识库不能正常工作，通常是不正确的资源管理问题。

不正确的资源选择需要进行调查来确定[需要更改的资源](azure-resources.md#when-to-change-a-pricing-tier)。

## <a name="knowledge-bases"></a>知识库

知识库直接与 QnA Maker 资源关联。 它包含用于回答查询预测请求的问题和答案（QnA）对。

### <a name="language-considerations"></a>语言注意事项

在 QnA Maker 资源上创建的第一个知识库为资源设置了语言。 一个 QnA Maker 资源只能有一种语言。

您可以按语言构建 QnA Maker 资源，也可以在将查询发送到查询预测终结点之前，使用[转换器](../../translator/translator-info-overview.md)将查询从另一种语言更改为知识库的语言。

### <a name="ingest-data-sources"></a>引入数据源

您可以使用以下引入[数据源](knowledge-base.md)之一来创建知识库：

* 公共 URL
* 私有 SharePoint URL
* 文件

引入过程会将[支持的内容类型](content-types.md)转换为 markdown。 对*答案*的所有进一步编辑都是通过 markdown 来完成的。 创建知识库后，可以在 QnA Maker 门户中使用[富文本创作](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer)来编辑[QnA 对](question-answer-set.md)。

### <a name="data-format-considerations"></a>数据格式注意事项

由于 QnA 对的最终格式是 markdown，因此必须了解[markdown 支持](../reference-markdown-format.md)。

必须从公共 URL 提供链接图像，才能在 QnA Maker 门户的 "测试" 窗格或客户端应用程序中显示这些图像。 QnA Maker 不提供内容身份验证（包括图像）。

### <a name="bot-personality"></a>机器人个性

使用[chit-聊天](../how-to/chit-chat-knowledge-base.md)将 bot 个性添加到知识库。 这项预配的答案是在某些对话音中提供的，例如*专业*和*友好*的。 此 chit 是一种对话集，它具有用于添加、编辑和删除的总体控制。

如果机器人连接到知识库，则建议使用 bot 个性。 即使还连接到其他服务，你也可以选择在知识库中使用 chit-聊天，但应查看 bot 服务如何进行交互，了解这是否是正确的体系结构设计以供你使用。

### <a name="conversation-flow-with-a-knowledge-base"></a>包含知识库的会话流

会话流通常以用户的贺词开头，例如 `Hi` 或 `Hello` 。 您的知识库可以使用一般答案来回答，如 `Hi, how can I help you` ，还可以提供后续提示以继续进行对话。

你应在设计会话流时考虑循环，使用户知道如何使用你的机器人，而不会被会话中的机器人弃用。 [跟进提示](../how-to/multiturn-conversation.md)提供 QnA 对之间的链接，这允许会话流。

### <a name="authoring-with-collaborators"></a>用合作者创作

协作者可以是共享知识库应用程序的完整开发堆栈的其他开发人员，也可以仅编写该知识库。

知识库创作支持在 Azure 门户中应用的多个[基于角色的访问权限](../how-to/collaborate-knowledge-base.md)，以限制合作者的能力范围。

## <a name="integration-with-client-applications"></a>与客户端应用程序集成

与[客户端应用程序](integration-with-other-applications.md)集成是通过向预测运行时终结点发送查询来完成的。 使用 SDK 或基于 REST 的请求将查询发送到你的 QnA Maker 的 web 应用终结点。

若要正确验证客户端请求，客户端应用程序必须发送正确的凭据和知识库 ID。 如果使用的是 Azure 机器人服务，请在 Azure 门户中将这些设置配置为机器人配置的一部分。

### <a name="conversation-flow-in-a-client-application"></a>客户端应用程序中的会话流

[客户端应用程序](integration-with-other-applications.md)中的会话流（如 Azure 机器人）可能需要在与知识库交互前后使用功能。

你的客户端应用程序是否支持会话流，方法是提供替代方法来处理后续提示或包括 chit-chit？ 如果是这样，请及早设计这些设置，并确保客户端应用程序查询可以通过其他服务或发送到您的知识库进行正确处理。

### <a name="dispatch-between-qna-maker-and-language-understanding-luis"></a>QnA Maker 和语言理解（LUIS）之间的调度

客户端应用程序可以提供多种功能，其中只有一个功能由知识库应答。 其他功能仍然需要了解对话文本，并从中提取含义。

常见的客户端应用程序体系结构是同时使用 QnA Maker 和[语言理解（LUIS）](../../LUIS/what-is-luis.md) 。 LUIS 为任何查询（包括其他服务）提供文本分类和提取。 QnA Maker 提供了知识库的答案。

在这种[共享的体系结构](../choose-natural-language-processing-service.md)方案中，两个服务之间的调度由 Bot 框架中的[调度](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)工具来完成。

### <a name="active-learning-from-a-client-application"></a>来自客户端应用程序的活动学习

QnA Maker 使用_活动学习_，通过向答案建议替代问题来改善您的知识库。 客户端应用程序负责此[活动学习](active-learning-suggestions.md)。 通过会话式提示，客户端应用程序可以确定该知识库是否返回了对用户无效的答案，还可以确定更好的答案。 客户端应用程序需要将[该信息发送回知识库](active-learning-suggestions.md#how-you-give-explicit-feedback-with-the-train-api)以提高预测质量。

### <a name="providing-a-default-answer"></a>提供默认答案

如果知识库找不到答案，将返回_默认的答案_。 此答案可在 QnA Maker 门户的 "**设置**" 页或[api](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body)中进行配置。

此默认答案不同于 Azure 机器人默认答案。 在 "配置设置" 中，在 Azure 门户中配置 Azure bot 的默认答案。 如果未满足分数阈值，则返回此值。

## <a name="prediction"></a>预测

预测是来自您的知识库的响应，它包含的信息比答案更多。 若要获取查询预测响应，请使用[GENERATEANSWER API](query-knowledge-base.md)。

### <a name="prediction-score-fluctuations"></a>预测分数波动

分数可以根据多个因素变化：

* 为响应[GenerateAnswer](query-knowledge-base.md) with property 而请求的答案数 `top`
* 各种可用的替代问题
* 筛选元数据
* 查询发送到 `test` 或 `production` 知识库

有[两阶段答案排名](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer)：
- 认知搜索优先级别。 设置所允许的最大_答案_数，认知搜索，并将其传递到 QnA Maker ranker。
- QnA Maker 秒排名。 应用特征化和机器学习来确定最佳答案。

### <a name="service-updates"></a>服务更新

应用[最新的运行时更新](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)以自动管理服务更新。

### <a name="scaling-throughput-and-resiliency"></a>缩放、吞吐量和复原

缩放、吞吐量和复原能力取决于[Azure 资源](../how-to/set-up-qnamaker-service-azure.md)、其定价层和任何围绕[流量管理器](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager)的体系结构。

### <a name="analytics-with-application-insights"></a>分析与 Application Insights

对知识库的所有查询都存储在 Application Insights 中。 使用[最常见的查询](../how-to/get-analytics-knowledge-base.md)来了解指标。

## <a name="development-lifecycle"></a>开发生命周期

知识库的[开发生命周期](development-lifecycle-knowledge-base.md)是：编辑、测试和发布您的知识库。

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>QnA Maker 对的知识库开发

应根据客户端应用程序的使用情况设计和开发[QnA 对](question-answer-set.md)。

每个对可以包含：
* 元数据-可在查询时进行筛选，以允许通过有关数据的源、内容、格式和用途的附加信息来标记 QnA 对。
* 跟进提示-帮助确定通过知识库的路径，使用户到达正确的答案。
* 替换问题-重要的是，允许搜索与不同形式的问题的答案相匹配。 [活动学习建议](active-learning-suggestions.md)变成了替代问题。

### <a name="devops-development"></a>DevOps 开发

若要开发要插入到 DevOps 管道中的知识库，需要在[批处理测试](../quickstarts/batch-testing.md)过程中隔离此知识库。

知识库与 QnA Maker 资源上的所有其他知识库共享认知搜索的索引。 虽然知识库是按分区隔离的，但在与已发布的知识库进行比较时，共享索引可能会导致分数不同。

若要在和知识库上具有_相同的分数_ `test` `production` ，请将 QnA Maker 资源隔离到一个知识库中。 在此体系结构中，只要隔离的批处理测试，资源只需实时。

## <a name="next-steps"></a>后续步骤

* [Azure 资源](../how-to/set-up-qnamaker-service-azure.md)
* [问答集](question-answer-set.md)