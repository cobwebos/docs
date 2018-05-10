---
title: Azure 媒体服务 - 实时传送视频流中的超时元数据信号 | Microsoft Docs
description: 此规范针对实时传送视频流中的超时元数据信号概述了媒体服务支持的两种模式。 这包括对泛型超时元数据信号的支持，以及对用于广告接合插入的 SCTE-35 信号的支持。
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2018
ms.author: johndeu;
ms.openlocfilehash: 9c8472e74cab779e417e68316a6125d40410ef1c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>实时传送视频流中的超时元数据信号


## <a name="1-introduction"></a>1 简介 
为了促进客户端播放器上的广告插入或自定义事件，广播装置通常使用视频中嵌入的超时元数据。 为实现这些方案，媒体服务将提供对超时元数据以及媒体从实时传送视频流频道的引入点到客户端应用程序的传输支持。
此规范针对实时传送视频流信号中的超时元数据概述了媒体服务支持的两种模式：

1. 遵循 [SCTE-67] 概述的建议做法的 [SCTE-35] 信号

2. 泛型超时元数据信号模式（针对不属于 [SCTE-35] 的消息）

### <a name="12-conformance-notation"></a>1.2 一致表示法
本文档中的关键字“必须”、“不得”、“需要”、“应”、“不应”、“应该”、“不能”、“建议”、“可以”和“可选”根据 RFC 2119 中所述予以解释

### <a name="13-terms-used"></a>1.3 所用术语

| 术语              | 定义                                                                                                                                                                                                                       |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 呈现时间 | 事件呈现给查看者的时间。 该时间表示媒体时间线上查看者可以看到事件的那一刻。 例如，SCTE-35 splice_info() 命令消息的呈现时间为 splice_time()。 |
| 到达时间      | 事件消息到达的时间。 该时间通常不同于事件的呈现时间，因为事件消息在事件的呈现时间之前发送。                                     |
| 稀疏轨迹      | 不连续的媒体轨迹，时间与父级或控件轨迹的时间同步。                                                                                                                                    |
| 源            | Azure 媒体流式处理服务                                                                                                                                                                                                |
| 信道接收器      | Azure 媒体实时传送视频流服务                                                                                                                                                                                           |
| HLS               | Apple HTTP Live Streaming 协议                                                                                                                                                                                               |
| DASH              | 通过 HTTP 的动态自适应流式处理                                                                                                                                                                                             |
| 平滑            | 平滑流式处理协议                                                                                                                                                                                                        |
| MPEG2-TS          | MPEG 2 传输流                                                                                                                                                                                                         |
| RTMP              | 实时多媒体协议                                                                                                                                                                                                    |
| uimsbf            | 无符号整数，最高有效位优先。                                                                                                                                                                                    |

-----------------------

## <a name="2-timed-metadata-ingest"></a>2 超时元数据引入
## <a name="21-rtmp-ingest"></a>2.1 RTMP 引入
RTMP 支持超时元数据信号作为 RTMP 流中嵌入的 AMF 提示消息发送。 提示消息可能会在实际事件前的一段时间发送，否则需要发生广告接合插入。 为支持此方案，将在提示消息内发送接合或片段的实际时间。 有关详细信息，请参见 [AMF0]。

下表介绍媒体服务将引入的 AMF 消息负载的格式。

可以使用 AMF 消息的名称来区分相同类型的多个事件流。  对于 [SCTE-35] 消息，AMF 消息的名称必须为 [SCTE-67] 中所建议的“onAdCue”。  必须忽略下面未列出的任何字段，以便将来使用这种设计创新。

### <a name="signal-syntax"></a>信号语法
对于 RTMP 简单模式，媒体服务支持具有以下格式且名为“onAdCue”的单条 AMF 提示消息：

### <a name="simple-mode"></a>简单模式

| 字段名称 | 字段类型 | 必需？ | 说明                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| 提示        | String     | 必选 | 事件消息。  应为“SpliceOut”以指定简单模式接合。                                              |
| id         | String     | 必选 | 描述接合或片段的唯一标识符。 标识消息的此实例                            |
| duration   | Number     | 必选 | 接合持续时间。 单位为小数形式的秒。                                                                |
| 已用时间    | Number     | 可选 | 如果信号重复出现以便支持接收，此字段应为自接合开始后已经过去的呈现时间量。 单位为小数形式的秒。 使用简单模式时，此值不得超过接合的原始持续时间。                                                  |
| time       | Number     | 必选 | 呈现时间应为接合的时间。 单位为小数形式的秒。                                     |

