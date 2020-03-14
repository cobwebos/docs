---
title: 用 cdn 缩放流式处理
titleSuffix: Azure Media Services
description: 了解将内容直接传送到客户端播放器应用程序或内容交付网络（CDN）的流式处理服务。
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
ms.openlocfilehash: e918f7ee64d4bc49d5da80bf9a3e7595555296dc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203689"
---
# <a name="scaling-streaming-with-cdn"></a>使用 CDN 缩放流式处理

Azure 内容分发网络 (CDN) 为开发人员提供了一个全局解决方案，通过在世界各地按特定策略放置的物理节点缓存内容来快速分发高带宽内容。  

CDN 按每个编解码器、每个流式处理协议、每比特率、每个容器格式和每个加密/DRM 缓存从媒体服务[流式处理终结点（源）](streaming-endpoint-concept.md)传输的内容。 对于编解码器流式处理协议容器格式-比特率加密的每个组合，都将有一个单独的 CDN 缓存。 

只要缓存了视频片段，就会直接从 CDN 缓存中提供常用内容。 可能会缓存实时内容，因为通常会有很多人观看完全相同的内容。 按需内容可能有点棘手，因为你可能有一些常用的内容，某些内容并不是。 如果你有数百万个视频资产（一周内只有一位或两位观看者），但你有成千上万人观看了所有不同的视频，CDN 就变得不太有效了。 

还需要考虑自适应流式处理的工作原理。 每个单独的视频片段都作为自己的实体进行缓存。 例如，假设您首次监视某个视频。 如果查看器仅在此处显示了几秒钟的时间，并且在那里只显示与所监视人员关联的视频碎片，则会在 CDN 中缓存。 使用自适应流式处理，你通常会有 5 到 7 种不同的视频比特率。 如果一个人在观看一个比特率，而另一个用户正在观看不同的比特率，则它们分别在 CDN 中进行缓存。 即使两个用户观察到相同的比特率，也可以通过不同的协议进行流式处理。 每个协议（HLS、MPEG-DASH、平滑流式处理）都是单独缓存的。 因此，会单独缓存每个比特率和协议，并且仅缓存已请求的视频片段。

在决定是否在媒体服务[流式处理终结点](streaming-endpoint-concept.md)上启用 CDN 时，请考虑预期的查看者数量。 仅当你为内容预测许多查看器时，CDN 才会有所帮助。 如果查看器的最大并发度低于500，则建议禁用 CDN，因为 CDN 会使用并发度进行缩放。 

