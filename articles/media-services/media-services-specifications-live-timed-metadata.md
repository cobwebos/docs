---
title: Azure 媒体服务 - 实时传送视频流中的超时元数据信号 | Microsoft Docs
description: 此规范概述在引入和流式传输到 Azure 媒体服务时, 用于通知计时元数据的方法。 这包括对一般计时元数据信号 (ID3) 的支持, 以及用于广告插入和接合条件信号的 SCTE-35 信号。
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: df2a86dd1292f58511765e842ee97daddcff4e3e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102922"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>实时传送视频流中的超时元数据信号 

上次更新时间:2019-08-22

### <a name="conformance-notation"></a>一致表示法

本文档中的关键字“必须”、“不得”、“需要”、“应”、“不应”、“应该”、“不能”、“建议”、“可以”和“可选”根据 RFC 2119 中所述予以解释

## <a name="1-introduction"></a>1.简介 

为了向客户端播放器上的广告或自定义元数据事件发出信号, 广播者通常使用视频中嵌入的超时元数据。 为了实现这些方案, Media Services 提供了对从实时流式处理通道的引入点到客户端应用程序的定时元数据传输的支持。
此规范概述了媒体服务支持的多种模式, 这些模式适用于实时流式处理信号中的定时元数据。

1. [35 SCTE] 符合 [SCTE-35]、[SCTE-214-1]、[SCTE-214-3] 和 [RFC8216] 列出的标准的信号

2. [35 SCTE] 符合 RTMP 广告信号的旧版 [Adobe-Primetime] 规范的信号。
   
3. 一般计时的元数据信号模式, 对于**不**是 [SCTE-35] 并且可能携带 [ID3v2] 或由应用程序开发人员定义的其他自定义架构的消息。

## <a name="11-terms-used"></a>1.1 使用的术语

| 术语              | 定义 |
|-------------------|------------|
| 广告中断          | 可能计划交付一个或多个广告的位置或时间点;与可用性和放置机会相同。 |
| Ad 决策服务| 确定将向用户显示哪些 ad 和持续时间的外部服务。 服务通常由合作伙伴提供, 不在本文档的范围内。|
| 暗示               | 指示即将发生的广告中断的时间和参数。 请注意, 提示可以指示到广告中断的挂起切换, 等待切换到广告中断内的下一个广告, 并等待从广告中断到主要内容的切换。 |
| 打包          | Azure 媒体服务 "流式处理终结点" 提供适用于破折号和 HLS 的动态打包功能, 在媒体行业中称为 "包装器"。 
| 呈现时间 | 事件呈现给查看者的时间。 该时间表示媒体时间线上查看者可以看到事件的那一刻。 例如，SCTE-35 splice_info() 命令消息的呈现时间为 splice_time()。 |
| 到达时间      | 事件消息到达的时间。 该时间通常不同于事件的呈现时间，因为事件消息在事件的呈现时间之前发送。                                     |
| 稀疏轨迹      | 不连续的媒体轨迹，时间与父级或控件轨迹的时间同步。                                                                                                                                    |
| 原始            | Azure 媒体流式处理服务                                                                                                                                                                                                |
| 信道接收器      | Azure 媒体实时传送视频流服务                                                                                                                                                                                           |
| HLS               | Apple HTTP Live Streaming 协议                                                                                                                                                                                               |
| DASH              | 通过 HTTP 的动态自适应流式处理                                                                                                                                                                                             |
| 平滑            | 平滑流式处理协议                                                                                                                                                                                                        |
| MPEG2-TS          | MPEG 2 传输流                                                                                                                                                                                                         |
| RTMP              | 实时多媒体协议                                                                                                                                                                                                    |
| uimsbf            | 无符号整数，最高有效位优先。                                                                                                                                                                                    |

---

## <a name="12-normative-references"></a>1.2 规范引用

以下文档包含规定, 这些规定通过此文本中的引用构成了本文档的条款。 所有文档都受标准主体的修订, 并鼓励读者调查应用最新版本的文档的可能性。 还会提醒读者, 较新版本的引用文档可能与 Azure 媒体服务的定时元数据规范版本不兼容。


