---
title: "通过 Azure 内容交付网络实现大型文件下载优化"
description: "深入介绍大型文件下载优化"
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
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 7a5d5d1d0de24ebb0a5115ede1e572f38454bd78
ms.contentlocale: zh-cn
ms.lasthandoff: 07/21/2017

---
# <a name="large-file-download-optimization-via-the-azure-content-delivery-network"></a>通过 Azure 内容交付网络实现大型文件下载优化

得益于增强的功能、改进的图形和丰富的媒体内容，通过 Internet 交付内容的文件大小持续增长。 这一增长存在许多推动因素：宽带渗透、容量更大的存储设备、高清晰度视频的普遍增长以及连接到 Internet 的设备 (IoT)。 为了确保顺畅和愉快的使用者体验，为大型文件提供快速高效的交付机制至关重要。

大型文件的交付面临多项挑战。 首先，大型文件的平均下载时间可能很长，因为应用程序可能无法逐次下载所有数据。 在某些情况下，应用程序可能先下载文件的最后一部分，然后再下载第一部分。 当仅请求文件的一小部分或者用户暂停下载时，下载会失败。 下载还可能会延迟，直至内容交付网络 (CDN) 从源服务器检索整个文件。 

其次，用户的计算机与文件之间的延迟将决定他们查看内容的速度。 此外，网络拥塞和容量问题也会影响吞吐量。 服务器和用户之间的距离越远，就越有可能导致丢包，进而降低质量。 因吞吐量有限和丢包率增加导致的质量下降可能会使文件完成下载的等待时间增加。 

第三，许多大型文件都没有全部交付。 用户可能中途取消下载文件，或者仅观看长 MP4 视频的前几分钟。 因此，许多软件和媒体交付公司希望仅交付用户请求的部分文件。 所请求部分的高效分发可降低来自源服务器的出口流量。 高效分发还会减少源服务器上的内存以及 I/O 压力。 

Akamai 的 Azure 内容交付网络现在提供一种功能，可在全球范围内向用户高效地交付大型文件。 该功能可减少延迟，因为它减少了源服务器上的负载。 此功能适用于标准 Akamai 定价层。

## <a name="configure-a-cdn-endpoint-to-optimize-delivery-of-large-files"></a>配置 CDN 终结点以优化大型文件交付

可以配置 CDN 终结点，以优化通过 Azure 门户的大型文件交付。 可以使用 REST API 或任意客户端 SDK 执行此操作。 下面的步骤演示了通过 Azure 门户的交付过程。

1. 若要添加新的终结点，请在“CDN 配置文件”页上，选择“终结点”。

    ![新建终结点](./media/cdn-large-file-optimization/01_Adding.png)  
 
2. 在“针对以下项优化”下拉列表中，选择“大型文件下载”。

    ![选择的“大型文件优化”](./media/cdn-large-file-optimization/02_Creating.png)


创建 CDN 终结点后，它会将大型文件优化应用于符合特定条件的所有文件。 以下部分介绍了此过程。

## <a name="optimize-for-delivery-of-large-files-with-the-azure-content-delivery-network-from-akamai"></a>借助 Akamai 提供的 Azure 内容交付网络优化大型文件交付

大型文件优化类型功能可启用网络优化和配置，以便更快、更敏捷地交付大型文件。 Akamai 的常规 Web 交付只能缓存大小不到 1.8 GB 的文件，并且只能传输（而非缓存）不大于 150 GB 的文件。 而大型文件优化可缓存最大 150 GB 的文件。

当满足某些条件时，大型文件优化会很有效。 这些条件包括源服务器的运行方式以及所请求文件的大小和类型。 在我们深入探讨这些主题之前，你应了解优化的工作原理。 

### <a name="object-chunking"></a>对象区块 

Akamai 的 Azure 内容交付网络使用一项称为对象区块的技术。 当请求大型文件时，CDN 会从源检索文件的较小部分。 CDN 边缘/POP 服务器收到完整的或字节范围的文件请求后，会检查文件类型是否支持此优化。 还会检查文件类型是否满足文件大小要求。 如果文件大小超过 10 MB，则 CDN 边缘服务器会以 2 MB 的区块为单位从源请求文件。 

区块到达 CDN 边缘后，会立即缓存并提供给用户。 然后 CDN 会并行预提取下一个区块。 此预提取可确保内容先于用户一个区块，这可以减少延迟。 此过程将继续，直到整个文件下载完成（如果已请求），所有字节范围都可用（如果已请求），或客户端终止连接。 

