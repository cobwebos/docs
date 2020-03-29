---
title: 学习复习工具概念 - 内容审阅者
titleSuffix: Azure Cognitive Services
description: 了解内容审阅人审阅工具，这是一个协调联合 AI 和人工审核审核工作的网站。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169506"
---
# <a name="content-moderator-review-tool"></a>内容审阅者审阅工具

Azure 内容审阅人提供的服务是将机器学习内容审核与人工审阅相结合，[而"审阅"工具](https://contentmoderator.cognitive.microsoft.com)网站是用户友好的前端，提供对这些服务的详细访问。

![浏览器中的审阅工具仪表板](./images/0-dashboard.png)

## <a name="what-it-does"></a>作用

与机器辅助的审核 API 结合使用时，"[审阅"工具](https://contentmoderator.cognitive.microsoft.com)允许您在内容审核过程中完成以下任务：

- 使用一组工具来调节多种格式（文本、图像和视频）的内容。
- 当审核 API 结果出来时，自动创建人工[评审](../review-api.md#reviews)。
- 将内容评审分配或上报给多个审核团队，按内容类别或体验级别组织。
- 使用默认或自定义逻辑筛选器 （[工作流](../review-api.md#workflows)） 对内容进行排序和跟踪，而无需编写任何代码。
- 使用[连接器](./configure.md#connectors)处理内容与 Microsoft PhotoDNA、文本分析和人脸服务以及内容审阅人 API。
- 构建您自己的连接器，为任何 API 或业务流程创建工作流。
- 在内容审核过程中获得关键性能指标。

## <a name="review-tool-dashboard"></a>查看工具仪表板

在 **"仪表板"** 选项卡上，您可以看到工具中完成的内容评审的关键指标。 查看图像、文本和视频内容的总、完整和挂起评论数。 您还可以查看已完成审核的用户和团队以及已应用的审核标记的细分。

![查看仪表板](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>查看工具凭据

使用["审阅"工具](https://contentmoderator.cognitive.microsoft.com)注册时，系统将提示您为帐户选择 Azure 区域。 这是因为["审阅"工具](https://contentmoderator.cognitive.microsoft.com)为 Azure 内容审阅者服务生成免费试用密钥;因此，审核工具为 Azure 内容审阅者服务生成免费试用密钥。您需要此密钥才能从 REST 调用或客户端 SDK 访问任何服务。 您可以通过选择 **"设置** > **凭据**"来查看密钥和 API 终结点 URL。

![内容审查器“凭据”设置](images/settings-6-credentials.png)

## <a name="next-steps"></a>后续步骤

请参阅[配置审阅工具](./configure.md)以了解如何访问审阅工具资源和更改设置。