---
title: "Azure 媒体服务编码错误代码 | Microsoft 文档"
description: "此主题列出了在执行编码任务期间发生错误的情况下可能返回的错误代码。"
services: media-services
documentationcenter: 
author: juliako
manager: SyntaxC4
editor: 
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: b341416f837d03e0532b4f8c0a3f3f85641a8995
ms.openlocfilehash: d1e6421404d9c8845eb3ccd30d84c0c8cf5930b8
ms.contentlocale: zh-cn
ms.lasthandoff: 01/27/2017

---

# <a name="encoding-error-codes"></a>编码错误代码

下表列出了在执行编码任务期间发生错误的情况下可能返回的错误代码。  若要获取 .NET 代码中的错误详细信息，请使用 [ErrorDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) 类。 若要获取 REST 代码中的错误详细信息，请使用 [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) REST API。

| ErrorDetail.Code | 出错的可能原因 |
| --- | --- |
| Unknown |执行任务期间发生未知的错误 |
| ErrorDownloadingInputAssetMalformedContent |涵盖下载输入资产时出错（例如，错误的文件名称、文件长度为零、格式不正确，等等）的错误类别。 |
| ErrorDownloadingInputAssetServiceFailure |涵盖服务端问题（例如，下载时发生网络或存储错误）的错误类别。 |
| ErrorParsingConfiguration |任务 <see cref="MediaTask.PrivateData"/>（配置）无效的错误类别，例如，配置不是有效的系统预设或包含无效的 XML。 |
| ErrorExecutingTaskMalformedContent |在执行任务期间因输入媒体文件内部问题导致失败的错误类别。 |
| ErrorExecutingTaskUnsupportedFormat |媒体处理器无法处理提供的文件（不支持的媒体格式或与配置不匹配）的错误类别。 例如，尝试从只包含视频的资产生成只包含音频的输出 |
| ErrorProcessingTask |媒体处理器在处理与内容无关的任务时发生的其他错误类别。 |
| ErrorUploadingOutputAsset |上载输出资产时的错误类别 |
| ErrorCancelingTask |涵盖尝试取消任务时失败的错误类别 |
| TransientError |包含暂时性问题的错误类别（例如 Azure 存储的临时网络问题） |

若要获得**媒体服务**团队的帮助，请打开[支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>相关文章
* [通过自定义 Media Encoder Standard 预设执行高级编码任务](media-services-custom-mes-presets-with-dotnet.md)
* [配额和限制](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

