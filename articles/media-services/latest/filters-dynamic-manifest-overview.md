---
title: 使用动态打包器筛选清单
titleSuffix: Azure Media Services
description: 了解如何使用动态打包器创建筛选器，以筛选并有选择性地流式传输清单。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: acb30c1659c4c29e0af83da5594bdd9a7e3465d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89299025"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>使用动态打包器筛选清单

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

将自适应比特率流内容传送到设备时，有时需要发布多个版本的清单来适应特定的设备功能或可用网络带宽。 使用[动态打包器](dynamic-packaging-overview.md)可以指定筛选器，用于即时筛选出特定的编解码器、分辨率、比特率和音频轨迹组合。 这种筛选消除了创建多个副本的需要。 只需使用一组根据目标设备（iOS、Android、SmartTV 或浏览器）和网络功能（高带宽、移动或低带宽方案）配置的一组特定筛选器发布新的 URL。 在这种情况下，客户端可以通过查询字符串处理内容流（通过指定可用的[资产筛选器或帐户筛选器](filters-concept.md)），并使用筛选器来流式传输流的特定部分。

某些传送方案要求确保客户无法访问特定的轨迹。 例如，你可能不想要将包含 HD 轨迹的清单发布到特定的订户层。 或者，你可能想要删除特定的自适应比特率 (ABR) 轨迹，以降低传送到不会受益于更多轨迹的特定设备的成本。 在这种情况下，可以在创建时将预先创建的筛选器列表关联到[流定位符](streaming-locators-concept.md)。 于是，客户端将无法处理内容的流式传输方式，因为传输方式由**流定位符**定义。

