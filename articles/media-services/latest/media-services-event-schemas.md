---
title: 媒体服务事件的 Azure 事件网格架构
description: 介绍为 Azure 事件网格中的媒体服务事件提供的属性
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 08/17/2018
ms.author: juliako
ms.openlocfilehash: a6a6c459e170627d26aa1445f4f4dd193965fe70
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2018
ms.locfileid: "42144177"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>媒体服务事件的 Azure 事件网格架构

本文介绍媒体服务事件的架构和属性。

有关示例脚本和教程的列表，请参阅[媒体服务事件源](../../event-grid/event-sources.md#azure-subscriptions)。

## <a name="available-event-types"></a>可用事件类型

媒体服务发出以下事件类型：

| 事件类型 | Description |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| 作业状态更改。 |
| Microsoft.Media.LiveEventConnectionRejected | 编码器的连接尝试被拒绝。 |
| Microsoft.Media.LiveEventEncoderConnected | 编码器与实时事件建立连接。 |
| Microsoft.Media.LiveEventEncoderDisconnected | 编码器断开连接。 |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | 媒体服务器删除了数据区块，因为该区块的抵达时间过迟，或者带有重叠的时间戳（新数据区块的时间戳小于前一数据区块的结束时间）。 |
| Microsoft.Media.LiveEventIncomingStreamReceived | 媒体服务器收到流或连接中每个轨迹的第一个数据区块。 |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | 媒体服务器检测到音频和视频流不同步。用作警告，因为用户体验可能不受影响。 |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | 媒体服务器检测到来自外部编码器的任意两个视频流不同步。用作警告，因为用户体验可能不受影响。 |
| Microsoft.Media.LiveEventIngestHeartbeat | 当实时事件正在运行时，每隔 20 秒为每个轨迹发布。 提供引入运行状况摘要。 |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | 媒体服务器检测到传入轨迹中存在不连续的情况。 |

**实时**事件有两种类别：流级事件和轨迹级事件。 

按流或连接引发流级事件。 每个事件具有一个用于标识连接或流的 `StreamId` 参数。 每个流或连接具有一个或多个不同类型的轨迹。 例如，来自编码器的一个连接可能具有一个音频轨迹和四个视频轨迹。 流事件类型包括：

* LiveEventConnectionRejected
* LiveEventEncoderConnected
* LiveEventEncoderDisconnected

按轨迹引发轨迹级事件。轨迹事件类型包括：

* LiveEventIncomingDataChunkDropped
* LiveEventIncomingStreamReceived
* LiveEventIncomingStreamsOutOfSync
* LiveEventIncomingVideoStreamsOutOfSync
* LiveEventIngestHeartbeat
* LiveEventTrackDiscontinuityDetected

## <a name="jobstatechange"></a>JobStateChange

以下示例显示 **JobStateChange** 事件的架构： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

数据对象具有以下属性：

| 属性 | Type | Description |
| -------- | ---- | ----------- |
| previousState | 字符串 | 事件发生前的作业状态。 |
| state | 字符串 | 此事件中通知的作业的新状态。 例如，“已排队: 作业正在等待资源”或“已计划: 作业准备就绪”。|

作业状态可以为以下值之一：已排队、已计划、正在处理、已完成、错误、已取消、正在取消。

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

以下示例显示 **LiveEventConnectionRejected** 事件的架构： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "StreamId":"Mystream1",
      "IngestUrl": "http://abc.ingest.isml",
      "EncoderIp": "118.238.251.xxx",
      "EncoderPort": 52859,
      "ResultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0"
  }
]
```

数据对象具有以下属性：

| 属性 | Type | Description |
| -------- | ---- | ----------- |
| StreamId | 字符串 | 流或连接的标识符。 编码器或客户负责在引入 URL 中添加此 ID。 |  
| IngestUrl | 字符串 | 实时事件提供的引入 URL。 |  
| EncoderIp | 字符串 | 编码器的 IP。 |
| EncoderPort | 字符串 | 此流的来源编码器的端口。 |
| ResultCode | 字符串 | 拒绝连接的原因。 下表中列出了结果代码。 |

结果代码为：

| 结果代码 | Description |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | 引入 URL 不正确 |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | 编码器 IP 在配置的 IP 允许列表中不存在 |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | 编码器未发送有关流的元数据。 |
| MPE_INGEST_CODEC_NOT_SUPPORTED | 不支持指定的编解码器。 |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED | 在接收该流的标头之前收到一个片段。 |
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED | 指定的质量数目超过了允许的最大限制。 |
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED | 聚合比特率超过了允许的最大限制。 |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | RTMP 编码器中视频或音频 FLVTag 的时间戳无效。 |

## <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

以下示例显示 **LiveEventEncoderConnected** 事件的架构： 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

数据对象具有以下属性：

| 属性 | Type | Description |
| -------- | ---- | ----------- |
| StreamId | 字符串 | 流或连接的标识符。 编码器或客户负责在引入 URL 中提供此 ID。 |
| IngestUrl | 字符串 | 实时事件提供的引入 URL。 |
| EncoderIp | 字符串 | 编码器的 IP。 |
| EncoderPort | 字符串 | 此流的来源编码器的端口。 |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

以下示例显示 **LiveEventEncoderDisconnected** 事件的架构： 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

数据对象具有以下属性：

| 属性 | Type | Description |
| -------- | ---- | ----------- |
| StreamId | 字符串 | 流或连接的标识符。 编码器或客户负责在引入 URL 中添加此 ID。 |  
| IngestUrl | 字符串 | 实时事件提供的引入 URL。 |  
| EncoderIp | 字符串 | 编码器的 IP。 |
| EncoderPort | 字符串 | 此流的来源编码器的端口。 |
| ResultCode | 字符串 | 编码器断开连接的原因。 可能是正常断开连接，或者是由于出错而断开连接。 下表中列出了结果代码。 |

错误结果代码为：

| 结果代码 | Description |
| ----------- | ----------- |
| MPE_RTMP_SESSION_IDLE_TIMEOUT | 在空闲时间达到允许的时间限制后，RTMP 会话超时。 |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | RTMP 编码器中视频或音频 FLVTag 的时间戳无效。 |
| MPE_CAPACITY_LIMIT_REACHED | 编码器发送数据的频率过快。 |
| 未知错误代码 | 这些错误代码的范围可能包括内存错误、哈希映射中包含重复条目，等等。 |

正常断开连接结果代码为：

| 结果代码 | Description |
| ----------- | ----------- |
| S_OK | 编码器已成功断开连接。 |
| MPE_CLIENT_TERMINATED_SESSION | 编码器已断开连接 (RTMP)。 |
| MPE_CLIENT_DISCONNECTED | 编码器已断开连接 (FMP4)。 |
| MPI_REST_API_CHANNEL_RESET | 收到通道重置命令。 |
| MPI_REST_API_CHANNEL_STOP | 收到通道停止命令。 |
| MPI_REST_API_CHANNEL_STOP | 正在维护通道。 |
| MPI_STREAM_HIT_EOF | 编码器已发送 EOF 流。 |

## <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

以下示例显示 **LiveEventIncomingDataChunkDropped** 事件的架构： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "TrackType": "Video",
      "TrackName": "Video",
      "Bitrate": 300000,
      "Timestamp": 36656620000,
      "Timescale": 10000000,
      "ResultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0"
  }
]
```

