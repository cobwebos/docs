---
title: 了解评审工具概念-内容审查器
titlesuffix: Azure Cognitive Services
description: 了解内容审查器审查工具，可以协调组合的 AI 和人工审阅审查工作的网站。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: sajagtap
ms.openlocfilehash: b7ec997fd3e9bfe294050893d80fd57a96a47aae
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755880"
---
# <a name="content-moderator-review-tool"></a>内容审查器评审工具

Azure 内容审查器提供的服务可将机器学习内容审查与人工审阅并[审阅工具](https://contentmoderator.cognitive.microsoft.com)网站是可详细的访问这些服务的用户友好前端。

![在浏览器中查看工具仪表板](./images/0-dashboard.png)

## <a name="what-it-does"></a>作用

[审阅工具](https://contentmoderator.cognitive.microsoft.com)，当与计算机辅助下审查 Api，结合使用，可完成内容审核过程中的以下任务：

- 使用一的组工具审查以多种格式 （文本、 图像和视频） 的内容。
- 自动创建 human[评审](../review-api.md#reviews)审查 API 时结果附带中。
- 分配或升级到多个评审团队，按内容类别或体验级别的内容审查。
- 使用默认或自定义逻辑筛选器 ([工作流](../review-api.md#workflows)) 进行排序和跟踪的内容，而无需编写任何代码。
- 使用[连接器](./configure.md#connectors)来处理通过 Microsoft PhotoDNA、 文本分析和人脸 Api 除了内容审查器 Api 的内容。
- 生成你自己的连接器来创建工作流的任何 API 或业务流程。
- 在内容审核过程中获得关键性能指标。

## <a name="review-tool-dashboard"></a>评审工具仪表板

上**仪表板**选项卡上，可以查看的内容在该工具中完成的评审的关键指标。 请参阅数占总时间，完成的和挂起的图像、 文本和视频内容的审阅。 此外可以查看的用户和团队已完成审阅，以及已应用的审查标记的明细。

![查看仪表板](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>查看工具凭据

当你注册具有[审阅工具](https://contentmoderator.cognitive.microsoft.com)，系统将提示你选择 Azure 区域，为你的帐户。 这是因为[审阅工具](https://contentmoderator.cognitive.microsoft.com)生成 Azure 内容审查器服务; 一个免费试用版密钥将需要此密钥才能从客户端 SDK 或 REST 调用访问的任何服务。 选择可查看你的密钥和 API 终结点 URL**设置** > **凭据**。

![内容审查器“凭据”设置](images/settings-6-credentials.png)

## <a name="next-steps"></a>后续步骤

请参阅[配置审阅工具](./configure.md)若要了解如何访问评审工具资源和更改设置。