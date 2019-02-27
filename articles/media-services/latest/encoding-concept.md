---
title: 使用媒体服务在云中编码 - Azure | Microsoft Docs
description: 本主题介绍使用 Azure 媒体服务时的编码过程
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/17/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 52e7fdf6de25300d4f78ee9822aca4ad83f646e9
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408419"
---
# <a name="encoding-with-media-services"></a>使用媒体服务进行编码

使用 Azure 媒体服务可以将高质量的数字媒体文件编码为可在各种浏览器和设备上播放的格式。 例如，可能需要以 Apple 的 HLS 或 MPEG DASH 格式流式传输内容。 本主题介绍如何使用媒体服务 v3 对内容进行编码。

若要使用媒体服务 v3 进行编码，需创建[转换](https://docs.microsoft.com/rest/api/media/transforms)和[作业](https://docs.microsoft.com/rest/api/media/jobs)。 转换用于定义编码设置和输出的配方，作业则是该配方的一个实例。 有关详细信息，请参阅[转换和作业](transforms-jobs-concept.md)。

使用媒体服务进行编码时，可以使用预设来指示编码器应如何处理输入媒体文件。 例如，可以在编码内容中指定所需的视频分辨率和/或音频信道数量。 

可以使用行业最佳做法推荐的内置预设之一快速入门，也可以选择针对特定方案或设备要求生成自定义预设。 有关详细信息，请参阅[使用自定义转换编码](customize-encoder-presets-how-to.md)。 

从 2019 年 1 月开始，使用 Media Encoder Standard 编码生成 MP4 文件时，新的 .mpi 文件会生成并添加到输出资产中。 此 MPI 文件旨在提高[动态打包](dynamic-packaging-overview.md)和流式处理方案的性能。

> [!NOTE]
> 不应修改或删除该 MPI 文件，也不应在存在（或不存在）此类文件的情况下采用服务中的任何依赖项。

## <a name="built-in-presets"></a>内置预设

媒体服务当前支持以下内置编码预设：  

### <a name="builtinstandardencoderpreset-preset"></a>BuiltInStandardEncoderPreset 预设

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 用于设置内置预设，以便使用标准编码器对输入视频进行编码。 

目前支持以下预设：

- **EncoderNamedPreset.AdaptiveStreaming**（推荐）。 有关详细信息，请参阅[自动生成比特率梯形图](autogen-bitrate-ladder.md)。
- **EncoderNamedPreset.AACGoodQualityAudio**：生成一个 MP4 文件，其中仅包含以 192 kbps 编码的立体声音频。
- **EncoderNamedPreset.H264MultipleBitrate1080p**：生成一组 8 GOP 对齐的 MP4 文件（范围从 6000 kbps 到 400 kbps）和立体声 AAC 音频。 起始分辨率为 1080p，之后下降到 360p。
- **EncoderNamedPreset.H264MultipleBitrate720p**：生成一组 6 GOP 对齐的 MP4 文件（范围从 3400 kbps 到 400 kbps）和立体声 AAC 音频。 起始分辨率为 720p，之后下降到 360p。
- **EncoderNamedPreset.H264MultipleBitrateSD**：生成一组 5 GOP 对齐的 MP4 文件（范围从 1600 kbps 到 400 kbps）和立体声 AAC 音频。 起始分辨率为 480p，之后下降到 360p。<br/><br/>有关详细信息，请参阅[对文件进行上传、编码和流式传输](stream-files-tutorial-with-api.md)。

### <a name="standardencoderpreset-preset"></a>StandardEncoderPreset 预设

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 介绍使用标准编码器对输入视频进行编码时要使用的设置。 自定义转换预设时使用此预设。 

#### <a name="custom-presets"></a>自定义预设

媒体服务完全支持自定义预设中的所有值，可满足特定的编码需求和要求。 自定义转换预设时使用 **StandardEncoderPreset** 预设。 有关详细说明和示例，请参阅[如何自定义编码器预设](customize-encoder-presets-how-to.md)。

## <a name="scaling-encoding-in-v3"></a>在 v3 中缩放编码

目前，客户需要使用 Azure 门户或媒体服务 v2 API 来设置 RU（如[缩放媒体处理](../previous/media-services-scale-media-processing-overview.md)中所述）。 

## <a name="next-steps"></a>后续步骤

* [转换和作业](transforms-jobs-concept.md)
* [使用媒体服务上传、编码和流式传输](stream-files-tutorial-with-api.md)
