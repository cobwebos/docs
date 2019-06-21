---
title: Azure 媒体服务 LiveEvent 类型 | Microsoft Docs
description: 本文介绍比较 LiveEvent 类型的详细的表。
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
ms.openlocfilehash: 93f01513841d1174fea796f1615ab05df0a41af4
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150382"
---
# <a name="live-event-types-comparison"></a>实时事件类型比较

在 Azure 媒体服务中，[实时事件](https://docs.microsoft.com/rest/api/media/liveevents)可以是下述两种类型之一：实时编码和直通。 

## <a name="types-comparison"></a>类型比较 

下表比较了 Live 事件类型的功能。 在创建使用过程中设置类型[LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None** -的本地实时编码器将多个比特率流发送。 引入的流将通过无需任何进一步的处理实时事件。 
* **LiveEventEncodingType.Standard** -的本地实时编码器发送单比特率流的实时事件和媒体服务创建多个比特率流。 如果贡献源为 720p 或更高分辨率**Default720p**预设将编码的一组 6 解析/比特率对 （详细信息按照在本文后面）。
* **LiveEventEncodingType.Premium1080p** -的本地实时编码器发送单比特率流的实时事件和媒体服务创建多个比特率流。 Default1080p 预设指定输出组 （按照在本文后面详细信息） 的解决方法/比特率对。 

| Feature | 直通实时事件 | 标准或 Premium1080p 实时事件 |
| --- | --- | --- |
| 单比特率输入在云中被编码为多比特率 |否 |是 |
| 贡献源的最大视频分辨率 |4K（4096x2160，60 帧/秒） |1080p（1920x1088，30 帧/秒）|
| 贡献源中建议的最大层数|最大为 12|1 个音频|
| 输出中的最大层数| 与输入相同|最多为 6 （请参阅下面的系统预设）|
| 贡献源的最大聚合带宽|60 Mbps|不适用|
| 贡献中单个层的最大比特率 |20 Mbps|20 Mbps|
| 支持多语言音轨|是|否|
| 支持的输入视频编解码器 |H.264/AVC 和 H.265/HEVC|H.264/AVC|
| 支持的输出视频编解码器|与输入相同|H.264/AVC|
| 支持的视频位深、输入和输出|最多 10 位，包括 HDR 10/HLG|8 位|
| 支持的输入音频编解码器|AAC-LC、HE-AAC v1、HE-AAC v2|AAC-LC、HE-AAC v1、HE-AAC v2|
| 支持的输出音频编解码器|与输入相同|AAC-LC|
| 输出视频的最大视频分辨率|与输入相同|标准-720p，Premium1080p-1080p|
| 输入视频的最大帧速率|60 帧/秒|标准或 Premium1080p-30 帧/秒|
| 输入协议|RTMP、分段 MP4（平滑流式处理）|RTMP、分段 MP4（平滑流式处理）|
| 价格|请参阅[定价页](https://azure.microsoft.com/pricing/details/media-services/)并单击“实时视频”选项卡|请参阅[定价页](https://azure.microsoft.com/pricing/details/media-services/)并单击“实时视频”选项卡|
| 最长运行时间| 24 小时 x 365 天，实时线性 | 最多 24 小时|
| 传递嵌入式 CEA 608/708 字幕数据的能力|是|是|
| 支持插入静态图像|否|否|
| 支持通过 API 发出广告指示| 否|否|
| 支持通过带内 SCTE35 消息发出广告指示|是|是|
| 能够从贡献源出现的短时停顿中恢复|是|部分|
| 支持非一致性输入 GOP|是|否 – 输入必须具有固定的 GOP 持续时间|
| 支持可变帧率输入|是|否 - 输入必须是固定的帧速率。 轻微的帧率变化是容许的，例如在高速运动情况下出现的轻微帧率变化。 但贡献源不能删除帧速率 （例如，为 15 帧/秒）。|
| 输入源丢失时，会自动关闭实时事件|否|12 小时后，如果没有运行的 LiveOutput|

## <a name="system-presets"></a>系统预设

分辨率和比特率从实时编码器输出中包含由[presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding)。 如果使用**标准**实时编码器 (LiveEventEncodingType.Standard)，则*Default720p*预设指定一组 6 解析/比特率对如下所述。 否则为如果使用**Premium1080p**实时编码器 (LiveEventEncodingType.Premium1080p)，则*Default1080p*预设指定输出组解析/比特率对。

> [!NOTE]
> 不能应用 Default1080p 预设为实时事件，如果已设置为标准的实时编码会收到错误。 如果你尝试将应用到 Premium1080p 实时编码器预设 Default720p，还将会出错。

### <a name="output-video-streams-for-default720p"></a>Default720p 的输出视频流

如果贡献源为 720p 或更高分辨率**Default720p**预设将对以下 6 层到源进行编码。 在下表中，比特率是以 kbps 为单位，MaxFPS 表示，最大允许的帧速率 （在帧/秒），配置文件代表使用的 H.264 配置文件。

| Bitrate | 宽度 | 高度 | MaxFPS | 配置文件 |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |高 |
| 2200 |960 |540 |30 |高 |
| 1350 |704 |396 |30 |高 |
| 850 |512 |288 |30 |高 |
| 550 |384 |216 |30 |高 |
| 200 |340 |192 |30 |高 |

> [!NOTE]
> 如果需要自定义实时编码预设，请打开支持票证，通过 Azure 门户。 你应当指定所需的分辨率和比特率的表。 请确认只有一个 720p 的图层，最多有 6 个图层。 也不要指定您请求的标准的实时编码器的预设值。
> 可能会随着时间的推移进行调整的比特率和分辨率的特定值

### <a name="output-video-streams-for-default1080p"></a>有关 Default1080p 输出视频流

如果贡献源的分辨率为 1080p **Default1080p**预设将对以下 6 层到源进行编码。

| Bitrate | 宽度 | 高度 | MaxFPS | 配置文件 |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |高 |
| 3000 |1280 |720 |30 |高 |
| 1600 |960 |540 |30 |高 |
| 800 |640 |360 |30 |高 |
| 400 |480 |270 |30 |高 |
| 200 |320 |180 |30 |高 |

> [!NOTE]
> 如果需要自定义实时编码预设，请打开支持票证，通过 Azure 门户。 你应当指定所需的分辨率和比特率的表。 不要验证在 1080p，只有一个图层和最多 6 层。 也不要指定您请求的 Premium1080p 实时编码器的预设值。
> 随着时间的推移可能会进行调整的比特率和分辨率的特定值。

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>输出音频 Stream Default720p 和 Default1080p

两个*Default720p*并*Default1080p*预设，音频编码为立体声 AAC-LC 传感器以 128 kbps 速率进行。 采样率遵循的贡献源中的音频轨道。

## <a name="implicit-properties-of-the-live-encoder"></a>实时编码器的隐式属性

前一部分所述的实时编码器预设通过-例如层、 分辨率和比特率的数目可以显式控制的属性。 本部分阐明了隐式属性。

### <a name="group-of-pictures-gop-duration"></a>组 (gop) 持续时间的

实时编码器遵循[GOP](https://en.wikipedia.org/wiki/Group_of_pictures)贡献源-这意味着输出层将具有相同的 GOP 持续时间的结构。 因此，建议你配置的本地编码器以生成贡献源具有固定 GOP 持续时间 （通常为 2 秒）。 这将确保，从服务传出的 HLS 和 MPEG DASH 流还具有固定 GOP 持续时间。 GOP 持续时间的小变化很可能是可以容忍的大多数设备。

### <a name="frame-rate"></a>帧速率

实时编码器也遵循贡献源-这意味着输出层将具有相同的持续时间的帧中的各个视频帧的持续时间。 因此，建议配置的本地编码器以生成贡献源具有固定帧速率 （最多 30 帧/秒）。 这将确保，从服务传出的 HLS 和 MPEG DASH 流还具有固定帧速率持续时间。 帧速率的小变化可能会容许的大多数设备，但不能保证实时编码器将生成将正常播放的输出。 你的本地实时编码器应不丢弃帧 （例如。 在电池电量不足情况） 下或不同的帧速率以任何方式。

### <a name="resolution-of-contribution-feed-and-output-layers"></a>解析的贡献源和输出层

实时编码器配置为避免 upconverting 贡献源。 结果是输出层的最大分辨率不会超过的贡献源。

例如，如果发送贡献源到 720p 为 Default1080p 配置实时事件实时编码，输出也将仅具有 5 个层，从 720p 在 3 mbps，转到 200 kbps 1080p 开始。 或者，如果发送贡献源在 360p 到配置为标准的实时事件进行实时编码，则输出将包含 3 个层 （分辨率 288 p、 216 p 和 192 p）。 在退化情况下，如果将即 160 x 90 像素的贡献源发送到标准的实时编码器，输出将包含 160 x 90 分辨率以相同的比特率贡献源的一个层。

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>比特率为贡献源和输出层

实时编码器配置为接受的预设，而不考虑的比特率贡献源中的比特率设置。 因此可能会超过贡献源的输出层的比特率。 例如，如果您发送贡献源，分辨率为 720p 1 mbps，输出层将保持中的相同[表](live-event-types-comparison.md#output-video-streams-for-default720p)上面。

## <a name="next-steps"></a>后续步骤

[实时传送视频流概述](live-streaming-overview.md)
