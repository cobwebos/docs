---
title: 使用 Azure 媒体服务实时传送视频流概述 | Microsoft Docs
description: 本文概述如何使用 Azure 媒体服务 v3 实时传送视频流。
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
ms.date: 11/26/2018
ms.author: juliako
ms.openlocfilehash: 634563a2010562e20691abae132dc7540ef8faf2
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632688"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>使用 Azure 媒体服务 v3 实时传送视频流

使用 Azure 媒体服务可将实时事件传送到 Azure 云中的客户。 若要使用媒体服务流式传输实时事件，需要以下组件：  

- 一个相机，用于捕获实时事件。
- 一个实时视频编码器，用于将相机（或其他设备，例如便携式计算机）的信号转换为可发送到媒体服务的贡献源。 贡献源可包括与广告相关的信号，例如 SCTE-35 标记。
- 媒体服务中的组件，用于引入、预览、打包、记录、加密实时事件并将其广播给客户，或者广播给 CDN 进行进一步分发。

本文将提供使用媒体服务的实时传送视频流涉及的主要组件的详细概述、指导和关系图。

## <a name="overview-of-main-components"></a>主要组件概述

若要使用媒体服务传送点播流或实时流，至少需要一个 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints)。 创建媒体服务帐户时，会将一个处于“已停止”状态的**默认** StreamingEndpoint 添加到帐户。 需要启动该 StreamingEndpoint，然后可从中向观看者流式传输内容。 可以使用默认的 **StreamingEndpoint**，或使用所需的配置和 CDN 设置创建另一个自定义的 **StreamingEndpoint**。 可根据需要启用多个 StreamingEndpoint，其中每个 StreamingEndpoint 面向不同的 CDN，并提供唯一的主机名用于传送内容。 

