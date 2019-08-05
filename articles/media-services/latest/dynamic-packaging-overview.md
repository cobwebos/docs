---
title: Azure 媒体服务动态打包概述 | Microsoft 文档
description: 本文提供 Azure 媒体服务中的动态打包概述。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 5979e34e7c186a0484c8db2d432a3c57a5ed1d15
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679157"
---
# <a name="dynamic-packaging"></a>动态打包

Microsoft Azure 媒体服务可用于向多种客户端技术（例如，iOS 和 XBOX）传送多种媒体源文件格式、媒体流格式和内容保护格式。 这些客户端可识别不同的协议，例如，iOS 需要 HTTP Live Streaming (HLS) 格式，Xbox 需要平滑流式处理。 如果有一组自适应比特率（多码率）MP4（ISO 基媒体 14496-12）文件或一组自适应比特率平滑流式处理文件要提供给了解 HLS、MPEG DASH 或平滑流式处理的客户端，则可利用动态打包  。 打包与视频分辨率无关，支持 SD/HD/UHD-4K。

在媒体服务中，[流式处理终结点](streaming-endpoint-concept.md)表示动态（即时）打包和源服务，该服务可使用一个常见流式处理媒体协议（HLS 或 DASH）直接将你的实时和按需内容发送到客户端播放器应用程序。 动态打包是所有**流式处理终结点**（标准或高级）的标准功能。 

若要利用动态打包，需要有一个[资产](assets-concept.md)，其中包含一组自适应比特率 MP4 文件和流式处理配置文件（.ism、.ismc、.mpi 等）。 获取这些文件的一种方式是使用媒体服务对夹层（源）文件进行编码。 要使编码资产中的视频可供客户端播放，必须创建[流式处理定位符](streaming-locators-concept.md)，然后生成流式处理 URL。 然后，根据流式处理客户端清单中指定的格式（HLS、DASH 或平滑），使用你选择的协议接收流。

因此，只需以单一存储格式存储文件并为其付费，然后媒体服务服务就会基于客户端的请求构建并提供相应响应。 

在媒体服务中，无论是进行实时流式处理还是按需流式处理，都使用动态打包。 

> [!NOTE]
> 目前，无法使用 Azure 门户来管理 v3 资源。 请使用 [REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref) 或受支持的 [SDK](media-services-apis-overview.md#sdks) 之一。

## <a name="on-demand-streaming-workflow"></a>按需流式处理工作流

以下是使用动态打包进行媒体服务按需流式处理的常用工作流：

1. 上传输入或源文件（称为夹层文件）  。 示例包括 MP4、MOV 或 MXF 文件。 
1. 将夹层文件编码为 H.264 MP4 自适应比特率集。 
1. 发布包含自适应比特率 MP4 集的输出资产。 通过创建流式处理定位符进行发布。
1. 生成针对不同格式（HLS、MPEG-DASH 和平滑流式处理）的 URL。 流式处理终结点负责提供正确的清单并请求不同的格式。

此关系图显示使用动态打包进行按需流式处理的工作流：

![使用动态打包进行按需流式处理的工作流关系图](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

### <a name="encoding-to-adaptive-bitrate-mp4s"></a>编码为自适应比特率 MP4

以下文章介绍[如何使用媒体服务对视频进行编码](encoding-concept.md)的示例：

* [使用内置预设从 HTTPS URL 进行编码](job-input-from-http-how-to.md)
* [使用内置预设对本地文件进行编码](job-input-from-local-file-how-to.md)
* [构建自定义预设，以确定特定方案或设备要求](customize-encoder-presets-how-to.md)

请参阅 Media Encoder Standard [格式和编解码器](media-encoder-standard-formats.md)的列表。

## <a name="live-streaming-workflow"></a>实时传送视频流工作流

实时事件可以是下述两种类型之一：直通或实时编码。 

以下是使用动态打包进行实时传送视频流的常用工作流：

1. 创建[实时事件](live-events-outputs-concept.md)。
1. 获取引入 URL 并配置本地编码器以使用 URL 发送贡献源。
1. 获取预览 URL 并使用它验证是否接收到来自编码器的输入。
1. 创建新资产。
1. 创建实时输出并使用创建的资产名称。<br />实时输出会将流存档到资产中。
1. 使用内置的流式处理策略类型创建流式处理定位符。<br />如果想要加密内容，请查看[内容保护概述](content-protection-overview.md)。
1. 列出流式处理定位符的路径，以获取要使用的 URL。
1. 获取要从中进行流式传输的流式处理终结点的主机名。
1. 生成针对不同格式（HLS、MPEG-DASH 和平滑流式处理）的 URL。 流式处理终结点负责提供正确的清单并请求不同的格式。

此关系图显示使用动态打包进行实时传送视频流的工作流：

![使用动态打包进行直通编码的工作流关系图](./media/live-streaming/pass-through.svg)

有关在媒体服务 v3 中实时传送视频流的信息，请参阅[实时传送视频流概述](live-streaming-overview.md)。

## <a name="delivery-protocols"></a>传递协议

可以将这些传递协议用于媒体服务动态打包中的内容：

|协议|示例|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|平滑流| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="delivery-codecs-support"></a>交付编解码器支持 

### <a name="video-codecs"></a>视频编解码器

动态打包支持以下视频编解码器：
* MP4 文件，其中包含使用 [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC)（MPEG-4 AVC 或 AVC1）或 [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding)（HEVC、hev1 或 hvc1）编码的视频。

### <a name="audio-codecs"></a>音频编解码器

动态打包支持下述音频协议：

* MP4 文件
* 多个音轨

动态打包不支持包含 [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) 音频（它是旧编解码器）的文件。

#### <a name="mp4-files"></a>MP4 文件

动态打包支持 MP4 文件，MP4 文件中包含使用以下协议编码的音频： 

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)（AAC-LC、HE-AAC v1 或 HE-AAC v2）
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)（增强型 AC-3 或 E-AC3）
* Dolby Atmos<br />
   流式处理 Dolby Atmos 内容支持 MPEG-DASH 协议等标准，包括采用公共流式处理格式 (CSF) 或公共媒体应用程序格式 (CMAF) 分段的 MP4，以及通过具有 CMAF 的 HTTP Live Streaming (HLS)。

* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   DASH-CSF、DASH-CMAF、HLS-M2TS 和 HLS-CMAF 打包格式支持的 DTS 编解码器包括：  

    * DTS Digital Surround (dtsc)
    * DTS-HD High Resolution 和 DTS-HD Master Audio  (dtsh)
    * DTS Express (dtse)
    * DTS-HD Lossless (no core) (dtsl)

#### <a name="multiple-audio-tracks"></a>多个音轨

动态打包支持 HLS 输出（版本 4 或更高版本）的多音轨，用于流式传输具有使用多个编解码器和语言的多音轨的资产。

## <a name="manifests"></a>清单 
 
在媒体服务动态打包中，HLS、MPEG-DASH 和平滑流式处理的流式处理客户端清单是基于 URL 中的格式选择器动态生成的。 有关详细信息，请参阅[传递协议](#delivery-protocols)。 

清单文件包含流元数据，例如轨迹类型（音频、视频或文本）、轨迹名称、开始和结束时间、比特率（质量）、轨迹语言、演播窗口（持续时间固定的滑动窗口）和视频编解码器 (FourCC)。 此文件还会通过提供有关下一个可播放视频片段及其位置的信息，来指示播放器检索下一个片段。 片段（或段）实际上是视频内容的“区块”。

### <a name="examples"></a>示例

#### <a name="hls"></a>HLS

以下示例是 HLS 清单文件，也称为 HLS 主播放列表： 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

#### <a name="mpeg-dash"></a>MPEG-DASH

以下示例是 MPEG-DASH 清单文件，也称为 MPEG-DASH 媒体演示说明 (MPD)：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
#### <a name="smooth-streaming"></a>平滑流

下面是平滑流式处理清单文件的示例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

### <a name="naming-of-tracks-in-the-manifest"></a>命名清单中的曲目

如果在 .ism 文件中指定了音轨名称，则媒体服务会在 `AdaptationSet` 中添加 `Label` 元素，以指定特定音轨的纹理信息。输出 DASH 清单的示例：

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

播放机可以使用 `Label` 元素在其 UI 上显示。

### <a name="signaling-audio-description-tracks"></a>发出音频描述轨道的信号

客户可以将音轨批注为清单中的音频描述。 为此，他们会在 .ism 文件中添加“accessibility”和“role”参数。 如果音轨具有值为“description”的参数“accessibility”和值为“alternate”的参数“role”，则媒体服务将识别音频描述。 如果媒体服务检测到 .ism 文件中的音频描述，则将音频描述信息作为 `Accessibility="description"` 和 `Role="alternate"` 属性传递给客户端清单，并将其传递到 `StreamIndex` 元素中。

如果在 .ism 文件中设置了“accessibility”=“description”和“role”=“alternate”的组合，则 DASH 清单和 Smooth 清单会携带“accessibility”和“role”参数中设置的值。 客户有责任将这两个值设置正确，并将音轨标记为音频描述。 根据 DASH 规范，“accessibility”=“description”和“role”=“alternate”一起设置意味着音轨是音频描述。

对于 HLS v7 及更高版本 (`format=m3u8-cmaf`)，仅当在 .ism 文件中设置了“accessibility”=“description”和“role”=“alternate”的组合时，其播放列表才会带有 `CHARACTERISTICS="public.accessibility.describes-video"`。 

## <a name="dynamic-manifest"></a>动态清单

若要控制发送到播放器的曲目数目、格式、比特率和呈现时间范围，可以将动态筛选与媒体服务动态包生成工具配合使用。 有关详细信息，请参阅[预筛选清单与动态包生成工具配合使用](filters-dynamic-manifest-overview.md)。

## <a name="dynamic-encryption"></a>动态加密

可以使用动态加密借助 AES-128 或三种主要数字版权管理 (DRM) 系统中的任何一种对实时或按需内容进行动态加密  ：内容。 媒体服务还提供用于向已授权客户端传送 AES 密钥和 DRM 许可证的服务。 有关详细信息，请参阅[动态加密](content-protection-overview.md)。

## <a name="more-information"></a>详细信息

查看 [Azure 媒体服务社区](media-services-community.md)，了解提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

了解如何[对视频进行上传、编码和流式处理](stream-files-tutorial-with-api.md)。

