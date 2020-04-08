---
title: 服务配置 - QnA 制造商
description: 了解如何配置资源以及在哪里配置资源。
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 3be32d1778604121c2acac88415cbfbc4bdbca3d
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804254"
---
# <a name="service-configuration"></a>服务配置

QnA Maker 使用多个 Azure 资源（服务），包括认知搜索、应用服务、应用服务计划和应用程序见解。

下面列出了 QnA Maker 支持的这些设置的所有自定义项。

## <a name="app-service"></a>应用服务

QnA Maker 使用应用服务提供[生成应答 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)使用的查询运行时。


这些设置在 Azure 门户中可用于应用服务。 这些设置可通过选择 **"设置"（** 然后 **"配置"）** 来提供。

您可以通过"应用程序设置"列表设置单个设置，也可以通过选择 **"高级编辑**"来修改多个设置。

|资源|设置|
|--|--|
|Azure 搜索管理密钥|认知搜索 - 用于 QnA 对存储和 Ranker #1|
|Azure 搜索名称|认知搜索 - 用于 QnA 对存储和 Ranker #1|
|默认答案|未找到匹配项时的应答文本|
|用户AppInsightsAppId|聊天日志和遥测|
|用户应用见解键|聊天日志和遥测|
|用户应用见解名称|聊天日志和遥测|

[了解如何将更改认知搜索服务](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource)添加到服务中。

完成更改后，需要从 Azure 门户的 **"概述"** 页**重新启动**服务。

## <a name="qna-maker-service"></a>QnA Maker 服务

QnA Maker 服务为以下用户提供配置，以便协作处理单个 QnA Maker 服务及其所有知识库。

[了解如何向服务添加协作者](./how-to/collaborate-knowledge-base.md)。

## <a name="application-insights"></a>Application Insights

应用程序见解没有特定于 QnA 制造商的配置设置。

## <a name="app-service-plan"></a>应用服务计划

应用服务计划没有特定于 QnA 制造商的配置设置。

## <a name="next-steps"></a>后续步骤

详细了解要导入知识库的文档和 URL 的[格式](reference-document-format-guidelines.md)。