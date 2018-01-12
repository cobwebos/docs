---
title: "筛选器和动态清单 | Microsoft Docs"
description: "本主题介绍如何创建筛选器，以便客户端能够使用它们来流式传输流的特定部分。 媒体服务将创建动态清单来存档此选择性的流。"
services: media-services
documentationcenter: 
author: cenkdin
manager: cfowler
editor: 
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 12/07/2017
ms.author: cenkd;juliako
ms.openlocfilehash: 5512be8ce5b9cf28bceb3468ec6032c0778156f4
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="filters-and-dynamic-manifests"></a>筛选器和动态清单
从 2.17 版开始，可使用媒体服务为资产定义筛选器。 这些筛选器是服务器端规则，可让客户选择运行如下操作：只播放一段视频（而非播放完整视频），或只指定客户设备可以处理的一部分音频和视频再现内容（而非与该资产相关的所有再现内容）。 通过按客户请求创建的**动态清单**可以实现对资产进行这种筛选，并基于指定的筛选器流式传输视频。

本主题讨论一些常见方案，其中使用筛选器对于客户和到主题的链接非常有利，这些主题演示如何以编程方式创建筛选器（目前，仅可以使用 REST API 创建筛选器）。

## <a name="overview"></a>概述
在将内容传送到客户（流式传输实时事件或视频点播）时，目标就是：将优质视频传递到处于不同网络条件下的各种设备。 若要实现此目标，请执行以下操作：

