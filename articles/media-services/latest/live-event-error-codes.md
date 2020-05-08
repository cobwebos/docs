---
title: Azure 媒体服务实时事件错误代码 |Microsoft Docs
description: 本文列出了实时事件错误代码。
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/4/2020
ms.author: inhenkel
ms.openlocfilehash: e039a240465478e86ce816890219e5dfaa07dfbf
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791045"
---
# <a name="media-services-live-event-error-codes"></a>媒体服务实时事件错误代码

此部分中的表列出了[实时事件](live-events-outputs-concept.md)错误代码。

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

当你订阅实时事件的[事件网格](https://docs.microsoft.com/azure/event-grid/)事件时，可能会看到[LiveEventConnectionRejected](media-services-event-schemas.md#liveeventconnectionrejected)事件出现以下错误之一。

| 结果代码 | 说明 |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | 引入 URL 不正确 |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | 编码器 IP 在配置的 IP 允许列表中不存在 |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | RTMP 编码器未发送 setDataFrame 命令。 |
| MPE_INGEST_CODEC_NOT_SUPPORTED | 不支持指定的编解码器。 |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |在实际媒体数据送达之前，未收到媒体说明信息。|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |音频或视频类型的质量计数超出了允许的最大限制。|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |实时事件或通道服务中的总传入比特率超出了允许的最大限制。|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | RTMP 编码器中视频或音频 FLVTag 的时间戳无效。 |
| MPE_INGEST_FRAMERATE_EXCEEDED | 带 framerates 的传入编码器引入流超过了允许的最大30fps 编码实时事件/通道。|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | 传入编码器引入流超过以下允许的分辨率：用于对实时事件/通道进行编码的1920x1088 和用于传递实时事件/通道的 4096 x 2160。|
| MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV | 实时事件同时接收了大量音频数据，或者没有任何关键帧的视频数据量。 我们已断开编码器连接，使其有机会使用正确的数据重试。 |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

你可能会看到来自[LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected)事件的以下错误之一。

|结果代码|说明|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|在空闲时间达到允许的时间限制后，RTMP 会话超时。|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|RTMP 编码器中视频或音频 FLVTag 的时间戳无效。|
|MPE_CAPACITY_LIMIT_REACHED|编码器发送数据的频率过快。|
|未知错误代码|这些错误代码的范围可能包括内存错误、哈希映射中包含重复条目，等等。|


## <a name="see-also"></a>另请参阅

[流式处理终结点（源）错误代码](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>后续步骤

[教程：通过 Media Services 实时流](stream-live-tutorial-with-api.md)