本主题讨论如何启用[CDN 集成](#enable-azure-cdn-integration)。 它还介绍预提取（主动缓存）和[源协助 CDN 预提取](#origin-assist-cdn-prefetch)概念。

## <a name="considerations"></a>注意事项

* 无论是否启用 CDN，流式[处理终结点](streaming-endpoint-concept.md)`hostname` 和流式处理 URL 都是相同的。
* 如果你需要能够通过或不通过 CDN 来测试你的内容，请创建另一个不启用 CDN 的流式处理终结点。

## <a name="enable-azure-cdn-integration"></a>启用 Azure CDN 集成

> [!IMPORTANT]
> 不能为试用版或学生版 Azure 帐户启用 CDN。
>
> 所有 Azure 数据中心（美国联邦政府版和中国区域除外）均已启用 CDN 集成。

启用 CDN 后，在设置了一个流式处理终结点后，在完成 DNS 更新以将流式处理终结点映射到 CDN 终结点之前，媒体服务会有一个定义的等待时间。

如果以后想要禁用/启用 CDN，流式处理终结点必须处于“已停止”状态。 可能需要长达两小时才能启用 Azure CDN 集成并使更改在所有 CDN POP 中生效。 但是，你可以在不中断流式处理终结点的情况下启动流式处理终结点和流，而在集成完成后，将从 CDN 传送流。 在设置期间，流式处理终结点将处于 "**正在启动**" 状态，你可能会看到性能下降。

创建标准流式处理终结点时，默认情况下，它是使用标准 Verizon 进行配置的。 可以使用 REST Api 配置高级 Verizon 或标准 Akamai 提供程序。

标准流式处理终结点可在 **Verizon 提供的 Azure CDN** 上实现 Azure 媒体服务与 Azure CDN 的集成。 可以使用所有 **Azure CDN 定价层和提供程序**配置高级流式处理终结点。 

> [!NOTE]
> 有关 Azure CDN 的详细信息，请参阅[CDN 概述](../../cdn/cdn-overview.md)。

## <a name="determine-if-a-dns-change-was-made"></a>确定是否进行 DNS 更改

可以通过使用 https://www.digwebinterface.com来确定是否已在流式处理终结点（流量定向到 Azure CDN）上进行 DNS 更改。 如果在结果中看到 azureedge.net 的域名，则流量现在指向 CDN。

## <a name="origin-assist-cdn-prefetch"></a>源-协助 CDN-预提取

CDN 缓存是一种反应过程。 如果 CDN 能预测下一个对象将被请求，CDN 可以主动请求并缓存下一个对象。 使用此过程，可以为所有（或大部分）对象实现缓存命中，从而提高性能。

预提取的概念会将对象放置在 "Internet 边缘" 上，以预测这些对象将被播放机即将请求，从而减少将该对象传递给播放机的时间。

若要实现此目标，流式处理终结点（源）和 CDN 需要手头工作： 

- 媒体服务源需要具有 "智能" （源辅助）才能通知 CDN 要预提取的下一个对象，以及 
- CDN 执行预提取和缓存（CDN 预提取部分）。 CDN 还需要使用 "智能" 来告知源是预提取还是常规提取，处理404响应，以及避免无休止的预提取循环的方法。

### <a name="benefits"></a>优点

*源协助 CDN 预提取*功能的优点包括：

- 预提取可通过以下方式改善视频播放质量：在播放期间将预期的视频段预定位到边缘，缩短查看器的延迟并改善视频段下载时间。 这会缩短视频启动时间，减少 rebuffering 的发生次数。
- 此概念适用于常规 CDN 源方案，但不限于媒体。
- Akamai 已将此功能添加到[Akamai Cloud Embed （ACE）](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html)。

> [!NOTE]
> 此功能目前不适用于与媒体服务流式处理终结点集成的 Akamai CDN。 但是，它可用于具有预先存在的 Akamai 合同并且需要 Akamai CDN 与媒体服务源之间的自定义集成的媒体服务客户。

### <a name="how-it-works"></a>工作原理

针对源的 CDN 支持-*协助 cdn 预提取*标头（适用于实时和视频点播流式处理）适用于具有 Akamai CDN 直接协定的客户。 此功能涉及到 Akamai CDN 与媒体服务源之间的以下 HTTP 标头交换：

|HTTP 标头|值|发送方|接收方|目的|
| ---- | ---- | ---- | ---- | ----- |
|CDN-源助手-已启用 | 1（默认值）或0 |CDN|源|指示 CDN 已启用预提取|
|CDN-源助手-预提取-路径| 示例： <br/>片段（视频 = 1400000000，格式 = mpd-cmaf）|源|CDN|提供 CDN 的预提取路径|
|CDN-源-辅助-请求|1（预提取请求）或0（常规请求）|CDN|源|指示 CDN 的请求是预提取|

若要查看标头交换的一部分，可以尝试执行以下步骤：

1. 使用 Postman 或卷向 Media Services 源发送音频或视频段或片段的请求。 请确保在请求中添加标头 "CDN-支持预提取： 1"。
2. 在响应中，应会看到标头 "CDN-源-辅助-预提取-路径"，其中包含相对路径作为其值。

### <a name="supported-streaming-protocols"></a>支持的流式处理协议 

对于实时和按需流式处理，*原点协助 CDN 预提取*功能支持以下流式处理协议：

* HLS v3
* HLS v4
* HLS CMAF
* 短划线（CSF）
* 短划线（CMAF）
* 顺畅流式处理

### <a name="faqs"></a>常见问题解答

* 如果预提取路径 URL 无效以便 CDN 预提取获得404，会发生什么情况？ 

    CDN 将仅缓存10秒（或其他配置值）的404响应。
* 假设你有点播视频。 如果启用了 CDN 预提取功能，则此功能意味着在客户端请求第一个视频段后，预提取会开始一个循环，以便在同一比特率预提取所有后续视频段？ 

    不能，CDN 预提取仅在客户端启动的请求/响应之后执行。 预提取不会触发 CDN 预提取，以避免预提取循环。 
* 是否为源-协助 CDN-预提取功能始终打开？ 如何打开/关闭它？ 

    默认情况下，此功能处于关闭状态。 客户需要通过 Akamai API 打开它。
* 对于实时流式处理，会发生什么情况—协助下一段或片段是否尚不可用？ 

    在这种情况下，媒体服务源将不提供 CDN 源辅助预提取-路径标头，且不会进行 CDN 预提取。
* 源如何*协助 CDN 预提取*如何使用动态清单筛选器？ 

    此功能独立于清单筛选器。 当下一个片段不在筛选器窗口中时，通过查看原始客户端清单，然后以 CDN 预提取响应标头的形式返回其 URL。 这意味着，CDN 将获取从破折号/HLS/平滑清单筛选掉的片段的 URL。 不过，播放机将永远不会向 CDN 发出 GET 请求以提取该片段，因为在播放机持有的短划线/HLS/平滑清单中不包含该片段（播放机不知道该片段存在）。
* 是否可预回迁 MPD/HLS 播放列表/平滑清单？

    不，破折号 MPD，HLS 主播放列表，HLS 变体播放列表或平滑清单 URL 未添加到预提取标头。
* 预提取 Url 是相对的还是绝对的？ 

    尽管 Akamai CDN 允许使用这两种方法，但媒体服务源仅为预提取路径提供相对 Url，因为使用绝对 Url 并没有明显的好处。
* 此功能是否适用于受 DRM 保护的内容？

    是的，因为此功能在 HTTP 级别工作，所以它既不解码也不分析任何段/片段。 它并不关心内容是否已加密。
* 此功能是否适用于服务器端 Ad 插入（SSAI）？
    
    对于原始内容/主要内容（广告插入前的原始视频内容），因为 SSAI 不会更改媒体服务源的源内容的时间戳。 此功能是否适用于 ad 内容，具体取决于 ad 源是否支持源辅助。 例如，如果 ad 内容也托管在 Azure 媒体服务中（相同或不同的源），则也会预回迁 ad 内容。
* 此功能是否适用于 UHD/HEVC 内容？

    是的。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

* 请确保审阅[流式处理终结点（原始）](streaming-endpoint-concept.md)文档。
* 此[存储库](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)中的示例介绍了如何使用 .NET 启动默认流式处理终结点。
