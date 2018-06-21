---
title: 使用 Azure 媒体服务在云中编码 | Microsoft Docs
description: 本主题介绍使用 Azure 媒体服务时的编码过程
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2018
ms.author: juliako
ms.openlocfilehash: e1c7536c59b110ae3dd753ff5f4b01195f8dadca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658985"
---
# <a name="encoding-with-azure-media-services"></a>使用 Azure 媒体服务进行编码

使用 Azure 媒体服务可以将高质量的数字媒体文件编码为可在各种浏览器和设备上播放的格式。 例如，可能需要以 Apple 的 HLS 或 MPEG DASH 格式流式传输内容。 媒体服务还可用于分析视频或音频内容。 本主题介绍如何使用媒体服务 v3 对内容进行编码。

若要使用媒体服务 v3 进行编码，需创建转换和作业。 转换用于定义编码设置和输出的配方，作业则是该配方的一个实例。 有关详细信息，请参阅[转换和作业](transform-concept.md)。

使用 Azure 媒体服务进行编码时，可以使用预设来指示编码器应如何处理输入媒体文件。 例如，可以在编码内容中指定所需的视频分辨率和/或音频信道数量。 

可以使用行业最佳做法推荐的内置预设之一快速入门，也可以选择针对特定方案或设备要求生成自定义预设。 

在 [OpenAPI 规范](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2018-03-30-preview)中查找有关编码器的详细信息。 

## <a name="built-in-presets"></a>内置预设

媒体服务当前支持以下内置编码预设：  

|**预设名称**|**方案**|**详细信息**|
|---|---|---|
|**AudioAnalyzerPreset**|分析音频|该预设应用一组基于 AI 的预定义分析操作，包括语音听录。 目前，该预设支持处理单个音轨的内容。<br/>可以使用 BCP-47 格式“language tag-region”（例如“en-US”）为输入中的音频有效负载指定语言。 受支持语言列表中包括“en-US”、“en-GB”、“es-ES”、“es-MX”、“fr-FR”、“it-IT”、“ja-JP”、“pt-BR”、“zh-CN”。|
|**VideoAnalyzerPreset**|分析音频和视频|从音频和视频中提取见解（丰富的元数据），并输出 JSON 格式的文件。 可以指定在处理视频文件时是否只想提取音频见解。 有关详细信息，请参阅[分析视频](analyze-videos-tutorial-with-api.md)。|
|**BuiltInStandardEncoderPreset**|流式处理|用于设置内置预设，以便使用标准编码器对输入视频进行编码。 <br/>目前支持以下预设：<br/>**EncoderNamedPreset.AdaptiveStreaming**（推荐）。 有关详细信息，请参阅[自动生成比特率梯形图](autogen-bitrate-ladder.md)。<br/>**EncoderNamedPreset.AACGoodQualityAudio**：生成一个 MP4 文件，其中仅包含以 192 kbps 编码的立体声音频。<br/>**EncoderNamedPreset.H264MultipleBitrate1080p**：生成一组 8 GOP 对齐的 MP4 文件（范围从 6000 kbps 到 400 kbps）和立体声 AAC 音频。 起始分辨率为 1080p，之后下降到 360p。<br/>**EncoderNamedPreset.H264MultipleBitrate720p**：生成一组 6 GOP 对齐的 MP4 文件（范围从 3400 kbps 到 400 kbps）和立体声 AAC 音频。 起始分辨率为 720p，之后下降到 360p。<br/>**EncoderNamedPreset.H264MultipleBitrateSD**：生成一组 5 GOP 对齐的 MP4 文件（范围从 1600 kbps 到 400 kbps）和立体声 AAC 音频。 起始分辨率为 480p，之后下降到 360p。<br/><br/>有关详细信息，请参阅[对文件进行上传、编码和流式传输](stream-files-tutorial-with-api.md)。|
|**StandardEncoderPreset**|流式处理|介绍使用标准编码器对输入视频进行编码时要使用的设置。 <br/>自定义转换预设时使用此预设。 有关详细信息，请参阅[如何自定义转换预设](customize-encoder-presets-how-to.md)。|

## <a name="custom-presets"></a>自定义预设

媒体服务完全支持自定义预设中的所有值，可满足特定的编码需求和要求。 自定义转换预设时使用 **StandardEncoderPreset** 预设。 有关详细说明和示例，请参阅[如何自定义编码器预设](customize-encoder-presets-how-to.md)。

## <a name="scaling-encoding-in-v3"></a>在 v3 中缩放编码

目前，客户需要使用 Azure 门户或 AMS v2 API 来设置 RU（如[缩放媒体处理](../previous/media-services-scale-media-processing-overview.md)中所述）。 

## <a name="next-steps"></a>后续步骤

### <a name="tutorials"></a>教程

以下教程介绍如何使用媒体服务对内容进行编码：

* [使用 Azure 媒体服务上传、编码和流式传输](stream-files-tutorial-with-api.md)
* [使用 Azure 媒体服务分析视频](analyze-videos-tutorial-with-api.md)

### <a name="code-samples"></a>代码示例

以下代码示例包含展示如何使用媒体服务进行编码的代码：

* [.NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore)
* [CLI 2.0](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)

### <a name="sdks"></a>SDK

可以使用以下任何受支持的媒体服务 v3 SDK 对内容进行编码。

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [REST](https://docs.microsoft.com/rest/api/media/transforms)
* [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices)
* [Python](https://docs.microsoft.com/python/api/overview/azure/media-services?view=azure-python)

