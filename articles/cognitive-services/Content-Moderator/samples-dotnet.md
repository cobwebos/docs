---
title: 代码示例 - 内容审查器、.NET
titleSuffix: Azure Cognitive Services
description: 了解如何通过 SDK 在 .NET 应用程序中使用 Azure 认知服务内容审查器。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 930571c841146e0b12efbf7325915ba2b23a7efa
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73744342"
---
# <a name="content-moderator-net-sdk-samples"></a>内容审查器 .NET SDK 示例

下面列出了使用用于 .NET 的 Azure 内容审查器 SDK 生成的代码示例的链接。

## <a name="moderation"></a>审查

- **图像审查**：[评估图像中是否有成人和挑逗性、文本和人脸](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs)。 请参阅 [.NET SDK 快速入门](dotnet-sdk-quickstart.md)。
- **自定义图像**：[使用自定义图像列表进行审查](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs)。 请参阅 [.NET SDK 快速入门](dotnet-sdk-quickstart.md)。

> [!NOTE]
> 最多只能使用 5 个图像列表  ，每个列表中的图像数不得超过 10,000 张  。
>

- **文本审查**：[筛查文本中是否有猥亵内容和个人数据](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs)。 请参阅 [.NET SDK 快速入门](dotnet-sdk-quickstart.md)。
- **自定义术语**：[使用自定义术语列表进行审查](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs)。 请参阅 [.NET SDK 快速入门](dotnet-sdk-quickstart.md)。

> [!NOTE]
> 最多只能使用 5 个术语列表  ，每个列表中的术语数不得超过 10,000 个  。
>

- **视频审查**：[扫描视频中是否有成人内容和挑逗性内容，并获取结果](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs)。 请参阅[快速入门](video-moderation-api.md)。

## <a name="review"></a>审阅

- **图像作业**：[启动用于扫描和创建审阅的审查作业](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs)。 请参阅[快速入门](moderation-jobs-quickstart-dotnet.md)。
- **图像审阅**：[创建人机回环审阅](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs)。 请参阅[快速入门](dotnet-sdk-quickstart.md)。
- **视频审阅**：[创建人机回环视频审阅](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs)。 请参阅[快速入门](video-reviews-quickstart-dotnet.md)
- **视频脚本审阅**：[创建人机回环视频脚本审阅](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs)。请参阅[快速入门](video-reviews-quickstart-dotnet.md)

请参阅 [GitHub 上内容审查器 .NET 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)中的所有 .NET 示例。
