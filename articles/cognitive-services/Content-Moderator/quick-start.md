---
title: Azure 内容审查器入门 | Microsoft Docs
description: 如何开始使用 Azure 内容审查器。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/15/2018
ms.author: sajagtap
ms.openlocfilehash: ae4333047ebd95733c7baaed0323a0c2c477d323
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365789"
---
# <a name="get-started-with-content-moderator"></a>内容审查器入门

可以通过以下方式开始使用内容审查器 API 和评审工具：

- [从评审工具开始](#start-with-the-review-tool)，创建 API 密钥和评审团队。 浏览评审工具并了解如何使用内容审查器 API 进行集成。
- 在 Azure 门户中，[订阅内容审查器](#start-with-the-apis)。 仍需要联机注册才能创建评审团队。
- [使用 Flow 连接器和模板](https://flow.microsoft.com/connectors/shared_cognitiveservicescontentmoderator/content-moderator/)，通过易于使用的设计器检查各种集成。

无论你选择哪个选项，请参阅[管理凭据](review-tool-user-guide/credentials.md)一文以查找 API 凭据。

## <a name="start-with-the-review-tool"></a>从评审工具开始
在内容审查器评审工具网站上[注册](http://contentmoderator.cognitive.microsoft.com/)。

![内容审查器主页](images/homepage.PNG)

### <a name="create-a-review-team"></a>创建评审团队
为团队指定名称。 如果想要邀请同事，可以输入他们的电子邮件地址来完成此操作。

![邀请团队成员](images/QuickStart-2-small.png)

### <a name="upload-images-or-enter-text"></a>上传图像或输入文本
单击“尝试”>“图像”或“尝试”>“文本”。 最多上传五个示例图像或输入示例文本进行审核。

![请尝试图像或文本审核](images/tryimagesortext.png)

### <a name="submit-for-automated-moderation"></a>提交以进行自动审核
提交内容以进行自动审核。 在内部，评审工具将调用审核 API 来审查你的内容。 扫描完成后，你会看到一条消息，通知等待你查看的结果。

![审核文件](images/submitted.png)

### <a name="review-and-confirm-results"></a>查看并确认结果
查看自动审核的标记，根据需要进行更改，然后使用“下一步”按钮进行提交。 当业务应用程序调用审查器 API 时，标记的内容将开始排队，等待人工评审团队进行评审。 可以使用此方法快速评审大量内容。

![查看结果](images/reviewresults.png)

了解如何使用所有[评审工具的功能](Review-Tool-User-Guide/human-in-the-loop.md)或继续学习下一部分以了解 API。 跳过注册步骤，因为你已在评审工具中为自己预配了 API 密钥，如[管理凭据](review-tool-user-guide/credentials.md)一文中所示。

### <a name="use-the-apis"></a>使用 API

现在，你已探索了内容审核和评审工具体验，请学习如何将内容审查器与业务应用程序集成。 使用以下部分了解更多信息，并快速跟踪你对 SDK 和示例的理解。

## <a name="start-with-the-apis"></a>从 API 开始

在 Azure 门户中，[订阅内容审查器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)。 从以下 API 之一开始：

### <a name="image-moderation"></a>图像审查

从 [API 控制台](try-image-api.md)开始，或按照 [.NET 快速入门](image-moderation-quickstart-dotnet.md)使用标记、置信度分数和其他提取的信息扫描图像并检测潜在的成人和猥亵内容。

### <a name="text-moderation"></a>文本审查

从 [API 控制台](try-text-api.md)开始，或使用 [.NET 快速入门](text-moderation-quickstart-dotnet.md)扫描文本内容以查找潜在的亵渎内容、机器辅助的不需要的文本分类（预览）和个人身份信息 (PII)。 


### <a name="video-moderation"></a>视频审查

从 [.NET 快速入门](video-moderation-api.md)开始，扫描视频并检测潜在的成人和猥亵内容。 


### <a name="review-apis"></a>查看 API

通过从作业、评审和工作流 API 中选择，从这里开始。

- [作业 API](try-review-api-job.md) 使用审核 API 审查你的内容，并在评审工具中生成评论。 
- [评审 API](try-review-api-review.md) 无需先扫描内容即可直接为人工审查器创建图像、文本或视频评论。 
- [工作流 API](try-review-api-workflow.md) 可创建、更新和获取有关团队创建的自定义工作流的详细信息。

## <a name="next-steps"></a>后续步骤

从[图像审核 API](image-moderation-api.md) 开始，详细了解内容审核。
