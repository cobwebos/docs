---
title: "通过 Azure CDN 优化大型文件下载"
description: "深入了解如何优化大型文件下载"
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
ms.openlocfilehash: 27e202b05f86eeee7071f3fae145caeba3d66827
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017

---
# <a name="large-file-download-optimization-via-azure-cdn"></a>通过 Azure CDN 优化大型文件下载

得益于增强的功能、改进的图形和丰富的媒体内容，通过 Internet 传送内容的文件大小一直在稳步增长。 这存在许多推动因素，包括宽带渗透、成本较低且容量更大的存储设备、高清晰度视频的传播、连接到 Internet 的设备 (IoT) 等。为了确保顺畅和愉快的使用者体验，为此类大型文件提供更快速高效的交付机制至关重要。 

交付大型文件存在几个固有难题。 首先，下载大型文件的平均时间可能很长，因为许多应用程序可能无法逐次下载所有数据。 在某些情况下，应用程序可能先下载文件的最后部分，然后再下载第一部分。 因此，如果仅请求了少量文件，或用户暂停下载，则可能导致下载失败或延迟，直至 CDN 从源检索到整个文件。 

其次，随着 Internet 上大型文件的增多，用户经常发现，用户与文件之间的延迟最终决定了吞吐量或用户能够查看内容的速度。 此外，网络拥塞和容量问题会进一步影响吞吐量；这些问题，连同服务器与最终用户之间的较大距离会增加数据包丢失的概率，进而降低质量。 因吞吐量有限和数据包丢失概率增加导致的质量下降本身可能表现出，文件完成下载的等待时间显著增加。 

最后，许多大型文件都没有全部交付。 用户可能中途取消下载文件，或仅观看长 MP4 视频的前几分钟。 因此，对许多媒体和软件交付公司而言，提供仅最终用户请求的部分文件可能有所帮助。 这样，仅请求的部分被有效地分发到 Internet 的最远距离，从而减少源的出口流量，并降低源服务器上的内存和 IO 压力。 

Akamai 的 Azure CDN 现提供一种功能，可在全球范围内向最终用户大规模地高效提供大型文件，并在减轻源服务器负载的同时降低延迟。 可通过带“标准 Akamai”定价层的 Azure CDN 配置文件下创建的 Azure CDN 终结点上的“优化对象”功能，使用此功能。

## <a name="configuring-cdn-endpoint-to-optimize-delivery-of-large-files"></a>配置 CDN 终结点以优化大型文件交付

终结点创建期间，只需选择“优化对象”属性下的“大型文件下载”选项，即可配置 CDN 终结点，以优化通过 Azure 门户的大型文件交付。 可以使用 REST API 或任意客户端 SDK 执行此操作。 下面的屏幕截图通过 Azure 门户演示了该过程。

![新建 CDN 终结点](./media/cdn-large-file-optimization/01_Adding.png)  
 
图 1：从 CDN 配置文件添加新的 CDN 终结点
 
![已选择 LFO](./media/cdn-large-file-optimization/02_Creating.png)

图 2：创建 CDN 终结点，其中选定了“大型文件下载优化”

创建 CDN 终结点后，它会将大型文件优化应用于符合特定条件的所有文件。 以下部分对此进行了详细介绍。

## <a name="optimizing-for-delivery-of-large-files-with-azure-cdn-from-akamai"></a>使用 Akamai 的 Azure CDN 优化大型文件交付

对于 Akamai 的 Azure CDN，可使用大型文件优化类型功能来启用网络优化和配置，以便更快、更敏捷地交付大型文件。 Akamai 的常规 Web 交付功能只能缓存低于 1.8 GB 的文件，可优化（但不可缓存）高达 150 GB 的文件，而大型文件优化允许缓存高达 150 GB 的文件。

如果满足关于源服务器运行方式、请求的文件类型和请求的文件大小等特定条件，大型文件优化会非常高效。 深入探讨上述每个条件之前，请务必了解优化工作原理的高级概述。 

### <a name="object-chunking"></a>对象区块 

Akamai 的 Azure CDN 采用一种称为对象区块的技术，通过这项技术，CDN 在收到大型文件请求时，可从源检索该文件的较小部分。 CDN 边缘/POP 服务器收到来自最终用户的完整或字节范围文件请求时，会首先检查该文件类型是否包含在此优化支持的文件类型列表中，以及其是否满足文件大小要求。 如果文件大小大于 10 MB，则 CDN 边缘服务器会从源服务器以 2 MB 为单位的区块开始处理文件请求。 区块抵达 CDN 边缘后，系统会将其缓存并立即提供给最终用户，而 CDN 会“预提取”下一个并行区块。此“预提取”操作可通过为用户预留 1 个区块并降低最终用户的延迟，确保内容尽快可用。 此过程继续进行，直至整个文件下载完成（如果最终用户请求的是整个文件）、直至所有请求的字节范围可用（如果最终用户请求的是字节范围），或者直至客户端终止连接。 

