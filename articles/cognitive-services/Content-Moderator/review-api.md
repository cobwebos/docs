---
title: 评论、工作流和作业概念 - 内容审阅者
titleSuffix: Azure Cognitive Services
description: 在本文中，您将了解"审阅"工具的核心概念;评论、工作流和作业。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 1aba86efb9ea76fbf060e80b47f9f2f6cdf8ee71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221146"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>内容审核审核、工作流和作业

内容管理员将机器辅助审核与"循环中人"功能相结合，为实际方案创建最佳的审核过程。 它通过基于云的[审阅工具](https://contentmoderator.cognitive.microsoft.com)来完成此情况。 在本指南中，您将了解"审阅"工具的核心概念：评论、工作流和作业。

## <a name="reviews"></a>审阅

在审阅中，内容将上载到"审阅"工具，并显示在 **"审阅**"选项卡下。从这里，用户可以更改应用的标记，并根据需要应用自己的自定义标记。 当用户提交审核时，结果将发送到指定的回调终结点，并且内容将从站点中删除。

![在"审阅"选项卡上，在浏览器中打开的工具网站](./Review-Tool-user-Guide/images/image-workflow-review.png)

请参阅[Review 工具指南](./review-tool-user-guide/review-moderated-images.md)以开始创建评论，或查看[REST API 指南](./try-review-api-review.md)，了解如何以编程方式执行此操作。

## <a name="workflows"></a>工作流

工作流是内容的基于云的自定义筛选器。 工作流可以连接到各种服务，以不同的方式筛选内容，然后采取适当的操作。 使用内容审阅者连接器，工作流可以自动应用审核标记并创建包含已提交内容的评论。

### <a name="view-workflows"></a>查看工作流

要查看现有工作流，请转到["审阅"工具](https://contentmoderator.cognitive.microsoft.com/)并选择 **"设置** > **工作流**"。

![默认工作流](images/default-workflow-listed.PNG)

工作流可以完全描述为 JSON 字符串，这使得它们可以通过编程方式访问。 如果为工作流选择 **"编辑"** 选项，然后选择 **"JSON"** 选项卡，您将看到一个 JSON 表达式，如下所示：

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

请参阅[Review 工具指南](./review-tool-user-guide/workflows.md)以开始创建和使用工作流，或者请参阅 REST [API 指南](./try-review-api-workflow.md)，了解如何以编程方式执行此操作。

## <a name="jobs"></a>作业

审核作业充当内容审核、工作流和审阅功能的包装器。 作业使用内容审阅者图像审核 API 或文本审阅 API 扫描内容，然后根据指定的工作流进行检查。 根据工作流结果，它可能会或可能不会为[审阅工具](./review-tool-user-guide/human-in-the-loop.md)中的内容创建审阅。 虽然可以使用各自的 API 创建和配置审核和工作流，但作业 API 允许您获取整个过程的详细报告（可发送到指定的回调终结点）。

请参阅[REST API 指南](./try-review-api-job.md)以开始使用作业。

## <a name="next-steps"></a>后续步骤

* 试用[作业 API 控制台](try-review-api-job.md)，并运行 REST API 代码示例。 如果熟悉 Visual Studio 和 C#，还请参阅[作业 .NET 快速入门](moderation-jobs-quickstart-dotnet.md)。 
* 对于审阅，请开始使用[审阅 API 控制台](try-review-api-review.md)，并运行 REST API 代码示例。 然后查看[.NET 快速入门](dotnet-sdk-quickstart.md)的评论部分。
* 对于视频审阅，请参阅[视频审阅快速入门](video-reviews-quickstart-dotnet.md)，并了解如何[向视频审阅添加脚本](video-transcript-reviews-quickstart-dotnet.md)。
