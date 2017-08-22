---
title: "如何使用用于 .NET 的 Azure 媒体服务 SDK 创建媒体处理器 | Microsoft Docs"
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
ms.date: 07/31/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: cb14bebfaf073cb38bdc1f1718ef3d8c7f6c45fc
ms.contentlocale: zh-cn
ms.lasthandoff: 08/01/2017

---
# <a name="how-to-get-a-media-processor-instance"></a>如何：获取媒体处理器实例
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>概述
在媒体服务中，媒体处理器是完成特定处理任务（例如，对媒体内容进行编码、格式转换、加密或解密）的组件。 通常，创建一个任务以便对媒体内容进行编码、加密或格式转换时，就需要创建一个媒体处理器。

## <a name="azure-media-processors"></a>Azure 媒体处理器 

以下主题提供媒体处理器列表：

* [编码媒体处理器](scenarios-and-availability.md#encoding-media-processors)
* [分析媒体处理器](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>获取媒体处理器

以下方法演示了如何获取媒体处理器实例。 该代码示例假设使用名为 **_context** 的模块级变量来引用[如何：以编程方式连接到媒体服务](media-services-use-aad-auth-to-access-ams-api.md)部分中描述的服务器上下文。

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


