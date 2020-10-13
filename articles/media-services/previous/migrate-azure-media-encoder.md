---
title: 从 Azure 媒体编码器迁移到 Media Encoder Standard | Microsoft Docs
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 32eab0f729280ad25f45853e05ea982b60a6d9f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89259330"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>从 Azure 媒体编码器迁移到 Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

本文介绍了从旧 Azure 媒体编码器 (AME) 媒体处理器（即将停用）迁移到 Media Encoder Standard 媒体处理器的步骤。 有关停用日期，请参阅此[旧组件](legacy-components.md)主题。

使用 AME 对文件进行编码时，客户通常使用了命名预设字符串，如 `H264 Adaptive Bitrate MP4 Set 1080p`。 为了进行迁移，需要更新代码以使用 **Media Encoder Standard** 媒体处理器而不是 AME，以及一个等效的[系统预设](media-services-mes-presets-overview.md)（如 `H264 Multiple Bitrate 1080p`）。 

## <a name="migrating-to-media-encoder-standard"></a>迁移到 Media Encoder Standard

下面是使用旧媒体处理器的典型 C# 代码示例。 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("AME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    " H264 Adaptive Bitrate MP4 Set 1080p", 
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

如果已使用 AME 的架构为 AME 创建了自己的编码预设，则会有一个[用于 Media Encoder Standard 的等效架构](media-services-mes-schema.md)。 如果你对如何将旧设置映射到新编码器有疑问，请通过 mailto:amshelp@microsoft.com 与我们联系  
## <a name="known-differences"></a>已知差异 

与旧的 AME 编码器相比，Media Encoder Standard 更强大、更可靠、性能更好且输出质量更好。 此外： 

* Media Encoder Standard 生成的输出文件的命名约定与 AME 不同。
* Media Encoder Standard 生成项目，例如包含[输入文件元数据](media-services-input-metadata-schema.md)和[输出文件元数据](media-services-output-metadata-schema.md)的文件。

## <a name="next-steps"></a>后续步骤

* [旧组件](legacy-components.md)
* [定价页](https://azure.microsoft.com/pricing/details/media-services/#encoding)