可以通过指定[针对流定位符的筛选器](filters-concept.md#associating-filters-with-streaming-locator)，以及客户端在 URL 中指定的其他设备特定筛选器，来组合筛选。 这种组合可以有效地限制其他轨迹，例如元数据或事件流、音频语言，或描述性的音频轨迹。

针对流指定不同筛选器的功能提供了一种强大的**动态清单**处理解决方案用于定位目标设备的多用例方案。 本主题介绍与**动态清单**相关的概念，并提供可以使用此功能的示例方案。

> [!NOTE]
> 动态清单不会更改资产和该资产的默认清单。

## <a name="overview-of-manifests"></a>清单概述

Azure 媒体服务支持 HLS、MPEG DASH 和平滑流式处理协议。 作为[动态打包](dynamic-packaging-overview.md)的一部分，流式处理客户端清单（HLS Master 播放列表、DASH 媒体演播描述 [MPD] 和平滑流式处理）将会根据 URL 中的格式选择器动态生成。 有关详细信息，请参阅[常用按需工作流](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery)中的传送协议。

### <a name="get-and-examine-manifest-files"></a>获取并检查清单文件

指定筛选器轨迹属性条件的列表，应该根据该列表将流（直播或点播视频 [VOD]）的轨迹包含到动态创建的清单中。 若要获取并检查轨迹的属性，必须先加载平滑流式处理清单。

[使用 .NET 上传、编码和流式传输文件](stream-files-tutorial-with-api.md#get-streaming-urls)教程介绍了如何使用 .NET 创建流式处理 URL。 如果运行应用，某个 URL 将指向平滑流式处理清单：`https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`。<br/> 请复制该 URL 并将其粘贴到浏览器的地址栏中。 随后将下载文件。 可以在任何文本编辑器中将其打开。

有关 REST 示例，请参阅[使用 REST 上传、编码和流式传输文件](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)。

### <a name="monitor-the-bitrate-of-a-video-stream"></a>监视视频流的比特率

可以使用 [Azure Media Player 演示页](https://aka.ms/azuremediaplayer)监视视频流的比特率。 演示页在“诊断”选项卡中显示诊断信息： 

![Azure Media Player 诊断][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>示例:在查询字符串中包含筛选器的 URL

可将筛选器应用到 ABR 流式处理协议：HLS、MPEG-DASH 和平滑流式处理。 下表显示了一些包含筛选器的 URL 示例：

|协议|示例|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|平滑流|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>再现内容筛选

可选择将资产编码成多个编码配置文件（H.264 Baseline、H.264 High、AACL、AACH、Dolby Digital Plus），以及多个优质比特率。 不过，并非所有的客户端设备都支持所有的资产配置文件和比特率。 例如，早期的 Android 设备只支持 H.264 Baseline+AACL。 将较高的比特率发送到不能利用这些优势的设备会浪费带宽及设备计算资源。 此类设备必须解码所有给定信息，目的仅仅是为了缩小信息以便能够显示。

借助动态清单，可以创建设备配置文件（例如移动配置文件、控制台或 HD/SD），并包含你想要纳入配置文件中的曲目与质量。 这称为“再现内容筛选”。 下图显示了一个示例。

![使用动态清单的再现内容筛选示例][renditions2]

以下示例使用编码器将夹层资产编码成七个 ISO MP4 视频再现内容（从 180p 到 1080p）。 编码的资产可[动态打包](dynamic-packaging-overview.md)成以下任一流协议：HLS、MPEG DASH 和平滑流式处理。

下图的顶部显示了不包含筛选器的资产的 HLS 清单。 （它包含全部七个再现内容。）左下角显示了名为“ott”的筛选器已应用到 HLS 清单。 “ott”筛选器指定要删除所有低于 1 Mbps 的比特率，因此最差的两个质量级别已从响应中剥除。 右下角显示了名为“mobile”的筛选器已应用到 HLS 清单。 “mobile”筛选器指定要删除分辨率大于 720p 的再现内容，因此已剥除两个 1080p 再现内容。

![使用动态清单的再现内容筛选][renditions1]

## <a name="removing-language-tracks"></a>删除语言音轨
你的资产可能包含多种音频语言，例如英语、西班牙语、法语等。 通常，播放器 SDK 管理器会按默认选择音频轨迹，并根据用户的选择来选择可用音频轨迹。

开发此类播放器 SDK 相当有挑战性，因为各个设备特定的播放器框架之间需要不同的实现。 此外，播放器 API 在某些平台上受到限制，且不包含音频选择功能，因此用户无法选择或更改默认的音频轨迹。使用资产筛选器，可以通过创建只包含所需音频语言的筛选器来控制行为。

![使用动态清单的语言轨迹筛选][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>修剪资产开头

大多数实时流事件中，操作员必须在发生实际事件之前进行某些测试。 例如，他们可以在事件开始之前提供如下盖板：“节目即将开始。”

如果节目正在存档，测试和静态数据也会一并存档并包含在演播中。 但是，此信息不应向客户端显示。 借助动态清单，可以创建开始时间筛选器，并从清单中删除不需要的数据。

![使用动态清单修剪资产的开头][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>基于实时存档创建子剪辑（视图）

许多直播活动长期运行，直播存档可能包含多个活动。 直播活动结束后，广播者可能需要将实时存档分解成符合逻辑的节目启动和停止序列。

您可以单独发布这些虚拟程序，而无需处理实时存档，也不会创建单独的资产 (这不会获得 Cdn) 中现有缓存片段的优势。 此类虚拟节目的示例包括橄榄球或篮球比赛中的节、棒球比赛中的局，或者任何体育节目中的单项赛事。

借助动态清单，可以使用开始/结束时间创建筛选器，并基于实时存档创建虚拟视图。

![使用动态清单的子剪辑筛选器][subclip_filter]

下面是筛选的资产：

![使用动态清单筛选的资产][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>调整演播窗口 (DVR)

目前，Azure 媒体服务提供持续时间可设为 1 分钟到 25 小时的循环存档。 清单筛选可以基于存档创建循环 DVR 窗口存档，而不会删除媒体。 许多情况下，广播者需要提供受限制的 DVR 窗口，此窗口可随着实时边缘移动，并同时保留更大的存档窗口。 广播者可能想要使用超出 DVR 窗口的数据来突出显示剪辑，或者想要为不同的设备提供不同的 DVR 窗口。 例如，大多数移动设备不处理大的 DVR 窗口（可以让移动设备有 2 分钟的 DVR 窗口，桌面客户端有 1 小时的 DVR 窗口）。

![包含动态清单的 DVR 窗口][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>调整实时补偿（实时位置）

清单筛选可用于删除实时节目的实时边缘几秒钟的时间。 通过筛选，广播者便可以在观众收到流之前（倒退 30 秒）先观赏预览发布点的演播，并创建广告插入点。 接下来，广播者可将这些广告及时推送到他们的客户端框架，以便能够接收与处理信息，并借机播放广告。

除了广告支持外，还可使用实时补偿设置来调整观看者位置，以便在客户端偏移并命中实时边缘时，仍然可以从服务器获取片段。 这样，客户端就不会收到 HTTP 404 或 412 错误。

![使用动态清单按实时后退时间筛选][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>将多个规则合并成单个筛选器

可以将多个筛选规则合并成单个筛选器。 例如，可以定义一个“范围规则”，将静态内容从实时存档中删除，并筛选出可用的比特率。 应用多项筛选规则时，最终结果是所有规则的交集。

![使用动态清单的多个筛选规则][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>组合多个筛选器（筛选器组合）

也可以在单个 URL 中组合多个筛选器。 以下方案演示了可能需要组合多个筛选器的原因：

1. 需要筛选视频质量（目的是限制视频质量）以供 Android 或 iPad 之类的移动设备使用。 若要删除其质量不符合需要的视频，可创建一个适合设备配置文件的帐户筛选器。 帐户筛选器可用于同一媒体服务帐户下的所有资产，这些资产并没有更多的其他联系。
1. 还可以修改资产的开始时间和结束时间。 若要执行修剪，需要创建一个资产筛选器并设置开始/结束时间。
1. 需要组合这两个筛选器。 如果不组合，则需要将质量筛选添加到修剪筛选器，这会导致筛选器的使用更加困难。

若要组合筛选器，请在清单/播放列表 URL 中设置分号分隔格式的筛选器名称。 假设你有一个名为 *MyMobileDevice* 的筛选器，用于筛选质量，另外还有一个名为 *MyStartTime* 的筛选器，用于设置具体的开始时间。 最多可以组合三个筛选器。

有关详细信息，请参阅 [此博客文章](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)。

## <a name="considerations-and-limitations"></a>注意事项和限制

- 不应设置 VOD 筛选器的 **forceEndTimestamp**、**presentationWindowDuration** 和 **liveBackoffDuration** 值。 它们仅用于动态筛选方案。
- 动态清单在 GOP 边界（关键帧）内运行，因此修剪后具有精确的 GOP。
- 可对帐户和资产筛选器使用相同的筛选器名称。 资产筛选器的优先级更高，会替代帐户筛选器。
- 如果更新筛选器，则流式处理终结点需要最多 2 分钟来刷新规则。 如果你使用筛选器来提供内容 (并将内容缓存在代理和 CDN 缓存) 中，则更新这些筛选器可能会导致播放机失败。 我们建议在更新筛选器之后清除缓存。 如果无法做到这一点，请考虑使用其他筛选器。
- 客户需要手动下载清单，并分析确切的开始时间戳和时间刻度。

    - 若要确定资产中轨迹的属性，请[获取并检查清单文件](#get-and-examine-manifest-files)。
    - 设置资产筛选器时间戳属性的公式是： <br/>startTimestamp = &lt;清单中的开始时间&gt; +  &lt;预期筛选器开始时间（秒）&gt;* 时间刻度

## <a name="next-steps"></a>后续步骤

以下文章介绍了如何以编程方式创建筛选器：  

- [使用 REST API 创建筛选器](filters-dynamic-manifest-rest-howto.md)
- [使用 .NET 创建筛选器](filters-dynamic-manifest-dotnet-howto.md)
- [使用 CLI 创建筛选器](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
