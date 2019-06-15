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
ms.openlocfilehash: bd4899374c06246ddd4d5fa81d0f6e3a6a1e7017
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075003"
---
# <a name="live-event-types-comparison"></a>实时事件类型比较

在 Azure 媒体服务中，[实时事件](https://docs.microsoft.com/rest/api/media/liveevents)可以是下述两种类型之一：实时编码和直通。 

## <a name="types-comparison"></a>类型比较 

下表比较了 Live 事件类型的功能。

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

分辨率和比特率从实时编码器输出中包含由[presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding)。 如果使用**标准**实时编码器 (LiveEventEncodingType.Standard)，则*Default720p*预设指定一组 6 解析/位速率对如下所述。 否则为如果使用**Premium1080p**实时编码器 (LiveEventEncodingType.Premium1080p)，则*Default1080p*预设的比较输出的解决方法/位速率对集。 

### <a name="output-video-streams-for-default720p"></a>Default720p 的输出视频流

**Default720p**会将输入的视频编码为以下 6 层。

| 比特率 | 宽度 | 高度 | MaxFPS | 配置文件 | 输出流名称 |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |高 |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |高 |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |高 |Video_704x396_1350kbps |
| 850 |512 |288 |30 |高 |Video_512x288_850kbps |
| 550 |384 |216 |30 |高 |Video_384x216_550kbps |
| 200 |340 |192 |30 |高 |Video_340x192_200kbps |

> [!NOTE]
> 如果需要自定义实时编码预设，请打开支持票证，通过 Azure 门户。 你应当指定所需的分辨率和比特率的表。 请确认只有一个 720p 的图层，最多有 6 个图层。 也不要指定您请求的标准的实时编码器的预设值。

### <a name="output-video-streams-for-default1080p"></a>有关 Default1080p 输出视频流

**Default1080p**会将输入的视频编码为以下 6 层。

| 比特率 | 宽度 | 高度 | MaxFPS | 配置文件 | 输出流名称 |
| --- | --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |高 |Video_1920x1080_5500kbps |
| 3000 |1280 |720 |30 |高 |Video_1280x720_3000kbps |
| 1600 |960 |540 |30 |高 |Video_960x540_1600kbps |
| 800 |640 |360 |30 |高 |Video_640x360_800kbps |
| 400 |480 |270 |30 |高 |Video_480x270_400kbps |
| 200 |320 |180 |30 |高 |Video_320x180_200kbps |

> [!NOTE]
> 如果需要自定义实时编码预设，请打开支持票证，通过 Azure 门户。 你应当指定所需的分辨率和比特率的表。 不要验证在 1080p，只有一个图层和最多 6 层。 也不要指定您请求的 Premium1080p 实时编码器的预设值。

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>输出音频 Stream Default720p 和 Default1080p

两个*Default720p*并*Default1080p*预设，音频编码为立体声 AAC-LC 以 128 kbps 速率进行，采样率 48khz。

## <a name="next-steps"></a>后续步骤

[实时传送视频流概述](live-streaming-overview.md)
