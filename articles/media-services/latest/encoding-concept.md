---
title: 使用媒体服务编码视频和音频
titleSuffix: Azure Media Services
description: 本文介绍如何使用 Azure 媒体服务对视频和音频进行编码。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 8533c99011232385e31f4e698743bbb1c6c00bc5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89300198"
---
# <a name="encoding-video-and-audio-with-media-services"></a>使用媒体服务编码视频和音频

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

媒体服务中的术语“编码”适用于将包含数字视频和/或音频的文件从一种标准格式转换为另一种标准格式的过程，其目的是 (a) 减小文件大小，和/或 (b) 生成与各种设备和应用兼容的格式。 此过程也称为视频压缩或转码。 有关概念的进一步讨论，请参阅[数据压缩](https://en.wikipedia.org/wiki/Data_compression)及[什么是编码和转码？](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx)。

视频通常通过[渐进式下载](https://en.wikipedia.org/wiki/Progressive_download)方式或[自适应比特率流式处理](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)传送到设备和应用。

> [!IMPORTANT]
> 媒体服务不会对已取消或已出错的作业计费。 例如，进度已达到 50% 而被取消的作业不会按作业时间的 50% 计费。 你仅为已完成作业付费。

* 若要通过渐进式下载方式传送内容，可以使用 Azure 媒体服务将数字媒体文件（夹层）转换为 [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) 文件，其中包含已通过 [H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) 编解码器编码的视频，以及已通过 [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) 编解码器编码的音频。 此 MP4 文件将写入到存储帐户中的资产。 可以使用 Azure 存储 API 或 SDK（例如[存储 REST API](../../storage/common/storage-rest-api-auth.md)或 [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)）直接下载文件。 如果在存储中创建了具有特定容器名称的输出资产，请使用该位置。 否则，可以使用媒体服务[列出资产容器 URL](/rest/api/media/assets/listcontainersas)。 
* 若要准备通过自适应比特率流式处理传送的内容，需以多个比特率（从高到低）编码夹层文件。 为了确保质量的平稳过渡，视频的分辨率会随着比特率的降低而降低。 这会造成所谓的编码梯度 – 分辨率和比特率的表（请参阅[自动生成的自适应比特率梯度](autogen-bitrate-ladder.md)）。 可以使用媒体服务以多个比特率编码夹层文件。 在此过程中，你将获得一组 MP4 文件和关联的流式处理配置文件，这些文件将写入到存储帐户中的资产。 然后，可以使用媒体服务中的[动态打包](dynamic-packaging-overview.md)功能，通过 [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) 和 [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming) 等流式处理协议来传送视频。 这需要创建一个[流定位符](streaming-locators-concept.md)并生成与受支持协议对应的流 URL，然后，可以根据功能将这些内容移交到设备/应用。

下图显示了使用动态打包进行按需编码的工作流。

![使用动态打包进行按需编码的工作流](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

本主题介绍如何使用媒体服务 v3 对内容进行编码。

## <a name="transforms-and-jobs"></a>转换和作业

若要使用媒体服务 v3 进行编码，需创建[转换](/rest/api/media/transforms)和[作业](/rest/api/media/jobs)。 转换用于定义编码设置和输出的配方，作业则是该配方的一个实例。 有关详细信息，请参阅[转换和作业](transforms-jobs-concept.md)。

使用媒体服务进行编码时，可以使用预设来指示编码器应如何处理输入媒体文件。 在媒体服务 v3 中，使用标准编码器对文件进行编码。 例如，可以在编码内容中指定所需的视频分辨率和/或音频信道数量。

可以使用行业最佳做法推荐的内置预设之一快速入门，也可以选择针对特定方案或设备要求生成自定义预设。 有关详细信息，请参阅[使用自定义转换编码](customize-encoder-presets-how-to.md)。

从 2019 年 1 月开始，使用标准编码器编码以生成 MP4 文件时，将生成一个新的 .mpi 文件并将其添加到输出资产中。 此 MPI 文件旨在提高[动态打包](dynamic-packaging-overview.md)和流式处理方案的性能。

> [!NOTE]
> 不应修改或删除该 MPI 文件，也不应在存在（或不存在）此类文件的情况下采用服务中的任何依赖项。

### <a name="creating-job-input-from-an-https-url"></a>从 HTTPS URL 创建作业输入

提交用于处理视频的作业时，必须告知媒体服务查找输入视频的位置。 其中一个选项是指定 HTTPS URL 作为作业输入。 媒体服务 v3 目前不支持基于 HTTPS URL 的块式传输编码。

#### <a name="examples"></a>示例

* [使用 .NET 从 HTTPS URL 进行编码](stream-files-dotnet-quickstart.md)
* [使用 REST 从 HTTPS URL 进行编码](stream-files-tutorial-with-rest.md)
* [使用 CLI 从 HTTPS URL 进行编码](stream-files-cli-quickstart.md)
* [使用 Node.js 从 HTTPS URL 进行编码](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>从本地文件创建作业输入

可将输入视频存储为媒体服务资产，这种情况下会基于文件（存储在本地或 Azure Blob 存储中）创建输入资产。

#### <a name="examples"></a>示例

[使用内置预设对本地文件进行编码](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>使用子剪辑创建作业输入

对视频进行编码时，可以指定同时修剪或剪裁源文件，并生成只包含所需输入视频部分的输出。 此功能适用于使用 [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 预设或 [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) 预设生成的任何[转换](/rest/api/media/transforms)。

可以指定使用点播视频或实时存档（录制的事件）的单个剪辑创建[作业](/rest/api/media/jobs/create)。 作业输入可以是资产或 HTTPS URL。

> [!TIP]
> 若要流式传输视频的子剪辑而不重新编码视频，请考虑[使用动态打包器预筛选清单](filters-dynamic-manifest-overview.md)。

#### <a name="examples"></a>示例

参阅示例：

* [使用 .NET 创建视频的子剪辑](subclip-video-dotnet-howto.md)
* [使用 REST 创建视频的子剪辑](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>内置预设

媒体服务支持以下内置编码预设：  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 用于设置内置预设，以便使用标准编码器对输入视频进行编码。

目前支持以下预设：

- **EncoderNamedPreset.AACGoodQualityAudio**：生成一个 MP4 文件，其中仅包含以 192 kbps 编码的立体声音频。
- **EncoderNamedPreset.AdaptiveStreaming**（推荐）：有关详细信息，请参阅[自动生成比特率梯形图](autogen-bitrate-ladder.md)。
- **EncoderNamedPreset.ContentAwareEncoding**：公开内容感知编码的预设。 在提供任何输入内容的情况下，服务将尝试自动确定最佳层数，以及自适应流式处理适合使用的比特率和分辨率设置。 底层算法将不断演进。 输出将包含带有交错式视频和音频的 MP4 文件。 有关详细信息，请参阅[内容感知编码](content-aware-encoding.md)。

  > [!NOTE]
  > 请确保使用 ContentAwareEncoding 而不使用 ContentAwareEncodingExperimental。
- **EncoderNamedPreset.H264MultipleBitrate1080p**：生成一组 8 GOP 对齐的 MP4 文件（范围从 6000 kbps 到 400 kbps）和立体声 AAC 音频。 起始分辨率为 1080p，之后下降到 360p。
- **EncoderNamedPreset.H264MultipleBitrate720p**：生成一组 6 GOP 对齐的 MP4 文件（范围从 3400 kbps 到 400 kbps）和立体声 AAC 音频。 起始分辨率为 720p，之后下降到 360p。
- **EncoderNamedPreset.H264MultipleBitrateSD**：生成一组 5 GOP 对齐的 MP4 文件（范围从 1600 kbps 到 400 kbps）和立体声 AAC 音频。 起始分辨率为 480p，之后下降到 360p。
- **EncoderNamedPreset.H264SingleBitrate1080p**：生成一个 MP4 文件，其中的视频已使用 H.264 编解码器以 6750 kbps 编码，图片高度为 1080 像素，立体声音频已使用 AAC-LC 编解码器以 64 kbps 编码。
- **EncoderNamedPreset.H264SingleBitrate720p**：生成一个 MP4 文件，其中的视频已使用 H.264 编解码器以 4500 kbps 编码，图片高度为 720 像素，立体声音频已使用 AAC-LC 编解码器以 64 kbps 编码。
- **EncoderNamedPreset.H264SingleBitrateSD**：生成一个 MP4 文件，其中的视频已使用 H.264 编解码器以 2200 kbps 编码，图片高度为 480 像素，立体声音频已使用 AAC-LC 编解码器以 64 kbps 编码。

若要查看最新预设列表，请参阅[用于编码视频的内置预设](/rest/api/media/transforms/createorupdate#encodernamedpreset)。

若要了解预设的用法，请参阅[上传、编码和流式处理文件](stream-files-tutorial-with-api.md)。

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) 介绍使用标准编码器对输入视频进行编码时要使用的设置。 自定义转换预设时使用此预设。

#### <a name="considerations"></a>注意事项

创建自定义预设时，请注意以下事项：

- AVC 内容上的所有高度和宽度值必须是 4 的倍数。
- 在 Azure 媒体服务 v3 中，所有编码比特率均以每秒比特数为单位。 这与我们的 v2 API 的预设不同，后者使用 千比特/秒作为单位。 例如，如果 v2 中的比特率指定为 128（千比特/秒），则在 v3 中它将设置为 128000（比特/秒）。

### <a name="customizing-presets"></a>自定义预设

媒体服务完全支持自定义预设中的所有值，可满足特定的编码需求和要求。 有关演示如何自定义编码器预设的示例，请参阅以下列表：

#### <a name="examples"></a>示例

- [使用 .NET 自定义预设](customize-encoder-presets-how-to.md)
- [使用 CLI 自定义预设](custom-preset-cli-howto.md)
- [使用 REST 自定义预设](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>预设架构

在媒体服务 v3 中，预设是 API 本身中的强类型化实体。 可以在[开放 API 规范（或 Swagger）](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)中找到这些对象的“架构”定义。 也可以在 [REST API](/rest/api/media/transforms/createorupdate#standardencoderpreset)、[.NET SDK](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)（或其他媒体服务 v3 SDK 参考文档）中查看预设定义（例如 **StandardEncoderPreset**）。

## <a name="scaling-encoding-in-v3"></a>在 v3 中缩放编码

若要缩放媒体处理，请参阅[使用 CLI 进行缩放](media-reserved-units-cli-how-to.md)。

## <a name="billing"></a>计费

媒体服务不会对已取消或已出错的作业计费。 例如，进度已达到 50% 而被取消的作业不会按作业时间的 50% 计费。 你仅为已完成作业付费。

有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/media-services/)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

* [使用媒体服务上传、编码和流式传输](stream-files-tutorial-with-api.md)。
* [使用内置预设从 HTTPS URL 进行编码](job-input-from-http-how-to.md)。
* [使用内置预设对本地文件进行编码](job-input-from-local-file-how-to.md)。
* [构建自定义预设，以确定特定方案或设备要求](customize-encoder-presets-how-to.md)。
