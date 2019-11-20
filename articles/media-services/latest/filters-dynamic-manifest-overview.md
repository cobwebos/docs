---
title: 使用动态包装器筛选清单
titleSuffix: Azure Media Services
description: 了解如何使用动态包装器创建筛选器，以筛选和选择性地流式传输清单。
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
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: cd955f97a2f26543f799d95b7dc0b1de235333c5
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186216"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>使用动态包装器筛选清单

将自适应比特率流式处理内容传递到设备时，有时需要发布多个版本的清单以定位特定设备功能或可用网络带宽。 [动态包装](dynamic-packaging-overview.md)器允许你指定筛选器，这些筛选器可以即时筛选出特定的编解码器、分辨率、比特率和音频跟踪组合。 此筛选不再需要创建多个副本。 只需使用一组根据目标设备（iOS、Android、SmartTV 或浏览器）和网络功能（高带宽、移动或低带宽方案）配置的一组特定筛选器发布新的 URL。 在这种情况下，客户端可以通过查询字符串处理内容流（通过指定可用的[资产筛选器或帐户筛选器](filters-concept.md)），并使用筛选器来流式传输流的特定部分。

某些交付方案要求您确保客户无法访问特定的曲目。 例如，你可能不想将包含 HD 轨迹的清单发布到特定订阅者层。 或者，您可能想要删除特定的自适应比特率（ABR）磁道，以降低交付到特定设备的成本，而不会从其他踪迹中获益。 在这种情况下，可以在创建时将预先创建的筛选器列表与[流式处理定位符](streaming-locators-concept.md)相关联。 然后，客户端无法处理内容的流式处理方式，因为它是由**流式处理定位符**定义的。