|标准  |定义  |
|---------|---------|
|[Adobe-Primetime] | [Primetime 数字程序插入信号规范1。2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf) |
|[Adobe-闪光-AS] | [FLASH ActionScript 语言参考](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf) |
| AMF0            | ["操作消息格式 AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf) |
| [短线-IF-IOP]     | 短划线行业论坛互操作指南 v 4。2[https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html) |
| [HLS-TMD]         | HTTP Live Streaming 的定时元数据-[https://developer.apple.com/streaming](https://developer.apple.com/streaming) |
| [CMAF]         | [常见媒体应用程序格式的定时元数据 (CMAF)](https://aomediacodec.github.io/av1-id3/)
| ID3v2           | ID3 标记版本2.4。0[http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure) |
| [ISO-14496-12]    | ISO/IEC 14496-12:第12部分 ISO 基本媒体文件格式, FourthEdition 2012-07-15  |
| [MPEGDASH]        | 信息技术-通过 HTTP 的动态自适应流式处理 (破折号)--第1部分:媒体演示说明和段格式。 可能为2014。 发布. URL： https://www.iso.org/standard/65274.html |
| [MPEGCMAF]        | 信息技术-多媒体应用程序格式 (MPEG)--第19部分:分段媒体的常见媒体应用程序格式 (CMAF)。 2018年1月。 发布. URL： https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | 信息技术--MPEG 系统技术-第7部分:ISO 基本媒体文件格式文件中的通用加密。 2016年2月。 发布. URL： https://www.iso.org/standard/68042.html |
| [SSTR]         | ["Microsoft 平滑流式处理协议", 5 月15日2014](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) |
| [SSTR]  | [Azure 媒体服务零碎的 MP4 实时引入规范](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview) |
| [RFC8216]         | Jenkins， Pantos, Ed.;可能。 HTTP Live Streaming。 2017年8月。 条. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216) |
| RFC4648         |Base16、Base32 和 Base64 数据编码-[https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648) |
| RTMP            |[2012年12月21日,](https://www.adobe.com/devnet/rtmp.html)  |
| [SCTE-35-2019]    | SCTE 35:2019-提示的数字程序插入消息- https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf  |
| [SCTE-214-1]      | SCTE 214-1 2016 –基于 IP 的电缆服务的 MPEG 虚线第1部分:MPD 约束和扩展 |
| [SCTE-214-3]      | SCTE 214-3 2015 对于基于 IP 的电缆服务的 MPEG 破折号第3部分:短划线/FF 配置文件 |
| [SCTE-224]        | SCTE 224 2018r1 –事件计划和通知接口 |
| [SCTE-250]        | 事件和信号管理 API (ESAM) |

---


## <a name="2-timed-metadata-ingest"></a>2.计时数据引入

Azure 媒体服务支持 [RTMP] 和平滑流式处理 [SSTR] 协议的实时带内元数据。 实时元数据可用于定义自定义事件、使用自己独特的自定义架构 (JSON、二进制、XML) 以及行业定义格式 (如 ID3) 或 SCTE-35 (用于广播流中的广告信号)。 

本文提供了有关如何使用 Azure 媒体服务支持的引入协议发送自定义的定时元数据信号的详细信息。 本文还介绍了如何使用计时元数据信号修饰 HLS、破折号和平滑流式处理的清单, 以及如何在使用 CMAF (片段) 或 HLS 传输流 (TS) 段传输内容时将其带内传输。 

计时元数据的常见用例方案包括:

 - SCTE-35 用于在实时事件或线性广播中触发广告中断的广告信号
 - 可在客户端应用程序 (浏览器、iOS 或 Android) 上触发事件的自定义 ID3 元数据
 - 自定义的 JSON、二进制或 XML 元数据, 用于在客户端应用程序中触发事件
 - 来自实时编码器、IP 照相机或无人机的遥测
 - 来自 IP 摄像机的事件, 如动作、面部检测等。
 - 操作相机、无人机或移动设备的地理位置信息
 - 歌曲歌词
 - 线性实时源上的程序边界
 - 要在实时源上显示的图像或已扩充的元数据
 - 体育评分或游戏-时钟信息
 - 要在浏览器中与视频一起显示的交互式广告包
 - 测验或投票
  
Azure 媒体服务实时事件和包装器可以接收这些计时的元数据信号, 并将它们转换为可使用基于标准的协议 (如 HLS 和短划线) 访问客户端应用程序的元数据流。


## <a name="21-rtmp-timed-metadata"></a>2.1 RTMP 计时元数据

[RTMP] 协议允许针对各种方案 (包括自定义元数据和 35 SCTE 广告信号) 发送定时元数据信号。 

广告信号 (提示消息) 以 [AMF0] 提示消息的形式发送, 该消息嵌入在 [RTMP] 流中。 在实际事件或 [SCTE35] ad 接合信号需要发生之前, 可能会发送提示消息。 若要支持此方案, 请在提示消息中发送事件的实际显示时间戳。 有关详细信息，请参见 [AMF0]。

Azure 媒体服务支持以下 [AMF0] 命令用于 RTMP 摄取:

- **onUserDataEvent** -用于自定义元数据或 [ID3v2] 计时元数据
- **onAdCue** -主要用于在实时流中发出广告定位机会。 支持两种形式的提示: 简单模式和 "SCTE-35" 模式。 
- **onCuePoint** -由某些本地硬件编码器 (如 Elemental 实时编码器) 支持, 以发出 [SCTE35] 消息。 
  

下表描述了媒体服务将为 "简单" 和 "SCTE35" 消息模式引入的 AMF 消息负载的格式。

[AMF0] 消息的名称可用于区分同一类型的多个事件流。  对于 [SCTE-35] 消息和 "简单" 模式, AMF 消息的名称在 [Adobe-Primetime] 规范中必须是 "onAdCue"。  Azure 媒体服务将在引入时忽略下面未列出的任何字段。

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 使用 "onUserDataEvent" 的自定义元数据的 RTMP

如果要使用 RTMP 协议从上游编码器、IP 照相机、无人机或设备提供自定义元数据源, 请使用 "onUserDataEvent" [AMF0] 数据消息命令类型。

**"OnUserDataEvent"** 数据消息命令必须携带具有以下定义的消息负载, 以由媒体服务捕获并打包成带内文件格式, 并打包为 HLS、破折号和平滑流式处理清单。
建议每隔0.5 秒 (500 毫秒) 发送一次定时元数据消息, 否则可能会出现实时流的稳定性问题。 如果需要提供框架级元数据, 则每个消息都可以聚合来自多个帧的元数据。 如果要发送多比特率流, 则建议你仅在单比特率提供元数据, 以减少带宽并避免视频/音频处理干扰。 

**"OnUserDataEvent"** 的有效负载应为 [MPEGDASH] EventStream XML 格式消息。 这样, 就可以轻松地传入自定义的架构, 这些架构可以在 "emsg" 有效负载中携带, 用于 CMAF [MPEGCMAF] 通过 HLS 或短划线协议传递的内容。 每个短划线事件流消息都包含一个 schemeIdUri, 它充当 URN 消息方案标识符, 并定义消息的负载。 [ID3v2] 的某些 https://aomedia.org/emsg/ID3 方案 (如 "") 或**urn: scte: scte35: 2013: bin** for [35 scte] 按行业 consortia 标准化以实现互操作性。 任何应用程序提供程序都可以使用其控制的 URL (拥有的域) 定义自己的自定义方案, 并可以在该 URL 中提供规范 (如果选择)。 如果播放机具有已定义方案的处理程序, 则这是需要理解有效负载和协议的唯一组件。

[MPEG-短线] EventStream XML 有效负载的架构定义为 (从短划线 ISO-IEC-23009-第三版开始)。 请注意, 此时仅支持每个 "EventStream" 一个 "事件类型"。 如果**EventStream**中提供了多个事件, 则仅处理第一个**事件**元素。

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>带有 ID3 架构 ID 和 base64 编码的数据负载的示例 XML 事件流。  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>带有自定义架构 ID 和 base64 编码的二进制数据的示例事件流  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>带有自定义架构 ID 和自定义 JSON 的示例事件流  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>内置支持的方案 ID Uri
| 方案 ID URI                 |  描述                                             |
|-------------------------------|----------------------------------------------------------|
| https:\//aomedia.org/emsg/ID3   | 描述如何将 [ID3v2] 元数据作为与 CMAF 兼容的 [MPEGCMAF] 分散的形式传递。 有关详细信息, 请参阅[常见媒体应用程序格式的定时元数据 (CMAF)](https://aomediacodec.github.io/av1-id3/) |

### <a name="event-processing-and-manifest-signaling"></a>事件处理和清单信号

收到有效的 **"onUserDataEvent"** 事件时, Azure 媒体服务将查找与 EventStreamType 匹配的有效 XML 有效负载 (在 [MPEGDASH] 中定义), 分析 XML 有效负载并将其转换为 [MPEGCMAF] 片段 "emsg" 版本1框实时存档并传输到媒体服务包装器的存储。   包装器将检测实时流中的 "emsg" 框, 并执行以下操作:

- (a) 将其 "动态打包" 到 TS 段, 以便在符合 HLS 定时元数据规范 [HLS-TMD] 的情况下传递到 HLS 客户端, 或
- (b) 通过 HLS 或短划线向传递到 CMAF 片段, 或 
- (c) 通过平滑流式处理 [SSTR] 将其转换为稀疏轨迹信号以进行传送。

除了 HLS 的带内 "emsg" 格式 CMAF 或 TS PE 数据包外, 短划线 (MPD) 和平滑流式处理的清单将包含对带内事件流的引用 (在平滑流式处理中也称为稀疏流跟踪)。 

单个事件或其数据负载不会直接在 HLS、破折号或光滑清单中输出。 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>OnUserDataEvent 事件的其他信息性约束和默认值

- 如果未在 EventStream 元素中设置时间刻度, 则默认情况下将使用 RTMP 1 kHz 时间刻度
- OnUserDataEvent 消息的传递限于每个500毫秒最大值一次。如果更频繁地发送事件, 可能会影响实时源的带宽和稳定性

## <a name="212-rtmp-ad-cue-signaling-with-oncuepoint"></a>具有 "onCuePoint" 的 2.1.2 RTMP 广告提示

Azure 媒体服务可以侦听并响应若干 [AMF0] 消息类型, 这些消息类型可用于在实时流中通知各种实时同步元数据。  [Primetime] 规范定义了两个称为 "简单" 和 "SCTE-35" 模式的提示类型。 对于 "简单" 模式, 媒体服务支持使用与下表 (为 "简单模式" 信号定义) 匹配的 "onAdCue" 的单个 AMF 提示消息。  

以下部分显示了 RTMP "简单" 模式 "负载, 可用于发送基本" spliceOut "广告信号, 该信号会传递到 HLS、破折号和 Microsoft 平滑流式处理的客户端清单。 这对于以下情况非常有用: 客户不提供基于 SCTE 35 的复杂广告信号部署或插入系统, 并使用基本的本地编码器通过 API 在提示消息中发送。 通常, 本地编码器将支持基于 REST 的 API 触发此信号, 该信号还会通过将 IDR 帧插入视频并启动新的 GOP 来 "拼接条件" 视频流。

## <a name="213--rtmp-ad-cue-signaling-with-oncuepoint---simple-mode"></a>2.1.3 RTMP "onCuePoint" 的广告提示-简单模式

| 字段名 | 字段类型 | 必需？ | 说明                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| 提示        | String     | 必填 | 事件消息。  应为“SpliceOut”以指定简单模式接合。                                              |
| id         | String     | 必填 | 描述接合或片段的唯一标识符。 标识消息的此实例                            |
| 持续时间   | 数量     | 必填 | 接合持续时间。 单位为小数形式的秒。                                                                |
| elapsed    | 数量     | 可选 | 如果信号重复出现以便支持接收，此字段应为自接合开始后已经过去的呈现时间量。 单位为小数形式的秒。 使用简单模式时，此值不得超过接合的原始持续时间。                                                  |
| 时间       | 数量     | 必填 | 呈现时间应为接合的时间。 单位为小数形式的秒。                                     |

---
 
## <a name="214-rtmp-ad-cue-signaling-with-oncuepoint---scte-35-mode"></a>2.1.4 RTMP 广告提示, 带 "onCuePoint"-SCTE-35 模式

使用需要将完整的 SCTE-35 负载消息传递到 HLS 或短划线清单的更高级的广播生产工作流时, 最好使用 [Adobe-Primetime] 规范的 "SCTE-35 模式"。  此模式支持将带内 SCTE-35 信号直接发送到本地实时编码器, 然后使用 [Adobe-Primetime] 规范中指定的 "SCTE-35 模式" 将信号编码到 RTMP 流中。 

通常 35, SCTE 消息只能出现在本地编码器的 MPEG-2 传输流 (TS) 输入中。 请与编码器制造商联系, 以了解有关如何配置包含 SCTE-35 的传输流引入的详细信息, 以及如何在 Adobe SCTE-35 模式下将其用于传递到 RTMP。

在此方案中, 必须使用 **"onAdCue"** [AMF0] 消息类型从本地编码器发送以下有效负载。

| 字段名 | 字段类型 | 必需？ | 说明                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| 提示        | String     | 必填 | 事件消息。  对于 [SCTE-35] 消息, 这必须是 base64 编码的 [RFC4648] binary splice_info_section (), 以便将消息发送到 HLS、平滑和短信客户端。                                              |
| type       | String     | 必填 | 标识消息方案的 URN 或 URL。 对于 [SCTE-35] 消息, 此项**应**为 **"scte35"** , 以便将消息发送到 HLS、平滑和短信客户端, 与 [Adobe-Primetime] 兼容。 也可以选择使用 URN "urn: scte: scte35: 2013: bin" 发出 [SCTE-35] 消息的信号。 |
| id         | String     | 必填 | 描述接合或片段的唯一标识符。 标识消息的此实例。  具有等效语义的消息应具有相同的值。|
| 持续时间   | 数量     | 必填 | 如果已知，则为事件或广告接合段的持续时间。 如果未知, 则该值**应**为0。                                                                 |
| elapsed    | 数量     | 可选 | 如果 [SCTE-35] 广告信号重复出现以便接收，此字段应为自接合开始后已经过去的呈现时间量。 单位为小数形式的秒。 在 [SCTE-35] 模式下，此值可能超过接合或片段的最初指定持续时间。                                                  |
| 时间       | 数量     | 必填 | 事件或广告接合的呈现时间。  显示时间和持续时间**应**与类型为1或2的流访问点 (SAP) 一致, 如 [ISO-14496-12] 附录 I 中所定义。对于 HLS 出口, 时间和持续时间**应**与段边界对齐。 相同事件流中的不同事件消息的呈现时间和持续时间不得重叠。 单位为小数形式的秒。

---
## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>用于 Elemental Live 的 "onCuePoint" 的 2.1.5 RTMP 广告信号

Elemental Live 本地编码器支持 RTMP 信号中的广告标记。 Azure 媒体服务目前只支持 RTMP 的 "onCuePoint" 广告标记类型。  可以通过将 "**ad_markers**" 设置为 "onCuePoint", 在 Elemental Media Live 编码器设置或 API 的 Adobe RTMP 组设置中启用此功能。  有关详细信息, 请参阅 Elemental Live 文档。 如果在 RTMP 组中启用此功能, 则会将 SCTE-35 信号传递到 Adobe RTMP 输出, 以由 Azure 媒体服务进行处理。

在 [Adobe-闪光] 中定义了 "onCuePoint" 消息类型, 并在从 Elemental Live RTMP 输出发送时具有以下有效负载结构。


| 属性  |描述  |
|---------|---------|
|  name     | 名称应为 "**scte35**" (通过 Elemental Live)。 |
|时间     |  时间线中的提示点在视频文件中发生的时间 (以秒为单位) |
| type     | 提示点的类型应设置为 "**事件**"。 |
| 参数 | 名称/值对字符串的关联数组, 其中包含 SCTE-35 消息中的信息, 包括 Id 和 duration。 这些值由 Azure 媒体服务进行分析, 并包含在清单修饰标记中。  |


当使用广告标记的此模式时, HLS 清单输出类似于 Adobe "Simple" 模式。 

### <a name="216-cancellation-and-updates"></a>2.1.6 取消和更新

可以通过发送多条具有相同呈现时间和 ID 的消息来取消或更新消息。 呈现时间和 ID 用于唯一标识事件，并且针对具体呈现时间收到的满足前置式约束的最后一条消息为待处理的消息。 已更新的事件将替换以前接收的任何消息。 前置式约束为 4 秒。 在呈现时间前的至少 4 秒内收到的消息将有待处理。

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 分片 MP4 引入（平滑流式处理）

有关实时流引入的要求, 请参阅 [SSTR-摄取]。 以下各节提供有关引入超时呈现元数据的详细信息。  超时呈现元数据作为稀疏轨迹引入，Live Server Manifest Box（请参阅 MS-SSTR）和 Movie Box（“moov”）中都对稀疏轨迹进行了定义。  

每个稀疏片段都包含 Movie Fragment Box（“moof”）和 Media Data Box（“mdat”），其中“mdat”块是二进制消息。

为了实现帧准确插入广告, 编码器必须在显示需要插入提示的演示时间拆分片段。  必须创建以新创建的 IDR 帧开头的新片段, 或使用 [ISO-14496-12] 附录 I 中定义的 "1" 或 "2" 类型的流访问点 (SAP)。这允许 Azure 媒体包装器正确生成 HLS 清单和一个短划线多段清单, 其中的新期间从帧准确的接合法表示时间开始。

### <a name="221-live-server-manifest-box"></a>2.2.1 Live Server Manifest Box

**必须**在 "实时服务器清单" 框中使用 **\<textstream\>** 项声明稀疏轨迹, 并且**必须**设置以下属性:

| **属性名称** | **字段类型** | **必需？** | **说明**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | 数量         | 必填      | **必须**为 "0", 表示具有未知可变比特率的轨迹。                                                                                                                                                                                                 |
| parentTrackName    | String         | 必填      | **必须**为父轨迹的名称, 稀疏轨迹时间代码的时间刻度为该名称。 父级轨迹不能为稀疏轨迹。                                                                                                                    |
| manifestOutput     | Boolean        | 必填      | **必须**为 "true", 以指示将在平滑客户端清单中嵌入稀疏轨迹。                                                                                                                                                               |
| 子类型            | String         | 必填      | **必须**是四个字符代码 "DATA"。                                                                                                                                                                                                                        |
| 方案             | String         | 必填      | **必须**为标识消息方案的 URN 或 URL。 对于 [SCTE-35] 消息, 此项**必须**为 "urn: SCTE: scte35: 2013: bin", 以便将消息发送到 HLS、平滑和短信客户端与 [SCTE-35] 兼容。 |
| trackName          | String         | 必填      | **必须**是稀疏轨迹的名称。trackName 可用来区分具有相同方案的多个事件流。 每个唯一的事件流都**必须**有唯一的轨道名称。                                                                           |
| 时间刻度          | 数量         | 可选      | **必须**为父轨迹的时间刻度。                                                                                                                                                                                                                      |

---

### <a name="222-movie-box"></a>2.2.2 Movie Box

作为稀疏轨迹的流标头的一部分，Movie Box（“moov”）将遵循 Live Server Manifest Box。

"Moov" 框**应**包含在 [ISO-14496-12] 中定义的**TrackHeaderBox ("tkhd")** , 且具有以下约束:

| **字段名称** | **字段类型**          | **必需？** | **说明**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| 持续时间       | 64 位无符号整数 | 必填      | **应**为 0, 因为跟踪框有零个样本, 并且 "跟踪" 框中的样本的总持续时间为0。 |

---

"Moov" 框**应**包含在 [ISO-14496-12] 中定义的**HandlerBox ("hdlr")** , 其约束如下:

| **字段名称** | **字段类型**          | **必需？** | **说明**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | 32 位无符号整数 | 必填      | **应**为 "meta"。 |

---

"Stsd" 框**应**包含带有编码名称的 MetaDataSampleEntry 框, 如 [ISO-14496-12] 中所定义。  例如, 对于 SCTE-35 消息, 编码名称**应**为 "SCTE"。

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Movie Fragment Box 和 Media Data Box

稀疏轨迹片段包含 Movie Fragment Box（“moof”）和 Media Data Box（“mdat”）。

> [!NOTE]
> 为了实现帧准确插入广告, 编码器必须在显示需要插入提示的演示时间拆分片段。  必须创建以新创建的 IDR 帧开头的新片段, 或类型为1或2的流访问点 (SAP), 如 [ISO-14496-12] 附录 I 中所定义
> 

MovieFragmentBox ("moof") 框**必须**包含在 [MS-SSTR] 中定义的**TrackFragmentExtendedHeaderBox ("uuid")** 框和以下字段:

| **字段名称**         | **字段类型**          | **必需？** | **说明**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | 64 位无符号整数 | 必填      | **必须**是事件的到达时间。 此值可使消息与父级轨迹保持一致。   |
| fragment_duration      | 64 位无符号整数 | 必填      | **必须**是事件的持续时间。 持续时间可以为零，以指示持续时间未知。 |

---


MediaDataBox (' mdat ') 框**必须**采用以下格式:

| **字段名称**          | **字段类型**                   | **必需？** | **说明**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| version                 | 32 位无符号整数 (uimsbf) | 必填      | 确定“mdat”块的内容格式。 将忽略无法识别的版本。 当前仅支持版本 1。                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32 位无符号整数 (uimsbf) | 必填      | 标识消息的此实例。 具有等效语义的消息应具有相同的值；即，处理具有相同 ID 的任何一个事件消息块就足够了。                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32 位无符号整数 (uimsbf) | 必填      | 在 TrackFragmentExtendedHeaderBox 中指定的 fragment_absolute_time 和 presentation_time_delta 的总和**必须**是事件的呈现时间。 显示时间和持续时间**应**与类型为1或2的流访问点 (SAP) 一致, 如 [ISO-14496-12] 附录 I 中所定义。对于 HLS 出口, 时间和持续时间**应**与段边界对齐。 同一事件流中不同事件消息的显示时间和持续时间**不得**重叠。 |
| 消息                 | Byte Array                       | 必填      | 事件消息。 对于 [SCTE-35] 消息, 消息是二进制 splice_info_section ()。 对于 [SCTE-35] 消息, 这**必须**是 splice_info_section (), 以便将消息发送到 HLS、平滑和短信客户端, 并遵循 [SCTE-35]。 对于 [SCTE-35] 消息, 二进制 splice_info_section () 是 "mdat" 框的负载, 它**不**是 base64 编码的。                                                            |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 取消和更新

可以通过发送多条具有相同呈现时间和 ID 的消息来取消或更新消息。  呈现时间和 ID 可唯一标识事件。 针对具体呈现时间收到的满足前置式约束的最后一条消息为待处理的消息。 已更新的消息将替换以前接收的任何消息。  前置式约束为 4 秒。 在呈现时间前的至少 4 秒内收到的消息将有待处理。 


## <a name="3-timed-metadata-delivery"></a>3 超时元数据交付

事件流数据对媒体服务不透明。 媒体服务仅传递引入终结点和客户端终结点之间的三条信息。 以下属性将传递给客户端, 符合 [SCTE-35] 和/或客户端的流式处理协议:

1.  架构 - 标识消息方案的 URN 或 URL。
2.  呈现时间 - 媒体时间线上的事件的呈现时间。
3.  持续时间 - 事件的持续时间。
4.  ID - 事件的可选唯一标识符。
5.  消息 - 事件数据。

## <a name="31-microsoft-smooth-streaming-manifest"></a>3.1 Microsoft 平滑流式处理清单  

请参阅稀疏轨迹处理 [SSTR], 了解有关如何设置稀疏消息轨迹格式的详细信息。对于 [SCTE35] 消息, 平滑流式处理会将 base64 编码的 splice_info_section () 输出到稀疏片段。
StreamIndex**必须**具有 "DATA" 的子类型, 并且 CustomAttributes**必须**包含名称为 "Schema" 且值为 "urn: scte: scte35: 2013: bin" 的属性。

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>显示 base64 编码 [SCTE35] splice_info_section () 的平滑客户端清单示例
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
  <StreamIndex Type=”text” Name=”scte35-sparse-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t=”600000000” d=”300000000”>    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>3.2 Apple HLS 清单修饰

Azure 媒体服务支持以下 HLS 清单标记, 用于在实时或按需事件期间通知广告的可用信息。 

- 在 Apple HLS [RFC8216] 中定义的 DATERANGE
- [Adobe-Primetime] 中定义的 EXT X 提示, 此模式被视为 "旧版"。 如果可能, 客户应采用 DATERANGE 标记。

每个标记的数据输出将根据所使用的引入信号模式而有所不同。 例如, Adobe Simple 模式下的 RTMP 引入不包含完全 SCTE-35 base64 编码的有效负载。

## <a name="321-apple-hls-with-adobe-primetime-ext-x-daterange-recommended"></a>3.2.1 Apple HLS 与 Adobe Primetime EXT-DATERANGE (推荐)

Apple HTTP Live Streaming [RFC8216] 规范允许发出 [SCTE-35] 消息的信号。 将消息插入到每个 [RFC8216] 部分的 DATERANGE 标记中的段播放列表, 其中标题为 "映射 SCTE-35 到 EXT-DATERANGE"。  客户端应用程序层可以分析 M3U 播放列表, 处理 M3U 标记, 或通过 Apple player framework 接收事件。  

Azure 媒体服务 (版本 3 API) 中的**建议**方法是遵循 [RFC8216], 并将 X_DATERANGE 标记用于清单中的 [SCTE35] ad 可用性修饰。

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 Apple HLS with Adobe Primetime EXT X-提示 (旧版)

Azure 媒体服务 (版本2和 3 API) 中还提供了 "旧版" 实现, 该实现使用 [Primetime] "SCTE-35 模式" 中定义的 EXT X 提示标记。 在此模式下, Azure 媒体服务将在 EXT X 提示标记中嵌入 base64 编码的 [SCTE-35] splice_info_section ()。  

"传统" EXT X 提示标记定义如下, 还可以在 [Adobe-Primetime] 规范中对其进行规范。 这只应在需要时用于旧的 SCTE35 信号, 否则, 建议的标记将在 [RFC8216] 中定义为扩展 X-DATERANGE。 

| **属性名称** | 类型                      | **必需？**                             | **说明**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 提示                | 带引号的字符串                 | 必填                                  | 编码为 base64 编码字符串的消息, 如 [RFC4648] 中所述。 对于 [SCTE-35] 消息, 这是 base64 编码的 splice_info_section ()。                                                                                                |
| 类型               | 带引号的字符串                 | 必填                                  | 标识消息方案的 URN 或 URL。 对于 [SCTE-35] 消息，类型将采用特殊值“scte35”。                                                                                                                                |
| id                 | 带引号的字符串                 | 必填                                  | 事件的唯一标识符。 引入消息时，如果未指定 ID，Azure 媒体服务将生成唯一 ID。                                                                                                                                          |
| DURATION           | 十进制浮点数 | 必填                                  | 事件持续时间。 如果未知, 则该值**应**为0。 单位为小数形式的秒。                                                                                                                                                                                           |
| 已用时间            | 十进制浮点数 | 可选，但是对于滑动窗口为必需 | 当该信号重复以支持滑动显示窗口时, 此字段**必须**为自事件开始后已经过去的呈现时间量。 单位为小数形式的秒。 此值可能超过接合或片段的最初指定持续时间。 |
| TIME               | 十进制浮点数 | 必填                                  | 事件的呈现时间。 单位为小数形式的秒。                                                                                                                                                                                                                    |


HLS 播放器应用程序层将使用 TYPE 执行以下操作：标识消息的格式、对消息进行解码、应用必要的时间转换以及处理事件。  根据事件的时间戳，事件时间在父级轨迹的片段播放列表中保持同步。  它们被插入在最近的片段（#EXTINF 标记）前面。

### <a name="323-hls-segment-playlist-example-using-legacy-adobe-primetime-ext-x-cue"></a>3.2.3 HLS 分段播放列表示例使用 "旧版" Adobe Primetime EXT X-提示

下面的示例演示了使用 Adobe Primetime EXT X 提示标记的 HLS 清单修饰。  "提示" 参数包含完全 base64 编码的 SCTE-35 splice_info 有效负载, 这表示该信号是在 Adobe SCTE-35 信号模式下使用 RTMP 发出的, 或者通过平滑流式处理 SCTE-35 信号模式进入。 

~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:346
#EXT-X-TARGETDURATION:6
#EXT-X-I-FRAMES-ONLY
#EXT-X-PROGRAM-DATE-TIME:2018-12-13T15:54:19.462Z
#EXTINF:4.000000,no-desc
KeyFrames(video_track=15447164594627600,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447164634627600,format=m3u8-aapl)
#EXT-X-CUE:ID="1026",TYPE="scte35",DURATION=30.000000,TIME=1544716520.022760,CUE="/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w=="
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447165474627600,format=m3u8-aapl)
~~~

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>3.2.4 HLS 消息处理 "传统" Adobe Primetime EXT X-提示

事件会在每个视频和音频轨迹的片段播放列表中发出信号。EXT X 提示标记的位置**必须**始终位于第一个 HLS 段之前 (用于向外或段开头), 或紧跟在其时间和持续时间属性引用的最后一个 HLS 段 (对于接合或段结束) 之后。要求: [Adobe-Primetime]。

当启用滑动显示窗口时,**必须**经常重复使用 EXT X 提示标记, 使接合或段在段播放列表中始终完全描述, 并且已运行的属性**必须**用于指示根据 [Primetime] 的要求, 接合或段处于活动状态。

启用滑动呈现窗口后，当 EXT-X-CUE 标记引用的媒体时间滚出滑动呈现窗口时，将从片段播放列表中删除该标记。

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 划线清单修饰 (MPD)

[MPEGDASH] 提供了三种通知事件的方法:

1.  MPD EventStream 中的事件已发出信号
2.  使用事件消息框 ("emsg") 带内信号的事件
3.  两者的组合

MPD EventStream 中发出信号的事件对于 VOD 流式处理非常有用, 因为在下载 MPD 后, 客户端可以访问所有事件。 此方法也适用于 SSAI 信号, 其中下游 SSAI 供应商需要从多期 MPD 清单分析信号, 并动态插入广告内容。  带内 ("emsg") 解决方案可用于实时流式处理, 其中客户端无需再次下载 MPD, 或者在客户端与源之间没有任何 SSAI 清单操作。 

对于虚线, Azure 媒体服务的默认行为是使用事件消息框 ("emsg") 在 MPD EventStream 和带内传输信号。

将引入 over [RTMP] 或 [SSTR 摄取] 的提示消息映射为虚线事件, 使用带内 "emsg" 框和/或 MPD EventStreams。 

带内 SCTE-35 在 [SCTE-214-3] 中定义的定义和要求以及在 [短线-] 部分中定义的要求 ("SCTE35 Events")。 

对于带内 [SCTE-35], 事件消息框 (' emsg ') 使用 schemeId = "urn: SCTE: scte35: 2013: bin"。 对于 MPD 清单修饰, EventStream schemeId 使用 "urn: scte: scte35: 2014: xml + bin"。  此格式是事件的 XML 表示形式, 其中包含传入的完整 SCTE-35 消息的二进制 base64 编码的输出。 

[SCTE-35] 中的 "SCTE" 提示消息的规范引用定义在 [-214-1] sec 6.7.4 (MPD) 和 [SCTE-214-3] sec 7.3.2 ("SCTE" 35 提示消息) 中可用。

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG 破折号 (MPD) EventStream 信号

事件的清单 (MPD) 在 MPD 中将使用 EventStream 元素 (该元素出现在 Period 元素中) 发出信号。 使用的 schemeId 为 "urn: scte: scte35: 2014: xml + bin"。

> [!NOTE]
> 出于简洁目的 [35 SCTE] 允许使用 SpliceInfoSection 元素中的 base64 编码部分 (而不是元素) 作为完全分析的提示消息的回车的替代项。
> Azure 媒体服务使用此 "xml + bin" 方法在 MPD 清单中发出信号。
> 这也是在 [短线-----------IOP] 中使用的建议方法-请参阅["](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 

EventStream 元素具有以下属性：

| **属性名称** | 类型                | **必需？** | **说明**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | string                  | 必填      | 标识消息的方案。 方案将设为“Live Server Manifest”块中的方案属性的值。 该值**应**为标识消息方案的 URN 或 URL;支持的输出 schemeId 应为 "urn: scte: scte35: 2014: xml + bin" (每个 [SCTE-214-1] sec 6.7.4 (MPD)), 因为该服务此时仅支持 "xml + bin", 以便在 MPD 中简洁。  |
| value              | string                  | 可选      | 方案所有者用于自定义消息的语义的附加字符串值。 为了区分具有相同方案的多个事件流,**必须**将该值设置为事件流的名称 ([SSTR-摄取] 的 trackName 或 [RTMP] 摄取的 AMF 消息名称)。 |
| Timescale          | 32 位无符号整数 | 必填      | 时间刻度, 以每秒的刻度为单位。                                                                                                                                                                                                       |


### <a name="332-example-mpeg-dash-manifest-mpd-signaling-of-scte-35-using-eventstream"></a>3.3.2 示例 MPEG 虚线清单 (MPD) 使用 EventStream 的 SCTE-35 的信号

~~~ xml
<!-- Example MPD using xml+bin style signaling per [SCTE-214-1] -->
  <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35_track_001_000" timescale="10000000">
        <Event presentationTime="15447165200227600" duration="300000000" id="1026">
            <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
        <Event presentationTime="15447166250227600" duration="300000000" id="1027">
           <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
    </EventStream>
~~~

> [!IMPORTANT]
> 请注意, presentationTime 是转换为相对于时间段开始时间的 [SCTE-35] 事件的显示时间, 而不是消息的到达时间。
> [MPEGDASH] 将Event@presentationTime定义为 "指定事件相对于时间段开始的表示时间。
> 显示时间的值 (以秒为单位) 是此属性的值与EventStream@timescale属性的值的相除。
> 如果不存在, 则表示时间的值为0。


### <a name="333-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.3 MPEG 破折号带内事件消息框信号

带内事件流要求 MPD 具有适应集级别的 InbandEventStream 元素。  此元素具有一个必需的 schemeIdUri 属性和可选的时间刻度属性，该属性还显示在事件消息块（“emsg”）中。  未在 MPD 中定义的方案标识符的事件消息框不**应**存在。

对于带内 [SCTE-35], 信号**必须**使用 schemeId = "urn: SCTE: scte35: 2013: bin"。
[SCTE-35] 带内消息的 "规范" 的定义在 [SCTE-214-3] sec 7.3.2 (SCTE 35 提示消息) 中定义。

以下详细信息概述了客户端应在与 [SCTE-214-3] 兼容时在 "emsg" 中预期的特定值:

| **字段名称**          | **字段类型**          | **必需？** | **说明**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | string                  | 必填      | 标识消息的方案。 方案将设为“Live Server Manifest”块中的方案属性的值。 该值**必须**为标识消息方案的 URN。 对于 [SCTE-35] 消息, 此项**必须**为 "urn: SCTE: scte35: 2013: bin" (符合 [SCTE-214-3]) |
| ReplTest1                   | string                  | 必填      | 方案所有者用于自定义消息的语义的附加字符串值。 为便于区分具有相同方案的多个事件流，值将设为 事件流的名称（平滑引入的 trackName 或 RTMP 引入的 AMF 消息）。                                                                  |
| Timescale               | 32 位无符号整数 | 必填      | “emsg”块中的时间和持续时间字段的时间刻度（以“滴答数/秒”为单位）。                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | 32 位无符号整数 | 必填      | 此片段中的事件呈现时间的媒体呈现时间增量和最早的呈现时间。 显示时间和持续时间**应**与类型为1或2的流访问点 (SAP) 一致, 如 [ISO-14496-12] 附录 I 中所定义。                                                                                            |
| event_duration          | 32 位无符号整数 | 必填      | 事件的持续时间或 0xFFFFFFFF，以指示未知的持续时间。                                                                                                                                                                                                                                                                                          |
| Id                      | 32 位无符号整数 | 必填      | 标识消息的此实例。 具有等效语义的消息应具有相同的值。 引入消息时，如果未指定 ID，Azure 媒体服务将生成唯一 ID。                                                                                                                                                    |
| Message_data            | Byte Array              | 必填      | 事件消息。 对于 [SCTE-35] 消息, 消息数据是与 [SCTE-214-3] 相容的二进制 splice_info_section () |

### <a name="334-dash-message-handling"></a>3.3.4 短划线消息处理

对于视频和音频轨迹，事件都是“emsg”块内的带内信号。  所有片段请求都会发出信号，因为 presentation_time_delta 为 15 秒或更少。 

启用滑动呈现窗口后，当事件消息的时间和持续时间之和小于清单中的媒体数据的时间时，将从 MPD 中删除事件消息。  换而言之，当事件消息引用的媒体时间滚出滑动呈现窗口时，将从清单中删除事件消息。

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4.SCTE-35 引入编码器供应商实现指南

以下准则是可能影响编码器供应商对此规范的实现的常见问题。  以下准则是根据真实的合作伙伴反馈收集的, 以便更轻松地为其他人实现此规范。 

[35 SCTE] 使用方案 **"urn: SCTE: scte35: 2013: bin"** 作为 [引入] 的 [SSTR] 引入的类型 **"scte35"** , 以二进制格式消息。 为便于转换基于 MPEG-2 传输流呈现时间戳 (PTS) 的 [SCTE-35] 计时，事件呈现时间（对于平滑引入为 fragment_absolute_time 字段，对于 RTMP 引入为时间字段）在 PTS (pts_time + pts_adjustment of the splice_time()) 和媒体时间线之间提供了一种映射。 映射是必需的，因为 33 位 PTS 值每隔大约 26.5 小时将汇总一次。

平滑流式处理摄取 [SSTR] 要求 Media Data Box ("mdat")**必须**包含在 [SCTE-35] 中定义的**splice_info_section ()** 。 

对于 RTMP 引入, AMF 消息的提示特性设置为 [SCTE-35] 中定义的 base64 编码的**splice_info_section ()** 。  

如果消息采用上述格式, 则会将其发送到上面定义的 HLS、平滑和短信客户端。  

使用 Azure 媒体服务平台测试实现时, 请先通过 "传递" LiveEvent 开始测试, 然后再转到对编码 LiveEvent 进行测试。

---

## <a name="change-history"></a>更改历史记录

| Date     | 更改                                                                            |
|----------|------------------------------------------------------------------------------------|
| 07/2/19  | 修订了用于 SCTE35 支持的 RTMP 摄取, 添加了 RTMP "onCuePoint" 进行 Elemental Live | 
| 08/22/19 | 更新以将 OnUserDataEvent 添加到自定义元数据的 RTMP                         |

## <a name="next-steps"></a>后续步骤
查看媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