若要详细了解字节范围请求，请参阅 [RFC 7233](https://tools.ietf.org/html/rfc7233)。

CDN 会缓存其收到的任何区块，无需将整个文件缓存在 CDN 缓存中。 针对文件或字节范围的后续请求将从 CDN 缓存提供；如果 CDN 上未缓存所有区块，则将使用“预提取”从源请求区块。 可见，这种优化依赖于支持字节范围请求的源服务器。 如果源服务器不支持字节范围请求，则此优化不会生效。 

### <a name="caching"></a>缓存
大型文件使用的默认缓存到期时间与常规交付不同。 它可根据 HTTP 响应代码区分正缓存和负缓存。 如果源通过响应中的 Cache-Control 或 Expires 标头指定了用作到期时间的时间，则 CDN 将始终遵循该值。 如果源未指定此时间，并且该文件与此优化类型列表中的文件类型和文件大小条件相符，则 CDN 会使用大型文件优化的默认值。 否则，CDN 会使用常规 Web 交付的默认值。

 
|    | 常规 Web | 大型文件优化 
--- | --- | --- 
缓存 - 正 <br> HTTP 200、203、300、 <br> 301、302 和 410 | 7 天 |1 天  
缓存 - 负 <br> HTTP 204、305、404 <br> 和 405 | 无 | 1 秒 

### <a name="dealing-with-origin-failure"></a>处理源故障

在大型文件优化类型中，源读取超时时间从常规 Web 交付的 2 秒增加到了 2 分钟，以防止较大文件因提前超时而断开连接。

与常规 Web 交付一样，如果连接超时，我们将在向客户端发送 504 网关超时错误前重试一定次数。 

### <a name="conditions-for-large-file-optimization"></a>大型文件优化的条件

下表列出了大型文件优化需满足的一组条件：

条件 | 值 
--- | --- 
支持的文件类型 | 3g2、3gp、asf、avi、bz2、dmg、exe、f4v、flv、 <br> gz、hdp、iso、jxr、m4v、mkv、mov、mp4、 <br> mpeg、mpg、mts、pkg、qt、rm、swf、tar、 <br> tgz、wdp、webm、webp、wma、wmv、zip  
文件大小下限 | 10 MB 
文件大小上限 | 150 GB 
源服务器特点 | 必须支持字节范围请求 

## <a name="optimizing-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>使用 Verizon 的 Azure CDN 优化大型文件交付

Verizon 的 Azure CDN 能够交付大型文件（无文件大小上限），并具有各种功能，这些功能默认开启以使大型文件交付更加快捷。

### <a name="complete-cache-fill"></a>完成缓存填充

Verizon 的 Azure CDN 有一种称为“完成缓存填充”的默认功能，通过此功能，CDN 可在初始请求被丢弃或丢失时，将文件拉入缓存。 

此功能最适用于用户在下载时通常会半途而废的大型资产（例如渐进式下载视频）。 因此，Verizon 的 Azure CDN 默认启用此功能。 此默认行为是为了强制边缘服务器启动对源服务器中资产的后台获取。 然后，资产将位于边缘服务器的本地缓存中。 缓存整个对象后，边缘即可满足已缓存对象的 CDN 字节范围请求。

可通过 Verizon 高级层中的规则引擎禁用默认的“完成缓存填充”行为。

### <a name="peer-cache-fill-hotfiling"></a>对等缓存填充 - 热填充

这是 Verizon 的 Azure CDN 的默认功能，通过此功能，复杂的专有算法可以基于带宽和聚合请求等指标，利用其他边缘缓存服务器来满足大型、极常用对象的客户端请求。 这可防止出现大量额外请求被发送到客户的源服务器的情况。 

### <a name="conditions-for-large-file-optimization"></a>大型文件优化的条件

这些 Verizon 的优化功能默认开启，并且没有文件大小上限。 

## <a name="additional-considerations"></a>其他注意事项

使用此优化类型时，需考虑一些其他方面。
 
### <a name="azure-cdn-from-akamai"></a>Akamai 的 Azure CDN

- 区块过程会导致对源服务器的额外请求，但从源交付的数据总量要小得多，因为区块有利于优化 CDN 的缓存特性。
- 由于只需交付较小文件，这还有利于降低源的内存和 IO 压力。 
- 对于缓存于 CDN 上的区块，除非内容从缓存中失效或由于其他原因导致内容从缓存中被逐出，否则不会有针对源的附加请求。 
- 用户可向 CDN 发送范围请求，系统会将其视为任何普通文件处理。 仅当文件类型有效且字节范围介于 10 MB 到 150 GB 之间时，才能应用优化。如果请求的平均文件大小低于 10 MB，可能需要改用常规 Web 交付。

### <a name="azure-cdn-from-verizon"></a>Verizon 的 Azure CDN

常规 Web 交付优化能够交付大型文件。

