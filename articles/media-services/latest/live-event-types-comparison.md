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
ms.date: 02/06/2019
ms.author: juliako
ms.openlocfilehash: 9d62ef2295abbb8f8fc6f45ffc0c7ab1ce9616e4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878351"
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
| 输出中的最大层数| 与输入相同|最大为 7|
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

## <a name="next-steps"></a>后续步骤

[实时传送视频流概述](live-streaming-overview.md)
