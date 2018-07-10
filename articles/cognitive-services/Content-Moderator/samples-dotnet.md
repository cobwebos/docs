---
title: Azure 内容审查器代码示例 | Microsoft Docs
description: 在应用程序中使用内容审查器
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2018
ms.author: sajagtap
ms.openlocfilehash: a7f5b0a7433631e303de47667871cc1354053c08
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365790"
---
# <a name="net-sdk-samples"></a>.NET SDK 示例

以下列表包括使用用于 .NET 的 Azure 内容审查器 SDK 构建的代码示例的链接。

- **帮助程序库**：[创建内容审查器客户端以用于其他示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs)。 请参阅[快速入门](content-moderator-helper-quickstart-dotnet.md)。

## <a name="moderation"></a>审查

- **图像审查**：[评估图像中是否存在成人和不雅内容、文本和人脸](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs)。 请参阅[快速入门](image-moderation-quickstart-dotnet.md)。
- **自定义图像**：[借助自定义图像列表进行审查](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs)。 请参阅[快速入门](image-lists-quickstart-dotnet.md)。

> [!NOTE]
> 最大限制为“5 个图像列表”，每个列表“不超过 10,000 个图像”。
>

- **文本审查**：[屏幕文字是否出现不雅内容和个人身份信息 (PII)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs)。 请参阅[快速入门](text-moderation-quickstart-dotnet.md)。
- **自定义术语**：[借助自定义术语列表进行审查](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs)。 请参阅[快速入门](term-lists-quickstart-dotnet.md)。

> [!NOTE]
> 最大限制为“5 个术语列表”，每个列表“不超过 10,000 条术语”。
>

- **视频审查**：[扫描视频有无成人和不雅内容并获取结果](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs)。 请参阅[快速入门](video-moderation-api.md)。

## <a name="review"></a>评审

- **图像作业**：启[动扫描和创建评审的审查作业](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs)。 请参阅[快速入门](moderation-jobs-quickstart-dotnet.md)。
- **图像评审**：[针对有人参与的循环创建评审](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs)。 请参阅[快速入门](moderation-reviews-quickstart-dotnet.md)。
- **视频评审**：[针对有人参与的循环创建视频评审](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs)。 请参阅[快速入门](video-reviews-quickstart-dotnet.md)
- **视频脚本评审**：[针对有人参与的循环创建视频脚本评审](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) 请参阅[快速入门](video-reviews-quickstart-dotnet.md)

请在 [GitHub 上的内容审查器 .NET 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)中查看所有的 .NET 示例。
