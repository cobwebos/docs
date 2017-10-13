---
title: "通过 Azure 内容交付网络实现媒体流式处理优化"
description: "优化流媒体文件，以实现顺畅交付"
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: v-semcev
ms.openlocfilehash: 02cd0fe30a2a14f42a16ed12f714d496bbb23b36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="media-streaming-optimization-via-the-azure-content-delivery-network"></a>通过 Azure 内容交付网络实现媒体流式处理优化 
 
随着 Internet 上高清晰度视频的使用日渐增多，这对于高效交付大型文件是一个挑战。 客户希望能够在各种网络和世界各地的客户端上流畅播放点播视频或实时视频资产。 因此，为媒体流文件提供快速高效的交付机制至关重要，可确保顺畅和愉快的使用者体验。  

但实时传送视频流媒体的交付却格外困难，因为同时观看视频的观众的规模和数量过于庞大。 长时间的延迟会导致用户离开。 观众无法提前缓存实时流，而且也不接受高延迟，因此必须及时交付视频片段。 

流式处理的请求模式也会带来一些新的挑战。 当发布热门实时流或针对点播视频发布新系列时，可能有数百万观众同时请求同一视频流。 在这种情况下，如果尚未缓存资产，则请务必执行智能请求合并，以免覆盖源服务器。
 
Akamai 的 Azure 内容交付网络现在提供一种功能，可在全球范围内向用户高效地交付流媒体资产。 该功能可减少延迟，因为它减少了源服务器上的负载。 此功能适用于标准 Akamai 定价层。 

Verizon 的 Azure 内容交付网络可直接以常规 Web 交付优化类型交付流媒体。
 
## <a name="configure-an-endpoint-to-optimize-media-streaming-in-the-azure-content-delivery-network-from-akamai"></a>配置终结点以在 Akamai 的 Azure 内容交付网络中优化媒体流式处理
 
你可以配置内容交付网络 (CDN) 终结点，以优化通过 Azure 门户的大型文件交付。 可以使用 REST API 或任意客户端 SDK 执行此操作。 下面的步骤演示了通过 Azure 门户的交付过程：

1. 若要添加新的终结点，请在“CDN 配置文件”页上，选择“终结点”。
  
    ![新建终结点](./media/cdn-media-streaming-optimization/01_Adding.png)

2. 在“针对以下项优化”下拉列表中，针对点播视频资产选择“点播视频媒体流”。 如果对实时流和点播视频流进行了组合，请选择“常规媒体流”。

    ![已选择“流式处理”](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
创建 CDN 终结点后，它会将优化应用于符合特定条件的所有文件。 以下部分介绍了此过程。 
 
## <a name="media-streaming-optimizations-for-the-azure-content-delivery-network-from-akamai"></a>通过 Akamai 的 Azure 内容交付网络实现媒体流式处理优化
 
Akamai 的媒体流式处理优化对于使用个别媒体片段进行交付的实时或点播视频媒体流很有效。 此过程与通过渐进式下载或使用字节范围请求进行传输的单个大型资产不同。 有关这种形式的媒体交付的信息，请查看[大型文件优化](cdn-large-file-optimization.md)。


常规媒体交付或点播视频媒体交付优化类型使用具备后端优化能力的 CDN 来更快地交付媒体资产。 它们还基于随着时间推移总结出的最佳做法使用媒体资产的配置。

### <a name="caching"></a>缓存

如果 Akamai 的 Azure 内容交付网络检测到资产属于流式处理清单或片段，则使用与常规 Web 交付不同的缓存过期时间。 （请参阅下表中的完整列表。）与往常一样，遵循从源发送的 Cache-Control 或 Expires 标头。 如果该资产不是媒体资产，则通过用于常规 Web 交付的过期时间进行缓存。

如果多个用户请求尚不存在的片段，较短的负缓存时间对于源卸载很有用。 例如，在那一秒无法从源获取实时流的数据包。 较长的缓存间隔时间还有助于从源卸载请求，因为通常不会修改视频内容。
 

|   | 常规 Web 传送 | 常规媒体流式处理 | 点播视频媒体流式处理  
--- | --- | --- | ---
缓存：正 <br> HTTP 200、203、300、 <br> 301、302 和 410 | 7 天 |365 天 | 365 天   
缓存：负 <br> HTTP 204、305、404 <br> 和 405 | 无 | 1 秒 | 1 秒
 
### <a name="deal-with-origin-failure"></a>处理源故障  

根据典型请求模式的最佳做法，常规媒体交付和点播视频媒体交付也包含源超时和重试日志。 例如，因为常规媒体交付适用于实时和点播视频媒体交付，因此，由于实时传送视频流的时间敏感特质，它的连接超时时间较短。

如果连接超时，CDN 会在向客户端发送“504 - 网关超时”错误前重试一定的次数。 

如果文件符合文件类型和大小条件列表，CDN 将使用媒体流式处理行为。 否则将使用常规 Web 交付。
   
### <a name="conditions-for-media-streaming-optimization"></a>流媒体优化的条件 

下表列出了媒体流式处理优化需满足的一组条件： 
 
支持的流式处理类型 | 文件扩展名  
--- | ---  
Apple HLS | m3u8、m3u、m3ub、key、ts、aac
Adobe HDS | f4m、f4x、drmmeta、bootstrap、f4f、<br>Seg-Frag URL 结构 <br> （匹配正则表达式： ^(/.*)Seq(\d+)-Frag(\d+)
DASH | mpd、dash、divx、ismv、m4s、m4v、mp4、mp4v、 <br> sidx、webm、mp4a、m4a、isma
顺畅流式处理 | /manifest/,/QualityLevels/Fragments/
  

 
## <a name="media-streaming-optimizations-for-the-azure-content-delivery-network-from-verizon"></a>针对 Verizon 的 Azure 内容交付网络的媒体流式处理优化

Verizon 的 Azure 内容交付网络可直接使用常规 Web 交付优化类型交付流媒体资产。 默认情况下，CDN 上还有几个功能可直接协助交付媒体资产。

### <a name="partial-cache-sharing"></a>部分缓存共享

部分缓存共享允许 CDN 将部分缓存的内容提供给新请求。 例如，如果向 CDN 发送的首个请求导致缓存失误，该请求会被发送至源。 尽管此不完整的内容会加载到 CDN 缓存，但向 CDN 发送的其他请求可以开始获取此数据。 

### <a name="cache-fill-wait-time"></a>缓存填充等待时间

 缓存填充等待时间功能强制边缘服务器保留对相同资源的任何后续请求，直到 HTTP 响应标头从源服务器到达。 如果来自源的 HTTP 响应标头在计时器过期前到达，则可以从不断增长的缓存提供被保留的所有请求。 同时，由来自源的数据填充缓存。 默认情况下，缓存填充等待时间设为 3,000 毫秒。 

