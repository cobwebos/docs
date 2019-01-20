---
title: 代码示例 - 内容审查器、.NET
description: 通过 SDK 在 .NET 应用程序中使用内容审查器。
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 6911b9b718edfc84a7c219be448dee06f4a0f835
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259184"
---
# <a name="content-moderator-net-sdk-samples"></a>内容审查器 .NET SDK 示例

下面列出了使用用于 .NET 的 Azure 内容审查器 SDK 生成的代码示例的链接。

- **帮助程序库**：[创建用于其他示例的内容审查器客户端](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs)。 请参阅[快速入门](content-moderator-helper-quickstart-dotnet.md)。

## <a name="moderation"></a>审查

- **图像审查**：[评估图像中是否有成人和挑逗性、文本和人脸](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs)。 请参阅[快速入门](image-moderation-quickstart-dotnet.md)。
- **自定义图像**：[使用自定义图像列表进行审查](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs)。 请参阅[快速入门](image-lists-quickstart-dotnet.md)。

> [!NOTE]
> 最多只能使用 5 个图像列表，每个列表中的图像数不得超过 10,000 张。
>

- **文本审查**：[筛查文本中是否有猥亵内容和个人身份信息 (PII)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs)。 请参阅[快速入门](text-moderation-quickstart-dotnet.md)。
- **自定义术语**：[使用自定义术语列表进行审查](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs)。 请参阅[快速入门](term-lists-quickstart-dotnet.md)。

> [!NOTE]
> 最多只能使用 5 个术语列表，每个列表中的术语数不得超过 10,000 个。
>

- **视频审查**：[扫描视频中是否有成人内容和挑逗性内容，并获取结果](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs)。 请参阅[快速入门](video-moderation-api.md)。

## <a name="review"></a>审阅

- **图像作业**：[启动用于扫描和创建审阅的审查作业](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs)。 请参阅[快速入门](moderation-jobs-quickstart-dotnet.md)。
- **图像审阅**：[创建人机回环审阅](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs)。 请参阅[快速入门](moderation-reviews-quickstart-dotnet.md)。
- **视频审阅**：[创建人机回环视频审阅](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs)。 请参阅[快速入门](video-reviews-quickstart-dotnet.md)
- **视频脚本审阅**：[创建人机回环视频脚本审阅](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs)。请参阅[快速入门](video-reviews-quickstart-dotnet.md)

请参阅 [GitHub 上内容审查器 .NET 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)中的所有 .NET 示例。
