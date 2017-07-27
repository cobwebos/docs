---
title: "通过 Azure CDN 的流媒体优化"
description: "优化流媒体文件，以实现顺利交付"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 582eb69ccd2830643644760753cca1c96d72128d
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017

---
# <a name="media-streaming-optimization-via-azure-cdn"></a>通过 Azure CDN 的流媒体优化 
 
高清视频在 Internet 中越来越重要，通过 Internet 高效交付此类大型文件存在各种困难。 客户希望能够在各种网络和世界各地的客户端上流畅播放 VOD 或实时视频资产。 因此，为流媒体文件提供快速高效的交付机制至关重要，以确保顺畅和愉快的使用者体验。  

但实时传送视频流媒体的交付却格外困难，不仅是因为观众的规模和数量庞大，而且还因为延迟破坏了用户协议。 观众不能提前缓存实时流，而且也不接受高延迟，因此必须及时传递视频片段。 

流式处理的请求模式也会带来一些新的挑战。 对于热门实时流或针对点播视频 (VOD) 发布的新系列，可能有数百万观众同时尝试请求同一视频流。 在这种情况下，如果尚未缓存资产，最明智的决策是请求合并，以免覆盖源服务器。
 
Akamai 的 Azure CDN 现提供一种功能，可在全球范围内向最终用户大规模地高效提供流媒体资产，并在减轻源服务器负载的同时降低延迟。 可通过带“标准 Akamai”定价层的 Azure CDN 配置文件下创建的 Azure CDN 终结点上的“优化对象”功能，使用此功能。 如果合并实时视频流和 VOD 流，则应使用 VOD 资产的“点播视频流媒体”优化类型和“常规流媒体”优化类型。

Verizon 的 Azure CDN 可直接以“常规 Web 交付”优化类型交付流媒体。
 
## <a name="configuring-cdn-endpoint-to-optimize-delivery-of-media-streaming-in-azure-cdn-from-akamai"></a>配置 CDN 终结点，以优化 Akamai 的 CDN 中的流媒体交付
 
终结点创建期间，只需选择“优化对象”属性选择下的“常规流媒体”或“点播视频流媒体”选项，即可配置 CDN 终结点，以优化通过 Azure 门户的大型文件交付。 可以使用 REST API 或任意客户端 SDK 执行此操作。 下面的屏幕截图通过 Azure 门户演示了该过程。 
  
![添加新的终结点](./media/cdn-media-streaming-optimization/01_Adding.png)

图 1：从 CDN 配置文件添加新的 CDN 终结点

![已选择“流式处理”](./media/cdn-media-streaming-optimization/02_Creating.png) 
  
图 2：创建 CDN 终结点，其中已选中“点播视频流媒体” 
 
创建 CDN 终结点后，它会将优化应用于匹配特定条件的所有文件。 以下部分对此进行了详细介绍。 
 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Akamai 的 Azure CDN 的流媒体优化
 
与通过渐进式下载或使用字节范围请求传输的大型资产截然不同，Akamai 的流媒体优化对使用单个媒体片段进行交付的实时或 VOD 流媒体十分有效。 有关这种形式的媒体交付，请查看[大型文件优化](cdn-large-file-optimization.md)。

如果使用常规媒体交付或 VOD 媒体交付优化类型，将使用具有后端优化的 CDN 网络更快速地交付媒体资产，以及根据随时间所了解的最佳做法进行的媒体资产配置。

### <a name="caching"></a>缓存

如果 Akamai 的 Azure CDN 检测到资产属于流式处理清单或片段（请参阅下面的完整列表），CDN 将使用与常规交付不同的缓存过期时间。 与往常一样，将遵守从源发送的缓存控制或过期标头，并且如果资产不是媒体资产，将使用常规 Web 交付的过期时间进行缓存。

如果大量用户请求尚不存在的片段（例如在实时流中，此时源未提供数据包），则可利用短负缓存时间进行源卸载。 较长的缓存间隔时间还有助于帮助卸载源的请求，因为通常不会修改视频内容。
 

|    | 常规<br> Web<br>调试 | 常规<br> 介质<br> 流式处理 | VOD<br>介质<br> 流式处理  
--- | --- | --- | ---
缓存 - 正 <br> HTTP 200、203、300、 <br> 301、302 和 410 | 7 天 |365 天 | 365 天   
缓存 - 负 <br> HTTP 204、305、404 <br> 和 405 | 无 | 1 秒 | 1 秒
 
### <a name="dealing-with-origin-failure"></a>处理源故障  

根据典型请求模式的最佳做法，常规媒体交付和 VOD 媒体交付也包含源超时和重试日志。 例如，由于常规媒体交付同时面向实时和 VOD 媒体交付，因此它的连接超时因实时传送视频流的时间敏感特质而要短得多。

如果连接超时，CDN 将在向客户端发送 504 网关超时错误前重试一定次数。 

如果文件匹配文件类型和大小条件列表，CDN 将使用媒体流式处理行为，否则将使用常规 Web 交付。 
   
### <a name="conditions-for-media-streaming-optimization"></a>流媒体优化的条件 

下表列出了此优化需满足的一组条件 
 
支持的流式处理类型 | 文件扩展名  
--- | ---  
Apple HLS | m3u8、m3u、m3ub、key、ts、aac
Adobe HDS | f4m、f4x、drmmeta、bootstrap、f4f、<br>Seg-Frag URL 结构 <br> （匹配正则表达式： ^(/.*)Seq(\d+)-Frag(\d+)
DASH | mpd、dash、divx、ismv、m4s、m4v、mp4、mp4v、 <br> sidx、webm、mp4a、m4a、isma
平滑流 | /manifest/,/QualityLevels/Fragments/
  

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Verizon 的 Azure CDN 的流媒体优化

Verizon 的 Azure CDN 可直接以“常规 Web 交付”优化类型交付流媒体资产。 默认情况下，CDN 上还有几个功能可直接协助交付媒体资产。

### <a name="partial-cache-sharing"></a>部分缓存共享

这允许通过 CDN 向新请求提供部分缓存的内容。 例如，向 CDN 发送的首个请求会导致缓存失误，该请求会被发送至源。 尽管此内容仍将加载到 CDN 缓存（但仍未完成），但向 CDN 发送的其他请求可以开始获取该数据。 

### <a name="cache-fill-wait-time"></a>缓存填充等待时间

与部分缓存共享结合时十分有用，缓存填充等待时间功能强制边缘服务器保存对相同资源的任何后续请求，直到 HTTP 响应标头从源服务器到达。 如果源服务器的 HTTP 响应标头在计时器过期前到达，则可以从不断增长的缓存（同时由来自源的数据进行填充）中获取被保留的所有请求。 默认情况下，缓存填充等待时间设为 3000 毫秒。 


