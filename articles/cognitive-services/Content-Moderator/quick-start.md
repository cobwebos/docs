---
title: 快速入门：尝试在 Web 上使用内容审查器 - 内容审查器
titlesuffix: Azure Cognitive Services
description: 本快速入门将使用联机内容审查器评审工具来测试内容审查器的基本功能，且无需编写任何代码。
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: sajagtap
ms.openlocfilehash: 31bbc59b0587bb71999c4b10d273f75942737be2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604131"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>快速入门：尝试在 Web 上使用内容审查器

本快速入门将使用联机内容审查器评审工具来测试内容审查器的基本功能，且无需编写任何代码。 若要更快速地将此服务集成到应用中，请参阅[后续步骤](#next-steps)部分中的其他快速入门。

## <a name="prerequisites"></a>先决条件

- Web 浏览器

## <a name="set-up-the-review-tool"></a>设置评审工具
内容审查器评审工具是一个基于 Web 的工具，可让评审人员协助认知服务做出决策。 在本指南中，你将执行一个简短的过程来设置评审工具，以便可以了解内容审查器服务的工作原理。 转到[内容审查器评审工具](https://contentmoderator.cognitive.microsoft.com/)站点并注册。

![内容审查器主页](images/homepage.PNG)

## <a name="create-a-review-team"></a>创建评审团队

接下来创建评审团队。 在工作方案中，这是将要手动评审服务审查决策的人员组。 暂时只需创建团队名称。 若要邀请同事加入团队，可在此处输入他们的电子邮件地址。

![邀请团队成员](images/QuickStart-2-small.png)

## <a name="upload-sample-content"></a>上传示例内容

现在，可以上传示例内容。 选择“尝试 > 图像”、“尝试 > 文本”或“尝试 > 视频”。   

![请尝试图像或文本审核](images/tryimagesortext.png)

提交内容进行审查。 在内部，评审工具将调用审查 API 来扫描该内容。 扫描完成后，会出现一条消息，告知有结果等待你的评审。

![审核文件](images/submitted.png)

## <a name="review-moderation-tags"></a>评审审查标记

评审已应用的审查标记。 可以查看哪些标记已应用到内容，以及每个类别的评分。 请参阅[图像](image-moderation-api.md)、[文本](text-moderation-api.md)和[视频](video-moderation-api.md)审核主题，详细了解不同内容标记指示的内容。

![查看结果](images/reviewresults_text.png)

在项目中，你或评审团队可以更改这些标记，或根据需要添加更多的标记。 单击“下一步”按钮提交这些更改。  当业务应用程序调用审查器 API 时，标记的内容将在此处排队，等待评审人员团队进行评审。 可以使用此方法快速评审大量内容。

现在，你已使用内容审查器评审工具来通过示例了解了内容审查器服务的功能。 接下来，可以详细了解评审工具，以及如何使用评审 API 将它集成到软件项目，或者，可以跳转到[后续步骤](#next-steps)部分来了解如何在应用中单独使用审查 API。

## <a name="learn-more-about-the-review-tool"></a>详细了解评审工具

若要详细了解如何使用内容审查器评审工具，请查看[审核工具](Review-Tool-User-Guide/human-in-the-loop.md)指南，并参阅审核工具 API 以了解如何优化人工审核体验：
- [作业 API](try-review-api-job.md) 使用审核 API 审查你的内容，并在评审工具中生成评论。 
- [评审 API](try-review-api-review.md) 无需先扫描内容即可直接为人工审查器创建图像、文本或视频评论。 
- [工作流 API](try-review-api-workflow.md) 可创建、更新和获取有关团队创建的自定义工作流的详细信息。

或者继续执行后续步骤，在代码中开始使用审查 API。

## <a name="next-steps"></a>后续步骤

了解如何在应用中单独使用审查 API。
- 实施图像审查。 使用 [API 控制台](try-image-api.md)或 [C# 快速入门](image-moderation-quickstart-dotnet.md)，根据标记、置信度评分和提取的其他信息扫描图像并检测潜在的成人和猥亵内容。
- 实施文本审查。 使用 [API 控制台](try-text-api.md)或 [C# 快速入门](text-moderation-quickstart-dotnet.md)扫描文本内容以查找潜在的亵渎内容、机器辅助的不需要的文本分类（预览）和个人数据。
- 实施视频审查。 请按照[适用于 C# 的视频审核操作指南](video-moderation-api.md)扫描视频并检测潜在的成人和猥亵内容。 