---------------------------

### <a name="scte-35-mode"></a>SCTE-35 模式

| 字段名称 | 字段类型 | 必需？ | 说明                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| 提示        | String     | 必选 | 事件消息。  对于 [SCTE-35] 消息，这必须是采用 splice_info_section() 编码的 base64 (IETF RFC 4648) 二进制文件，以便按照 [SCTE-67] 将消息发送至 HLS、Smooth 和 Dash 客户端。                                              |
| type       | String     | 必选 | 标识消息方案的 URN 或 URL；例如，“urn:example:signaling:1.0”。  对于 [SCTE-35] 消息，这必须为“urn:scte:scte35:2013a:bin”，以便按照 [SCTE-67] 将消息发送至 HLS、Smooth 和 Dash 客户端。  |
| id         | String     | 必选 | 描述接合或片段的唯一标识符。 标识消息的此实例。  具有等效语义的消息应具有相同的值。|
| duration   | Number     | 必选 | 如果已知，则为事件或广告接合段的持续时间。 如果未知，该值应为 0。                                                                 |
| 已用时间    | Number     | 可选 | 如果 [SCTE-35] 广告信号重复出现以便接收，此字段应为自接合开始后已经过去的呈现时间量。 单位为小数形式的秒。 在 [SCTE-35] 模式下，此值可能超过接合或片段的最初指定持续时间。                                                  |
| time       | Number     | 必选 | 事件或广告接合的呈现时间。  按照 [ISO-14496-12] Annex I 中的定义，呈现时间和持续时间应与类型 1 或 2 的流访问点保持一致。对于 HLS 出口，时间和持续时间应与片段边界保持一致。 相同事件流中的不同事件消息的呈现时间和持续时间不得重叠。 单位为小数形式的秒。

---------------------------

#### <a name="211-cancelation-and-updates"></a>2.1.1 取消和更新

可以通过发送多条具有相同呈现时间和 ID 的消息来取消或更新消息。 呈现时间和 ID 用于唯一标识事件，并且针对具体呈现时间收到的满足前置式约束的最后一条消息为待处理的消息。 已更新的事件将替换以前接收的任何消息。 前置式约束为 4 秒。 在呈现时间前的至少 4 秒内收到的消息将有待处理。

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 分片 MP4 引入（平滑流式处理）
有关实时流引入的要求，请参阅 [LIVE-FMP4]。 以下各节提供有关引入超时呈现元数据的详细信息。  超时呈现元数据作为稀疏轨迹引入，Live Server Manifest Box（请参阅 MS-SSTR）和 Movie Box（“moov”）中都对稀疏轨迹进行了定义。  每个稀疏片段都包含 Movie Fragment Box（“moof”）和 Media Data Box（“mdat”），其中“mdat”块是二进制消息。

#### <a name="221-live-server-manifest-box"></a>2.2.1 Live Server Manifest Box
稀疏轨迹必须在具有 \<textstream\> 条目的 Live Server Manifest Box 中进行声明，并且必须包含以下属性集：

| **属性名称** | **字段类型** | **必需？** | **说明**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Number         | 必选      | 必须为“0”，以指示具有未知、可变比特率的轨迹。                                                                                                                                                                                                 |
| parentTrackName    | String         | 必选      | 必须是父级轨迹的名称，稀疏轨迹时间码与父级轨迹时间刻度一致。 父级轨迹不能为稀疏轨迹。                                                                                                                    |
| manifestOutput     | 布尔        | 必选      | 必须为“true”，以指示将在 Smooth 客户端清单中嵌入稀疏轨迹。                                                                                                                                                               |
| 子类型            | String         | 必选      | 必须是四字符代码的“DATA”。                                                                                                                                                                                                                         |
| 方案             | String         | 必选      | 必须为标识消息方案的 URN 或 URL；例如，“urn:example:signaling:1.0”。 对于 [SCTE-35] 消息，这必须为“urn:scte:scte35:2013a:bin”，以便按照 [SCTE-67] 将消息发送至 HLS、Smooth 和 Dash 客户端。 |
| trackName          | String         | 必选      | 必须为稀疏轨迹的名称。trackName 可用来区分具有相同方案的多个事件流。 每个唯一事件流必须具有唯一的轨迹名称。                                                                           |
| 时间刻度          | Number         | 可选      | 必须为父级轨迹时间刻度。                                                                                                                                                                                                                      |

