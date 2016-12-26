---
title: "MES (Media Encoder Standard) 的任务预设 | Microsoft Docs"
description: "本主题概述了 MES (Media Encoder Standard) 的任务预设。"
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 0124c884d3d805a407041e8ecbc5961209a9653d
ms.openlocfilehash: 708444a1b2569cf794ce1f6d8d6c97c7d9e339b7


---

# <a name="task-presets-for-mes-media-encoder-standard"></a>MES (Media Encoder Standard) 的任务预设

`Media Encoder Standard` 定义了一组在创建编码作业时可使用的编码预设  
  
 XML 或 JSON 现有字符串应基于以下文件中显示的预设。 可以将使用自定义值的预设传递到编码器（值必须有效。）有关这些预设中的每个元素的含义及其有效值的说明，请参阅 [Media Encoder Standard 架构](media-services-mes-schema.md)主题。  
  
> [!NOTE]
>  使用预设进行 4k 编码时，应获取 `S3` 保留单位类型。 有关详细信息，请参阅[如何缩放编码](https://azure.microsoft.com/en-us/documentation/articles/media-services-portal-encoding-units)。  
  
 使用 Media Encoder Standard 时，默认启用旋转。 如果视频在智能手机或其他移动设备上采用纵向模式录制，则默认情况下，这些预设会在编码之前将其旋转为横向模式（与 Azure 媒体编码器不同，使用 Azure 媒体编码器时，需手动进行视频旋转，如[此](http://azure.microsoft.com/blog/2014/08/21/advanced-encoding-features-in-azure-media-encoder/)博客的“视频旋转”中所述）。  
  
 预设名称会映射到以下主题中所示的预设。  
  
 [H264 多比特率 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) 生成一组 8 GOP 对齐的 MP4 文件（范围从 6000 kbps 到 400 kbps）和 AAC 5.1 音频。  
  
 [H264 多比特率 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) 生成一组 8 GOP 对齐的 MP4 文件（范围从 6000 kbps 到 400 kbps）和立体声 AAC 音频。  
  
 [H264 多比特率 16x9 (iOS)](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) 生成一组 8 GOP 对齐的 MP4 文件（范围从 8500 kbps 到 200 kbps）和立体声 AAC 音频。  
  
 [H264 多比特率 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) 生成一组 5 GOP 对齐的 MP4 文件（范围从 1900 kbps 到 400 kbps）和 AAC 5.1 音频。  
  
 [H264 多比特率 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) 生成一组 5 GOP 对齐的 MP4 文件（范围从 1900 kbps 到 400 kbps）和立体声 AAC 音频。  
  
 [H264 多比特率 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) 生成一组 12 GOP 对齐的 MP4 文件（范围从 20000 kbps 到 1000 kbps）和 AAC 5.1 音频。  
  
 [H264 多比特率 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) 生成一组 12 GOP 对齐的 MP4 文件（范围从 20000 kbps 到 1000 kbps）和立体声 AAC 音频。  
  
 [H264 多比特率 4x3 (iOS)](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) 生成一组 8 GOP 对齐的 MP4 文件（范围从 8500 kbps 到 200 kbps）和立体声 AAC 音频。  
  
 [H264 多比特率 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) 生成一组 5 GOP 对齐的 MP4 文件（范围从 1600 kbps 到 400 kbps）和 AAC 5.1 音频。  
  
 [H264 多比特率 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) 生成一组 5 GOP 对齐的 MP4 文件（范围从 1600 kbps 到 400 kbps）和立体声 AAC 音频。  
  
 [H264 多比特率 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) 生成一组 6 GOP 对齐的 MP4 文件（范围从 3400 kbps 到 400 kbps）和 AAC 5.1 音频。  
  
 [H264 多比特率 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md)生成一组 6 GOP 对齐的 MP4 文件（范围从 3400 kbps 到 400 kbps）和立体声 AAC 音频。  
  
 [H264 单比特率 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) 生成比特率为 6750 kbps 的单个 MP4 文件和 AAC 5.1 音频。  
  
 [H264 单比特率 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) 生成比特率为 6750 kbps 的单个 MP4 文件和立体声 AAC 音频。  
  
 [H264 单比特率 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) 生成比特率为 18000 kbps 的单个 MP4 文件和 AAC 5.1 音频。  
  
 [H264 单比特率 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) 生成比特率为 18000 kbps 的单个 MP4 文件和立体声 AAC 音频。  
  
 [H264 单比特率 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) 生成比特率为 18000 kbps 的单个 MP4 文件和 AAC 5.1 音频。  
  
 [H264 单比特率 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) 生成比特率为 18000 kbps 的单个 MP4 文件和立体声 AAC 音频。  
  
 [H264 单比特率 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) 生成比特率为 2200 kbps 的单个 MP4 文件和 AAC 5.1 音频。  
  
 [H264 单比特率 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) 生成比特率为 2200 kbps 的单个 MP4 文件和立体声 AAC 音频。  
  
 [H264 单比特率 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) 生成比特率为 4500 kbps 的单个 MP4 文件和 AAC 5.1 音频。  
  
 [H264 单比特率 720p (Android)](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) 预设生成比特率为 2000 kbps 的单个 MP4 文件和立体声 AAC。  
  
 [H264 单比特率 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) 生成比特率为 4500 kbps 的单个 MP4 文件和立体声 AAC 音频。  
  
 [H264 单比特率高品质 SD (Android)](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) 生成比特率为 500 kbps 的单个 MP4 文件和立体声 AAC 音频。  
  
 [H264 单比特率低质量 SD (Android)](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) 生成比特率为 56 kbps 的单个 MP4 文件和立体声 AAC 音频。  
  
 有关媒体服务编码器的详细信息，请参阅[使用 Azure 媒体服务按需编码](https://azure.microsoft.com/en-us/documentation/articles/media-services-encode-asset/)。


<!--HONumber=Nov16_HO5-->


