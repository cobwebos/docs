---
title: Azure 媒体服务 LiveEvent 类型 | Microsoft Docs
description: 本文展示了对 LiveEvent 类型进行比较的一个详细表格。
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
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: 9952a7bbac1eb79de0d3425f839e3bd30196844e
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243884"
---
# <a name="live-event-types-comparison"></a>实时事件类型比较

在 Azure 媒体服务中，[实时事件](https://docs.microsoft.com/rest/api/media/liveevents)可以是下述两种类型之一：实时编码和直通。 

## <a name="types-comparison"></a>类型比较 

下表比较了两种实时事件类型的功能。

| Feature | 直通实时事件 | 标准实时事件 |
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
| 输出视频的最大视频分辨率|与输入相同|720p（速率为 30 帧/秒）|
| 输入协议|RTMP、分段 MP4（平滑流式处理）|RTMP、分段 MP4（平滑流式处理）|
| 价格|请参阅[定价页](https://azure.microsoft.com/pricing/details/media-services/)并单击“实时视频”选项卡|请参阅[定价页](https://azure.microsoft.com/pricing/details/media-services/)并单击“实时视频”选项卡|
| 最长运行时间| 24 小时 x 365 天，实时线性 | 最多 24 小时|
| 传递嵌入式 CEA 608/708 字幕数据的能力|是|是|
| 支持插入静态图像|否|否|
| 支持通过 API 发出广告指示| 否|否|
| 支持通过带内 SCTE35 消息发出广告指示|是|是|
| 能够从贡献源出现的短时停顿中恢复|是|否（如果超过 6 秒而没有输入数据，实时事件就会开始插入静态图像）|
| 支持非一致性输入 GOP|是|否 – 输入必须具有固定的 GOP 持续时间|
| 支持可变帧率输入|是|否 - 输入必须是固定的帧速率。 轻微的帧率变化是容许的，例如在高速运动情况下出现的轻微帧率变化。 但是，贡献源不能降低帧速率（例如降低到 15 帧/秒）。|
| 输入源丢失时，会自动关闭实时事件|否|12 小时后，如果没有运行的 LiveOutput|

## <a name="system-presets"></a>系统预设

使用实时编码时 (实时事件设置为**标准**)，编码预设定义如何将传入流编码为多个比特率或图层。 目前，唯一允许的值为预设*Default720p* （默认值）。

Default720p 会将视频编码为以下 6 层。

### <a name="output-video-stream"></a>输出视频流

| 比特率 | 宽度 | 高度 | MaxFPS | 配置文件 | 输出流名称 |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |高 |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |高 |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |高 |Video_704x396_1350kbps |
| 850 |512 |288 |30 |高 |Video_512x288_850kbps |
| 550 |384 |216 |30 |高 |Video_384x216_550kbps |
| 200 |340 |192 |30 |高 |Video_340x192_200kbps |

> [!NOTE]
> 如果您需要使用自定义的实时编码预设，请联系amshelp@microsoft.com。 您应指定所需的表的分辨率和比特率。 不要验证只有一个图层 720p 和最多 6 层。

### <a name="output-audio-stream"></a>输出音频流

音频以 128 kbps 的速率编码为立体声 AAC-LC，采样率为 48 kHz。

## <a name="next-steps"></a>后续步骤

[实时传送视频流概述](live-streaming-overview.md)
