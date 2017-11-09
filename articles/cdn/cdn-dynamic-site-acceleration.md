---
title: "通过 Azure CDN 进行动态站点加速"
description: "深入了解动态站点加速"
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
ms.date: 08/02/2017
ms.author: v-semcev
ms.openlocfilehash: be2719e0e02c8bc69800ef4a3e7da3c3164cb9dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>通过 Azure CDN 进行动态站点加速

随着社交媒体、电子商务和超个性化 web 的迅速发展，实时生成了提供给最终用户的不断增长的内容。 用户希望获得独立于其浏览器、位置、设备或网络的快速、可靠且个性化的 web 体验。 然而，使得这些体验极具吸引力的创新却会使页面下载变慢，很可能会影响使用者体验质量。 

标准 CDN 功能包括更接近于最终用户缓存文件，以加快静态文件的交付这一功能。 但是，使用动态 web 应用程序无法在边缘位置缓存该内容，因为服务器将生成内容以响应用户行为。 加快此类内容交付比传统边缘缓存更加复杂，需要沿着从开始到交付的整个数据路径微调每个元素的端到端解决方案。 通过 Azure CDN 动态站点加速 (DSA)，显著改进了使用动态内容的网页性能。

在终结点创建期间，Akamai 和 Verizon 中的 Azure CDN 通过“优化对象”菜单提供 DSA 优化。

## <a name="configuring-cdn-endpoint-to-accelerate-delivery-of-dynamic-files"></a>配置 CDN 终结点，以加速动态文件的交付

终结点创建期间，只需选择“优化对象”属性选项下的“动态站点加速”选项，即可配置 CDN 终结点，以优化通过 Azure 门户的动态文件交付。 可使用 REST API 或任意客户端 SDK 以编程方式执行相同的操作。 

### <a name="probe-path"></a>探测路径
探测路径是一项特定于动态站点加速的功能，且创建需要一个有效探测路径。 DSA 使用一个放置在原点的较小的“探测路径”文件来优化 CDN 的网络路由配置。 可将示例文件下载和上传到你的站点，或改为使用对于探测路径约 10 KB 的原点上的现有资产（如果资产存在）。

> [!Note]
> DSA 会产生额外费用。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/cdn/)。

以下屏幕截图通过 Azure 门户演示了该过程。
 
![添加新的 CDN 终结点](./media/cdn-dynamic-site-acceleration/01_Endpoint_Profile.png) 

图 1：从 CDN 配置文件添加新的 CDN 终结点
 
![使用 DSA 创建新的 CDN 终结点](./media/cdn-dynamic-site-acceleration/02_Optimized_DSA.png)  

图 2：创建 CDN 终结点，其中选定了“动态站点加速优化”

创建 CDN 终结点后，它会将 DSA 优化应用于符合特定条件的所有文件。 以下部分对 DSA 优化进行了详细介绍。

## <a name="dsa-optimization-using-azure-cdn"></a>使用 Azure CDN 进行 DSA 优化

Azure CDN 上的动态站点加速使用以下方法加快了动态资产的交付：

-   路由优化
-   TCP 优化
-   对象预提取（仅 Akamai）
-   移动图像压缩（仅 Akamai）

### <a name="route-optimization"></a>路由优化

路由优化非常重要，因为 Internet 是一种动态的位置，其中流量和暂时中断不断更改着网络拓扑。 边界网关协议 (BGP) 是 Internet 的路由协议，但可能存在通过中间接入点 (PoP) 的更快路由。 

路由优化选择到原点的最佳路径，以便可持续访问站点和通过可能的最快和最可靠的路由将动态内容交付给最终用户。 

Akamai 网络使用技术来收集实时数据，并比较通过 Akamai 服务器中的不同节点的各种路径，以及通过开放 Internet 的默认 BGP 路由，确定原点和 CDN 边缘之间的最快路由。 这些技术避免了 Internet 出现拥塞点和长路由。 

同样，Verizon 网络也是结合使用 Anycast DNS、高容量支持 PoP 和运行状况检查来确定从客户端到源头的最佳路由数据的最佳网关的。
 
因此，可更快速、更可靠地向最终用户交付完全动态和事务性内容，即使它不可缓存。 

### <a name="tcp-optimizations"></a>TCP 优化

传输控制协议 (TCP) 是一种用于在 IP 网络上的应用程序之间交付信息的 Internet 协议套件标准。  默认情况下，需要几个来回请求以设置 TCP 连接，且需要限制以避免会导致大规模效率低下的网络拥塞。 Akamai 中的 Azure CDN 通过在 3 个区域中进行优化来处理此问题： 

 - 消除慢启动
 - 利用持久连接
 - 优化 TCP 数据包参数（仅 Akamai）

#### <a name="eliminating-slow-start"></a>消除慢启动

“慢启动”是 TCP 协议的一部分，通过限制通过网络发送的数据量防止网络拥塞。 它从发送者和接收者之间的较小拥塞窗口大小开始，直到达到最大值或检测到数据包丢失。

