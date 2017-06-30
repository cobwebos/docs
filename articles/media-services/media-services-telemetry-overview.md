---
title: "Azure 媒体服务遥测 | Microsoft Docs"
description: "本文概述了 Azure 媒体服务遥测。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 99c43c63f75e01713600ef5ca46a8d11e8c5c7ce
ms.openlocfilehash: b6560fdd50c93a7e84f12047ec4401328b601deb
ms.contentlocale: zh-cn
ms.lasthandoff: 12/09/2016


---

# <a name="azure-media-services-telemetry"></a>Azure 媒体服务遥测

通过 Azure 媒体服务 (AMS) 可访问其服务的遥测/指标数据。 通过当前版本的 AMS，可收集活动 **Channel**、**StreamingEndpoint** 和 **Archive** 实体的遥测数据。 

遥测将写入指定 Azure 存储帐户的存储表中，通常情况下，应使用与 AMS 帐户关联的存储帐户。 

遥测系统不会管理数据保留。 通过删除存储表，可移除旧的遥测数据。

本主题讨论如何配置和使用 AMS 遥测。

## <a name="configuring-telemetry"></a>配置遥测

可按组件级的粒度配置遥测。 有两种详细级别：“正常”和“详细”。 目前，这两种级别会返回相同的信息。 建议使用“正常”。 

以下主题说明如何启用遥测：

[通过 .NET 启用遥测](media-services-dotnet-telemetry.md) 

