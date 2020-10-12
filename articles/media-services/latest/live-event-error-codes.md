---
title: Azure 媒体服务直播活动错误代码 | Microsoft Docs
description: 本文列出了直播活动错误代码。
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 13277c3d46d5b68aa5705699f45a2bf9e97a78d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291528"
---
# <a name="media-services-live-event-error-codes"></a>媒体服务直播活动错误代码

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本节中的表列出了[直播活动](live-events-outputs-concept.md)错误代码。

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

订阅直播活动的[事件网格](../../event-grid/index.yml)事件时，可从 [LiveEventConnectionRejected](media-services-event-schemas.md#liveeventconnectionrejected) 事件中看到以下错误之一。

| 结果代码 | 说明 |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | 引入 URL 不正确 |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | 编码器 IP 在配置的 IP 允许列表中不存在 |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | RTMP 编码器未发送 setDataFrame 命令。 |
| MPE_INGEST_CODEC_NOT_SUPPORTED | 不支持指定的编解码器。 |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |在实际媒体数据送达之前未收到媒体说明信息。|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |音频或视频类型的质量计数超过了允许的最大限制。|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |直播活动或频道服务中的总传入比特率超过了允许的最大限制。|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | RTMP 编码器中视频或音频 FLVTag 的时间戳无效。 |
| MPE_INGEST_FRAMERATE_EXCEEDED | 传入的编码器引入流的帧速率超过了直播活动/频道编码所允许的最大值，即 30fps。|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | 传入的编码器引入流超过了以下允许的分辨率：1920x1088（直播活动/频道编码）和 4096x2160（直接传递直播活动/频道编码）。|
| MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV | 直播活动同时接收了大量音频数据或大量没有任何关键帧的视频数据。 已断开编码器连接，以使其有机会使用正确的数据进行重试。 |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

你可能会从 [LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) 事件中看到以下错误之一。

|结果代码|说明|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|在空闲时间达到允许的时间限制后，RTMP 会话超时。|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|RTMP 编码器中视频或音频 FLVTag 的时间戳无效。|
|MPE_CAPACITY_LIMIT_REACHED|编码器发送数据的频率过快。|
|未知错误代码|这些错误代码的范围可能包括内存错误、哈希映射中包含重复条目，等等。|


## <a name="see-also"></a>另请参阅

[流式处理终结点（来源）错误代码](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>后续步骤

[教程：使用媒体服务进行实时流式传输](stream-live-tutorial-with-api.md)
