---
title: 从 Windows Azure 媒体编码器迁移到 Media Encoder Standard |Microsoft Docs
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
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: 2e9008d039f014c95d473f3197b48651bdaa5a45
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019363"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>从 Windows Azure 媒体编码器迁移到 Media Encoder Standard

本文介绍了从2019年11月30日停用的旧 Windows Azure 媒体编码器 (WAME) 媒体处理器迁移到 Media Encoder Standard 媒体处理器的步骤。

使用 WAME 对文件进行编码时, 客户通常使用命名预设字符串 ( `H264 Adaptive Bitrate MP4 Set 1080p`如)。 若要迁移, 你的代码需要更新为使用**Media Encoder Standard**媒体处理器而不是 WAME, 并使用类似`H264 Multiple Bitrate 1080p`的等效[系统预设](media-services-mes-presets-overview.md)之一。 

## <a name="migrating-to-media-encoder-standard"></a>迁移到 Media Encoder Standard

下面是使用旧C#组件的典型代码示例。 

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

下面是使用 Media Encoder Standard 的更新版本。

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

如果已使用其架构为 WAME 创建了自己的编码预设, 则会有一个[等效的架构用于 Media Encoder Standard](media-services-mes-schema.md)。 如果有关于如何将较旧的设置映射到新编码器的问题, 请通过 mailto:amshelp@microsoft.com  

## <a name="known-differences"></a>已知差异 

Media Encoder Standard 更可靠、更可靠, 具有更好的性能, 并产生比旧 WAME 编码器更好的输出质量。 另外： 

* Media Encoder Standard 生成的输出文件的命名约定不同于 WAME。
* Media Encoder Standard 生成包含[输入文件元数据](media-services-input-metadata-schema.md)和[输出文件元数据](media-services-output-metadata-schema.md)的项目 (如文件)。
* 如 "定价"[页](https://azure.microsoft.com/pricing/details/media-services/#encoding)上所述 (尤其是在 "常见问题解答" 部分中), 当你使用 Media Encoder Standard 对视频进行编码时, 将基于作为输出生成的文件的持续时间进行计费。 对于 WAME, 将根据输入视频文件的大小和输出视频文件的大小计费。

## <a name="next-steps"></a>后续步骤

* [旧组件](legacy-components.md)
* [定价页](https://azure.microsoft.com/pricing/details/media-services/#encoding)
