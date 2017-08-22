---
title: "Azure 点播媒体编码器之间的对比 | Microsoft 文档"
description: "本主题对 **Media Encoder Standard** 和**媒体编码器高级工作流** 的编码功能进行了比较。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: a0e93881e0d75541fc04d7bc736459f8109d1c9f
ms.contentlocale: zh-cn
ms.lasthandoff: 07/26/2017

---

# <a name="comparison-of-azure-on-demand-media-encoders"></a>Azure 点播媒体编码器之间的对比

本主题对 **Media Encoder Standard** 和**媒体编码器高级工作流**的编码功能进行了比较。

## <a name="video-and-audio-processing-capabilities"></a>视频和音频处理功能

下表比较了 Media Encoder Standard (MES) 和媒体编码器高级工作流 (MEPW) 的功能。 

|功能|媒体编码器标准版|媒体编码器高级工作流|
|---|---|---|
|在编码时应用条件逻辑<br/>（例如，如果输入是 HD，执行 5.1 音频编码）|否|是|
|隐藏式字幕|否|[是](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Dolby® 专业响度校正](http://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> （使用 Dialogue Intelligence™）|否|是|
|反交错、反转电视电影|基本|广播质量|
|检测和删除黑色边框 <br/>（左右黑边、上下黑边）|否|是|
|生成缩略图|[是](media-services-dotnet-generate-thumbnail-with-mes.md)|[是](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|剪裁/修整并拼接视频|[是](media-services-advanced-encoding-with-mes.md#trim_video)|是|
|音频或视频叠加层|[是](media-services-advanced-encoding-with-mes.md#overlay)|[是](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|图形叠加层|来自图像源|来自图像和文本源|
|多语言音轨|受限制|[是](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>每个编码器使用的计费表
| 媒体处理器名称 | 适用定价 | 说明 |
| --- | --- | --- |
| **Media Encoder Standard** |编码器 |将基于作为输出生成的所有媒体文件的总持续时间（以分钟为单位）对编码任务进行收费，按照“编码器”列下[此处][1]指定的费率。 |
| **媒体编码器高级工作流** |高级编码器 |将基于作为输出生成的所有媒体文件的总持续时间（以分钟为单位）对编码任务进行收费，按照“高级编码器”列下[此处][1]指定的费率。 |

## <a name="input-containerfile-formats"></a>输入容器/文件格式
| 输入容器/文件格式 | 媒体编码器标准版 | 媒体编码器高级工作流 |
| --- | --- | --- |
| Adobe® Flash® F4V |是 |是 |
| MXF/SMPTE 377M |是 |是 |
| GXF |是 |是 |
| Mpeg-2 传输流 |是 |是 |
| MPEG-2 节目流 |是 |是 |
| MPEG-4/MP4 |是 |是 |
| Windows Media/ASF |是 |是 |
| AVI（8 位/10 位未压缩） |是 |是 |
| 3GPP/3GPP2 |是 |否 |
| 平滑流文件格式 (PIFF 1.3) |是 |否 |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |是 |否 |
| Matroska/WebM |是 |否 |
| QuickTime (.mov) |是 |否 |

## <a name="input-video-codecs"></a>输入视频编解码器
| 输入视频编解码器 | 媒体编码器标准版 | 媒体编码器高级工作流 |
| --- | --- | --- |
| AVC 8 位/10 位，最高支持 4:2:2，包括 AVCIntra |8 位 4:2:0 和 4:2:2 |是 |
| Avid DNxHD（MXF 格式） |是 |是 |
| DVCPro/DVCProHD（MXF 格式） |是 |是 |
| JPEG2000 |是 |是 |
| Mpeg-2（最高支持 422 Profile 和 High Level；包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs® 和 D10 等变体） |最高支持 422 Profile |是 |
| MPEG-1 |是 |是 |
| Windows Media 视频/VC-1 |是 |是 |
| Canopus HQ/HQX |否 |否 |
| Mpeg-4 第 2 部分 |是 |否 |
| [Theora](https://en.wikipedia.org/wiki/Theora) |是 |否 |
| Apple ProRes 422 |是 |否 |
| Apple ProRes 422 LT |是 |否 |
| Apple ProRes 422 HQ |是 |否 |
| Apple ProRes Proxy |是 |否 |
| Apple ProRes 4444 |是 |否 |
| Apple ProRes 4444 XQ |是 |否 |

## <a name="input-audio-codecs"></a>输入音频编解码器
| 输入音频编解码器 | 媒体编码器标准版 | 媒体编码器高级工作流 |
| --- | --- | --- |
| AES（SMPTE 331M 和 302M、AES3-2003） |否 |是 |
| Dolby® E |否 |是 |
| Dolby® Digital (AC3) |否 |是 |
| Dolby® Digital Plus (E-AC3) |否 |是 |
| AAC(AAC-LC、AAC-HE 和 AAC-HEv2；最高支持 5.1） |是 |是 |
| MPEG Layer 2 |是 |是 |
| MP3 (MPEG-1 Audio Layer 3) |是 |是 |
| Windows Media 音频 |是 |是 |
| WAV/PCM |是 |是 |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |是 |否 |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |是 |否 |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |是 |否 |

## <a name="output-containerfile-formats"></a>输出容器/文件格式
| 输出容器/文件格式 | 媒体编码器标准版 | 媒体编码器高级工作流 |
| --- | --- | --- |
| Adobe® Flash® F4V |否 |是 |
| MXF（OP1a、XDCAM 和 AS02） |否 |是 |
| DPP（包括 AS11） |否 |是 |
| GXF |否 |是 |
| MPEG-4/MP4 |是 |是 |
| MPEG-TS |是 |是 |
| Windows Media/ASF |否 |是 |
| AVI（8 位/10 位未压缩） |否 |是 |
| 平滑流文件格式 (PIFF 1.3) |否 |是 |

## <a name="output-video-codecs"></a>输出视频编解码器
| 输出视频编解码器 | 媒体编码器标准版 | 媒体编码器高级工作流 |
| --- | --- | --- |
| AVC（H.264；8 位；最高支持 High Profile、Level 5.2；4K Ultra HD；AVC Intra） |仅限 8 位 4:2:0 |是 |
| Avid DNxHD（MXF 格式） |否 |是 |
| Mpeg-2（最高支持 422 Profile 和 High Level；包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs® 和 D10 等变体） |否 |是 |
| MPEG-1 |否 |是 |
| Windows Media 视频/VC-1 |否 |是 |
| JPEG 缩图创建 |是 |是 |
| PNG 缩略图创建 |是 |是 |
| BMP 缩略图创建 |是 |否 |

## <a name="output-audio-codecs"></a>输出音频编解码器
| 输出音频编解码器 | 媒体编码器标准版 | 媒体编码器高级工作流 |
| --- | --- | --- |
| AES（SMPTE 331M 和 302M、AES3-2003） |否 |是 |
| Dolby® Digital (AC3) |否 |是 |
| Dolby® Digital Plus (E-AC3)，最高支持 7.1 |否 |是 |
| AAC(AAC-LC、AAC-HE 和 AAC-HEv2；最高支持 5.1） |是 |是 |
| MPEG Layer 2 |否 |是 |
| MP3 (MPEG-1 Audio Layer 3) |否 |是 |
| Windows Media 音频 |否 |是 |

>[!NOTE]
>如果编码为 Dolby® Digital (AC3)，则只能将输出写入到 ISO MP4 文件。

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>相关文章
* [通过自定义 Media Encoder Standard 预设执行高级编码任务](media-services-custom-mes-presets-with-dotnet.md)
* [配额和限制](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