可以通过[在流式处理定位符上指定筛选器](filters-concept.md#associating-filters-with-streaming-locator)并在 URL 中指定客户端指定的其他特定于设备的筛选器来合并筛选。 这种组合有助于限制其他跟踪，如元数据或事件流、音频语言或描述性音频轨迹。

此功能可以在流中指定不同的筛选器，它提供了一个功能强大的**动态清单**操作解决方案，可针对目标设备使用多个用例方案。 本主题介绍与**动态清单**相关的概念，并提供可使用此功能的方案示例。

> [!NOTE]
> 动态清单不会更改资产和该资产的默认清单。

## <a name="overview-of-manifests"></a>清单概述

Azure 媒体服务支持 HLS、MPEG DASH 和平滑流式处理协议。 作为[动态打包](dynamic-packaging-overview.md)的一部分，流式处理客户端清单（HLS Master 播放列表、DASH 媒体演播描述 [MPD] 和平滑流式处理）将会根据 URL 中的格式选择器动态生成。 有关详细信息，请参阅[常见按需工作流](dynamic-packaging-overview.md#delivery-protocols)中的传递协议。

### <a name="get-and-examine-manifest-files"></a>获取并检查清单文件

你可以根据流的哪些音轨（实时或视频点播 [VOD]）指定筛选器跟踪属性条件的列表，该列表应包含在动态创建的清单中。 若要获取并检查轨迹的属性，必须先加载平滑流式处理清单。

[使用 .NET 上传、编码和流式传输文件](stream-files-tutorial-with-api.md#get-streaming-urls)教程介绍了如何使用 .NET 创建流式处理 URL。 如果运行应用，某个 URL 将指向平滑流式处理清单：`https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`。<br/> 请复制该 URL 并将其粘贴到浏览器的地址栏中。 随后将下载文件。 可以在任何文本编辑器中将其打开。

有关 REST 示例，请参阅[使用 REST 上传、编码和流式传输文件](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)。

### <a name="monitor-the-bitrate-of-a-video-stream"></a>监视视频流的比特率

可以使用 [Azure Media Player 演示页](https://aka.ms/azuremediaplayer)监视视频流的比特率。 演示页在“诊断”选项卡中显示诊断信息：

![Azure Media Player 诊断][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>示例：查询字符串中包含筛选器的 Url

可以将筛选器应用于 ABR 流式处理协议： HLS、MPEG-短线和平滑流式处理。 下表显示了一些包含筛选器的 URL 示例：

|协议|示例|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|平滑流|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>再现内容筛选

可选择将资产编码成多个编码配置文件（H.264 Baseline、H.264 High、AACL、AACH、Dolby Digital Plus），以及多个优质比特率。 不过，并非所有的客户端设备都支持资产的所有配置文件和比特率。 例如，早期的 Android 设备只支持 H.264 Baseline+AACL。 将较高的比特率发送到不能利用这些优势的设备会浪费带宽及设备计算资源。 此类设备必须解码所有给定信息，目的仅仅是为了缩小信息以便能够显示。

借助动态清单，可以创建设备配置文件（例如移动配置文件、控制台或 HD/SD），并包含你想要纳入配置文件中的曲目与质量。 这称为“再现内容筛选”。 下图显示了一个示例。

![动态清单的格式副本筛选示例][renditions2]

以下示例使用编码器将夹层资产编码成七个 ISO MP4 视频再现内容（从 180p 到 1080p）。 编码的资产可以[动态打包](dynamic-packaging-overview.md)成以下任意流式处理协议： HLS、MPEG 破折号和平滑。

下图的顶部显示了不包含筛选器的资产的 HLS 清单。 （它包含所有七个呈现形式。） 在左下角，关系图显示了应用名为 "ott" 的筛选器的 HLS 清单。 “ott”筛选器指定要删除所有低于 1 Mbps 的比特率，因此最差的两个质量级别已从响应中剥除。 右下角显示了名为“mobile”的筛选器已应用到 HLS 清单。 “mobile”筛选器指定要删除分辨率大于 720p 的再现内容，因此已剥除两个 1080p 再现内容。

![动态清单的格式副本筛选][renditions1]

## <a name="removing-language-tracks"></a>删除语言轨迹
你的资产可能包含多种音频语言，例如英语、西班牙语、法语等。 通常，播放机 SDK 会管理默认的音轨选择和每个用户选择的可用音频轨迹。

开发此类播放器 Sdk 是一项挑战，因为它需要跨设备特定的播放器框架的不同实现。 此外，在某些平台上，播放器 Api 受到限制，且不包含音频选择功能，用户无法选择或更改默认的音频轨迹。使用资产筛选器，可以通过创建只包含所需音频语言的筛选器来控制行为。

![通过动态清单筛选语言跟踪][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>修剪资产开头

在大多数实时流事件中，操作员必须在发生实际事件之前进行某些测试。 例如，在事件开始前，它们可能会包含如下所示的一个石板： "程序将立即开始"。

如果节目正在存档，则测试和静态数据也会一并存档并包含在演播中。 但是，此信息不应显示给客户端。 借助动态清单，可以创建开始时间筛选器，并从清单中删除不需要的数据。

![使用动态清单修剪资产的开始][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>基于实时存档创建子剪辑（视图）

许多直播活动长期运行，直播存档可能包含多个活动。 直播活动结束后，广播者可能需要将实时存档分解成符合逻辑的节目启动和停止序列。

您可以单独发布这些虚拟程序，而无需处理实时存档，也不会创建单独的资产（这不会获得 Cdn 中现有缓存片段的优势）。 此类虚拟节目的示例包括足球或篮球比赛中的上下半场（第几节）、棒球比赛中的局，或者任何体育项目的单项赛事。

借助动态清单，可以使用开始/结束时间创建筛选器，并基于实时存档创建虚拟视图。

![动态清单的子剪辑筛选器][subclip_filter]

下面是筛选的资产：

![通过动态清单筛选的资产][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>调整演播窗口 (DVR)

目前，Azure 媒体服务提供持续时间可设为 1 分钟到 25 小时的循环存档。 清单筛选可以基于存档创建循环 DVR 窗口存档，而不会删除媒体。 在许多情况下，广播者想要提供受限制的 DVR 窗口，此窗口可随着实时边缘移动，并同时保留更大的存档窗口。 广播者可能想要使用来自 DVR 窗口的数据来突出显示剪辑，或者可能需要为不同的设备提供不同的 DVR 窗口。 例如，大多数移动设备不处理大的 DVR 窗口（可以让移动设备有 2 分钟的 DVR 窗口，桌面客户端有 1 小时的 DVR 窗口）。

![包含动态清单的 DVR 窗口][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>调整实时补偿（实时位置）

清单筛选可用于删除实时节目的实时边缘几秒钟的时间。 通过筛选，广播者便可以在观众收到流之前（倒退 30 秒）先观赏预览发布点的演播，并创建广告插入点。 接下来，广播者可将这些广告及时推送到他们的客户端框架，以便能够接收与处理信息，并借机播放广告。

除了广告支持外，还可使用实时补偿设置来调整观看者位置，以便在客户端偏移并命中实时边缘时，仍然可以从服务器获取片段。 这样，客户端就不会收到 HTTP 404 或 412 错误。

![筛选动态清单的实时回退][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>将多个规则合并成单个筛选器

可以将多个筛选规则合并成单个筛选器。 例如，你可以定义一个 "范围规则" 以从实时存档中删除清单，还可以筛选出可用的比特率。 应用多项筛选规则时，最终结果是所有规则的交集。

![具有动态清单的多个筛选规则][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>组合多个筛选器（筛选器组合）

也可以在单个 URL 中组合多个筛选器。 以下方案演示为何需要组合多个筛选器：

1. 需要为移动设备（如 Android 或 iPad）筛选视频质量（以限制视频质量）。 若要删除其质量不符合需要的视频，可创建一个适合设备配置文件的帐户筛选器。 帐户筛选器可用于同一媒体服务帐户下的所有资产，这些资产并没有更多的其他联系。
1. 还可以修改资产的开始时间和结束时间。 若要进行修整，你将创建一个资产筛选器并设置开始/结束时间。
1. 需要组合这两个筛选器。 如果不组合，则需要将质量筛选添加到修剪筛选器，这会导致筛选器的使用更加困难。

若要合并筛选器，请将筛选器名称设置为以分号分隔的格式的清单/播放列表 URL。 假设你有一个名为 *MyMobileDevice* 的筛选器，用于筛选质量，另外还有一个名为 *MyStartTime* 的筛选器，用于设置具体的开始时间。 最多可以组合 3 个筛选器。

有关详细信息，请参阅[此博客文章](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)。

## <a name="considerations-and-limitations"></a>注意事项和限制

- 不应为 VOD 筛选器设置**forceEndTimestamp**、 **presentationWindowDuration**和**liveBackoffDuration**的值。 它们仅用于动态筛选方案。
- 动态清单在 GOP 边界（关键帧）内运行，因此修剪后具有精确的 GOP。
- 可对帐户和资产筛选器使用相同的筛选器名称。 资产筛选器的优先级更高，会替代帐户筛选器。
- 如果更新筛选器，则流式处理终结点需要最多 2 分钟来刷新规则。 如果你使用了筛选器来处理内容（并在代理和 CDN 缓存中缓存了内容），则更新这些筛选器会导致播放器失败。 我们建议在更新筛选器之后清除缓存。 如果无法做到这一点，请考虑使用其他筛选器。
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
