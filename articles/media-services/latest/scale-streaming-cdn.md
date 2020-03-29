---
title: 通过 CDN 集成流式传输内容
titleSuffix: Azure Media Services
description: 了解使用 CDN 集成流式传输内容，以及预取和源辅助 CDN 预取。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: 4ed8ada306720b7a8b44ddd59cefe399238c906a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128072"
---
# <a name="stream-content-with-cdn-integration"></a>通过 CDN 集成流式传输内容

Azure 内容分发网络 (CDN) 为开发人员提供了一个全局解决方案，通过在世界各地按特定策略放置的物理节点缓存内容来快速分发高带宽内容。  

CDN 缓存从媒体服务[流终结点（源）](streaming-endpoint-concept.md)流式传输的内容，每个编解码器、每个流式处理协议、每个比特率、每个容器格式和每个加密/DRM。 对于编解码器流协议-容器格式-比特率加密的每个组合，将有一个单独的 CDN 缓存。

只要缓存视频片段，热门内容就会直接从 CDN 缓存中提供。 可能会缓存实时内容，因为通常会有很多人观看完全相同的内容。 点播内容可能有点棘手，因为您可能有一些受欢迎的内容，有些内容不是。 如果你有数以百万计的视频资产，其中没有一个是受欢迎的（只有一个或两个观众一个星期），但你有数千人观看所有不同的视频，CDN变得不那么有效。

还需要考虑自适应流式处理的工作原理。 每个单独的视频片段都缓存为自己的实体。 例如，想象一下第一次观看特定视频。 如果观看者跳过只在这里和那里观看几秒钟，则只有与观看者关联的视频片段才会在 CDN 中缓存。 使用自适应流式处理，你通常会有 5 到 7 种不同的视频比特率。 如果一个人正在观察一个比特率，而另一个人正在观察不同的比特率，则他们每人都在 CDN 中单独缓存。 即使两个人正在观看相同的比特率，他们也可能通过不同的协议进行流式传输。 每个协议（HLS、MPEG-DASH、平滑流式处理）都是单独缓存的。 因此，会单独缓存每个比特率和协议，并且仅缓存已请求的视频片段。

在决定是否在媒体服务[流终结点](streaming-endpoint-concept.md)上启用 CDN 时，请考虑预期的观看者数量。 CDN 仅在您期待许多内容的观看者时才有所帮助。 如果查看器的最大并发小于 500，建议禁用 CDN，因为 CDN 与并发性一起最大比例。