有关字节范围请求的详细信息，请参阅 [RFC 7233](https://tools.ietf.org/html/rfc7233)。

CDN 会在收到区块后进行缓存。 无需在 CDN 缓存上缓存整个文件。 文件或字节范围的后续请求将从 CDN 缓存提供。 如果未在 CDN 上缓存所有区块，将使用预提取从源请求区块。 此优化取决于源服务器能否支持字节范围请求。 如果源服务器不支持字节范围请求，则此优化不会生效。 

### <a name="caching"></a>缓存
大型文件优化使用与常规 Web 交付不同的默认缓存过期时间。 它可根据 HTTP 响应代码区分正缓存和负缓存。 如果源服务器通过响应中的 Cache-Control 或 Expires 标头指定了过期时间，则 CDN 将遵循该值。 如果源未指定此时间，并且该文件与此优化类型的类型和文件条件相符，则 CDN 会针对大型文件优化使用默认值。 否则，CDN 会使用常规 Web 交付的默认值。


|    | 常规 Web | 大型文件优化 
--- | --- | --- 
缓存：正 <br> HTTP 200、203、300、 <br> 301、302 和 410 | 7 天 |1 天  
缓存：负 <br> HTTP 204、305、404 <br> 和 405 | 无 | 1 秒 

### <a name="deal-with-origin-failure"></a>处理源故障

源读取超时时长从常规 Web 交付的两秒钟增加到大型文件优化类型的两分钟。 超时增加是由于文件大小较大，为了避免过早发生连接超时。

如果连接超时，CDN 会在向客户端发送“504 - 网关超时”错误前重试一定的次数。 

### <a name="conditions-for-large-file-optimization"></a>大型文件优化的条件

下表列出了大型文件优化需满足的一组条件：

条件 | 值 
--- | --- 
支持的文件类型 | 3g2、3gp、asf、avi、bz2、dmg、exe、f4v、flv、 <br> gz、hdp、iso、jxr、m4v、mkv、mov、mp4、 <br> mpeg、mpg、mts、pkg、qt、rm、swf、tar、 <br> tgz、wdp、webm、webp、wma、wmv、zip  
文件大小下限 | 10 MB 
文件大小上限 | 150 GB 
源服务器特点 | 必须支持字节范围请求 

## <a name="optimize-for-delivery-of-large-files-with-the-azure-content-delivery-network-from-verizon"></a>借助 Verizon 的 Azure 内容交付网络优化大型文件交付

Verizon 的 Azure 内容交付网络可交付大型文件，对于文件大小不设限制。 默认情况下，会启用其他功能以更快地交付大型文件。

### <a name="complete-cache-fill"></a>完整缓存填充

默认的完整缓存填充功能可在初始请求放弃或丢失时，使 CDN 能够将文件拉取到缓存中。 

完整缓存填充最适用于大型资产。 通常情况下，用户不会从头到尾下载这些资产。 而是使用渐进式下载。 默认行为会强制边缘服务器启动对源服务器中资产的后台提取。 然后，资产位于边缘服务器的本地缓存中。 缓存整个对象后，边缘服务器会执行已缓存对象的 CDN 字节范围请求。

可通过 Verizon 高级层中的规则引擎禁用默认行为。

### <a name="peer-cache-fill-hot-filing"></a>对等缓存填充的热填充

默认对等缓存填充的热填充功能使用复杂的专有算法。 它基于带宽和聚合请求度量值使用其他边缘缓存服务器，以满足大型、高度热门对象的客户端请求。 此功能可防止出现大量额外请求被发送到用户的源服务器的情况。 

### <a name="conditions-for-large-file-optimization"></a>大型文件优化的条件

Verizon 的优化功能默认开启。 对文件大小上限没有任何限制。 

## <a name="additional-considerations"></a>其他注意事项

请了解此优化类型的以下其他方面的特点。
 
### <a name="azure-content-delivery-network-from-akamai"></a>Akamai 的 Azure 内容交付网络

- 分块过程会向源服务器发出其他请求。 但是，从源交付的数据总量要小得多。 分块使 CDN 具备更好的缓存性能。

- 源的内存和 I/O 压力会降低，因为交付的是文件的较小部分。

- 对于在 CDN 上缓存的区块，在内容过期或从缓存中逐出之前，不会向源发出其他请求。

- 用户可向 CDN 发送范围请求，系统会将其视为普通文件进行处理。 仅当文件类型有效且字节范围在 10 MB 到 150 GB 之间时，才会应用优化。 如果所请求的平均文件大小小于 10 MB，则可能需要改为使用常规 Web 交付。

### <a name="azure-content-delivery-network-from-verizon"></a>Verizon 的 Azure 内容交付网络

常规 Web 交付优化类型能够交付大型文件。