-------------------------------------

### <a name="222-movie-box"></a>2.2.2 Movie Box

作为稀疏轨迹的流标头的一部分，Movie Box（“moov”）将遵循 Live Server Manifest Box。

如 [ISO-14496-12] 中定义的，“moov”块应包含 TrackHeaderBox（“tkhd”）块，且具有以下约束：

| **字段名称** | **字段类型**          | **必需？** | **说明**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| duration       | 64 位无符号整数 | 必选      | 应为 0，因为轨迹块具有零个示例，且轨迹块中示例的总持续时间为 0。 |
-------------------------------------

如 [ISO-14496-12] 中所述，“moov”块应包含 HandlerBox（“hdlr”），且具有以下约束：

| **字段名称** | **字段类型**          | **必需？** | **说明**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | 32 位无符号整数 | 必选      | 应为“meta”。 |
-------------------------------------

如 [ISO-14496-12] 中所述，“stsd”块应包含带有编码名称的 MetaDataSampleEntry 块。  例如，对于 SCTE-35 消息，编码名称应为“scte”。

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Movie Fragment Box 和 Media Data Box

稀疏轨迹片段包含 Movie Fragment Box（“moof”）和 Media Data Box（“mdat”）。

如 [MS-SSTR] 中定义，MovieFragmentBox（“moof”）框必须包含 TrackFragmentExtendedHeaderBox（“uuid”）框，且具有以下字段：

| **字段名称**         | **字段类型**          | **必需？** | **说明**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | 64 位无符号整数 | 必选      | 必须为事件的到达时间。 此值可使消息与父级轨迹保持一致。   |
| fragment_duration      | 64 位无符号整数 | 必选      | 必须为事件的持续时间。 持续时间可以为零，以指示持续时间未知。 |

------------------------------------


MediaDataBox（“mdat”）块必须具有以下格式：

| **字段名称**          | **字段类型**                   | **必需？** | **说明**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 版本                 | 32 位无符号整数 (uimsbf) | 必选      | 确定“mdat”块的内容格式。 将忽略无法识别的版本。 当前仅支持版本 1。                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32 位无符号整数 (uimsbf) | 必选      | 标识消息的此实例。 具有等效语义的消息应具有相同的值；即，处理具有相同 ID 的任何一个事件消息块就足够了。                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32 位无符号整数 (uimsbf) | 必选      | TrackFragmentExtendedHeaderBox 中指定的 fragment_absolute_time 总和以及 presentation_time_delta 必须是事件的呈现时间。 按照 [ISO-14496-12] Annex I 中的定义，呈现时间和持续时间应与类型 1 或 2 的流访问点保持一致。对于 HLS 出口，时间和持续时间应与片段边界保持一致。 相同事件流中的不同事件消息的呈现时间和持续时间不得重叠。 |
| message                 | Byte Array                       | 必选      | 事件消息。 对于 [SCTE-35] 消息，尽管 [SCTE-67] 建议其他内容，但消息仍为二进制 binary splice_info_section()。 对于 [SCTE-35] 消息，这必须为 splice_info_section()，以便按照 [SCTE-67] 将消息发送至 HLS、Smooth 和 Dash 客户端。 对于 [SCTE-35] 消息，二进制 splice_info_section() 是“mdat”块的有效负载，且它不是 base64 编码。                                                            |

------------------------------


### <a name="224-cancelation-and-updates"></a>2.2.4 取消和更新
可以通过发送多条具有相同呈现时间和 ID 的消息来取消或更新消息。  呈现时间和 ID 可唯一标识事件。 针对具体呈现时间收到的满足前置式约束的最后一条消息为待处理的消息。 已更新的消息将替换以前接收的任何消息。  前置式约束为 4 秒。 在呈现时间前的至少 4 秒内收到的消息将有待处理。 


