---
title: "如何创建媒体处理器 | Microsoft Docs"
description: "了解如何创建一个媒体处理器组件用来为 Azure 媒体服务编码、转换格式、加密或解密媒体内容。 代码示例用 C# 编写且使用适用于 .NET 的媒体服务 SDK。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 88f6e1da090eb6088e54c6f81d0f83b1737d3c2c
ms.lasthandoff: 04/12/2017


---
# <a name="how-to-get-a-media-processor-instance"></a>如何：获取媒体处理器实例
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>概述
在媒体服务中，媒体处理器是完成特定处理任务（例如，对媒体内容进行编码、格式转换、加密或解密）的组件。 通常，当你创建一个任务以便对媒体内容进行编码、加密或格式转换时，就需要创建一个媒体处理器。

下表提供了每个可用媒体处理器的名称和说明。

| 媒体处理器名称 | 说明 | 更多信息 |
| --- | --- | --- |
| 媒体编码器标准版 |为按需编码提供标准功能。 |[简要介绍并比较 Azure 按需媒体编码器](media-services-encode-asset.md) |
| 媒体编码器高级工作流 |允许你使用媒体编码器高级工作流运行编码任务。 |[简要介绍并比较 Azure 按需媒体编码器](media-services-encode-asset.md) |
| Azure Media Indexer |使媒体文件和内容可搜索，以及生成隐藏字幕跟踪和关键字。 |[Azure Media Indexer](media-services-index-content.md) |
| Azure Media Hyperlapse（预览） |使你能够通过视频防抖动功能消除视频中的“晃动”。 也可使将内容制作为可用剪辑的速度加快。 |[Azure Media Hyperlapse](media-services-hyperlapse-content.md) |
| Azure Media Encoder |已放弃 | |
| 存储解密 |已放弃 | |
| Azure 媒体包装器 |已放弃 | |
| Azure 媒体加密器 |已放弃 | |

## <a name="get-media-processor"></a>获取媒体处理器
以下方法演示了如何获取媒体处理器实例。 该代码示例假设使用名为 **_context** 的模块级变量来引用[如何：以编程方式连接到媒体服务](media-services-dotnet-connect-programmatically.md)部分中描述的服务器上下文。

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>后续步骤
了解如何获取媒体处理器实例后，请转到[如何对资产进行编码](media-services-dotnet-encode-with-media-encoder-standard.md)主题，其中说明了如何使用 Media Encoder Standard 对资产进行编码。


