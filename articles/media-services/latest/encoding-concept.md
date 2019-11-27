---
title: 用媒体服务编码视频和音频
titleSuffix: Azure Media Services
description: 了解如何通过 Azure 媒体服务对视频和音频进行编码。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: d3de307b1dadf302004fa9fd02c8cf23e36b3046
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73574281"
---
# <a name="encoding-video-and-audio-with-media-services"></a>用媒体服务编码视频和音频

媒体服务中的术语编码适用于将包含数字视频和/或音频的文件从一种标准格式转换为另一种标准格式的过程，其目的为（a）减小文件大小，并/或（b）生成与兼容的格式各种设备和应用。 此过程也称为视频压缩或转码。 有关概念的进一步讨论，请参阅[数据压缩](https://en.wikipedia.org/wiki/Data_compression)及[什么是编码和转码？](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx)。

视频通常通过[渐进式下载](https://en.wikipedia.org/wiki/Progressive_download)或[自适应比特率流式处理](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)传送到设备和应用。

* 若要通过渐进式下载来提供，可以使用 Azure 媒体服务将数字[媒体文件（](https://en.wikipedia.org/wiki/MPEG-4_Part_14)夹层）转换为 "AAC" 文件，其中包含使用[h.264 编解码器](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC)编码的视频，以及使用[](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)编解码器编码的音频。 此 MP4 文件将写入到存储帐户中的资产。 可以使用 Azure 存储 API 或 SDK（例如[存储 REST API](../../storage/common/storage-rest-api-auth.md)或 [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)）直接下载文件。 如果在存储中创建了具有特定容器名称的输出资产，请使用该位置。 否则，可以使用媒体服务[列出资产容器 URL](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)。 
* 若要准备通过自适应比特率流式处理传送的内容，需以多个比特率（从高到低）编码夹层文件。 为了确保质量的正常过渡，视频的分辨率会降低，因为比特率降低。 这会生成所谓的编码阶梯–一张解析表和比特率（请参阅[自动生成的自适应比特率阶梯](autogen-bitrate-ladder.md)）。 可以使用媒体服务在多个比特率上对中间文件进行编码。 在此过程中，你将获得一组未处理的文件和关联的流式处理配置文件，这些文件写入存储帐户中的资产。 然后，可以使用媒体服务中的[动态打包](dynamic-packaging-overview.md)功能，通过 [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) 和 [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming) 等流式处理协议来传送视频。 这要求你创建一个[流式处理定位符](streaming-locators-concept.md)并生成与受支持的协议对应的流式处理 url，然后，可以根据其功能将其移交给设备/应用。

下图显示了使用动态打包进行按需编码的工作流。

![动态打包的按需编码工作流](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

本主题介绍如何使用媒体服务 v3 对内容进行编码。

## <a name="transforms-and-jobs"></a>转换和作业

若要使用媒体服务 v3 进行编码，需创建[转换](https://docs.microsoft.com/rest/api/media/transforms)和[作业](https://docs.microsoft.com/rest/api/media/jobs)。 转换用于定义编码设置和输出的配方，作业则是该配方的一个实例。 有关详细信息，请参阅[转换和作业](transforms-jobs-concept.md)。

使用媒体服务进行编码时，可以使用预设来指示编码器应如何处理输入媒体文件。 例如，可以在编码内容中指定所需的视频分辨率和/或音频信道数量。

可以使用行业最佳做法推荐的内置预设之一快速入门，也可以选择针对特定方案或设备要求生成自定义预设。 有关详细信息，请参阅[使用自定义转换编码](customize-encoder-presets-how-to.md)。

从 2019 年 1 月开始，使用 Media Encoder Standard 编码生成 MP4 文件时，新的 .mpi 文件会生成并添加到输出资产中。 此 MPI 文件旨在提高[动态打包](dynamic-packaging-overview.md)和流式处理方案的性能。

> [!NOTE]
> 不应修改或删除 MPI 文件，或者在服务中对此类文件存在（或不存在）的任何依赖项。

### <a name="creating-job-input-from-an-https-url"></a>从 HTTPS URL 创建作业输入

提交用于处理视频的作业时，必须告知媒体服务查找输入视频的位置。 其中一个选项是指定 HTTPS URL 作为作业输入。 目前，媒体服务 v3 不支持通过 HTTPS Url 进行的分块传输编码。

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

对视频进行编码时，可以指定同时修剪或剪裁源文件，并生成只包含所需输入视频部分的输出。 此功能适用于使用[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)预设或[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)预设生成的任何[转换](https://docs.microsoft.com/rest/api/media/transforms)。

可以指定使用点播视频或实时存档（录制的事件）的单个剪辑创建[作业](https://docs.microsoft.com/rest/api/media/jobs/create)。 作业输入可以是资产或 HTTPS URL。

> [!TIP]
> 如果要在不重新编码视频的情况下流式传输视频的 sublip，请考虑将[预筛选清单用于动态包装](filters-dynamic-manifest-overview.md)器。

#### <a name="examples"></a>示例

参阅示例：

* [使用 .NET 创建视频的子剪辑](subclip-video-dotnet-howto.md)
* [使用 REST 创建视频的子剪辑](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>内置预设

媒体服务支持以下内置编码预设：  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 用于设置内置预设，以便使用标准编码器对输入视频进行编码。

目前支持以下预设：

- **EncoderNamedPreset. AACGoodQualityAudio**：生成单个文件，其中仅包含以 192 kbps 编码的立体声音频。
- **EncoderNamedPreset. microsoft.media.adaptivestreaming.smoothbytestreamhandler** （推荐）：有关详细信息，请参阅[自动生成比特率阶梯](autogen-bitrate-ladder.md)。
- **EncoderNamedPreset. ContentAwareEncodingExperimental**：公开用于识别内容的编码的实验性预设。 根据给定的任何输入内容，该服务会尝试自动确定最佳层数，并尝试通过自适应流式处理传送适当的比特率和分辨率设置。 底层算法将不断演进。 输出将包含带有交错式视频和音频的 MP4 文件。 有关详细信息，请参阅[内容感知编码的试验性预设](cae-experimental.md)。
- **EncoderNamedPreset. H264MultipleBitrate1080p**：生成一组8个 GOP 对齐的文件，范围从 6000 kbps 到 400 kbps，以及立体声 AAC 音频。 起始分辨率为 1080p，之后下降到 360p。
- **EncoderNamedPreset. H264MultipleBitrate720p**：生成六个 GOP 对齐的文件的集合，范围为 3400 kbps 到 400 kbps，以及立体声 AAC 音频。 起始分辨率为 720p，之后下降到 360p。
- **EncoderNamedPreset. H264MultipleBitrateSD**：生成一组5个 GOP 对齐的文件，范围从 1600 kbps 到 400 kbps，以及立体声 AAC 音频。 起始分辨率为 480p，之后下降到 360p。
- **H264SingleBitrate1080p**：生成一个 EncoderNamedPreset 文件，其中视频编码为 6750 kbps 的 node.js 编解码器，图片高度为1080像素，立体声音频以 64 KBPS 的 AAC-LC 编解码器进行编码。
- **H264SingleBitrate720p**：生成一个 EncoderNamedPreset 文件，其中视频编码为 4500 kbps 的 node.js 编解码器，图片高度为720像素，立体声音频以 64 KBPS 的 AAC-LC 编解码器进行编码。
- **H264SingleBitrateSD**：生成一个 EncoderNamedPreset 文件，其中视频编码为 2200 kbps 的 node.js 编解码器，图片高度为480像素，立体声音频以 64 KBPS 的 AAC-LC 编解码器进行编码。

若要查看最新预设列表，请参阅[用于编码视频的内置预设](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)。

若要查看如何使用预设，请参阅[上传、编码和流式处理文件](stream-files-tutorial-with-api.md)。

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 介绍使用标准编码器对输入视频进行编码时要使用的设置。 自定义转换预设时使用此预设。

#### <a name="considerations"></a>注意事项

创建自定义预设时，请注意以下事项：

- AVC 内容的高度和宽度值必须是四的倍数。
- 在 Azure 媒体服务 v3 中，所有编码比特率均以每秒比特数为单位。 这与我们的 v2 API 的预设不同，后者使用 千比特/秒作为单位。 例如，如果 v2 中的比特率指定为 128（千比特/秒），则在 v3 中它将设置为 128000（比特/秒）。

### <a name="customizing-presets"></a>自定义预设

媒体服务完全支持自定义预设中的所有值，可满足特定的编码需求和要求。 有关演示如何自定义编码器预设的示例，请参阅下面的列表：

#### <a name="examples"></a>示例

- [使用 .NET 自定义预设](customize-encoder-presets-how-to.md)
- [使用 CLI 自定义预设](custom-preset-cli-howto.md)
- [使用 REST 自定义预设](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>预设架构

在媒体服务 v3 中，预设是 API 本身中的强类型化实体。 可以在[开放 API 规范（或 Swagger）](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)中找到这些对象的“架构”定义。 也可以在 **REST API**、[.NET SDK](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)（或其他媒体服务 v3 SDK 参考文档）中查看预设定义（例如 [StandardEncoderPreset](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)）。

## <a name="scaling-encoding-in-v3"></a>在 v3 中缩放编码

若要缩放媒体处理，请参阅[使用 CLI 进行缩放](media-reserved-units-cli-how-to.md)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

* [使用媒体服务进行上传、编码和流式](stream-files-tutorial-with-api.md)处理。
* [使用内置预设从 HTTPS URL 编码](job-input-from-http-how-to.md)。
* [使用内置预设对本地文件进行编码](job-input-from-local-file-how-to.md)。
* [构建自定义预设，以确定特定方案或设备要求](customize-encoder-presets-how-to.md)。