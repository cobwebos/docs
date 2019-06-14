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
ms.date: 05/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 25b3209bed98ea217db9e414caa6f08cee6d8c89
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65761887"
---
# <a name="encoding-with-media-services"></a>使用媒体服务进行编码

在媒体服务中的术语编码适用于转换包含数字视频和/或从一种标准格式到另一个，、 目的在于 （a） 减小文件的大小和/或 （b） 生成与兼容的格式的音频文件的过程广泛的设备和应用程序。 此过程也称为视频压缩或转码。 请参阅[数据压缩](https://en.wikipedia.org/wiki/Data_compression)并[什么是编码和转码？](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx)有关概念的进一步讨论。

视频通常情况下传递到设备和应用程序通过[渐进式下载](https://en.wikipedia.org/wiki/Progressive_download)或通过[自适应比特率流式处理](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)。 

* 若要通过渐进式下载传送，可以使用 Azure 媒体服务来转换数字媒体文件 （夹层） 到[MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14)文件，其中包含已编码使用的视频[H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC)编解码器，和使用已编码的音频[AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)编解码器。 此 MP4 文件写入到你的存储帐户中的资产。 可以使用 Azure 存储 Api 或 Sdk (例如，[存储 REST API](../../storage/common/storage-rest-api-auth.md)， [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md)，或[.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) 直接下载该文件。 如果您创建的输出资产的特定容器名称在存储中，使用该位置。 否则，可以使用媒体服务添加到[列出资产容器 Url](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)。 
* 若要准备的自适应比特率流式处理交付内容，需要将夹层文件 （从高到低） 的多个比特率进行编码。 若要确保质量的正常转换，如降低比特率，因此是视频的分辨率。 这会导致所谓的编码阶梯 – 表的分辨率和比特率 (请参阅[自动生成自适应比特率阶梯](autogen-bitrate-ladder.md))。 您可以使用媒体服务在多个比特率 – 在此过程中将夹层文件编码，你将收到一组 MP4 文件和关联流式处理的配置文件，写入到你的存储帐户中的资产。 然后，可以使用[动态打包](dynamic-packaging-overview.md)媒体服务中的功能将通过流式处理协议，如视频[MPEG DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP)并[HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming)。 这需要你创建[流式处理定位符](streaming-locators-concept.md)和生成流 Url 对应于受支持的协议，然后可以传递给设备/应用程序基于其功能关闭。

下图显示了按需编码使用动态打包的工作流。

![动态打包](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

本主题介绍如何使用媒体服务 v3 对内容进行编码。

## <a name="transforms-and-jobs"></a>转换和作业

若要使用媒体服务 v3 进行编码，需创建[转换](https://docs.microsoft.com/rest/api/media/transforms)和[作业](https://docs.microsoft.com/rest/api/media/jobs)。 转换可定义一个方案，用于在编码的设置和输出;作业是该方案的实例。 有关详细信息，请参阅[转换和作业](transforms-jobs-concept.md)。

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

- **EncoderNamedPreset.AACGoodQualityAudio**：生成一个 MP4 文件，其中仅包含以 192 kbps 编码的立体声音频。
- **EncoderNamedPreset.AdaptiveStreaming**（推荐）。 有关详细信息，请参阅[自动生成比特率梯形图](autogen-bitrate-ladder.md)。
- **EncoderNamedPreset.ContentAwareEncodingExperimental** -公开实验性的内容识别的编码预设。 给定输入的任何内容，服务将尝试自动确定层、 相应的比特率和分辨率设置为传递的最佳数量的自适应流式处理。 将继续随时间而改进基础算法。 输出将包含使用视频和音频交错的 MP4 文件。 有关详细信息，请参阅[识别的内容的编码预设的实验性](cae-experimental.md)。
- **EncoderNamedPreset.H264MultipleBitrate1080p**：生成一组 8 GOP 对齐的 MP4 文件（范围从 6000 kbps 到 400 kbps）和立体声 AAC 音频。 起始分辨率为 1080p，之后下降到 360p。
- **EncoderNamedPreset.H264MultipleBitrate720p**：生成一组 6 GOP 对齐的 MP4 文件（范围从 3400 kbps 到 400 kbps）和立体声 AAC 音频。 起始分辨率为 720p，之后下降到 360p。
- **EncoderNamedPreset.H264MultipleBitrateSD**：生成一组 5 GOP 对齐的 MP4 文件（范围从 1600 kbps 到 400 kbps）和立体声 AAC 音频。 起始分辨率为 480p，之后下降到 360p。
- **EncoderNamedPreset.H264SingleBitrate1080p** -生成了 MP4 文件，其中对视频进行编码的 H.264 编解码器在 6750 kbps 和图片高度为 1080年像素，并使用以 64 kbps 速率进行 AAC LC 编解码器编码的立体声音频。
- **EncoderNamedPreset.H264SingleBitrate720p** -生成了 MP4 文件，其中对视频进行编码的 H.264 编解码器在最多返回 4500 kbps 和图片高度为 720 像素，并使用以 64 kbps 速率进行 AAC LC 编解码器编码的立体声音频。
- **EncoderNamedPreset.H264SingleBitrateSD** -生成了 MP4 文件，其中对视频进行编码的 H.264 编解码器在 2200 kbps 和图片高度为 480 像素，并使用以 64 kbps 速率进行 AAC LC 编解码器编码的立体声音频。

若要查看最新的预设列表，请参阅[内置要用于编码视频的预设](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)。

若要了解如何使用预设，请查看[正在上传、 编码和流式处理文件](stream-files-tutorial-with-api.md)。

### <a name="standardencoderpreset-preset"></a>StandardEncoderPreset 预设

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 介绍使用标准编码器对输入视频进行编码时要使用的设置。 自定义转换预设时使用此预设。 

#### <a name="considerations"></a>注意事项

时创建自定义预设，应该注意以下事项：

- 高度和宽度 AVC 内容上的所有值必须都是 4 的倍数。
- 在 Azure 媒体服务 v3 所有编码比特率是比特 / 秒。 这是不同于与我们使用千比特/秒为单位的 v2 Api 的预设。 例如，如果在 v2 中的比特率 （千比特/秒） 已指定为 128，v3 中它将设置为 128000 （比特/秒）。

#### <a name="examples"></a>示例

媒体服务完全支持自定义预设中的所有值，可满足特定的编码需求和要求。 有关演示如何自定义编码器预设的示例，请参阅：

- [自定义预设使用.NET](customize-encoder-presets-how-to.md)
- [自定义预设使用 CLI](custom-preset-cli-howto.md)
- [自定义预设使用 REST](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>预设的架构

在媒体服务 v3 预设是 API 本身中的强类型化的实体。 可以找到这些对象中的"架构"定义[开放 API 规范 （或 Swagger）](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)。 你还可以查看预设的定义 (如**StandardEncoderPreset**) 中[REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)， [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) （或其他媒体服务 v3 SDK 参考文档）。

## <a name="scaling-encoding-in-v3"></a>在 v3 中缩放编码

若要缩放媒体处理，请参阅[使用 CLI 缩放](media-reserved-units-cli-how-to.md)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

* [从 HTTPS URL 使用内置的预设编码](job-input-from-http-how-to.md)
* [对本地文件使用内置的预设进行编码](job-input-from-local-file-how-to.md)
* [生成自定义预设，以满足特定的方案或设备要求](customize-encoder-presets-how-to.md)
* [使用媒体服务上传、编码和流式传输](stream-files-tutorial-with-api.md)