数据对象具有以下属性：

| 属性 | Type | Description |
| -------- | ---- | ----------- |
| TrackType | 字符串 | 轨道类型（音频/视频）。 |
| TrackName | 字符串 | 轨道名称。 |
| Bitrate | integer | 轨道的比特率。 |
| Timestamp | 字符串 | 已删除的数据区块的时间戳。 |
| 时间刻度 | 字符串 | 时间戳的时间刻度。 |
| ResultCode | 字符串 | 删除数据区块的原因。 **FragmentDrop_OverlapTimestamp** 或 **FragmentDrop_NonIncreasingTimestamp**。 |

## <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

以下示例显示 **LiveEventIncomingStreamReceived** 事件的架构： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

数据对象具有以下属性：

| 属性 | Type | Description |
| -------- | ---- | ----------- |
| TrackType | 字符串 | 轨道类型（音频/视频）。 |
| TrackName | 字符串 | 轨迹的名称（由编码器提供；对于 RTMP，由服务器以 *TrackType_Bitrate* 格式生成）。 |
| Bitrate | integer | 轨道的比特率。 |
| IngestUrl | 字符串 | 实时事件提供的引入 URL。 |
| EncoderIp | 字符串  | 编码器的 IP。 |
| EncoderPort | 字符串 | 此流的来源编码器的端口。 |
| Timestamp | 字符串 | 收到数据区块的第一个时间戳。 |
| 时间刻度 | 字符串 | 用于表示时间戳的时间刻度。 |

## <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

以下示例显示 **LiveEventIncomingStreamsOutOfSync** 事件的架构： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

数据对象具有以下属性：

