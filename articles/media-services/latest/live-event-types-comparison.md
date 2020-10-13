---
title: Azure 媒体服务 LiveEvent 类型 | Microsoft Docs
description: 在 Azure 媒体服务中，可以将直播活动设为“直通”  或“实时编码”  。 本文显示了比较直播活动类型的详细表格。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c79d45cfac22f41f05071b619c444e7b7ab7956a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89397298"
---
# <a name="live-event-types-comparison"></a>实时事件类型比较

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

在 Azure 媒体服务中，可以将[直播活动](/rest/api/media/liveevents)设为“直通”  （本地实时编码器发送多比特率流）或“实时编码”  （本地实时编码器发送单比特率流）。 

本文对直播活动类型的功能进行了比较。

## <a name="types-comparison"></a>类型比较 

下表比较了实时事件类型的功能。 这些类型是在创建期间使用 [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype) 设置的：

* **LiveEventEncodingType.None** - 本地实时编码器发送多比特率流。 引入的流通过实时事件传递，而不会经过任何进一步的处理。 也称为“直通直播活动”。
* **LiveEventEncodingType.Standard** - 本地实时编码器将单比特率流发送到实时事件，媒体服务创建多比特率流。 如果贡献源的分辨率为 720p 或更高，则 **Default720p** 预设将编码一组 6 分辨率/比特率对（本文稍后会提供详细信息）。
* **LiveEventEncodingType.Premium1080p** - 本地实时编码器将单比特率流发送到实时事件，媒体服务创建多比特率流。 Default1080p 预设指定分辨率/比特率对的输出集（本文稍后会提供详细信息）。 

