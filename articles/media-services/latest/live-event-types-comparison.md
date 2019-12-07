---
title: Azure 媒体服务 LiveEvent 类型 | Microsoft Docs
description: 在 Azure 媒体服务中，实时事件可以是以下两种类型之一：实时编码和传递。 本文显示比较实时事件类型的详细表。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 8377c4339b07e0b917e10ed413ffc79baef91fac
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888387"
---
# <a name="live-event-types-comparison"></a>实时事件类型比较

在 Azure 媒体服务中，[直播活动](https://docs.microsoft.com/rest/api/media/liveevents)可以是下述两种类型之一：实时编码和直通。 

## <a name="types-comparison"></a>类型比较 

下表对实时事件类型的功能进行了比较。 使用[LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)在创建过程中设置这些类型：

* **LiveEventEncodingType** -本地实时编码器发送多比特率流。 引入流通过实时事件传递，无需进行任何进一步的处理。 
* **LiveEventEncodingType** -本地实时编码器将单比特率流发送到实时事件，媒体服务将创建多比特率流。 如果贡献源的分辨率为720p 或更高，则**Default720p**预设将编码一组6分辨率/比特率对（详细信息将在本文稍后介绍）。
* **LiveEventEncodingType. Premium1080p** -本地实时编码器将单比特率流发送到实时事件，媒体服务将创建多比特率流。 Default1080p 预设指定分辨率/比特率对的输出集（详细信息将在本文稍后介绍）。 

| Feature | 直通直播活动 | 标准或 Premium1080p 实时事件 |
| --- | --- | --- |
| 单比特率输入在云中被编码为多比特率 |No |是 |
| 贡献源的最大视频分辨率 |4K（4096x2160，60 帧/秒） |1080p（1920x1088，30 帧/秒）|
| 贡献源中建议的最大层数|最大为 12|1 个音频|
| 输出中的最大层数| 与输入相同|最多6个（请参阅下面的系统预设）|
| 贡献源的最大聚合带宽|60 Mbps|N/A|
| 贡献中单个层的最大比特率 |20 Mbps|20 Mbps|
| 支持多语言音轨|是|No|
| 支持的输入视频编解码器 |H.264/AVC 和 H.265/HEVC|H.264/AVC|
| 支持的输出视频编解码器|与输入相同|H.264/AVC|
| 支持的视频位深、输入和输出|最多 10 位，包括 HDR 10/HLG|8 位|
| 支持的输入音频编解码器|AAC-LC、HE-AAC v1、HE-AAC v2|AAC-LC、HE-AAC v1、HE-AAC v2|
| 支持的输出音频编解码器|与输入相同|AAC-LC|
| 输出视频的最大视频分辨率|与输入相同|标准-720p，Premium1080p-1080p|
| 输入视频的最大帧速率|60帧/秒|标准或 Premium1080p-30 帧/秒|
| 输入协议|RTMP、分段 MP4（平滑流式处理）|RTMP、分段 MP4（平滑流式处理）|
| 价格|请参阅[定价页](https://azure.microsoft.com/pricing/details/media-services/)并单击“实时视频”选项卡|请参阅[定价页](https://azure.microsoft.com/pricing/details/media-services/)并单击“实时视频”选项卡|
| 最长运行时间| 24 小时 x 365 天，实时线性 | 24小时 x 365 天，实时线性（预览）|
| 传递嵌入式 CEA 608/708 字幕数据的能力|是|是|
| 支持插入静态图像|No|No|
| 支持通过 API 发出广告指示| No|No|
| 支持通过带内 SCTE35 消息发出广告指示|是|是|
| 能够从贡献源出现的短时停顿中恢复|是|部分|
| 支持非一致性输入 GOP|是|否 – 输入必须具有固定的 GOP 持续时间|
| 支持可变帧率输入|是|否 - 输入必须是固定的帧速率。 轻微的帧率变化是容许的，例如在高速运动情况下出现的轻微帧率变化。 但贡献源不能丢弃帧速率（例如，15帧/秒）。|
| 输入源丢失时，会自动关闭直播活动|No|12 小时后，如果没有运行的 LiveOutput|

## <a name="system-presets"></a>系统预设

实时编码器的输出中包含的分辨率和比特率由[presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding)决定。 如果使用**标准**实时编码器（LiveEventEncodingType），则*Default720p*预设指定一组6分辨率/比特率对（如下所述）。 否则，如果使用**Premium1080p**实时编码器（LiveEventEncodingType. Premium1080p），则*Default1080p*预设指定分辨率/比特率对的输出集。

> [!NOTE]
> 如果为标准实时编码设置了 Default1080p 预设，则不能将其应用于实时事件-您将会收到错误。 如果尝试将 Default720p 预设应用到 Premium1080p 实时编码器，还会出现错误。

### <a name="output-video-streams-for-default720p"></a>输出 Default720p 的视频流

如果贡献源的分辨率为720p 或更高，则**Default720p**预设会将源编码到以下6层中。 在下表中，比特率为 kbps，MaxFPS 表示允许的最大帧速率（在帧/秒）中，配置文件表示使用的 H-p 配置文件。

| Bitrate | 宽度 | 高度 | MaxFPS | 个人资料 |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |高 |
| 2200 |960 |540 |30 |高 |
| 1350 |704 |396 |30 |高 |
| 850 |512 |288 |30 |高 |
| 550 |384 |216 |30 |高 |
| 200 |340 |192 |30 |高 |

> [!NOTE]
> 如果需要自定义实时编码预设，请通过 Azure 门户打开支持票证。 你应当指定所需的分辨率和比特率的表。 请确认只有一个 720p 的图层，最多有 6 个图层。 还要指定你要为标准实时编码器请求预设。
> 可在一段时间内调整比特率和分辨率的特定值

### <a name="output-video-streams-for-default1080p"></a>输出 Default1080p 的视频流

如果贡献源的分辨率为1080p，则**Default1080p**预设会将源编码到以下6层中。

| Bitrate | 宽度 | 高度 | MaxFPS | 个人资料 |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |高 |
| 3000 |1280 |720 |30 |高 |
| 1600 |960 |540 |30 |高 |
| 800 |640 |360 |30 |高 |
| 400 |480 |270 |30 |高 |
| 200 |320 |180 |30 |高 |

> [!NOTE]
> 如果需要自定义实时编码预设，请通过 Azure 门户打开支持票证。 你应当指定所需的分辨率和比特率的表。 请确保至少有一个层为1080p，最多6层。 还要指定你要为 Premium1080p 实时编码器请求预设。
> 可在一段时间内调整比特率和分辨率的特定值。

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>输出 Default720p 和 Default1080p 的音频流

对于*Default720p*和*Default1080p*预设，音频在 128 KBPS 编码为立体声 AAC。 采样率将在贡献源中跟随音频轨迹。

## <a name="implicit-properties-of-the-live-encoder"></a>实时编码器的隐式属性

上一部分介绍了可以通过预设显式控制的实时编码器的属性，例如层数、分辨率和比特率。 本节阐明了隐式属性。

### <a name="group-of-pictures-gop-duration"></a>图片组（GOP）持续时间

实时编码器遵循贡献源的[GOP](https://en.wikipedia.org/wiki/Group_of_pictures)结构，这意味着输出层将具有相同的 GOP 持续时间。 因此，建议您配置本地编码器以生成具有固定 GOP 持续时间（通常为2秒）的 "发布内容源"。 这将确保从该服务传出的 HLS 和 MPEG 破折号流也具有固定的 GOP 持续时间。 大多数设备可能都可以容忍 GOP 持续时间中的小变体。

### <a name="frame-rate"></a>帧速率

实时编码器还会在 "贡献源" 中使用各个视频帧的持续时间，这意味着输出层的帧具有相同的持续时间。 因此，建议您配置本地编码器，以生成具有固定帧速率（最多30帧/秒）的 "发布内容源"。 这将确保从服务传出的 HLS 和 MPEG 短划线流也具有固定帧速率的持续时间。 大多数设备都可以容忍帧速率的小变体，但不能保证实时编码器将生成可正常播放的输出。 本地实时编码器不应删除帧（例如 在电池电量不足的情况下）或以任何方式改变帧速率。

### <a name="resolution-of-contribution-feed-and-output-layers"></a>发布源和输出层的解析

实时编码器配置为避免 upconverting 发布内容源。 因此，输出层的最大分辨率不会超过贡献源的最大分辨率。

例如，如果将 Default1080p 的发布内容源发送到配置为实时编码的实时事件，则输出将只包含5个层，从3Mbps 开始，直至 200 kbps。 或者，如果将360p 中的发布内容源发送到为标准实时编码配置的实时事件，则输出将包含3个层（在288p、216p 和192p 的分辨率下）。 在退化情况下，如果将160x90 像素发送到标准实时编码器，则输出将在160x90 分辨率上包含一个层，与发布源的比特率相同。

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>贡献源和输出层的比特率

实时编码器配置为在预设中服从比特率设置，而不考虑贡献源的比特率。 因此，输出层的比特率可能会超出贡献源的比特率。 例如，如果以 1 Mbps 的720p 分辨率来发送发布源，输出层将保持与上[表](live-event-types-comparison.md#output-video-streams-for-default720p)相同。

## <a name="next-steps"></a>后续步骤

[实时传送视频流概述](live-streaming-overview.md)
