---
title: 通过 CDN 集成流式传输内容
titleSuffix: Azure Media Services
description: 了解如何通过 CDN 集成流式传输内容，以及预提取和源-协助 CDN 预提取。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: e1ea0a43783fb7abdc17655e3a3431d125d426f8
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291273"
---
# <a name="stream-content-with-cdn-integration"></a>通过 CDN 集成流式传输内容

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure 内容分发网络 (CDN) 为开发人员提供了一个全局解决方案，通过在世界各地按特定策略放置的物理节点缓存内容来快速分发高带宽内容。  

CDN 缓存从媒体服务 [流式处理终结点流式处理终结点 (源) ](streaming-endpoint-concept.md) 每个编解码器、每个流式处理协议、比特率、每个容器格式以及每个加密/DRM 的内容。 对于编解码器流式处理协议容器格式-比特率加密的每个组合，都将有一个单独的 CDN 缓存。

只要缓存了视频片段，就会直接从 CDN 缓存中提供常用内容。 可能会缓存实时内容，因为通常会有很多人观看完全相同的内容。 按需内容可能有点棘手，因为你可能有一些常用的内容，某些内容并不是。 如果有数百万个视频资产，其中的每个观众都不受欢迎 (一周只需一个或两个查看者) 但你有数千人观看了所有不同的视频，CDN 就变得不太有效了。

还需要考虑自适应流式处理的工作原理。 每个单独的视频片段缓存为其自己的实体。 例如，假设您首次监视某个视频。 如果查看器仅在此处显示了几秒钟的时间，并且在那里只显示与所监视人员关联的视频碎片，则会在 CDN 中缓存。 使用自适应流式处理，你通常会有 5 到 7 种不同的视频比特率。 如果一个人在观看一个比特率，而另一个用户正在观看不同的比特率，则它们分别在 CDN 中进行缓存。 即使两个用户观察到相同的比特率，也可以通过不同的协议进行流式处理。 每个协议（HLS、MPEG-DASH、平滑流式处理）都是单独缓存的。 因此，会单独缓存每个比特率和协议，并且仅缓存已请求的视频片段。

建议为标准和高级流式处理终结点启用 CDN，但测试环境除外。 每种类型的流式处理终结点都有不同的受支持吞吐量限制。
很难准确计算流式处理终结点支持的最大并发流数，因为需要考虑各种因素。 这些方法包括：

- 用于流式处理的最大比特率
- 播放器预缓冲和切换行为。 播放机尝试从原点突发段并使用加载速度来计算自适应比特率切换。 如果流式处理终结点接近饱和，响应时间可能会有所不同，并且玩家会开始切换到较低质量。 因为这会减少流式处理终结点播放机上的负载，所以，请返回到较高质量，创建不需要的切换触发器。
总体而言，使用最大的流式处理终结点吞吐量来估算最大并发流并将其除以最大比特率 (（假定所有播放机使用高比特率），这是安全的。 ) 例如，你可以拥有一个限制为 600 Mbps 的标准流式处理终结点和3Mbp 的最高比特率。 在这种情况下，最高比特率支持大约200个并发流。 还应考虑音频带宽要求。 尽管音频流只能在 128 kps 进行流式传输，但在将其与并发流的数量相乘后，总流会迅速增加。

