---
title: 服务配置-QnA Maker
description: 了解配置资源的方式和位置。
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 8ef6fecbfeb119d0c68ec5bc3bbc90ec449dbb7d
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651857"
---
# <a name="service-configuration"></a>服务配置

QnA Maker 使用多个 Azure 资源（服务），包括认知搜索、应用服务、应用服务计划和 Application Insights。

下面列出了 QnA Maker 支持的这些设置的所有自定义项。

## <a name="app-service"></a>应用服务

QnA Maker 使用应用服务提供[GENERATEANSWER API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)使用的查询运行时。


这些设置适用于应用服务的 Azure 门户。 可以通过依次选择 "**设置**"、"**配置**" 来使用这些设置。

可以通过 "应用程序设置" 列表设置单个设置，也可以通过选择 "**高级编辑**" 来修改多个设置。

|资源|设置|
|--|--|
|AzureSearchAdminKey|认知搜索-用于 QnA 集存储和 Ranker #1|
|AzureSearchName|认知搜索-用于 QnA 集存储和 Ranker #1|
|DefaultAnswer|找不到匹配项时的应答文本|
|UserAppInsightsAppId|聊天日志和遥测|
|UserAppInsightsKey|聊天日志和遥测|
|UserAppInsightsName|聊天日志和遥测|

了解[如何将认知搜索服务更改](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource)为服务。

完成更改后，需要从 Azure 门户的 "**概述**" 页中**重新启动**该服务。

## <a name="qna-maker-service"></a>QnA Maker 服务

QnA Maker 服务为以下用户提供了配置，以便在单个 QnA Maker 服务及其所有知识库上进行协作。

了解[如何向服务添加协作](./how-to/collaborate-knowledge-base.md)者。

## <a name="application-insights"></a>Application Insights

Application Insights 没有特定于 QnA Maker 的配置设置。

## <a name="app-service-plan"></a>应用服务计划

应用服务计划没有特定于 QnA Maker 的配置设置。

## <a name="next-steps"></a>后续步骤

了解有关要导入到知识库中的文档和 Url[格式](reference-document-format-guidelines.md)的详细信息。