| Feature | 直通直播活动 | Standard 或 Premium1080p 实时事件 |
| --- | --- | --- |
| 单比特率输入在云中被编码为多比特率 |否 |是 |
| 贡献源的最大视频分辨率 |4K（4096x2160，60 帧/秒） |1080p（1920x1088，30 帧/秒）|
| 贡献源中建议的最大层数|最大为 12|1 个音频|
| 输出中的最大层数| 与输入相同|最多 6 个（请参阅下面的“系统预设”）|
| 贡献源的最大聚合带宽|60 Mbps|空值|
| 贡献中单个层的最大比特率 |20 Mbps|20 Mbps|
| 支持多语言音轨|是|否|
| 支持的输入视频编解码器 |H.264/AVC 和 H.265/HEVC|H.264/AVC|
| 支持的输出视频编解码器|与输入相同|H.264/AVC|
| 支持的视频位深、输入和输出|最多 10 位，包括 HDR 10/HLG|8 位|
| 支持的输入音频编解码器|AAC-LC、HE-AAC v1、HE-AAC v2|AAC-LC、HE-AAC v1、HE-AAC v2|
| 支持的输出音频编解码器|与输入相同|AAC-LC|
| 输出视频的最大视频分辨率|与输入相同|Standard - 720p，Premium1080p - 1080p|
| 输入视频的最大帧速率|60 帧/秒|Standard 或 Premium1080p - 30 帧/秒|
| 输入协议|RTMP、分段 MP4（平滑流式处理）|RTMP、分段 MP4（平滑流式处理）|
| 价格|请参阅[定价页](https://azure.microsoft.com/pricing/details/media-services/)并单击“实时视频”选项卡|请参阅[定价页](https://azure.microsoft.com/pricing/details/media-services/)并单击“实时视频”选项卡|
| 最长运行时间| 24 小时 x 365 天，实时线性 | 24 小时 x 365 天，实时线性（预览）|
| 传递嵌入式 CEA 608/708 字幕数据的能力|是|是|
| 能够开启实时脚本|是|是|
| 支持插入静态图像|否|否|
| 支持通过 API 发出广告指示| 否|否|
| 支持通过带内 SCTE35 消息发出广告指示|是|是|
| 能够从贡献源出现的短时停顿中恢复|是|部分|
| 支持非一致性输入 GOP|是|否 – 输入必须具有固定的 GOP 持续时间|
| 支持可变帧率输入|是|否 - 输入必须是固定的帧速率。 轻微的帧率变化是容许的，例如在高速运动情况下出现的轻微帧率变化。 但是，贡献源不能降低帧速率（例如降低到 15 帧/秒）。|
| 输入源丢失时，会自动关闭直播活动|否|12 小时后，如果没有运行的 LiveOutput|

## <a name="system-presets"></a>系统预设

实时编码器的输出中包含的分辨率和比特率由 [presetName](/rest/api/media/liveevents/create#liveeventencoding) 确定。 如果使用 **Standard** 实时编码器 (LiveEventEncodingType.Standard)，则 *Default720p* 预设将指定如下所述的一组 6 分辨率/比特率对。 否则，如果使用 **Premium1080p** 实时编码器 (LiveEventEncodingType.Premium1080p)，则 *Default1080p* 预设将指定分辨率/比特率对的输出集。

> [!NOTE]
> 如果为 Standard 实时编码设置了 Default1080p 预设，则无法将该预设应用到实时事件 - 会出现错误。 如果尝试将 Default720p 预设应用到 Premium1080p 实时编码器，也会出现错误。

### <a name="output-video-streams-for-default720p"></a>Default720p 的输出视频流

如果贡献源的分辨率为 720p 或更高，则 **Default720p** 预设会将源编码到以下 6 个层中。 在下表中，比特率以 kbps 为单位，MaxFPS 表示允许的最大帧速率（帧/秒），Profile 表示使用的 H.264 配置文件。

| Bitrate | 宽度 | 高度 | MaxFPS | 配置文件 |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |高 |
| 2200 |960 |540 |30 |高 |
| 1350 |704 |396 |30 |高 |
| 850 |512 |288 |30 |高 |
| 550 |384 |216 |30 |高 |
| 200 |340 |192 |30 |高 |

> [!NOTE]
> 如果需要自定义实时编码预设，请通过 Azure 门户开具支持票证。 你应当指定所需的视频分辨率和比特率的表。 不支持自定义音频编码比特率。 请确认只有一个 720p 的图层，最多有 6 个图层。 此外，请务必指定你要请求预设。

### <a name="output-video-streams-for-default1080p"></a>Default1080p 的输出视频流

如果贡献源的分辨率为 1080p，则 **Default1080p** 预设会将源编码到以下 6 个层中。

| Bitrate | 宽度 | 高度 | MaxFPS | 配置文件 |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |高 |
| 3000 |1280 |720 |30 |高 |
| 1600 |960 |540 |30 |高 |
| 800 |640 |360 |30 |高 |
| 400 |480 |270 |30 |高 |
| 200 |320 |180 |30 |高 |

> [!NOTE]
> 如果需要自定义实时编码预设，请通过 Azure 门户开具支持票证。 你应当指定所需的分辨率和比特率的表。 请确认只有一个层的分辨率为 1080p，且最多有 6 个层。 此外，请指定你要请求 Premium1080p 实时编码器的预设。 比特率和分辨率的特定值今后可能会调整。

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Default720p 和 Default1080p 的输出音频流

对于 *Default720p* 和 *Default1080p* 预设，音频将编码到 128 kbps 的立体声 AAC-LC。 采样率与贡献源中的音频轨迹采样率相同。

## <a name="implicit-properties-of-the-live-encoder"></a>实时编码器的隐式属性

上一部分介绍了可以通过预设显式控制的实时编码器属性 - 例如层数、分辨率和比特率。 本部分将阐明隐式属性。

### <a name="group-of-pictures-gop-duration"></a>帧组 (GOP) 持续时间

实时编码器遵从贡献源的 [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) 结构 - 这意味着，输出层具有相同的 GOP 持续时间。 因此，我们建议配置本地编码器，生成具有固定 GOP 持续时间（通常为 2 秒）的贡献源。 这可以确保服务传出的 HLS 和 MPEG DASH 流也具有固定的 GOP 持续时间。 大多数设备可能都会容忍 GOP 持续时间存在较小的变化。

### <a name="frame-rate"></a>帧速率

实时编码器还遵从贡献源中单个视频帧的持续时间 - 这意味着，输出层的帧具有相同的持续时间。 因此，我们建议配置本地编码器，生成具有固定帧速率（最大 30 帧/秒）的贡献源。 这可以确保服务传出的 HLS 和 MPEG DASH 流也具有固定的帧速率持续时间。 大多数设备可能都会容忍帧速率存在较小的变化，但不保证实时编码器生成可正常播放的输出。 本地实时编码器不应丢帧（例如 在电池电量不足的情况下），或以任何方式改变帧速率。

### <a name="resolution-of-contribution-feed-and-output-layers"></a>贡献源和输出层的分辨率

实时编码器的配置可避免向上转换贡献源。 因此，输出层的最大分辨率不会超过贡献源的最大分辨率。

例如，如果将 720p 的贡献源发送到为 Default1080p 实时编码配置的实时事件，则输出只包含 5 层，最前面的层的分辨率为 720p，比特率为 3 Mbps，最后面的层的分辨率为 1080p，比特率为 200 kbps。 或者，如果将 360p 的贡献源发送到为 Standard 实时编码配置的实时事件，则输出将包含 3 层（分辨率分别为 288p、216p 和 192p）。 在退化的情况下，如果将 160x90 像素（举例而言）的贡献源发送到 Standard 实时编码器，则输出将包含一个 160x90 分辨率的层，其比特率与贡献源的比特率相同。

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>贡献源和输出层的比特率

无论贡献源的比特率如何，实时编码器的配置都会遵从预设中的比特率设置。 因此，输出层的比特率可能会超过贡献源的比特率。 例如，如果传入一个比特率为 1 Mbps、分辨率为 720p 的贡献源，则输出层将与[上表](live-event-types-comparison.md#output-video-streams-for-default720p)中的描述相同。

## <a name="next-steps"></a>后续步骤

[实时传送视频流概述](live-streaming-overview.md)