[通过 REST 启用遥测](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>使用遥测信息

如果为媒体服务帐户配置了遥测，遥测将写入指定存储帐户的 Azure 存储表中。 本部分介绍适用于各项指标的存储表。

可通过以下方式之一使用遥测数据：

- 直接从 Azure 表存储中读取数据（例如使用存储 SDK）。 有关遥测存储表的说明，请参阅[此](https://msdn.microsoft.com/library/mt742089.aspx)主题中的**使用遥测信息**。

或

- 使用媒体服务 .NET SDK 中支持的内容来读取存储数据，如[此](media-services-dotnet-telemetry.md)主题中所述。 


下述遥测架构的设计目的是在 Azure 表存储限制内提供良好性能：

- 按照帐户 ID 和服务 ID 对数据进行分区，允许单独查询每个服务中的遥测。
- 分区包含日期，以便为分区大小提供合理的上限。
- 行键采用相反的时间顺序，可允许查询给定服务的最新遥测项。

这应使许多常见查询更高效：

- 单独服务数据的并行、独立下载。
- 检索某一日期范围内给定服务的所有数据。
- 检索某项服务的最新数据。

### <a name="telemetry-table-storage-output-schema"></a>遥测表存储输出架构

遥测数据汇总存储在表“TelemetryMetrics20160321”中，其中“20160321”是创建表的日期。 遥测系统为每个新日期（基于 00:00 UTC）单独创建一个表。 该表用于存储重复值，如给定时间范围内的引入比特率、发送的字节数等。 

属性|值|示例/说明
---|---|---
PartitionKey|{account ID}_{entity ID}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>帐户 ID 包括在分区键中，可简化将多个媒体服务帐户写入同一存储帐户的工作流。
RowKey|{seconds to midnight}_{random value}|01688_00199<br/><br/>行键以距午夜的秒数开头，可允许分区内的前 n 个样式查询。 有关详细信息，请参阅[此](../storage/storage-table-design-guide.md#log-tail-pattern)文章。 
Timestamp|日期/时间|Azure 表中的自动时间戳 2016-09-09T22:43:42.241Z
类型|提供遥测数据的实体类型|Channel/StreamingEndpoint/Archive<br/><br/>事件类型只是字符串值。
名称|遥测事件的名称|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|发生遥测事件的时间 (UTC)|2016-09-09T22:42:36.924Z<br/><br/>观察时间由发送遥测的实体（例如通道）提供。 组件之间可能存在时间同步问题，因此此值为近似值
ServiceID|{service ID}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
特定于实体的属性|由事件定义|StreamName: stream1, Bitrate 10123, …<br/><br/>其余属性针对给定时间类型定义。 Azure 表内容是键值对。  （即，表中的不同行具有不同的属性集）。

### <a name="entity-specific-schema"></a>特定于实体的架构

特定于实体的遥测数据条目有三种类型，每种类型的推送频率如下：

- 流式处理终结点：每 30 秒
- 实时频道：每分钟
- 实时存档：每分钟

**流式处理终结点**

属性|值|示例
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Azure 表中的自动时间戳 2016-09-09T22:43:42.241Z
类型|类型|StreamingEndpoint
名称|名称|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|服务 ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
HostName|终结点的主机名|builddemoserver.origin.mediaservices.windows.net
StatusCode|记录 HTTP 状态|200
ResultCode|结果代码详细信息|S_OK
RequestCount|聚合中的总请求数|3
BytesSent|发送的聚合字节数|2987358
ServerLatency|平均服务器延迟（包括存储）|129
E2ELatency|平均端到端延迟|250

**实时频道**

属性|值|示例/说明
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Azure 表中的自动时间戳 2016-09-09T22:43:42.241Z
类型|类型|通道
名称|名称|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|服务 ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|轨道视频/音频/文本的类型|视频/音频
TrackName|轨道名称|video/audio_1
Bitrate|轨道比特率|785000
CustomAttributes||   
IncomingBitrate|实际传入比特率|784548
OverlapCount|引入中的重叠|0
DiscontinuityCount|轨道的中断|0
LastTimestamp|上次引入数据的时间戳|1800488800
NonincreasingCount|由于非递增时间戳而丢弃的片段计数|2
UnalignedKeyFrames|是否收到关键帧不一致的片段（跨音质级别） |True
UnalignedPresentationTime|是否收到演示时间不一致的片段（跨音质级别/轨道）|True
UnexpectedBitrate|如果音频/视频轨道的计算/实际比特率 > 40,000 bps 且 IncomingBitrate == 0，或者 IncomingBitrate 和 actualBitrate 相差 50%，则为 true |True
Healthy|如果满足以下条件，则为 true： <br/>overlapCount、 <br/>DiscontinuityCount、 <br/>NonIncreasingCount、 <br/>UnalignedKeyFrames、 <br/>UnalignedPresentationTime、 <br/>UnexpectedBitrate<br/> 都为 0|True<br/><br/>Healthy 是一个复合函数，满足以下任何条件时返回 false：<br/><br/>- OverlapCount > 0<br/>- DiscontinuityCount > 0<br/>- NonincreasingCount > 0<br/>- UnalignedKeyFrames == True<br/>- UnalignedPresentationTime == True<br/>- UnexpectedBitrate == True

**实时存档**

属性|值|示例/说明
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Azure 表中的自动时间戳 2016-09-09T22:43:42.241Z
类型|类型|存档
名称|名称|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|服务 ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
ManifestName|节目 URL|asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba.ism
TrackName|轨道名称|audio_1
TrackType|轨道类型|音频/视频
CustomAttribute|十六进制字符串，用于区分具有相同名称和比特率的不同轨道（多摄像机角度）|
Bitrate|轨道比特率|785000
Healthy|如果 FragmentDiscardedCount == 0 且 ArchiveAcquisitionError == False，则为 true|True（这两个值不存在于指标中，但存在于源事件中）<br/><br/>Healthy 是一个复合函数，满足以下任何条件时返回 false：<br/><br/>- FragmentDiscardedCount > 0<br/>- ArchiveAcquisitionError == True

## <a name="general-qa"></a>常见问答

### <a name="how-to-consume-metrics-data"></a>如何使用指标数据？

指标数据存储为客户存储帐户中的一系列 Azure 表。 可通过以下工具使用此数据：

- AMS SDK
- Microsoft Azure 存储资源管理器（支持导出逗号分隔值格式并在 Excel 中进行处理）
- REST API

### <a name="how-to-find-average-bandwidth-consumption"></a>如何查找平均带宽消耗？

平均带宽消耗是一段时间内 BytesSent 的平均值。

### <a name="how-to-define-streaming-unit-count"></a>如何定义流式处理单元数？

流式处理单元数可定义为：服务的流式处理终结点中的最大吞吐量除以单个流式处理终结点的最大吞吐量。 单个流式处理终结点的最大可用吞吐量为 160 Mbps。
例如，假设客户服务中的最大吞吐量为 40 MBps（一段时间内 BytesSent 的最大值）。 然后，流式处理单位数等于 (40 MBps)*（8 位/字节）/(160 Mbps) = 2 流式处理单位。

### <a name="how-to-find-average-requestssecond"></a>如何查找每秒平均请求数？

若要查找每秒平均请求数，可计算一段时间内的平均请求数 (RequestCount)。

### <a name="how-to-define-channel-health"></a>如何定义频道运行状况？

可将频道运行状况定义为复合布尔函数，使其在满足以下任意条件时为 false：

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames == True 
- UnalignedPresentationTime == True 
- UnexpectedBitrate == True


### <a name="how-to-detect-discontinuities"></a>如何检测中断？

若要检测中断，请查找 DiscontinuityCount > 0 的所有频道数据条目。 对应的 ObservedTime 时间戳指示发生中断的时间。

### <a name="how-to-detect-timestamp-overlaps"></a>如何检测时间戳重叠？

若要检测时间戳重叠，请查找 OverlapCount > 0 的所有频道数据条目。 对应的 ObservedTime 时间戳指示发生时间戳重叠的时间。

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>如何查找失败的流式处理请求及其原因？

若要查找失败的流式处理请求及其原因，请查找 ResultCode 不等于 S_OK 的所有流式处理终结点数据条目。 对应的 StatusCode 字段指示请求失败的原因。

### <a name="how-to-consume-data-with-external-tools"></a>如何通过外部工具使用数据？

可使用以下工具对遥测数据进行处理和可视化：

- PowerBI
- Application Insights
- Azure Monitor（以前称为 Shoebox）
- AMS 实时仪表板
- Azure 门户（尚未发行）

### <a name="how-to-manage-data-retention"></a>如何管理数据保留？

遥测系统不提供数据保留管理，也不会自动删除旧记录。 因此，需要手动管理和删除存储表中的旧记录。 可参阅存储 SDK 以了解如何执行此操作。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈

[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