本主题讨论启用[CDN 集成](#enable-azure-cdn-integration)。 它还解释了预取（活动缓存）和[源辅助 CDN 预取](#origin-assist-cdn-prefetch)概念。

## <a name="considerations"></a>注意事项

* 无论是否启用 CDN，[流式处理终结点](streaming-endpoint-concept.md)`hostname`和流式处理 URL 都保持不变。
* 如果需要使用 CDN 或不使用 CDN 测试内容的能力，请创建另一个未启用 CDN 的流式处理终结点。

## <a name="enable-azure-cdn-integration"></a>启用 Azure CDN 集成

> [!IMPORTANT]
> 不能为试用版或学生 Azure 帐户启用 CDN。
>
> 除联邦政府和中国区域外，所有 Azure 数据中心都启用 CDN 集成。

在启用了 CDN 后预配流式处理终结点后，在进行 DNS 更新将流式处理终结点映射到 CDN 终结点之前，媒体服务上有定义的等待时间。

如果以后想要禁用/启用 CDN，流式处理终结点必须处于“已停止”**** 状态。 可能需要长达两小时才能启用 Azure CDN 集成并使更改在所有 CDN POP 中生效。 但是，您可以启动流式处理终结点和流，而不会中断流式处理终结点。 集成完成后，流将从 CDN 传递。 在预配期间，流式处理终结点将处于**启动**状态，您可能会观察到性能下降。

创建标准流式处理终结点时，默认情况下使用标准 Verizon 配置它。 您可以使用 REST API 配置高级 Verizon 或标准 Akamai 提供商。

标准流式处理终结点可在 **Verizon 提供的 Azure CDN** 上实现 Azure 媒体服务与 Azure CDN 的集成。 可以使用所有 **Azure CDN 定价层和提供程序**配置高级流式处理终结点。

> [!NOTE]
> 有关 Azure CDN 的详细信息，请参阅[CDN 概述](../../cdn/cdn-overview.md)。

## <a name="determine-if-a-dns-change-was-made"></a>确定是否进行了 DNS 更改

通过使用，可以确定 DNS 更改是否在流式处理终结点上（流量正定向到 Azure CDN）。 <https://www.digwebinterface.com> 如果在结果中看到azureedge.net个域名，则流量现在指向 CDN。

## <a name="origin-assist-cdn-prefetch"></a>原点辅助 CDN 预取

CDN 缓存是一个反应性过程。 如果 CDN 可以预测将请求哪些对象，CDN 可以主动请求和缓存下一个对象。 通过此过程，您可以对所有（或大多数）对象实现缓存命中，从而提高性能。

预取的概念力求将对象定位在"互联网的边缘"，以预期玩家会立即要求这些对象，从而减少将对象交付给玩家的时间。

为了实现这一目标，流式处理终结点（原点）和 CDN 需要以多种方式携手工作：

- 媒体服务源需要具有"智能"（原始辅助）来通知 CDN 下一个要预取的对象。
- CDN 执行预提取和缓存（CDN 预取部分）。 CDN 还需要有"智能"来通知来源，无论是预取还是常规提取，处理 404 响应，以及避免无休止的预取循环的方法。

### <a name="benefits"></a>优点

*原点辅助 CDN 预取*功能的优点包括：

- Prefetch 通过在播放期间预先将预期视频段放置在边缘，减少观看者延迟，并缩短视频段下载时间，从而提高视频播放质量。 这样可以缩短视频启动时间并减少重新缓冲事件。
- 此概念适用于常规 CDN 源方案，并不限于介质。
- Akamai 已将此功能添加到[Akamai 云嵌入 （ACE）](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html)中。

> [!NOTE]
> 此功能还不适用于与媒体服务流终结点集成的 Akamai CDN。 但是，它适用于具有预先存在的 Akamai 合同且需要在 Akamai CDN 和媒体服务来源之间进行自定义集成的媒体服务客户。

### <a name="how-it-works"></a>工作原理

CDN 对`Origin-Assist CDN-Prefetch`标头的支持（用于实时和视频点播流）可供与 Akamai CDN 有直接合同的客户使用。 此功能涉及 Akamai CDN 和媒体服务源之间的以下 HTTP 标头交换：

|HTTP 标头|值|发送方|接收方|目的|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1（默认）或 0 |CDN|源|指示 CDN 已启用预取。|
|`CDN-Origin-Assist-Prefetch-Path`| 示例： <br/>片段（视频=1400000000，格式=mpd-time-cmaf）|源|CDN|为 CDN 提供预取路径。|
|`CDN-Origin-Assist-Prefetch-Request`|1（预取请求）或 0（常规请求）|CDN|源|指示来自 CDN 的请求是预取。|

要查看标头交换的一部分，可以尝试以下步骤：

1. 使用 Postman 或 cURL 向媒体服务源发出音频或视频段或片段的请求。 请确保在请求中添加标头`CDN-Origin-Assist-Prefetch-Enabled: 1`。
2. 在响应中，应看到具有相对路径`CDN-Origin-Assist-Prefetch-Path`的标头的值。

### <a name="supported-streaming-protocols"></a>支持的流式处理协议

该`Origin-Assist CDN-Prefetch`功能支持以下实时和按需流式处理的流协议：

* HLS v3
* HLS v4
* HLS CMAF
* DASH （CSF）
* 破折号 （CMAF）
* 顺畅流式处理

### <a name="faqs"></a>常见问题解答

* 如果预取路径 URL 无效，使 CDN 预取获取 404，该怎么办？

    CDN 将仅缓存 404 响应 10 秒（或其他已配置的值）。

* 假设您有一个点播视频。 如果启用了 CDN 预取，此功能是否意味着一旦客户端请求第一个视频段，预取将启动循环以相同的比特率预取所有后续视频段？

    否，CDN 预获取仅在客户端启动的请求/响应后完成。 CDN 预取永远不会由预取触发，以避免预取循环。

* 原点辅助 CDN 预提取功能是否始终处于打开状态？ 如何打开/关闭它？

    默认情况下此功能处于关闭状态。 客户需要通过 Akamai API 将其打开。

* 对于实时流式处理，如果下一个分段或片段不可用，源辅助系统会发生什么情况？

    在这种情况下，媒体服务源不会提供`CDN-Origin-Assist-Prefetch-Path`标头，并且不会发生 CDN 预获取。

* `Origin-Assist CDN-Prefetch`如何使用动态清单筛选器？

    此功能独立于清单筛选器工作。 当下一个片段从筛选器窗口退出时，其 URL 仍将通过查看原始客户端清单而找到，然后作为 CDN 预取响应标头返回。 因此，CDN 将获得从 DASH/HLS/平滑清单中筛选出的片段的 URL。 但是，玩家永远不会向 CDN 发出 GET 请求以获取该片段，因为该片段不包括在玩家持有的 DASH/HLS/平滑清单中（玩家不知道该片段的存在）。

* DASH MPD/HLS 播放列表/平滑清单是否可以预接？

    否，DASH MPD、HLS 主播放列表、HLS 变体播放列表或平滑清单 URL 不会添加到预取标头。

* 预取 URL 是相对的还是绝对的？

    虽然 Akamai CDN 允许这两种 URL，但媒体服务源仅为预取路径提供相对 URL，因为使用绝对 URL 没有明显的好处。

* 此功能是否与受 DRM 保护的内容配合使用？

    是的，由于此功能在 HTTP 级别工作，因此它不会解码或解析任何段/片段。 它不关心内容是否加密。

* 此功能是否与服务器端广告插入 （SSAI） 配合使用？
    
    它适用于原始/主要内容（在广告插入前的原始视频内容）有效，因为 SSAI 不会更改媒体服务来源源内容的时间戳。 此功能是否与广告内容有效，取决于广告来源是否支持"来源-辅助"。 例如，如果广告内容也托管在 Azure 媒体服务（相同或单独的来源），广告内容也将预占。

* 此功能是否与 UHD/HEVC 内容配合使用？

    是的。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

* 请确保查看[流式处理终结点（源）](streaming-endpoint-concept.md)文档。
* 此[存储库](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)中的示例介绍了如何使用 .NET 启动默认流式处理终结点。
