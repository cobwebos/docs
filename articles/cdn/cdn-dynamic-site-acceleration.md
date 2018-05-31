---
title: 通过 Azure CDN 进行动态站点加速
description: Azure CDN 支持对具有动态内容的文件进行动态站点加速 (DSA) 优化。
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: rli; v-deasim
ms.openlocfilehash: 66032a68634be5b52bbfa544d0a3bcd3d1090652
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359979"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>通过 Azure CDN 进行动态站点加速

随着社交媒体、电子商务和超个性化 web 的迅速发展，实时生成了提供给最终用户的不断增长的内容。 用户希望获得独立于其浏览器、位置、设备或网络的快速、可靠且个性化的 Web 体验。 然而，使得这些体验极具吸引力的创新却会使页面下载变慢，很可能会影响使用者体验质量。 

标准内容分发网络 (CDN) 功能包括更接近于最终用户缓存文件，以加快静态文件的分发这一功能。 但是，使用动态 web 应用程序无法在边缘位置缓存该内容，因为服务器将生成内容以响应用户行为。 加快此类内容交付比传统边缘缓存更加复杂，需要沿着从开始到交付的整个数据路径微调每个元素的端到端解决方案。 通过 Azure CDN 动态站点加速 (DSA) 优化，显著改进了包含动态内容的网页性能。

在终结点创建期间，Akamai 的 Azure CDN 和 Verizon 的 Azure CDN 都通过“优化对象”菜单提供 DSA 优化。

> [!Important]
> 对于“Akamai 的 Azure CDN”配置文件，可以在创建 CDN 终结点后更改其优化设置。
>   
> 对于“Verizon 的 Azure CDN”配置文件，无法在创建 CDN 终结点后更改其优化设置。

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>配置 CDN 终结点，以加速动态文件的交付

若要将 CDN 终结点配置为优化动态文件的交付，可以使用 Azure 门户、REST API 或任何客户端 SDK 以编程方式执行相同的操作。 

**若要使用 Azure 门户为 CDN 终结点配置 DSA 优化，请执行以下操作：**

