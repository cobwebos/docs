---
title: 筛选器和 Azure 媒体服务动态清单 | Microsoft Docs
description: 本主题介绍如何创建筛选器，以便客户端能够使用它们来流式传输流的特定部分。 媒体服务将创建动态清单来存档此选择性流。
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
ms.openlocfilehash: ee0200f7c007b437a27b8e9d0f36becc13b8f611
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835820"
---
# <a name="pre-filtering-manifests-by-using-dynamic-packager"></a>通过使用动态打包程序的预筛选清单

你可以传递内容流式传输到设备的自适应比特率，通常需要将一个清单的多个版本发布到目标特定的设备功能或可用网络带宽。 [动态打包程序](dynamic-packaging-overview.md)允许您指定筛选器可以筛选出特定编解码器、 分辨率、 比特率和音频跟踪组合上实时需创建多个副本中删除。 只需发布一组特定的目标设备 （iOS、 Android、 SmartTV 或浏览器） 和网络功能 （高带宽、 移动、 或低带宽方案） 配置筛选器与新的 URL。 在这种情况下，客户端可以处理通过查询字符串内容的流式处理 (通过指定可用[资产筛选器或帐户筛选器](filters-concept.md)) 和到流的特定部分的流中使用筛选器。

某些传递方案要求，请确保客户不能访问特定的跟踪。 例如，可能不想要发布包含到特定订阅服务器层的 HD 轨道的清单。 或者，可能想要删除特定的自适应比特率 (ABR) 跟踪，从而减少成本传递到不会受益于其他跟踪的特定设备。 在这种情况下可以将关联一组预先创建筛选器与你[流式处理定位符](streaming-locators-concept.md)上创建。 在这种情况下，客户端不能操作如何传输的内容，则由定义**流式处理定位符**。

