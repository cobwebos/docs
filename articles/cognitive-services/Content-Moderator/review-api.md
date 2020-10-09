---
title: 审阅、工作流和作业概念 - 内容审查器
titleSuffix: Azure Cognitive Services
description: 本文介绍审阅工具（审阅、工作流和作业）的核心概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 1aba86efb9ea76fbf060e80b47f9f2f6cdf8ee71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91872045"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>内容审核审阅、工作流和作业

内容审查器将计算机辅助的审核与人机回环功能相结合，为真实场景创建最佳审核流程。 它通过基于云的[审阅工具](https://contentmoderator.cognitive.microsoft.com)来完成此操作。 本指南介绍审阅工具（审阅、工作流和作业）的核心概念。

## <a name="reviews"></a>审阅

在审阅中，内容上传到审阅工具并显示在“审阅”选项卡下****。在这里，用户可以更改已应用的标记，并根据需要应用自己的自定义标记。 当用户提交审阅时，系统会将结果发送到指定的回调终结点，并从网站中删除内容。

![在浏览器中打开的审阅工具网站，在“审阅”选项卡上](./Review-Tool-user-Guide/images/image-workflow-review.png)

若要开始创建审阅，请参阅[审阅工具指南](./review-tool-user-guide/review-moderated-images.md)；若要了解如何以编程方式创建审阅，请参阅 [REST API 指南](./try-review-api-review.md)。

## <a name="workflows"></a>工作流

工作流是一种基于云的自定义内容筛选器。 工作流可以连接到多种服务，以通过不同的方式筛选内容，然后采取相应操作。 使用内容审查器连接器，工作流可以自动应用审核标记并使用提交的内容创建审阅。

### <a name="view-workflows"></a>查看工作流

若要查看现有工作流，请转到[审阅工具](https://contentmoderator.cognitive.microsoft.com/)，然后选择“设置”**** > “工作流”****。

![默认工作流](images/default-workflow-listed.PNG)

可以采用 JSON 字符串的形式完整描述工作流，这样就能够以编程方式访问它们。 如果为工作流选择“编辑”**** 选项，然后选择“JSON”**** 选项卡，则会看到类似以下内容的 JSON 表达式：

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

若要开始创建和使用工作流，请参阅[审阅工具指南](./review-tool-user-guide/workflows.md)；若要了解如何以编程方式创建审阅，请参阅 [REST API 指南](./try-review-api-workflow.md)。

## <a name="jobs"></a>作业

审核作业充当内容审核、工作流和审阅功能的一种包装器。 该作业使用内容审查器图像审核 API 或文本审核 API 扫描内容，然后根据指定的工作流对其进行检查。 根据工作流的结果，它可能会也可能不会在[审阅工具](./review-tool-user-guide/human-in-the-loop.md)中为内容创建审阅。 审阅和工作流可以通过其各自的 API 创建和配置，而作业 API 允许获取整个流程的详细报告（可以将其发送到指定的回调终结点）。

若要开始使用作业，请参阅 [REST API 指南](./try-review-api-job.md)。

## <a name="next-steps"></a>后续步骤

* 试用[作业 API 控制台](try-review-api-job.md)，并运行 REST API 代码示例。 如果熟悉 Visual Studio 和 C#，还请参阅[作业 .NET 快速入门](moderation-jobs-quickstart-dotnet.md)。 
* 对于审阅，请开始使用[审阅 API 控制台](try-review-api-review.md)，并运行 REST API 代码示例。 然后，请参阅 [.NET 快速入门](dotnet-sdk-quickstart.md)的审阅部分。
* 对于视频审阅，请参阅[视频审阅快速入门](video-reviews-quickstart-dotnet.md)，并了解如何[向视频审阅添加脚本](video-transcript-reviews-quickstart-dotnet.md)。