在媒体服务中，[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) 负责引入和处理实时视频源。 创建 LiveEvent 时，会创建一个输入终结点，可以使用它来从远程编码器发送实时信号。 远程实时编码器使用 [RTMP](https://en.wikipedia.org/wiki/Real-Time_Messaging_Protocol) 或[平滑流式处理](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming#Microsoft_Smooth_Streaming)（分段 MP4）协议将贡献源发送到该输入终结点。  

一旦 **LiveEvent** 开始接收贡献源，你就可以使用其预览终结点（预览 URL）进行预览，并在进一步发布之前验证是否可以收到实时流。 确认预览流正常后，可以使用 LiveEvent 来确保可以通过一个或多个（预先创建的）**StreamingEndpoint** 传送实时流。 为此，需针对 **LiveEvent** 创建新的 [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs)。 

**LiveOutput** 对象类似于磁带录制机，它可以捕获实时流并将其记录到媒体服务帐户中的资产。 记录的内容将保存到你的帐户上附加的 Azure 存储帐户，并保存到资产资源定义的容器。  使用 **LiveOuput** 还可以控制传出实时流的某些属性，例如，存档录制内容中保留的流数量（如云 DVR 的容量）。 磁盘上的存档是一个环形存档“窗口”，仅保存 **LiveOutput** 的 **archiveWindowLength** 属性中指定的内容量。 超出此窗口的内容将自动从存储容器中丢弃，且不可恢复。 可以使用不同的存档长度和设置针对 LiveEvent 创建多个 LiveOutput （最多三个）。  

借助媒体服务可以利用**动态打包**，以便预览和广播要发送到服务的贡献源中采用 [MPEG DASH、HLS 和平滑流式处理格式](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)的实时流。 观看者可以使用任何与 HLS、DASH 或平滑流式处理兼容的播放器播放实时流。 可以使用 Web 应用程序或移动应用程序中的 [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) 传送采用上述任何协议的流。

使用媒体服务，可以传送使用高级加密标准 (AES-128) 或三个主要数字版权管理 (DRM) 系统（Microsoft PlayReady、Google Widevine 和 Apple FairPlay）中的任意一个动态加密（**动态加密**）的内容。 媒体服务还提供用于向已授权客户端传送 AES 密钥和 DRM 许可证的服务。 有关如何使用媒体服务加密内容的详细信息，请参阅[保护内容概述](content-protection-overview.md)

还可根据需要应用动态筛选，以便控制发送到播放器的篇目数目、格式、比特率和呈现时间窗口。 

### <a name="new-capabilities-for-live-streaming-in-v3"></a>v3 中实时传送视频流的新功能

使用媒体服务的 v3 API 可受益于以下新功能：

- 新的低延迟模式。 有关详细信息，请参阅[延迟](live-event-latency.md)。
- 改进的 RTMP 支持（提高了稳定性并提供了更多的源编码器支持）。
- RTMPS 安全引入。<br/>创建 LiveEvent 时，将获得 4 个引入 URL。 这 4 个引入 URL 几乎是相同的，具有相同的流式处理令牌 (AppId)，仅端口号部分不同。 其中两个 URL 是 RTMPS 的主要和备份 URL。   
- 使用媒体服务将单比特率贡献源转码为具有多比特率的输出流时，可以流式传输最长 24 小时的实时事件。 

## <a name="liveevent-types"></a>LiveEvent 类型

[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) 可以是下述两种类型之一：直通和实时编码。 

### <a name="pass-through"></a>直通

![直通](./media/live-streaming/pass-through.png)

使用直通 LiveEvent，可以依赖本地实时编码器生成多比特率视频流，并将其作为贡献源发送到 LiveEvent（使用 RTMP 或分段 MP4 协议）。 然后，LiveEvent 会携带无需进一步处理的传入视频流。 此类直通 LiveEvent 已针对长时间运行的实时事件或 24x365 线性实时传送视频流进行优化。 创建此类 LiveEvent 时，请指定 None (LiveEventEncodingType.None)。

发送的贡献源的最高分辨率可为 4K，帧速率可为 60 帧/秒，采用 H.264/AVC 或 H.265/HEVC 视频编解码器，以及 AAC（AAC-LC、HE-AACv1 或 HE-AACv2）音频编解码器。  有关详细信息，请参阅 [LiveEvent 类型的比较和限制](live-event-types-comparison.md)一文。

> [!NOTE]
> 当需要长时间处理多个事件，并且已经在本地编码器上进行了投入时，则可使用直通这种最经济的方法来实时传送视频流。 请参阅 [定价](https://azure.microsoft.com/pricing/details/media-services/) 详细信息。
> 

请参阅 [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126) 中的实时示例。

### <a name="live-encoding"></a>实时编码  

![实时编码](./media/live-streaming/live-encoding.png)

将实时编码与媒体服务配合使用时，需配置本地实时编码器，以便将单比特率视频作为贡献源发送到 LiveEvent（使用 RTMP 或分段 MP4 协议）。 LiveEvent 会将该传入的单比特率流编码为[多个比特率视频流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)，使其可通过 MPEG-DASH、HLS 和平滑流式处理等协议传送到播放设备。 创建此类 LiveEvent 时，请将编码类型指定为 **Basic** (LiveEventEncodingType.Basic)。

发送的贡献源的最高分辨率可为 1080p，帧速率可为 30 帧/秒，采用 H.264/AVC 视频编解码器，以及 AAC（AAC-LC、HE-AACv1 或 HE-AACv2）音频编解码器。 有关详细信息，请参阅 [LiveEvent 类型的比较和限制](live-event-types-comparison.md)一文。

## <a name="liveevent-types-comparison"></a>LiveEvent 类型比较

以下文章包含的表格对两种 LiveEvent 类型的功能做了比较：[比较](live-event-types-comparison.md)。

## <a name="liveoutput"></a>LiveOutput

使用 [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) 可以控制传出实时流的属性，例如，记录的流数量（如云 DVR 的容量），以及观看者是否可以开始观看实时流。 **LiveEvent** 及其 **LiveOutput** 之间的关系类似于传统的电视广播，其中的频道 (**LiveEvent**) 表示恒定的视频流，录制 (**LiveOutput**) 限定为特定的时间分段（例如，下午 6:30 到 7:00 的晚间新闻）。 可以使用数字视频录像机 (DVR) 录制电视节目 – LiveEvents 中的等效功能是通过 ArchiveWindowLength 属性管理的。 它是一个 ISO-8601 时间跨度持续时间（例如 PTHH:MM:SS），指定 DVR 的容量，最小可设置为 3 分钟，最大为 25 小时。


> [!NOTE]
> **LiveOutput** 在创建时启动，在删除后停止。 删除 **LiveOutput** 不会删除基础**资产**和资产中的内容。  

有关详细信息，请参阅[使用云 DVR](live-event-cloud-dvr.md)。

## <a name="streamingendpoint"></a>StreamingEndpoint

将流传输到 **LiveEvent** 后，可以通过创建**资产**、**LiveOutput** 和 **StreamingLocator** 来启动流式传输事件。 这会存档流，并使观看者可以通过 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) 使用该流。

创建媒体服务帐户后，一个处于“已停止”状态的默认流式处理终结点会添加到帐户。 若要开始流式传输内容并利用动态打包和动态加密，要从中流式传输内容的流式处理终结点必须处于“正在运行”状态。

## <a name="a-idbilling-liveevent-states-and-billing"></a><a id="billing" />LiveEvent 状态和计费

一旦 LiveEvent 的状态转换为“正在运行”，就会开始计费。 若要停止 LiveEvent 的计费，必须停止 LiveEvent。

有关详细信息，请参阅[状态和计费](live-event-states-billing.md)。

## <a name="latency"></a>Latency

有关 LiveEvent 延迟的详细信息，请参阅[延迟](live-event-latency.md)。

## <a name="live-streaming-workflow"></a>实时传送视频流工作流

下面是实时传送视频流工作流的步骤：

1. 创建 LiveEvent。
2. 创建新的资产对象。
3. 创建 LiveOutput 并使用创建的资产名称。
4. 若要使用 DRM 加密内容，则创建流式处理策略和内容密钥。
5. 如果不使用 DRM，则使用内置的流式处理策略类型创建流式处理定位符。
6. 列出流式处理策略中的路径，以取回要使用的 URL（确定性）。
7. 获取要从中流式传输的流式处理终结点的主机名。 
8. 将步骤 6 中的 URL 与步骤 7 中的主机名合并，以获取完整的 URL。

有关详细信息，请参阅基于 [Live .NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live) 示例的[实时传送视频流教程](stream-live-tutorial-with-api.md)。

## <a name="next-steps"></a>后续步骤

- [LiveEvent 类型比较](live-event-types-comparison.md)
- [状态和计费](live-event-states-billing.md)
- [延迟](live-event-latency.md)