## <a name="3-timed-metadata-delivery"></a>3 超时元数据交付

事件流数据对媒体服务不透明。 媒体服务仅传递引入终结点和客户端终结点之间的三条信息。 根据 [SCTE-67] 和/或客户端的流式处理协议，将向客户端传递以下属性：

1.  架构 - 标识消息方案的 URN 或 URL。

2.  呈现时间 - 媒体时间线上的事件的呈现时间。

3.  持续时间 - 事件的持续时间。

4.  ID - 事件的可选唯一标识符。

5.  消息 - 事件数据。


## <a name="31-smooth-streaming-delivery"></a>3.1 平滑流式处理交付

请参阅 [MS-SSTR] 中的稀疏轨迹处理详细信息。

#### <a name="smooth-client-manifest-example"></a>Smooth 客户端清单示例
~~~ xml
<?xml version=”1.0” encoding=”utf-8”?>
<SmoothStreamingMedia MajorVersion=”2” MinorVersion=”0” TimeScale=”10000000” IsLive=”true” Duration=”0”
  LookAheadFragmentCount=”2” DVRWindowLength=”6000000000”>

  <StreamIndex Type=”video” Name=”video” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(video={start time})”>
    <QualityLevel Index=”0” Bitrate=”230000”
      CodecPrivateData=”250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <QualityLevel Index=”1” Bitrate=”305000”
      CodecPrivateData=”250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”audio” Name=”audio” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(audio={start time})”>
    <QualityLevel Index=”0” Bitrate=”96000” CodecPrivateData=”1000030000000000000000000000E00042C0”
      FourCC=”WMAP” AudioTag=”354” Channels=”2” SamplingRate=”44100” BitsPerSample=”16” PacketSize=”4459”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”text” Name=”scte35-event-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013a:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- <f> contains base-64 encoded splice_info_section() message 
    <c t=”600000000” d=”300000000”>
<f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia> 
~~~ 

## <a name="32--apple-hls-delivery"></a>3.2 Apple HLS 交付
Apple HTTP Live Streaming (HLS) 的超时元数据可以嵌入到自定义 M3U 标记中的片段播放列表中。  应用程序层可以分析 M3U 播放列表和处理 M3U 标记。 Azure 媒体服务将在 [HLS] 中定义的 EXT-X-CUE 标记中嵌入超时元数据。  EXT-X-CUE 标记当前被 DynaMux 用于类型为 ADI3 的消息。  要支持 SCTE-35 和非 SCTE-35 消息，请按下面的定义设置 EXT-X-CUE 标记的属性：