本主题讨论如何启用 [CDN 集成](#enable-azure-cdn-integration)。 它还说明了预提取 (主动缓存) 和 [源协助 CDN 预提取](#origin-assist-cdn-prefetch) 概念。

## <a name="considerations"></a>注意事项

- 无论是否启用 CDN， [流式处理终结点](streaming-endpoint-concept.md) `hostname` 和流 URL 都是相同的。
- 如果你需要能够通过或不通过 CDN 来测试你的内容，请创建另一个不启用 CDN 的流式处理终结点。

## <a name="enable-azure-cdn-integration"></a>启用 Azure CDN 集成

> [!IMPORTANT]
> 不能为试用版或学生版 Azure 帐户启用 CDN。
>
> 所有 Azure 数据中心（美国联邦政府版和中国区域除外）均已启用 CDN 集成。

启用 CDN 后，在设置了一个流式处理终结点后，在完成 DNS 更新以将流式处理终结点映射到 CDN 终结点之前，媒体服务会有一个定义的等待时间。

如果以后想要禁用/启用 CDN，流式处理终结点必须处于“已停止”**** 状态。 启动流式处理终结点后，最多可能需要两个小时才能启用 Azure CDN 集成，并使更改在所有 CDN Pop 中处于活动状态。 但是，可以启动流式处理终结点和流，而不会中断流式处理终结点。 集成完成后，将从 CDN 传送流。 在设置期间，流式处理终结点将处于 " **正在启动** " 状态，你可能会看到性能下降。

创建标准流式处理终结点时，默认情况下，它是使用标准 Verizon 进行配置的。 可以使用 REST Api 配置高级 Verizon 或标准 Akamai 提供程序。

标准流式处理终结点可在 **Verizon 提供的 Azure CDN** 上实现 Azure 媒体服务与 Azure CDN 的集成。 可以使用所有 **Azure CDN 定价层和提供程序**配置高级流式处理终结点。

> [!NOTE]
> 有关 Azure CDN 的详细信息，请参阅 [CDN 概述](../../cdn/cdn-overview.md)。

## <a name="determine-if-a-dns-change-was-made"></a>确定是否进行 DNS 更改

可以通过使用将流量定向到 Azure CDN) ，确定是否已在流式处理终结 (点上进行 DNS 更改 <https://www.digwebinterface.com> 。 如果在结果中看到 azureedge.net 的域名，则流量现在指向 CDN。

## <a name="origin-assist-cdn-prefetch"></a>源-协助 CDN-预提取

CDN 缓存是一种反应过程。 如果 CDN 能预测下一个对象将被请求，CDN 可以主动请求并缓存下一个对象。 使用此过程，可以为所有 (或大多数对象) 实现缓存命中，从而提高性能。

预提取的概念会将对象放置在 "internet 边缘" 上，以预测这些对象将被播放机即将请求，从而减少将该对象传递给播放机的时间。

为实现此目标，流式处理终结点 (起源) ，CDN 需要通过以下几种方式手动工作：

- 媒体服务源需要具有 "智能" (原点帮助) 通知 CDN 要预提取的下一个对象。
- CDN)  (CDN 预提取和缓存。 CDN 还需要使用 "智能" 来告知源是预提取还是常规提取，处理404响应，以及避免无休止的预提取循环的方法。

### <a name="benefits"></a>优点

*源协助 CDN 预提取*功能的优点包括：

- 通过在播放期间将预期的视频段预定位到边缘，缩短了查看器的延迟，缩短了视频分段的下载时间，预提取提高了视频播放质量。 这会缩短视频启动时间，减少 rebuffering 的发生次数。
- 此概念适用于常规的 CDN 源方案，但不限于媒体。
- Akamai 已将此功能添加到 [Akamai Cloud 嵌入 (ACE) ](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html)。

> [!NOTE]
> 此功能目前不适用于与媒体服务流式处理终结点集成的 Akamai CDN。 但是，它可用于具有预先存在的 Akamai 合同并且需要 Akamai CDN 与媒体服务源之间的自定义集成的媒体服务客户。

### <a name="how-it-works"></a>工作原理

`Origin-Assist CDN-Prefetch`对于实时和视频点播流式处理)  (的标头的 CDN 支持可用于直接签订 AKAMAI CDN 的客户。 此功能涉及到 Akamai CDN 与媒体服务源之间的以下 HTTP 标头交换：

|HTTP 标头|值|发送方|接收方|目的|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1（默认值）或 0 |CDN|源|指示 CDN 已启用预提取。|
|`CDN-Origin-Assist-Prefetch-Path`| 示例： <br/>Fragments(video=1400000000,format=mpd-time-cmaf)|源|CDN|提供 CDN 的预回迁路径。|
|`CDN-Origin-Assist-Prefetch-Request`|1（预提取请求）或 0（常规请求）|CDN|源|如果为，则指示 CDN 的请求是预提取。|

若要查看标头交换部分的实际运行情况，可以尝试执行以下步骤：

1. 使用 Postman 或卷向 Media Services 源发送音频或视频段或片段的请求。 请确保 `CDN-Origin-Assist-Prefetch-Enabled: 1` 在请求中添加标头。
2. 在响应中，应会看到标头的 `CDN-Origin-Assist-Prefetch-Path` 相对路径作为其值。

### <a name="supported-streaming-protocols"></a>支持的流式处理协议

此 `Origin-Assist CDN-Prefetch` 功能支持以下用于实时流式处理和按需流式处理的流式处理协议：

* HLS v3
* HLS v4
* HLS CMAF
* 划线 (CSF) 
* 划线 (CMAF) 
* 顺畅流式处理

### <a name="faqs"></a>常见问题解答

* 如果预提取路径 URL 无效以便 CDN 预提取获得404，会发生什么情况？

    CDN 只会将404响应缓存10秒 (或其他配置的值) 。

* 假设你有点播视频。 如果启用了 CDN 预提取功能，则此功能会认为，一旦客户端请求第一个视频段，预提取会开始一次以相同的比特率预提取所有后续视频段？

    不能，CDN 预提取仅在客户端启动的请求/响应之后完成。 预提取不会触发 CDN 预提取，以避免预提取循环。

* 是否为源-协助 CDN-预提取功能始终打开？ 如何打开/关闭它？

    默认情况下此功能处于关闭状态。 客户需要通过 Akamai API 打开它。

* 对于实时流式处理，会发生什么情况—协助下一段或片段是否尚不可用？

    在这种情况下，媒体服务源不会提供 `CDN-Origin-Assist-Prefetch-Path` 标头，且不会进行 CDN 预提取。

* 如何 `Origin-Assist CDN-Prefetch` 使用动态清单筛选器？

    此功能独立于清单筛选器。 当下一个片段不在筛选器窗口中时，通过查看原始客户端清单并将其作为 CDN 预提取响应标头返回，仍会找到其 URL。 因此，CDN 将获取从破折号/HLS/平滑清单筛选掉的片段的 URL。 不过，播放机将永远不会向 CDN 发出 GET 请求以提取该片段，因为在播放机所持有的短划线/HLS/平滑清单中不包含该片段 (播放机不知道片段的存在) 。

* 是否可预回迁 MPD/HLS 播放列表/平滑清单？

    不会将 MPD、HLS、HLS variant 播放列表或平滑清单 URL 添加到预提取标头。

* 预提取 Url 是相对的还是绝对的？

    尽管 Akamai CDN 允许使用这两种方法，但媒体服务源仅为预提取路径提供相对 Url，因为使用绝对 Url 并没有明显的好处。

* 此功能是否适用于受 DRM 保护的内容？

    是的，因为此功能在 HTTP 级别工作，所以它不会对任何段/片段进行解码或分析。 它并不关心内容是否已加密。

* 此功能是否适用于服务器端 Ad 插入 (SSAI) ？
    
    由于 SSAI 不会更改媒体服务源的源内容的时间戳，因此在 ad 插入) 工作之前，原始内容或主要内容 (原始内容。 此功能是否适用于 ad 内容取决于 ad 源是否支持源辅助。 例如，如果 ad 内容也托管在 Azure 媒体服务中 (相同或单独的源) ，则也将预提取 ad 内容。

* 此功能是否适用于 UHD/HEVC 内容？

    可以。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

* 请确保查看 [流式处理终结点 (源) ](streaming-endpoint-concept.md) 文档。
* 此[存储库](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)中的示例介绍了如何使用 .NET 启动默认流式处理终结点。