Akamai 和 Verizon 中的 Azure CDN 消除缓慢启动的三个步骤：

1.  Akamai 和 Verizon 的网络都通过运行状况和带宽监控来测量边缘 PoP 服务器之间的连接带宽。
2. 边缘 PoP 服务器之间共享指标，以便每个服务器都知道其周围的其他 PoP 的网络状况和服务器运行状况。  
3. CDN 边缘服务器现在可假设某些传输参数，如和其附近的其他 CDN 边缘服务器通信时的最佳窗口大小应该是什么。 此步骤意味着如果 CDN 边缘服务器之间的连接的运行状况能够支持更高的数据包数据传输，则可以增加初始拥塞窗口大小。  

#### <a name="leveraging-persistent-connections"></a>利用持久连接

使用 CDN，与直接连接到原点的用户相比，直接连接到源服务器的唯一计算机更少。 Akamai 和 Verizon 中的 Azure CDN 还将用户请求集中在一起，以建立与原点的较少连接数。

如前所述，TCP 连接在握手中来回执行多个请求，以建立新连接。 持久连接，也称为“HTTP 连接”，将重用多个 HTTP 请求的现有 TCP 连接来节省往返次数并加快交付。 

Verizon 网络还通过 TCP 连接发送周期性保持活动数据包，以防止打开的连接被关闭。

#### <a name="tuning-tcp-packet-parameters"></a>优化 TCP 数据包参数

Akamai 中的 Azure CDN 还可通过使用以下技术来优化控制服务器到服务器的连接的参数，以及减少检索嵌入在站点中的内容所需的长距离往返数：

1.  增加初始拥塞窗口，以便发送更多数据包，而无需等待确认。
2.  减少初始重新传输超时，以便检测到丢失，及更快发生重新传输。
3.  减少最小和最大重新传输超时，以减少假设数据包已在传输中丢失前的等待时间。

### <a name="object-prefetch-akamai-only"></a>对象预提取（仅 Akamai）

大多数网站包含引用映像和脚本等各种其他资源的 HTML 页。 通常情况下，当客户端请求网页时，浏览器将首先下载和分析 HTML 对象，然后对完全加载页所需的链接资产进行其他请求。 

“预提取”是一种在 HTML 服务于浏览器时，以及在浏览器做出这些对象请求之前，用于检索嵌入在 HTML 页中的图像和脚本的技术。 

当 CDN 向客户端浏览器提供 HTML 基本页时，“预提取”选项将打开，CDN 将分析 HTML 文件并对任何链接资源进行额外的请求并将其存储在其缓存中。 当客户端发出对链接资产的请求时，CDN 边缘服务器已拥有所请求的对象，且可以立即提供服务，而不必往返于原点。 此优化有益于可缓存和不可缓存内容。

### <a name="adaptive-image-compression-akamai-only"></a>自适应图像压缩（仅 Akamai）

某些设备，尤其是移动设备，有时会遇到较慢的网络速度。 在这些情况下，在网页中以较快的速度接收较小的图像对于用户更加有利，而不是等待较长时间获取完整分辨率的图像。

此功能自动监视网络质量，并在网络速度较慢时采用标准 JPEG 压缩方法，以改善交付时间。

自适应图像压缩 | 文件扩展名  
--- | ---  
JPEG 压缩 | .jpg、.jpeg、.jpe、.jig、.jgig、.jgi

## <a name="caching"></a>缓存

使用 DSA 时，默认将关闭 CDN 上的缓存，即使原点在响应中包含缓存控制/过期标头也是如此。 此设置默认已关闭，因为 DSA 通常不会用于缓存动态资源，动态资源对每个客户端都是唯一的，因此默认打开缓存可能会中断此行为。

如果网站混合有静态资源和动态资源，最好采用混合方式来获取最佳性能。 

如果在 Verizon Premium 中使用 ADN，可针对特殊情况使用规则引擎重新开启缓存。  

另一种方法是使用两个 CDN 终结点。 一个是具有 DSA 的终结点，用于传递动态资产，另一个是具有静态优化类型的终结点（例如一般的 Web 传递），用于传递可缓存的资产。 为了完成此替代方法，需要修改网页 URL 以直接链接到计划使用的 CDN 终结点上的资产。 

例如：`mydynamic.azureedge.net/index.html` 是动态页面，从 DSA 终结点进行加载。  HTML 页面引用多个静态资产，如 JavaScript 库或从静态 CDN 终结点加载的图像，如 `mystatic.azureedge.net/banner.jpg` 和 `mystatic.azureedge.net/scripts.js`。 

若要了解如何在 ASP.NET Web 应用程序中使用控制器通过特定 CDN URL来提供内容，请参见[此处](https://docs.microsoft.com/azure/cdn/cdn-cloud-service-with-cdn#controller)示例。




