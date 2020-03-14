---
title: 评审、工作流和作业的概念-内容审查器
titleSuffix: Azure Cognitive Services
description: 在本文中，你将了解查看工具的核心概念;评审、工作流和作业。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 1aba86efb9ea76fbf060e80b47f9f2f6cdf8ee71
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221146"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>内容审核评审、工作流和作业

内容审查器结合了计算机辅助的审核与人为循环功能，为真实的方案创建最佳审核流程。 它通过基于云的[审核工具](https://contentmoderator.cognitive.microsoft.com)来实现此功能。 在本指南中，你将了解评审工具的核心概念：评审、工作流和作业。

## <a name="reviews"></a>审阅

在评审中，内容上载到 "审阅" 工具，并显示在 "**审阅**" 选项卡下。在这里，用户可以更改应用的标记并根据需要应用自己的自定义标记。 当用户提交审阅时，结果将发送到指定的回调终结点，并从网站中删除内容。

![查看工具网站在浏览器中打开，在 "审阅" 选项卡上](./Review-Tool-user-Guide/images/image-workflow-review.png)

若要了解如何以编程方式执行此操作[REST API](./try-review-api-review.md) ，请参阅[查看工具指南](./review-tool-user-guide/review-moderated-images.md)。

## <a name="workflows"></a>工作流

工作流是一种基于云的自定义内容筛选器。 工作流可以连接到多种服务，以不同的方式筛选内容，然后采取相应的措施。 使用内容审查器连接器，工作流可以自动应用审核标记并使用提交的内容创建评审。

### <a name="view-workflows"></a>查看工作流

若要查看现有工作流，请单击 "[查看" 工具](https://contentmoderator.cognitive.microsoft.com/)，然后选择 "**设置**" > **工作流**"。

![默认工作流](images/default-workflow-listed.PNG)

工作流可以完全描述为 JSON 字符串，使其可通过编程方式进行访问。 如果为工作流选择 "**编辑**" 选项，然后选择 " **json** " 选项卡，则会看到如下所示的 json 表达式：

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

若要开始创建和使用工作流，请参阅[查看工具指南](./review-tool-user-guide/workflows.md)，或参阅[REST API 指南](./try-review-api-workflow.md)了解如何以编程方式执行此操作。

## <a name="jobs"></a>作业

裁决作业作为内容审核、工作流和评论功能的一种包装。 作业使用内容审查器图像裁决 API 或文本裁决 API 扫描内容，然后根据指定的工作流对其进行检查。 根据工作流结果，它可能会也可能不会在[查看工具](./review-tool-user-guide/human-in-the-loop.md)中为内容创建评审。 尽管可以使用各自的 Api 创建和配置审阅和工作流，但作业 API 允许获取整个进程的详细报告（可以发送到指定的回调终结点）。

请参阅[REST API 指南](./try-review-api-job.md)，开始使用作业。

## <a name="next-steps"></a>后续步骤

* 试用[作业 API 控制台](try-review-api-job.md)，并运行 REST API 代码示例。 如果熟悉 Visual Studio 和 C#，还请参阅[作业 .NET 快速入门](moderation-jobs-quickstart-dotnet.md)。 
* 对于审阅，请开始使用[审阅 API 控制台](try-review-api-review.md)，并运行 REST API 代码示例。 然后，请参阅[.net 快速入门](dotnet-sdk-quickstart.md)的 "审阅" 部分。
* 对于视频审阅，请参阅[视频审阅快速入门](video-reviews-quickstart-dotnet.md)，并了解如何[向视频审阅添加脚本](video-transcript-reviews-quickstart-dotnet.md)。
