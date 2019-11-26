---
title: 从 Windows Azure 媒体编码器迁移到 Media Encoder Standard | Microsoft Docs
description: 本主题介绍如何从 Azure 媒体编码器迁移到 Media Encoder Standard 媒体处理器。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: 862643cb4eb26d7d88aa81d05433066a927a69aa
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424039"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>从 Windows Azure 媒体编码器迁移到 Media Encoder Standard

本文介绍了从2020年3月1日停用的旧 Windows Azure 媒体编码器（WAME）媒体处理器迁移到 Media Encoder Standard 媒体处理器的步骤。

使用 WAME 对文件进行编码时，客户通常使用了命名预设字符串，如 `H264 Adaptive Bitrate MP4 Set 1080p`。 为了进行迁移，需要更新代码以使用 **Media Encoder Standard** 媒体处理器而不是 WAME，以及一个等效的[系统预设](media-services-mes-presets-overview.md)（如 `H264 Multiple Bitrate 1080p`）。 

## <a name="migrating-to-media-encoder-standard"></a>迁移到 Media Encoder Standard

下面是使用旧组件的典型 C# 代码示例。 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

下面是使用 Media Encoder Standard 的已更新版本。

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>高级方案 

如果已使用 WAME 的架构为 WAME 创建了自己的编码预设，则会有一个[用于 Media Encoder Standard 的等效架构](media-services-mes-schema.md)。

## <a name="known-differences"></a>已知差异 

与旧的 WAME 编码器相比，Media Encoder Standard 更强大、更可靠、性能更好且输出质量更好。 另外： 

* Media Encoder Standard 生成的输出文件的命名约定与 WAME 不同。
* Media Encoder Standard 生成项目，例如包含[输入文件元数据](media-services-input-metadata-schema.md)和[输出文件元数据](media-services-output-metadata-schema.md)的文件。
* 如[定价页](https://azure.microsoft.com/pricing/details/media-services/#encoding)（特别是“常见问题解答”部分）所述，使用 Media Encoder Standard 对视频进行编码时，将根据作为输出产生的文件的持续时间进行计费。 使用 WAME，将根据输入视频文件和输出视频文件的大小进行计费。

## <a name="need-help"></a>需要帮助？

可以通过导航到[新建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)来开具支持票证

## <a name="next-steps"></a>后续步骤

* [旧组件](legacy-components.md)
* [定价页](https://azure.microsoft.com/pricing/details/media-services/#encoding)
