---
title: 媒体服务建议的实时传送视频流编码器 -  Azure | Microsoft Docs
description: 了解媒体服务建议的实时传送视频流本地编码器
services: media-services
keywords: 编码;编码器;媒体
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 04/16/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 0676b6b183c64dcd0fb15b87de48a4afed3a0011
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641807"
---
# <a name="tested-on-premises-live-streaming-encoders"></a>测试的本地实时流式处理器

在 Azure 媒体服务中，[直播活动](https://docs.microsoft.com/rest/api/media/liveevents)（频道）表示用于处理实时传送视频流内容的管道。 直播活动通过以下两种方式之一接收实时输入流。

* 本地实时编码器将多比特率 RTMP 或平滑流式处理（分片 MP4）流发送到无法通过媒体服务进行实时编码的直播活动。 引入流将通过直播活动，而不会进行任何进一步处理。 这种方法称为**直通**。 我们建议实时编码器将多比特率流而不是单比特率流发送到传递实时事件，以允许自适应比特率流到客户端。 

    如果要对传递实时事件使用多比特率流，则必须同步不同比特率上的视频 GOP 大小和视频片段，以避免播放端出现意外行为。

  > [!TIP]
  > 实时传送视频流时，使用直通方法是最经济的。
 
* 本地实时编码器向实时事件发送单比特率流，该流启用以以下格式之一对媒体服务执行实时编码：RTMP 或平滑流（碎片 MP4）。 然后，直播活动将对传入的单比特率流执行实时编码，使之转换为多比特率（自适应）视频流。

本文讨论了经过测试的本地实时流式处理编码器。 有关如何验证本地实时编码器的说明，请参阅[验证本地编码器](become-on-premises-encoder-partner.md)

若要详细了解如何使用媒体服务进行实时编码，请参阅[使用媒体服务 v3 的实时传送视频流](live-streaming-overview.md)。

## <a name="encoder-requirements"></a>编码器要求

使用 HTTPS 或 RTMPS 协议时，编码器必须支持 TLS 1.2。

## <a name="live-encoders-that-output-rtmp"></a>输出 RTMP 的实时编码器

媒体服务建议使用下列将 RTMP 作为输出的实时编码器之一。 支持的 URL 方案为 `rtmp://` 或 `rtmps://`。

通过 RTMP 流式处理时，检查防火墙和/或代理设置，确认出站 TCP 端口 1935 和 1936 已打开。<br/><br/>
通过 RTMPS 流式处理时，检查防火墙和/或代理设置，确认出站 TCP 端口 2935 和 2936 已打开。

> [!NOTE]
> 使用 RTMPS 协议时，编码器必须支持 TLS 1.2。

- Adobe Flash 媒体实时编码器 3.2
- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- 元素实时（版本 2.14.15 及更高版本）
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- 由于 TLS 1.2 要求，远程直播有线广播（版本 13.0.2 或更高版本）
- 远程有线广播 S（仅支持 RTMP）
- Teradek Slice 756
- VMIX
- xStream
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 和 Hero 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>输出分片 MP4 的实时编码器

媒体服务建议使用下列将多比特率平滑流式处理（分片 MP4）作为输出的实时编码器之一。 支持的 URL 方案为 `http://` 或 `https://`。

> [!NOTE]
> 使用 HTTPS 协议时，编码器必须支持 TLS 1.2。

- Ateme TITAN Live
- Cisco 数字媒体编码器 2200
- 元素实时（版本 2.14.15 及以上，由于 TLS 1.2 要求）
- Envivio 4Caster C4 Gen III 
- Imagine Communications Selenio MCP3
- Media Excel Hero Live 和 Hero 4K (UHD/HEVC)
- [Ffmpeg](https://www.ffmpeg.org)

> [!TIP]
>  如果要以多种语言（例如，一个英语音频轨道和一个西班牙语音频轨道）流式传输直播活动，则可以将 Media Excel 实时编码器配置为将实时源发送到直通直播活动，以实现此目的。

## <a name="configuring-on-premises-live-encoder-settings"></a>配置本地实时编码器设置

有关对实时事件类型有效的设置的信息，请参阅[事件类型比较](live-event-types-comparison.md)。

### <a name="playback-requirements"></a>播放要求

若要播放内容，音频和视频流都必须存在。 不支持播放纯视频流。

### <a name="configuration-tips"></a>配置提示

- 尽可能使用硬编码的 Internet 连接。
- 确定带宽要求时，可以认为它就是将流式处理比特率翻倍。 虽然不一定要这样考虑，但这种简单的经验法则有助于减轻网络拥塞的影响。
- 使用基于软件的编码器时，请关闭任何不需要的程序。
- 开始推送之后更改编码器配置会对事件造成负面影响。 配置更改可能会导致事件不稳定。 
- 请确保自己有充足的时间来设置事件。 对于大规模事件，我们建议在事件之前的一小时开始设置。
- 使用 H.264 视频和 AAC 音频编解码器输出。
- 确保视频质量有关键帧或 GOP 时间对齐。
- 确保每个视频质量都有唯一的流名称。
- 使用建议的严格 CBR 编码，以实现最佳的自适应比特率性能。

> [!IMPORTANT]
> 观察机器的物理状况（CPU/内存/等），因为将片段上载到云涉及 CPU 和 IO 操作。 如果更改编码器中的任何设置，请确定重置通道/实时事件，以便更改生效。

## <a name="see-also"></a>另请参阅

[使用媒体服务 v3 实时传送视频流](live-streaming-overview.md)

## <a name="next-steps"></a>后续步骤

[如何验证编码器](become-on-premises-encoder-partner.md)
