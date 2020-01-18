---
title: 了解评审工具的概念-内容审查器
titleSuffix: Azure Cognitive Services
description: 了解内容审查器审核工具，该工具是一个用于协调 AI 和人工审阅协调工作的网站。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169506"
---
# <a name="content-moderator-review-tool"></a>内容审查器查看工具

Azure 内容审查器提供服务，将机器学习内容审核与人工审查结合使用，而[审阅工具](https://contentmoderator.cognitive.microsoft.com)网站是用户友好的前端，提供对这些服务的详细访问权限。

![浏览器中的 "查看工具" 仪表板](./images/0-dashboard.png)

## <a name="what-it-does"></a>它的作用

与计算机辅助审核 Api 结合使用时，[查看工具](https://contentmoderator.cognitive.microsoft.com)可让你在内容审核过程中完成以下任务：

- 使用一组工具来以多种格式（文本、图像和视频）来适中内容。
- 当审核 API 结果到来时，自动创建人工[评论](../review-api.md#reviews)。
- 向多个评审团队分配或升级内容评审，按内容类别或经验级别进行组织。
- 使用默认或自定义逻辑筛选器（[工作流](../review-api.md#workflows)）来排序和跟踪内容，而无需编写任何代码。
- 除内容审查器 Api 外，还可以使用[连接器](./configure.md#connectors)处理 Microsoft PhotoDNA、文本分析和面部服务的内容。
- 构建你自己的连接器，为任何 API 或业务流程创建工作流。
- 在内容审核过程中获得关键性能指标。

## <a name="review-tool-dashboard"></a>查看工具仪表板

在 "**仪表板**" 选项卡上，可以看到工具中完成的内容审阅的关键指标。 查看图像、文本和视频内容的总数、完成和挂起评审的数目。 你还可以查看已完成评审的用户和团队的细目以及已应用的裁决标记。

![查看仪表板](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>查看工具凭据

当你使用[审阅工具](https://contentmoderator.cognitive.microsoft.com)注册时，系统将提示你为你的帐户选择一个 Azure 区域。 这是因为，[查看工具](https://contentmoderator.cognitive.microsoft.com)会为 Azure 内容审查器服务生成免费试用密钥;需要使用此密钥从 REST 调用或客户端 SDK 访问任何服务。 可以通过选择 "**设置**" > **凭据**来查看密钥和 API 终结点 URL。

![内容审查器“凭据”设置](images/settings-6-credentials.png)

## <a name="next-steps"></a>后续步骤

请参阅[配置查看工具](./configure.md)以了解如何访问审阅工具资源和更改设置。