| **属性名称** | **类型**                      | **必需？**                             | **说明**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 提示                | 带引号的字符串                 | 必选                                  | 如 [IETF RFC 4648](http://tools.ietf.org/html/rfc4648) 中所述，编码为 base64 字符串的消息。 对于 [SCTE-35] 消息，这是 base64 编码的 splice_info_section()。                                                                                                |
| 类型               | 带引号的字符串                 | 必选                                  | 标识消息方案的 URN 或 URL；例如，“urn:example:signaling:1.0”。 对于 [SCTE-35] 消息，类型将采用特殊值“scte35”。                                                                                                                                |
| ID                 | 带引号的字符串                 | 必选                                  | 事件的唯一标识符。 引入消息时，如果未指定 ID，Azure 媒体服务将生成唯一 ID。                                                                                                                                          |
| DURATION           | 十进制浮点数 | 必选                                  | 事件持续时间。 如果未知，该值应为 0。 单位为小数形式的秒。                                                                                                                                                                                           |
| 已用时间            | 十进制浮点数 | 可选，但是对于滑动窗口为必需 | 如果信号重复出现以便支持滑动呈现窗口，此字段应为事件开始后已经过去的呈现时间量。 单位为小数形式的秒。 此值可能超过接合或片段的最初指定持续时间。 |
| TIME               | 十进制浮点数 | 必选                                  | 事件的呈现时间。 单位为小数形式的秒。                                                                                                                                                                                                                    |


HLS 播放器应用程序层将使用 TYPE 执行以下操作：标识消息的格式、对消息进行解码、应用必要的时间转换以及处理事件。  根据事件的时间戳，事件时间在父级轨迹的片段播放列表中保持同步。  它们被插入在最近的片段（#EXTINF 标记）前面。

#### <a name="hls-segment-playlist-example"></a>HLS 片段播放列表示例
~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:6
#EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
#EXTINF:6.000000,no-desc
Fragments(video=0,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=60000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
#EXT-X-CUE: ID=”metadata-12.000000”,TYPE=”urn:example:signaling:1.0”,TIME=”12.000000”, DURATION=”18.000000”,CUE=”HrwOi8vYmWVkaWEvhhaWFRlRDa=”
Fragments(video=120000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=180000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=240000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=300000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=360000000,format=m3u8-aapl)
#EXT-X-CUE: ID=”metadata-42.000000”,TYPE=”urn:example:signaling:1.0”,TIME=”42.000000”, DURATION=”60.000000”,CUE=”PD94bWwgdm0iMS4wIiBlbmNvpD4=”
#EXTINF:6.000000,no-desc
Fragments(video=420000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=480000000,format=m3u8-aapl)
…
~~~

#### <a name="hls-message-handling"></a>HLS 消息处理

事件会在每个视频和音频轨迹的片段播放列表中发出信号。根据 [HLS] 的要求，EXT-X-CUE 必须始终位于第一个 HLS 片段（用于接合结束或片段开始）之前或位于最后一个 HLS 片段（用于接合开始或片段结束）之后，以便其 TIME 和 DURATION 属性进行引用。

根据 [HLS] 的要求，启用滑动呈现窗口后，EXT-X-CUE 标记必需重复出现足够多的次数，以便始终在片段播放列表中完整介绍接合或片段，并且 ELASPED 属性必须用于指示接合或片段活跃的时间量。

启用滑动呈现窗口后，当 EXT-X-CUE 标记引用的媒体时间滚出滑动呈现窗口时，将从片段播放列表中删除该标记。

## <a name="33--dash-delivery"></a>3.3 DASH 交付
[DASH] 提供三种方式来发出事件信号：

1.  MPD 中发出信号的事件
2.  呈现中发出带内信号的事件（使用事件消息块“emsg”）
3.  两者的组合

MPD 中标记的事件可用于 VOD 流式处理，因为下载 MPD 后，客户端即可有权访问所有事件。 带内解决方案可用于实时传送视频流，因为客户端无需再次下载 MPD。 对于基于时间的分段，客户端将通过添加当前片段的持续时间和时间戳来确定下一片段的 URL。 如果该请求失败，客户端将假定中断并下载 MPD，但在其他方面继续进行流式处理，而无需下载 MPD。

Azure 媒体服务将同时进行 MPD 中的信号发送和使用事件消息块的带内信号发送。

#### <a name="mpd-signaling"></a>MPD 信号

事件将使用 EventStream 元素在 MPD 中发送信号，该元素将显示在 Period 元素中。

EventStream 元素具有以下属性：

| **属性名称** | **类型**                | **必需？** | **说明**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | 字符串                  | 必选      | 标识消息的方案。 方案将设为“Live Server Manifest”块中的方案属性的值。 值必须为标识消息方案的 URN 或 URL；例如，“urn:example:signaling:1.0”。                                                                |
| 值              | 字符串                  | 可选      | 方案所有者用于自定义消息的语义的附加字符串值。 为便于区分具有相同方案的多个事件流，值必须设为 事件流的名称（平滑引入的 trackName 或 RTMP 引入的 AMF 消息）。 |
| 时间刻度          | 32 位无符号整数 | 必选      | “emsg”块中的时间和持续时间字段的时间刻度（以“滴答数/秒”为单位）。                                                                                                                                                                                                       |


EventStream 元素中包含零个或多个事件元素，并且它们具有以下属性：

| **属性名称**  | **类型**                | **必需？** | **说明**                                                                                                                                                                                                             |
|---------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| presentation_time   | 64 位无符号整数 | 可选      | 必须是相对于周期开始的事件的媒体呈现时间。 呈现时间和持续时间应与类型 1 或 类型 2 的流访问点 (SAP) 保持一致，如 [ISO-14496-12] 附录 I 中所述。 |
| duration            | 32 位无符号整数 | 可选      | 事件持续时间。 如果持续时间未知，则必须忽略它。                                                                                                                                                 |
| id                  | 32 位无符号整数 | 可选      | 标识消息的此实例。 具有等效语义的消息应具有相同的值。 引入消息时，如果未指定 ID，Azure 媒体服务将生成唯一 ID。             |
| 事件元素值 | 字符串                  | 必选      | 如 [IETF RFC 4648](http://tools.ietf.org/html/rfc4648) 中所述，作为 base64 字符串的事件消息。                                                                                                                   |

#### <a name="xml-syntax-and-example-for-dash-manifest-mpd-signaling"></a>XML 语法和 DASH 清单 (MPD) 信号发送示例

~~~ xml
<!-- XML Syntax -->
<xs:complexType name=”EventStreamType”>
  <xs:sequence>
    <xs:element name=”Event” type=”EventType” minOccurs=”0” maxOccurs=”unbounded”/>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute ref=”xlink:href”/>
  <xs:attribute ref=”xlink:actuate” default=”onRequest”/>
  <xs:attribute name=”schemeIdUri” type=”xs:anyURI” use=”required”/>
  <xs:attribute name=”value” type=”xs:string”/>
  <xs:attribute name=”timescale” type=”xs:unsignedInt”/>
</xs:complexType>
<!-- Event -->
<xs:complexType name=”EventType”>
  <xs:sequence>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute name=”presentationTime” type=”xs:unsignedLong” default=”0”/>
  <xs:attribute name=”duration” type=”xs:unsignedLong”/>
  <xs:attribute name=”id” type=”xs:unsignedInt”/>
  <xs:anyAttribute namespace=”##other” processContents=”lax”/>
</xs:complexType><?xml version=”1.0” encoding=”utf-8”?>


<!-- Example Section in MPD -->

<EventStream schemeIdUri=”urn:example:signaling:1.0” timescale=”1000” value=”player-statistics”>
  <Event presentationTime=”0” duration=”10000” id=”0”> PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</Event>
  <Event presentationTime=”20000” duration=”10000” id=”1”> PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</Event>
</EventStream>
~~~

>[!NOTE]
>请注意，该 presentationTime 是事件的呈现时间，而不是消息的到达时间。
>

### <a name="431-in-band-event-message-box-signaling"></a>4.3.1 带内事件消息块信号
带内事件流要求 MPD 具有适应集级别的 InbandEventStream 元素。  此元素具有一个必需的 schemeIdUri 属性和可选的时间刻度属性，该属性还显示在事件消息块（“emsg”）中。  不应存在具有未在 MPD 中定义的方案标识符的事件消息块。 如果 DASH 客户端检测到具有未在 MPD 中定义的方案的事件消息块，客户端应忽略它。
事件消息块（“emsg”）可提供用于与媒体呈现时间相关的泛型事件的信号。 如果存在，所有“emsg”块都应置于“moof”块前。

### <a name="dash-event-message-box-emsg"></a>DASH 事件消息块“emsg”
~~~
Box Type: `emsg’
Container: Segment
Mandatory: No
Quantity: Zero or more
~~~

~~~ c
aligned(8) class DASHEventMessageBox extends FullBox(‘emsg’, version = 0, flags =0) 
{
    string scheme_id_uri;
    string value;
    unsigned int(32) timescale;
    unsigned int(32) presentation_time_delta;
    unsigned int(32) event_duration;
    unsigned int(32) id;
    unsigned int(8) message_data[];
}
~~~

DASHEventMessageBox 字段定义如下：

| **字段名称**          | **字段类型**          | **必需？** | **说明**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | 字符串                  | 必选      | 标识消息的方案。 方案将设为“Live Server Manifest”块中的方案属性的值。 值必须为标识消息方案的 URN 或 URL；例如，“urn:example:signaling:1.0”。 对于 [SCTE-35] 消息，尽管 [SCTE-67] 建议其他内容，但这仍将采用特殊值“urn:scte:scte35:2013a:bin”。 |
| 值                   | 字符串                  | 必选      | 方案所有者用于自定义消息的语义的附加字符串值。 为便于区分具有相同方案的多个事件流，值将设为 事件流的名称（平滑引入的 trackName 或 RTMP 引入的 AMF 消息）。                                                                  |
| 时间刻度               | 32 位无符号整数 | 必选      | “emsg”块中的时间和持续时间字段的时间刻度（以“滴答数/秒”为单位）。                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | 32 位无符号整数 | 必选      | 此片段中的事件呈现时间的媒体呈现时间增量和最早的呈现时间。 呈现时间和持续时间应与类型 1 或 类型 2 的流访问点 (SAP) 保持一致，如 [ISO-14496-12] 附录 I 中所述。                                                                                            |
| event_duration          | 32 位无符号整数 | 必选      | 事件的持续时间或 0xFFFFFFFF，以指示未知的持续时间。                                                                                                                                                                                                                                                                                          |
| ID                      | 32 位无符号整数 | 必选      | 标识消息的此实例。 具有等效语义的消息应具有相同的值。 引入消息时，如果未指定 ID，Azure 媒体服务将生成唯一 ID。                                                                                                                                                    |
| Message_data            | Byte Array              | 必选      | 事件消息。 对于 [SCTE-35] 消息，尽管 [SCTE-67] 建议其他内容，但消息数据仍为二进制 binary splice_info_section()。                                                                                                                                                                                                                                 |

### <a name="332-dash-message-handling"></a>3.3.2 DASH 消息处理

对于视频和音频轨迹，事件都是“emsg”块内的带内信号。  所有片段请求都会发出信号，因为 presentation_time_delta 为 15 秒或更少。 启用滑动呈现窗口后，当事件消息的时间和持续时间之和小于清单中的媒体数据的时间时，将从 MPD 中删除事件消息。  换而言之，当事件消息引用的媒体时间滚出滑动呈现窗口时，将从清单中删除事件消息。

## <a name="4-scte-35-ingest"></a>4 SCTE-35 引入

[SCTE-35] 消息针对平滑引入使用方案“urn:scte:scte35:2013a:bin”以二进制格式引入，并针对 RTMP 引入使用类型“scte35”。 为便于转换基于 MPEG-2 传输流呈现时间戳 (PTS) 的 [SCTE-35] 计时，事件呈现时间（对于平滑引入为 fragment_absolute_time 字段，对于 RTMP 引入为时间字段）在 PTS (pts_time + pts_adjustment of the splice_time()) 和媒体时间线之间提供了一种映射。 映射是必需的，因为 33 位 PTS 值每隔大约 26.5 小时将汇总一次。

平滑流式处理引入要求 Media Data Box（“mdat”）必须包含 [SCTE-35] 表 8-1 中所定义的 splice_info_section()。 对于 RTMP 引入，AMF 消息的提示属性应设为 base64encoded splice_info_section()。 如果消息具有上面所述的格式，将发送到符合 [SCTE-67] 的 HLS、Smooth 和 Dash 客户端。


## <a name="5-references"></a>5 参考资料

**[SCTE-35]** ANSI/SCTE 35 2013a – 电缆的数字程序插入提示消息，2013a

**[SCTE-67]** ANSI/SCTE 67 2014 –SCTE 35 的建议做法：电缆的数字程序插入提示消息

**[DASH]** ISO/IEC 23009-1 2014 - 信息技术 - 通过 HTTP 的动态自适应流式处理 (DASH) - 第 1 部分：媒体展现说明和分段格式，第二版

**[HLS]** [“HTTP Live Streaming”, draft-pantos-http-live-streaming-14, 2014/10/14](http://tools.ietf.org/html/draft-pantos-http-live-streaming-14)

**[MS-SSTR]** [ Microsoft Smoot Streaming Protocol（Microsoft 平滑流式处理协议），2014/5/15](http://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

**[AMF0]**[ （操作消息格式 AMF0）](http://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)

**[LIVE-FMP4]**[ Azure 媒体服务分片 MP4 实时引入规范](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)

**[ISO-14496-12]** ISO/IEC 14496-12：第 12 部分 ISO 基础媒体文件格式，2012-07-15 第 4 版。

**[RTMP]**[ 2012 年 12 月 21 日，Adobe’s Real-Time Messaging Protocol（Adobe 的实时消息传送协议）](https://www.adobe.com/devnet/rtmp.html) 

------------------------------------------

## <a name="next-steps"></a>后续步骤
查看媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
