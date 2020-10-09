---
title: 了解审阅工具的概念 - 内容审查器
titleSuffix: Azure Cognitive Services
description: 了解内容审查器审阅工具，该工具是一个网站，可协调 AI 和人工审阅的审查工作。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "76169506"
---
# <a name="content-moderator-review-tool"></a>内容审查器审阅工具

Azure 内容审查器提供将机器学习内容审查与人工审阅相结合的服务，而[审阅工具](https://contentmoderator.cognitive.microsoft.com)网站是为这些服务提供精细访问的易用前端。

![浏览器中的“审阅工具”仪表板](./images/0-dashboard.png)

## <a name="what-it-does"></a>作用

当[审阅工具](https://contentmoderator.cognitive.microsoft.com)与计算机辅助的审查 API 一起使用时，可以完成内容审查过程中涉及的以下任务：

- 使用一组工具审查多种格式（文本、图像和视频）的内容。
- 收到审查 API 结果时，自动创建人工[审阅](../review-api.md#reviews)。
- 将内容审阅分配给或呈报给根据内容类别或经验级别组织的多个审阅团队。
- 使用默认或自定义逻辑筛选器（[工作流](../review-api.md#workflows)）在不编写任何代码的情况下对内容进行排序和跟踪。
- 除内容审查器 API 以外，还可以使用 Microsoft PhotoDNA、Text Analytics 和 Face 服务来配合[连接器](./configure.md#connectors)处理内容。
- 构建专属连接器，为任意 API 或业务流程创建工作流。
- 在内容审核过程中获得关键性能指标。

## <a name="review-tool-dashboard"></a>审阅工具仪表板

在“仪表板”选项卡上，可以查看在该工具中完成的内容审阅的关键指标****。 查看图像、文本和视频内容的总审阅数、已审阅数和待审阅数。 你还可以查看已完成审阅的用户和团队的明细，以及已应用的审阅标记。

![查看仪表板](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>审阅工具凭据

注册[审阅工具](https://contentmoderator.cognitive.microsoft.com)时，系统将提示你为帐户选择 Azure 区域。 这是因为[审阅工具](https://contentmoderator.cognitive.microsoft.com)会为 Azure 内容审查器服务生成免费的试用版密钥；从 REST 调用或客户端 SDK 访问任何服务时都需要此密钥。 可以通过选择“设置” > “凭据”，查看密钥和 API 终结点 URL**** ****。

![内容审查器“凭据”设置](images/settings-6-credentials.png)

## <a name="next-steps"></a>后续步骤

请参阅[配置审阅工具](./configure.md)，了解如何访问审阅工具资源和更改设置。