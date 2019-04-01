---
title: 评审会议中，工作流和作业概念-内容审查器
titlesuffix: Azure Cognitive Services
description: 了解如何评审、 工作流，以及作业
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: c1d4ef640e2ae072dacba7a665b6689e3224c55c
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756298"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>内容审核评审、 工作流和作业

内容审查器结合了机器辅助审查与人类的循环功能，可以创建一个最佳的审查过程，用于实际方案。 这是通过基于云的[审阅工具](https://contentmoderator.cognitive.microsoft.com)。 在本指南中，您将学习如何评审工具的核心概念： 评审、 工作流和作业。

## <a name="reviews"></a>审阅

在审阅中，内容上传到审阅工具，并显示在**查看**选项卡。从此处，用户可以更改标签应用和应用根据其自己自定义标记。 当用户提交评审时，将结果发送到指定的回调终结点，并从站点中删除的内容。

![在评审选项卡上的浏览器中打开评审工具网站](./Review-Tool-user-Guide/images/image-workflow-review.png)

请参阅[评审工具指南](./review-tool-user-guide/review-moderated-images.md)以开始创建评论，或者查看[REST API 指南](./try-review-api-review.md)若要了解如何以编程方式执行此操作。

## <a name="workflows"></a>工作流

工作流是基于云的自定义筛选器的内容。 工作流可以连接到各种服务来以不同方式筛选内容，然后采取相应的措施。 使用内容审查器连接器，工作流可以自动将审查标记应用，并使用已提交的内容创建评审。

### <a name="view-workflows"></a>查看工作流

若要查看现有的工作流，请转到[审阅工具](https://contentmoderator.cognitive.microsoft.com/)，然后选择**设置** > **工作流**。

![默认工作流](images/default-workflow-listed.PNG)

工作流可以被完全描述为 JSON 字符串，这使它们以编程方式访问。 如果选择**编辑**工作流的选项，然后选择**JSON**选项卡上，你将看到如下所示的 JSON 表达式：

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

请参阅[评审工具指南](./review-tool-user-guide/workflows.md)若要开始创建和使用工作流，或查看[REST API 指南](./try-review-api-workflow.md)若要了解如何以编程方式执行此操作。

## <a name="jobs"></a>作业

审查作业可作为一种类型的包装器的功能的内容审查、 工作流和评审。 作业扫描你使用内容审查器图像审查 API 或文本审查 API 的内容，然后检查其针对指定工作流。 基于工作流结果，可能会或可能不会创建中的内容审查[审阅工具](./review-tool-user-guide/human-in-the-loop.md)。 评审和工作流可以创建并配置了其各自的 Api，而作业 API，可获取详细的报告的整个过程 （它可发送到指定的回调终结点）。

请参阅[REST API 指南](./try-review-api-job.md)若要开始使用作业。

## <a name="next-steps"></a>后续步骤

* 试用[作业 API 控制台](try-review-api-job.md)，并运行 REST API 代码示例。 如果熟悉 Visual Studio 和 C#，还请参阅[作业 .NET 快速入门](moderation-jobs-quickstart-dotnet.md)。 
* 对于审阅，请开始使用[审阅 API 控制台](try-review-api-review.md)，并运行 REST API 代码示例。 然后，请参阅[审阅 .NET 快速入门](moderation-reviews-quickstart-dotnet.md)。
* 对于视频审阅，请参阅[视频审阅快速入门](video-reviews-quickstart-dotnet.md)，并了解如何[向视频审阅添加脚本](video-transcript-reviews-quickstart-dotnet.md)。