可以通过指定筛选将组合[流式处理定位符的筛选器](filters-concept.md#associating-filters-with-streaming-locator)+ 您的客户端在 URL 中指定的其他设备特定筛选器。 这可用于限制其他跟踪元数据或事件流、 音频语言或描述性的音频轨道等。 

指定不同的筛选器对你的流，此功能提供了一个强大**动态清单**操作解决方案以面向目标设备的多个用例场景。 本主题介绍与**动态清单**相关的概念，并提供可能需要使用此功能的示例方案。

> [!NOTE]
> 动态清单不会更改资产和该资产的默认清单。 
> 

##  <a name="overview-of-manifests"></a>清单的概述

Azure 媒体服务支持 HLS、 MPEG DASH 和平滑流式处理协议。 作为的一部分[动态打包](dynamic-packaging-overview.md)，流式处理客户端清单 （HLS 主播放列表、 短划线媒体演示文稿说明 [MPD] 和平滑流式处理） 动态生成基于在 URL 中的格式选择器。 请参阅中的交付协议[常见的按需工作流](dynamic-packaging-overview.md#delivery-protocols)。 

### <a name="get-and-examine-manifest-files"></a>获取并检查清单文件

指定筛选器跟踪属性条件基于你的流 （实时或按需 [VOD] 视频） 的跟踪应包括在动态创建的清单的列表。 若要获取并检查轨迹的属性，必须先加载平滑流式处理清单。

[上传、 编码和流式传输文件使用.NET](stream-files-tutorial-with-api.md#get-streaming-urls)教程演示如何创建使用.NET 的流式处理 Url。 如果运行应用，某个 URL 将指向平滑流式处理清单：`https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`。<br/> 请复制该 URL 并将其粘贴到浏览器的地址栏中。 随后将下载文件。 在所选的文本编辑器中打开该文件。

有关 REST 的示例，请参阅[上传、 编码和流式传输文件与其余部分](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)。

### <a name="monitor-the-bitrate-of-a-video-stream"></a>监视视频流的比特率

可以使用 [Azure Media Player 演示页](http://aka.ms/azuremediaplayer)监视视频流的比特率。 演示页上显示诊断信息**诊断**选项卡：

![Azure Media Player 诊断][amp_diagnostics]
 
### <a name="examples-urls-with-filters-in-query-string"></a>示例：Url 查询字符串中的筛选器

可以将筛选器应用到 ABR 流式处理协议：HLS、MPEG-DASH 和平滑流式处理。 下表显示了一些包含筛选器的 URL 示例：

|Protocol|示例|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|平滑流|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>再现内容筛选

您可以选择将资产编码成多个编码配置文件 （H.264 基线、 H.264 高、 AACL、 AACH、 Dolby Digital Plus） 和多个优质比特率。 不过，并非所有的客户端设备都支持资产的所有配置文件和比特率。 例如，较旧的 Android 设备支持仅 H.264 Baseline + AACL。 发送到设备不能获得的好处的更高的比特率会浪费带宽及设备计算资源。 此类设备必须解码所有给定的信息，只是为了缩小规模进行显示。

使用动态清单，可以创建设备配置文件 (例如移动控制台中或 HD/SD)，并且包括跟踪和你想要的每个配置文件一部分的质量。 可调用再现内容筛选。 下图显示了一个示例。

![再现内容筛选示例][renditions2]

以下示例使用编码器将夹层资产编码成七个 ISO MP4 视频再现内容（从 180p 到 1080p）。 编码的资产可以是[动态打包](dynamic-packaging-overview.md)成以下任一流协议：HLS、MPEG DASH 和平滑流式处理。 

以下关系图的顶部显示了 HLS 资产清单使用没有筛选器。 （它包含所有七个再现内容）。在左下方，该图显示名为"ott"筛选器已应用到 HLS 清单。 "Ott"筛选器指定所有比特率小于 1 Mbps，删除底部的两个质量级别已在响应中剥因此。 在右下方，该图显示名为"mobile"筛选器已应用到 HLS 清单。 "Mobile"筛选器指定其中分辨率大于 720p，因此两个 1080 p 再现内容已脱离的再现内容删除。

![再现内容筛选][renditions1]

## <a name="removing-language-tracks"></a>删除语言轨迹
你的资产可能包含多种音频语言，例如英语、西班牙语、法语等。通常，播放器 SDK 管理器会按默认选择音频轨迹，并根据用户的选择来选择可用音频轨迹。

开发此类播放器 Sdk 一项挑战，因为它在特定于设备的播放器框架之间需要不同的实现。 此外，在某些平台上播放机 Api 限制，不包含音频选择功能的用户不能在其中选择或更改默认的音频轨迹。使用资产筛选器，可以通过创建只包含所需音频语言的筛选器来控制行为。

![语言音轨筛选][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>修剪资产开头

在大多数实时流事件中，操作员必须在发生实际事件之前进行某些测试。 例如，他们可以在事件开始之前包含如下静态内容："节目即将开始。" 

如果节目正在存档，则测试和静态数据也会一并存档并包含在演播中。 但是，此信息不应向客户端显示。 借助动态清单，可以创建开始时间筛选器，并从清单中删除不需要的数据。

![剪裁开始][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>基于实时存档创建子剪辑（视图）

许多实时事件的运行时间较长，并且实时存档可能包含多个事件。 实时事件结束后，广播者可能想要将实时存档分解成有序的节目启动和停止序列。 

您可以分别发布这些虚拟节目，但不后续处理实时存档，也不创建独立的资产 （这不会无法利用 Cdn 中现有缓存片段的优势）。 此类虚拟节目的示例包括足球或篮球比赛中的上下半场（第几节）、棒球比赛中的局，或者任何体育项目的单项赛事。

借助动态清单，可以通过使用开始/结束时间创建筛选器，并基于实时存档的顶部创建虚拟视图。 

![子剪辑筛选器][subclip_filter]

下面是筛选的资产：

![滑雪][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>调整演播窗口 (DVR)

目前，Azure 媒体服务提供可在其中将持续时间配置介于 5 分钟到 25 个小时之间的循环存档。 清单筛选可以基于存档创建循环 DVR 窗口存档，而不会删除媒体。 在许多情况下，广播者想要提供受限制的 DVR 窗口，此窗口可随着实时边缘移动，并同时保留更大的存档窗口。 广播者可能想要使用超出 DVR 窗口的数据来突出显示剪辑，或者想要为不同的设备提供不同的 DVR 窗口。 例如，大多数移动设备不处理大的 DVR 窗口 （可以为桌面客户端有 2 分钟的 DVR 窗口的移动设备和 1 小时）。

![DVR 窗口][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>调整实时补偿（实时位置）

清单筛选可用于删除实时节目的实时边缘几秒钟的时间。 筛选功能，广播者预览发布点的演播，并创建广告插入点之前在观众收到流 （支持 30 秒）。 广播者可以随后将推送这些播发到其客户端框架的时间才能接收和处理之前播发机会的信息。

除了广告支持实时回退设置可用于调整观众的位置，以便客户端偏移并命中实时边缘，它们仍然可以从服务器来获取片段。 这样一来，客户端不会获得 HTTP 404 或 412 错误。

![有关实时回退的筛选器][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>将多个规则合并成单个筛选器

可以将多个筛选规则合并成单个筛选器。 例如，可以定义一个“范围规则”，以便将静态内容从实时存档中删除，并筛选出可用的比特率。 当您要将应用多个筛选规则时，最终结果将是所有规则的交集。

![多个筛选规则][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>组合多个筛选器（筛选器组合）

也可以在单个 URL 中组合多个筛选器。 以下方案演示为何需要组合多个筛选器：

1. 您需要筛选视频质量适用于 Android 或 iPad 之类的移动设备 （目的是限制视频质量）。 若要删除不需要的质量，你将创建一个帐户筛选器适用于设备配置文件。 您没有任何进一步联系在同一媒体服务帐户下的所有资源，可以使用帐户筛选器。
1. 还可以修改资产的开始时间和结束时间。 若要实现此目的，将创建一个资产筛选器并设置开始/结束时间。 
1. 想要合并这些筛选器。 如果不组合，你需要将质量筛选到修整筛选器，这会使筛选器的使用难度添加。


若要组合筛选器，您需要将筛选器名称设置为清单/播放列表以分号分隔的格式的 URL。 我们假设您有一个名为的筛选器*MyMobileDevice*筛选质量，另外还有一个名为*MyStartTime*用于设置具体的开始时间。 最多可以组合 3 个筛选器。 

有关详细信息，请参阅[此博客文章](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)。

## <a name="considerations-and-limitations"></a>注意事项和限制

- 值**forceEndTimestamp**， **presentationWindowDuration**，并**liveBackoffDuration**不应为 VOD 筛选器设置。 它们仅用于实时筛选器方案使用。 
-  动态清单在 GOP 边界 （关键帧） 运行，因此修剪 gop。
-  可以使用相同的帐户和资产筛选器的筛选器名称。 资产筛选器具有较高的优先级，并会替代帐户筛选器。
- 如果更新筛选器，它可能需要 2 分钟的时间来刷新规则流式处理终结点。 如果筛选器用于提供的内容 （和缓存代理服务器中的内容和 CDN 缓存），更新这些筛选器可能会导致播放器失败。 我们建议在更新筛选器之后清除缓存。 如果此选项不可行，请考虑使用不同的筛选器。
- 客户需要手动下载清单和分析的确切的开始时间戳和时间范围。
    
    - 若要确定的资产中, 跟踪的属性[获取并检查清单文件](#get-and-examine-manifest-files)。
    - 若要设置的资产筛选器的时间戳属性的公式为： <br/>startTimestamp =&lt;清单中开始时间&gt; +  &lt;预期以秒为单位的筛选器开始时间&gt;* 时间刻度

## <a name="next-steps"></a>后续步骤

以下文章说明如何以编程方式创建筛选器：  

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