1. 在“CDN 配置文件”页中，选择“终结点”。

   ![添加新的 CDN 终结点](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   此时会显示“添加终结点”窗格。

2. 在“优化对象”下，选择“动态站点加速”。

    ![创建使用 DSA 的新 CDN 终结点](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. 对于“探测路径”，输入文件的有效路径。

    探测路径是一项特定于 DSA 的功能，创建时需要一个有效路径。 DSA 使用一个放置在源服务器上的较小“探测路径”文件来优化 CDN 的网络路由配置。 如需探测路径文件，可以下载示例文件并将其上传到你的站点，或者使用源上现有的约 10 KB 大小的资产。

4. 输入其他所需的终结点选项（有关详细信息，请参阅[新建 CDN 终结点](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)），然后选择“添加”。

   创建 CDN 终结点后，它会将 DSA 优化应用于符合特定条件的所有文件。 


**若要为现有终结点配置 DSA（仅限“Akamai 的 Azure CDN”配置文件），请执行以下操作：**

1. 在“CDN 配置文件”页中，选择要修改的终结点。

2. 在左窗格中，选择“优化”。 

   此时将显示“优化”页。

3. 在“优化对象”下，选择“动态站点加速”，然后选择“保存”。

> [!Note]
> DSA 会产生额外费用。 有关详细信息，请参阅[内容分发网络定价](https://azure.microsoft.com/pricing/details/cdn/)。

## <a name="dsa-optimization-using-azure-cdn"></a>使用 Azure CDN 进行 DSA 优化

Azure CDN 上的动态站点加速使用以下方法加快了动态资产的交付：

-   [路由优化](#route-optimization)
-   [TCP 优化](#tcp-optimizations)
-   [对象预提取（仅限 Akamai 的 Azure CDN）](#object-prefetch-azure-cdn-from-akamai-only)
-   [自适应图像压缩（仅限 Akamai 的 Azure CDN）](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>路由优化

路由优化非常重要，因为 Internet 是一种动态的位置，其中流量和暂时中断不断更改着网络拓扑。 边界网关协议 (BGP) 是 Internet 的路由协议，但可能存在通过中间接入点 (PoP) 的更快路由。 

路由优化选择到原点的最佳路径，以便可持续访问站点和通过可能的最快和最可靠的路由将动态内容交付给最终用户。 

Akamai 网络使用技术来收集实时数据，并比较通过 Akamai 服务器中的不同节点的各种路径，以及通过开放 Internet 的默认 BGP 路由，确定原点和 CDN 边缘之间的最快路由。 这些技术避免了 Internet 出现拥塞点和长路由。 

同样，Verizon 网络也是结合使用 Anycast DNS、高容量支持 PoP 和运行状况检查来确定从客户端到源头的最佳路由数据的最佳网关的。
 
因此，可更快速、更可靠地向最终用户交付完全动态和事务性内容，即使它不可缓存。 

### <a name="tcp-optimizations"></a>TCP 优化

传输控制协议 (TCP) 是一种用于在 IP 网络上的应用程序之间交付信息的 Internet 协议套件标准。  默认情况下，需要几个来回请求以设置 TCP 连接，且需要限制以避免会导致大规模效率低下的网络拥塞。 **Akamai 的 Azure CDN** 通过在三个方面进行优化来处理此问题： 

 - [消除 TCP 慢启动](#eliminating-tcp-slow-start)
 - [利用持久连接](#leveraging-persistent-connections)
 - [优化 TCP 数据包参数](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>消除 TCP 慢启动

TCP 慢启动是 TCP 协议的算法，用于通过限制通过网络发送的数据量来防止网络拥塞。 它从发送者和接收者之间的较小拥塞窗口大小开始，直到达到最大值或检测到数据包丢失。

 **Akamai 的 Azure CDN** 和 **Verizon 的 Azure CDN** 配置文件使用以下三个步骤消除 TCP 慢启动：

1. 通过监视运行状况和带宽来测量边缘 PoP 服务器之间的连接带宽。
    
2. 在边缘 PoP 服务器之间共享指标，以便每个服务器都知道其周围的其他 PoP 的网络状况和服务器运行状况。  
    
3. CDN 边缘服务器对某些传输参数做出假设，如与其附近的其他 CDN 边缘服务器通信时的最佳窗口大小应该是什么。 此步骤意味着如果 CDN 边缘服务器之间的连接的运行状况能够支持更高的数据包数据传输，则可以增加初始拥塞窗口大小。  

#### <a name="leveraging-persistent-connections"></a>利用持久连接

使用 CDN，与直接连接到原点的用户相比，直接连接到源服务器的唯一计算机更少。 Azure CDN 还将用户请求集中在一起，以与源建立较少连接。

如前所述，需要多个握手请求才能建立 TCP 连接。 持久连接（由 `Keep-Alive` HTTP 标头实现）将重用多个 HTTP 请求的现有 TCP 连接来节省往返次数并加快交付。 

**Verizon 的 Azure CDN** 还通过 TCP 连接发送周期性保持活动数据包，以防止打开的连接被关闭。

#### <a name="tuning-tcp-packet-parameters"></a>优化 TCP 数据包参数

**Akamai 的 Azure CDN** 可通过使用以下技术来优化控制服务器到服务器连接的参数，以及减少检索嵌入在站点中的内容所需的长距离往返数：

- 增加初始拥塞窗口，以便发送更多数据包，而无需等待确认。
- 减少初始重新传输超时，以便检测到丢失，及更快发生重新传输。
- 减少最小和最大重新传输超时，以减少假设数据包已在传输中丢失前的等待时间。

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>对象预提取（仅限 Akamai 的 Azure CDN）

大多数网站包含引用映像和脚本等各种其他资源的 HTML 页。 通常情况下，当客户端请求网页时，浏览器将首先下载和分析 HTML 对象，然后对完全加载页所需的链接资产进行其他请求。 

“预提取”是一种在 HTML 服务于浏览器时，以及在浏览器做出这些对象请求之前，用于检索嵌入在 HTML 页中的图像和脚本的技术。 

如果 CDN 向客户端浏览器提供 HTML 基本页时打开了“预提取”选项，CDN 将分析 HTML 文件并对任何链接资源进行额外的请求并将其存储在其缓存中。 当客户端发出对链接资产的请求时，CDN 边缘服务器已拥有所请求的对象，且可以立即提供服务，而不必往返于原点。 此优化有益于可缓存和不可缓存内容。

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>自适应图像压缩（仅限 Akamai 的 Azure CDN）

某些设备，尤其是移动设备，有时会遇到较慢的网络速度。 在这些情况下，在网页中以较快的速度接收较小的图像对于用户更加有利，而不是等待较长时间获取完整分辨率的图像。

此功能自动监视网络质量，并在网络速度较慢时采用标准 JPEG 压缩方法，以改善交付时间。

自适应图像压缩 | 文件扩展名  
--- | ---  
JPEG 压缩 | .jpg、.jpeg、.jpe、.jig、.jgig、.jgi

## <a name="caching"></a>缓存

使用 DSA 时，默认将关闭 CDN 上的缓存，即使源在响应中包含 `Cache-Control` 或 `Expires` 标头也是如此。 DSA 通常用于因对每个客户端是唯一的而不应缓存的动态资产。 缓存可能会中断此行为。

如果网站混合有静态资源和动态资源，最好采用混合方式来获取最佳性能。 

对于 **Verizon 提供的标准 Azure CDN** 和 **Akamai 提供的标准 Azure CDN** 配置文件，可以通过使用[缓存规则](cdn-caching-rules.md)为特定 DSA 终结点启用缓存。

访问缓存规则：

1. 从“CDN 配置文件”页面中，在“设置”下，选择“缓存规则”。  
    
    ![CDN 缓存规则按钮](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    “缓存规则”页随即打开。

2. 创建一个全局的或自定义的缓存规则，来为 DSA 终结点启用缓存。 

仅对于“Verizon 提供的高级 Azure CDN”配置文件，可以通过使用[规则引擎](cdn-rules-engine.md)为特定 DSA 终结点启用缓存。 创建的任何规则仅影响配置文件中已针对 DSA 进行优化的这些终结点。 

访问规则引擎：
    
1. 从“CDN 配置文件”页中，选择“管理”。  
    
    ![CDN 配置文件管理按钮](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    CDN 管理门户打开。

2. 在 CDN 管理门户中，依次选择 **ADN**、“规则引擎”。 

    ![适用于 DSA 的规则引擎](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



或者，可以使用两个 CDN 终结点：一个终结点使用 DSA 进行优化，用于传送动态资产，另一个终结点使用静态优化类型进行优化（例如常规 Web 交付），用于传送可缓存的资产。 修改网页 URL 以直接链接到计划使用的 CDN 终结点上的资产。 

例如：`mydynamic.azureedge.net/index.html` 是动态页面，从 DSA 终结点进行加载。  HTML 页面引用多个静态资产，如 JavaScript 库或从静态 CDN 终结点加载的图像，如 `mystatic.azureedge.net/banner.jpg` 和 `mystatic.azureedge.net/scripts.js`。 