| 属性 | Type | Description |
| -------- | ---- | ----------- |
| MinLastTimestamp | 字符串 | 所有轨迹（音频或视频）中最后一个时间戳的最小值。 |
| TypeOfTrackWithMinLastTimestamp | 字符串 | 最后一个时间戳最小的轨迹的类型（音频或视频）。 |
| MaxLastTimestamp | 字符串 | 所有轨迹（音频或视频）中所有时间戳的最大值。 |
| TypeOfTrackWithMaxLastTimestamp | 字符串 | 最后一个时间戳最大的轨迹的类型（音频或视频）。 |

## <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

以下示例显示 **LiveEventIncomingVideoStreamsOutOfSync** 事件的架构： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "FirstTimestamp": "2162058216",
      "FirstDuration": "2000",
      "SecondTimestamp": "2162057216",
      "SecondDuration": "2000"
    },
    "dataVersion": "1.0"
  }
]
```

数据对象具有以下属性：

| 属性 | Type | Description |
| -------- | ---- | ----------- |
| FirstTimestamp | 字符串 | 收到视频类型的某个轨迹/质量级别的时间戳。 |
| FirstDuration | 字符串 | 具有第一个时间戳的数据区块的持续时间。 |
| SecondTimestamp | 字符串  | 收到视频类型的其他某个轨迹/质量级别的时间戳。 |
| SecondDuration | 字符串 | 具有第二个时间戳的数据区块的持续时间。 |

## <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

以下示例显示 **LiveEventIngestHeartbeat** 事件的架构： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

数据对象具有以下属性：

| 属性 | Type | Description |
| -------- | ---- | ----------- |
| TrackType | 字符串 | 轨道类型（音频/视频）。 |
| TrackName | 字符串 | 轨迹的名称（由编码器提供；对于 RTMP，由服务器以 *TrackType_Bitrate* 格式生成）。 |
| Bitrate | integer | 轨道的比特率。 |
| IncomingBitrate | integer | 基于来自编码器的数据区块计算出的比特率。 |
| LastTimestamp | 字符串 | 在过去 20 秒收到的轨迹的最新时间戳。 |
| 时间刻度 | 字符串 | 用于表示时间戳的时间刻度。 |
| OverlapCount | integer | 在过去 20 秒数据区块数目包含重叠的时间戳。 |
| DiscontinuityCount | integer | 在过去 20 秒观察到的不连续性数目。 |
| NonIncreasingCount | integer | 在过去 20 秒收到的具有以往时间戳的数据区块数。 |
| UnexpectedBitrate | bool | 在过去 20 秒，预期和实际比特率之差是否超过了允许的限制。 当且仅当 IncomingBitrate >= 2* 比特率，或者 IncomingBitrate <= 比特率/2，或者 IncomingBitrate = 0 时，此属性的值才为 true。 |
| State | 字符串 | 实时事件的状态。 |
| Healthy | bool | 指示引入是否正常（基于计数和标志判断）。 如果 OverlapCount = 0 并且 DiscontinuityCount = 0 并且 NonIncreasingCount = 0 并且 UnexpectedBitrate = false，则 Healthy 为 true。 |

## <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

以下示例显示 **LiveEventTrackDiscontinuityDetected** 事件的架构： 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

数据对象具有以下属性：

| 属性 | Type | Description |
| -------- | ---- | ----------- |
| TrackType | 字符串 | 轨道类型（音频/视频）。 |
| TrackName | 字符串 | 轨迹的名称（由编码器提供；对于 RTMP，由服务器以 *TrackType_Bitrate* 格式生成）。 |
| Bitrate | integer | 轨道的比特率。 |
| PreviousTimestamp | 字符串 | 前一个片段的时间戳。 |
| NewTimestamp | 字符串 | 当前片段的时间戳。 |
| DiscontinuityGap | 字符串 | 上面两个时间戳之间的差距。 |
| 时间刻度 | 字符串 | 用于表示时间戳和非连续性差距的时间刻度。 |

## <a name="common-event-properties"></a>自定义事件属性

事件具有以下顶级数据：

| 属性 | Type | Description |
| -------- | ---- | ----------- |
| 主题 | 字符串 | EventGrid 主题。 此属性包含媒体服务帐户的资源 ID。 |
| subject | 字符串 | 媒体服务帐户下媒体服务通道的资源路径。 连接主题和使用者可以获得作业的资源 ID。 |
| eventType | 字符串 | 此事件源的一个注册事件类型。 例如，“Microsoft.Media.JobStateChange”。 |
| EventTime | 字符串 | 基于提供程序 UTC 时间的事件生成时间。 |
| id | 字符串 | 事件的唯一标识符。 |
| 数据 | 对象 | 媒体服务事件数据。 |
| dataVersion | 字符串 | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | 字符串 | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

## <a name="next-steps"></a>后续步骤

[注册作业状态更改事件](job-state-events-cli-how-to.md)