* 将流编码成多比特率（[自适应比特率](http://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)）视频流（这会负责处理质量和网络条件），并 
* 使用媒体服务[动态打包](media-services-dynamic-packaging-overview.md)将流动态地重新打包成不同的协议（这会负责不同设备上的流式处理）。 媒体服务支持传送以下自适应比特率流式处理技术：HTTP Live Streaming (HLS)、平滑流式处理和 MPEG DASH。 

### <a name="manifest-files"></a>清单文件
将资产编码为以自适应比特率流式处理时，会创建一个**清单**（播放列表）文件（此文件基于文本或 XML）。 **清单**文件包含流元数据，例如：轨迹类型（音频、视频或文本）、轨迹名称、开始和结束时间、比特率（质量）、轨迹语言、演播窗口（持续时间固定的滑动窗口）和视频编解码器 (FourCC)。 此文件还会通过提供有关下一个可播放视频片段及其位置的信息，来指示播放器检索下一个片段。 片段（或段）实际上是视频内容的“区块”。

下面是清单文件的一个示例： 

    <?xml version="1.0" encoding="UTF-8"?>    
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">

    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />

    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>

    </SmoothStreamingMedia>

### <a name="dynamic-manifests"></a>动态清单
在客户端需要比默认资产的清单文件中描述的内容更加具有灵活性时，这里具有[方案](media-services-dynamic-manifest-overview.md#scenarios)。 例如：

* 特定于设备：只传送内容播放设备所支持的指定再现内容和/或指定的语言轨迹（“再现内容筛选”）。 
* 缩小清单以显示实时事件的子剪辑（“子剪辑筛选”）。
* 修剪视频开头（“修剪视频”）。
* 调整演播窗口，以便在播放器中提供长度有限的 DVR 窗口（“调整演播窗口”）。

为实现这种灵活性，媒体服务会根据预定义的[筛选器](media-services-dynamic-manifest-overview.md#filters)提供**动态清单**。  在定义筛选器后，客户端会使用筛选器来流式传输视频的特定再现内容或子剪辑。 客户端会在流 URL 中指定筛选器。 筛选器可应用到[动态打包](media-services-dynamic-packaging-overview.md)支持的自适应比特率流式处理协议：HLS、MPEG-DASH 和平滑流式处理。 例如：

包含筛选器的 MPEG DASH URL

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

包含筛选器的平滑流 URL

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


有关如何传送内容和构建流 URL 的详细信息，请参阅[传送内容概述](media-services-deliver-content-overview.md)。

> [!NOTE]
> 请注意，动态清单不会更改资产和该资产的默认清单。 客户端可以选择请求包含或不包含筛选器的流。 
> 
> 

### <a id="filters"></a>筛选器
有两种类型的资产筛选器： 

* 全局筛选器（可以应用到 Azure 媒体服务帐户中所有的资产，拥有帐户的生存期）和 
* 本地筛选器（在创建后只能应用到与筛选器关联的资产，拥有资产的生存期）。 

全局和本地筛选器类型具有完全相同的属性。 两者的主要差异在于它们更适合哪些方案。 全局筛选器通常适用于设备配置文件（再现内容筛选），而本地筛选器可用于修剪特定的资产。

## <a id="scenarios"></a>常见方案
如前所述，在将内容传送到客户（流式传输实时事件或视频点播）时，目标就是：将优质视频传递到处于不同网络条件下的各种设备。 此外，可能在筛选资产与使用**动态清单**方面具有其他的要求。 以下部分提供了不同筛选方案的简要概述。

* 仅指定某些设备可以处理的音频和视频再现内容子集（而不是与该资产关联的所有再现内容）。 
* 仅播放视频的某个部分（而不是整个视频）。
* 调整 DVR 演播窗口。

## <a name="rendition-filtering"></a>再现内容筛选
可以选择将资产编码成多个编码配置文件（H.264 Baseline、H.264 High、AACL、AACH、Dolby Digital Plus），以及多个优质比特率。 不过，并非所有的客户端设备都支持资产的所有配置文件和比特率。 例如，早期的 Android 设备只支持 H.264 Baseline+AACL。 将较高的比特率发送到不能利用这些优势的设备会浪费带宽及设备计算资源。 此类设备必须解码所有给定信息，而目的仅仅是为了缩小信号以便能够显示。

有了动态清单，可以创建设备配置文件（例如移动配置文件、控制台、HD/SD 等），并包含你想要纳入配置文件中的轨迹与质量。

![再现内容筛选示例][renditions2]

以下示例使用编码器将夹层资产编码成七个 ISO MP4 视频再现内容（从 180p 到 1080p）。 编码的资产可动态打包成以下任一流协议：HLS、平滑流和 MPEG DASH。  图表顶部显示了不包含筛选器的资产的 HLS 清单（包含全部七个再现内容）。  左下角显示名为“ott”的筛选器已应用到 HLS 清单。 “ott”筛选器指定要删除所有不低于 1Mbps 的比特率，因此将最差的两个质量级别从响应中剥除。  右下角显示名为“mobile”的筛选器已应用到 HLS 清单。 “mobile”筛选器指定要删除分辨率大于 720p 的再现内容，因此将剥除两个 1080p 再现内容。

![再现内容筛选][renditions1]

## <a name="removing-language-tracks"></a>删除语言轨迹
资产可能包含多种音频语言，例如英语、西班牙语、法语等。通常，播放器 SDK 管理器会按默认选择音频轨迹，并根据用户的选择来选择可用音频轨迹。 开发此类播放器 SDK 相当有挑战性，因为各个设备特定的播放器框架之间需要不同的实现。 此外，播放器 API 在某些平台上受到限制，且不包含音频选择功能，因此用户无法选择或更改默认的音频轨迹。使用资产筛选器，可以通过创建只包含所需音频语言的筛选器来控制行为。

![语言轨迹筛选][language_filter]

## <a name="trimming-start-of-an-asset"></a>修剪资产开头
在大多数实时流事件中，操作员必须在发生实际事件之前进行某些测试。 例如，他们可以在事件开始之前包含如下静态内容：“节目即将开始”。 如果节目正在存档，则测试和静态数据也会一并存档并包含在演播中。 但是，此信息不应向客户端显示。 通过动态清单，可以创建开始时间筛选器，并从清单中删除不需要的数据。

![剪裁开始][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>基于实时存档创建子剪辑（视图）
许多实时事件的运行时间较长，并且实时存档可能包含多个事件。 实时事件结束后，广播者可能想要将实时存档分解成有序的节目启动和停止序列。 接下来，分别发布这些虚拟节目，但不后续处理实时存档，也不创建独立的资产（这会无法利用 CDN 中现有缓存片段的优势）。 此类虚拟节目的示例包括足球或篮球比赛中的上下半场（第几节）、棒球比赛中的局，或者任何体育项目的单项赛事。

借助动态清单，可以使用开始/结束时间创建筛选器，并基于实时存档创建虚拟视图。 

![子剪辑筛选器][subclip_filter]

筛选的资产：

![滑雪][skiing]

## <a name="adjusting-presentation-window-dvr"></a>调整演播窗口（DVR）
目前，Azure 媒体服务提供持续时间可设为 5 分钟到 25 小时的循环存档。 清单筛选可以基于存档创建循环 DVR 窗口存档，而不会删除媒体。 在许多情况下，广播者想要提供受限制的 DVR 窗口，此窗口可随着实时边缘移动，并同时保留更大的存档窗口。 广播者可能想要使用超出 DVR 窗口的数据来突出显示剪辑，或者想要为不同的设备提供不同的 DVR 窗口。 例如，大多数移动设备不处理大的 DVR 窗口（可以让移动设备有 2 分钟的 DVR 窗口，桌面客户端有 1 小时的 DVR 窗口）。

![DVR 窗口][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>调整实时补偿（实时位置）
清单筛选可用于删除实时节目的实时边缘几秒钟的时间。 筛选操作使广播者可以在观众收到流之前（倒退 30 秒）先观赏预览发布点的演播，并创建广告插入点。 广播者接着可将这些广告及时推送到其客户端框架，以便能够接收与处理信息，然后借机播放广告。

除了广告支持外，实时补偿设置可用于调整观众位置，使客户端偏移并到达实时边缘时，仍然可以从服务器获取片段，而不会收到 HTTP 404 或 412 错误。

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>将多个规则合并成单个筛选器
可以将多个筛选规则合并成单个筛选器。 例如，可以定义一个“范围规则”，以便将静态内容从实时存档中删除，并筛选出可用的比特率。 应用多个筛选规则时，最终结果会是所有规则的交集。

![多规则][multiple-rules]

## <a name="create-filters-programmatically"></a>以编程方式创建筛选器
以下文章讨论与筛选器相关的媒体服务实体。 本文还演示如何以编程方式创建筛选器。  

[使用 REST API 创建筛选器](media-services-rest-dynamic-manifest.md)。

## <a name="combining-multiple-filters-filter-composition"></a>组合多个筛选器（筛选器组合）
也可以在一个 URL 中组合多个筛选器。 

以下方案演示为何需要组合多个筛选器：

1. 需要筛选视频质量（目的是限制视频质量）以供 Android 或 iPAD 之类的移动设备使用。 要删除其质量不符合需要的视频，可创建一个适合设备配置文件的全局筛选器。 如本文之前所述，全局筛选器可以用于在同一媒体服务帐户下的所有资产，这些资产并没有更多的其他联系。 
2. 还可以修改资产的开始时间和结束时间。 为此，可以创建一个本地筛选器并设置开始/结束时间。 
3. 希望能够将这些筛选器组合起来（如果不组合的话，则需要将质量筛选添加到进行修改的筛选器上，这会导致筛选器的使用更加困难）。

为了组合筛选器，需要在清单/播放列表 URL 中设置筛选器名称，用分号对名称进行分隔。 假设有一个名为 *MyMobileDevice* 的筛选器用于筛选质量，另外还有一个名为 *MyStartTime* 的筛选器用于设置具体的开始时间。 可以将它们组合成下面这样：

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

最多可以组合 3 个筛选器。 

有关详细信息，请参阅[此](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)博客。

## <a name="know-issues-and-limitations"></a>已知问题和限制
* 动态清单在 GOP 边界（主键帧）内运行，因此修剪后具有精确的 GOP。 
* 可以对本地和全局筛选器使用相同的筛选器名称。 请注意，本地筛选器的优先顺序更高，会取代全局筛选器。
* 如果更新筛选器，则流式处理终结点需要 2 分钟的时间来刷新规则。 如果内容是通过使用某些筛选器提供的（并在代理和 CDN 缓存中缓存），则更新这些筛选器会导致播放器失败。 建议在更新筛选器之后清除缓存。 如果此选项不可用，请考虑使用其他筛选器。

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>另请参阅
[将内容传送到客户概述](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
