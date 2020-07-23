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
ms.openlocfilehash: 53d68a18c5904b8b7e2f6145ae26221e99395a82
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84749926"
---
# <a name="verified-on-premises-live-streaming-encoders"></a>已验证本地实时流式处理编码器

在 Azure 媒体服务中，[直播活动](https://docs.microsoft.com/rest/api/media/liveevents)（频道）表示用于处理实时传送视频流内容的管道。 直播活动通过以下两种方式之一接收实时输入流。

* 本地实时编码器将多比特率 RTMP 或平滑流式处理（分片 MP4）流发送到无法通过媒体服务进行实时编码的直播活动。 引入流将通过直播活动，而不会进行任何进一步处理。 这种方法称为 **直通**。 建议通过实时编码器将多比特率流（而不是单比特率流）发送到传递型实时事件，以便进行目标为客户端的自适应比特率流式传输。 

    如果要将多比特率流用于传递型实时事件，则必须同步不同比特率上的视频 GOP 大小和视频片段，避免播放端出现意外的行为。

  > [!TIP]
  > 实时传送视频流时，使用直通方法是最经济的。
 
* 本地实时编码器采用以下格式之一将单比特率流发送至能够使用媒体服务执行实时编码的直播活动：RTMP 或平滑流式处理（分片 MP4）。 然后，直播活动将对传入的单比特率流执行实时编码，使之转换为多比特率（自适应）视频流。

本文介绍了已验证的本地实时流式处理编码器。 验证是通过供应商自助验证或客户验证来完成的。 Microsoft Azure 媒体服务不会对每个编码器进行完全或严格测试，也不会不断地重新验证更新。 有关如何验证本地实时编码器的说明，请参阅[验证本地编码器](become-on-premises-encoder-partner.md)

若要详细了解如何使用媒体服务进行实时编码，请参阅[使用媒体服务 v3 的实时传送视频流](live-streaming-overview.md)。

## <a name="encoder-requirements"></a>编码器要求

使用 HTTPS 或 RTMPS 协议时，编码器必须支持 TLS 1.2。

## <a name="live-encoders-that-output-rtmp"></a>输出 RTMP 的实时编码器

媒体服务建议使用下列将 RTMP 作为输出的实时编码器之一。 支持的 URL 方案为 `rtmp://` 或 `rtmps://`。

通过 RTMP 流式处理时，请检查防火墙和/或代理设置，确认出站 TCP 端口 1935 和 1936 已打开。<br/><br/>
通过 RTMPS 流式处理时，检查防火墙和/或代理设置，确认出站 TCP 端口 2935 和 2936 已打开。

> [!NOTE]
> 编码器在使用 RTMPS 协议时必须支持 TLS 1.2。

- Adobe Flash 媒体实时编码器 3.2
- [Blackmagic ATEM 微型和 ATEM 微型 PRO](https://www.blackmagicdesign.com/products/atemmini)
- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- Elemental Live（2.14.15 及更高版本）
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 和 Hero 8
- Haivision KB
- Haivision Makito X HEVC
- [Restream.io](https://restream.io/)
- OBS Studio
- [Streamlabs OBS](https://streamlabs.com/)
- [Switcher Studio (iOS)](https://www.switcherstudio.com/)
- Telestream Wirecast（根据 TLS 1.2 要求，版本为 13.0.2 或更高）
- Telestream Wirecast S （仅支持 RTMP。 由于缺少 TLS 1.2 +，无 RTMPS 支持
- Teradek Slice 756
- VMIX
- xStream

> [!WARNING]
> 以上编码器列表只是一个建议列表。 编码器无间断地测试或验证，因此，可能会破坏兼容性的编码器供应商或开源项目引入更新或重大更改。 

## <a name="live-encoders-that-output-fragmented-mp4-smooth-streaming-ingest"></a>输出零碎的类型（平滑流式处理摄取）的实时编码器

媒体服务建议使用下列将多比特率平滑流式处理（分片 MP4）作为输出的实时编码器之一。 支持的 URL 方案为 `http://` 或 `https://`。

> [!NOTE]
> 使用 HTTPS 协议时，编码器必须支持 TLS 1.2。

- Ateme TITAN Live
- Cisco 数字媒体编码器 2200
- Elemental Live（由于 TLS 1.2 要求，版本为 2.14.15 及更高）
- Envivio 4Caster C4 Gen III 
- [Ffmpeg](https://www.ffmpeg.org)
- Imagine Communications Selenio MCP3
- Media Excel Hero Live 和 Hero 4K (UHD/HEVC)

> [!TIP]
>  如果要以多种语言（例如，一个英语音频轨道和一个西班牙语音频轨道）流式传输直播活动，则可以将 Media Excel 实时编码器配置为将实时源发送到直通直播活动，以实现此目的。

> [!WARNING]
> 以上编码器列表只是一个建议列表。 编码器无间断地测试或验证，因此，编码器供应商或开源项目可能会在任何时间中断兼容性，并提供支持或 bug。 

## <a name="configuring-on-premises-live-encoder-settings"></a>配置本地实时编码器设置

有关对实时事件类型有效的设置的信息，请参阅[事件类型比较](live-event-types-comparison.md)。

### <a name="playback-requirements"></a>播放要求

若要播放内容，音频和视频流都必须存在。 不支持播放纯视频流。

### <a name="configuration-tips"></a>配置提示

- 尽可能使用硬编码的 Internet 连接。
- 确定带宽要求时，可以认为它就是将流式处理比特率翻倍。 虽然不一定要这样考虑，但这种简单的经验法则有助于减轻网络拥塞的影响。
- 使用基于软件的编码器时，请关闭任何不需要的程序。
- 开始推送之后更改编码器配置会对事件造成负面影响。 配置更改可能会导致事件不稳定。 
- 始终测试并验证更新版本的编码器软件，以与 Azure 媒体服务保持兼容。 Microsoft 不会重新验证此列表上的编码器，而且大多数验证都是由软件供应商直接作为 "自我认证" 来完成的。
- 请确保自己有充足的时间来设置事件。 对于大规模事件，我们建议在事件之前的一小时开始设置。
- 使用 H-p 视频和 AAC-LC 音频编解码器输出。
- 坚持你要广播到的实时事件类型的支持的分辨率和帧速率（例如，60fps 当前被拒绝。）
- 确保不同的视频质量中存在关键帧或 GOP 临时对齐。
- 确保每个视频质量具有唯一的流名称。
- 为获得最佳自适应比特率性能，建议使用严格的 CBR 编码。

> [!IMPORTANT]
> 查看计算机的物理状况（CPU/内存/其他），因为将片段上传到云涉及 CPU 和 IO 操作。 如果更改编码器中的任何设置，请务必在某些情况下重置频道/直播活动，使更改生效。

## <a name="see-also"></a>另请参阅

[使用媒体服务 v3 实时传送视频流](live-streaming-overview.md)

## <a name="next-steps"></a>后续步骤

[如何验证编码器](become-on-premises-encoder-partner.md)
