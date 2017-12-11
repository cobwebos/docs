---
title: "Azure CDN 概述 | Microsoft Docs"
description: "了解什么是 Azure 内容交付网络 (CDN)，以及如何使用它通过缓存 blob 和静态内容来交付高带宽内容。"
services: cdn
documentationcenter: 
author: dksimpson
manager: akucer
editor: 
ms.assetid: 866e0c30-1f33-43a5-91f0-d22f033b16c6
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/10/2017
ms.author: v-semcev
ms.openlocfilehash: cdcf07b6af2bd915345361c0bda2dcd9abe5486e
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2017
---
# <a name="overview-of-the-azure-content-delivery-network"></a>Azure 内容交付网络概述

Azure 内容交付网络 (CDN) 将静态 Web 内容缓存在按特定策略布置好的位置，以便提供最大的吞吐量，方便将内容安全地交付给用户。 CDN 为开发人员提供了一个全局解决方案，通过在世界各地的物理节点缓存内容来快速交付高带宽内容。 

> [!NOTE]
> 本文介绍 Azure CDN 和其工作原理，以及每个 Azure CDN 产品的功能。 若要跳过此信息去查看有关如何创建 CDN 终结点的教程，请参阅 [Azure CDN 入门](cdn-create-new-endpoint.md)。 若要查看当前 CDN 节点位置的列表，请参阅 [Azure CDN POP 位置](cdn-pop-locations.md)。

使用 CDN 来缓存网站资产的优点包括：

* 为最终用户提供更好的性能和改进的用户体验，尤其是在使用的应用程序需要多次往返来加载内容时。
* 大幅度缩放以更好地处理瞬间的高负载，例如在产品发布活动开始时。
* 通过分发用户请求并直接通过边缘服务器来提供内容，可以减少发送到源服务器的流量。

## <a name="how-it-works"></a>工作原理
![CDN 概述](./media/cdn-overview/cdn-overview.png)

1. 用户 (Alice) 使用带有特殊域名（例如 `<endpointname>.azureedge.net`）的 URL 来请求文件（也被称为资产）。 DNS 将请求路由到性能最佳的接入点 (POP) 位置，该位置通常是在地理上最靠近用户的 POP。
2. 如果 POP 中的边缘服务器在其缓存中没有文件，则边缘服务器将从原点请求文件。  原点可以是 Azure Web 应用、Azure 云服务、Azure 存储帐户或任何公开可用 Web 服务器。
3. 原点将文件返回到边缘服务器，包括描述文件生存时间 (TTL) 的可选 HTTP 标头。
4. 边缘服务器缓存文件并将文件返回到原始请求者 (Alice)。  TTL 过期之前，文件仍将缓存在边缘服务器中。  如果原点未指定 TTL，默认 TTL 为 7 天。
5. 其他用户则可能会使用相同的 URL 请求同一文件，也可能被引向同一 POP。
6. 如果文件的 TTL 未过期，边缘服务器会从缓存返回文件。 此过程会带来速度更快、响应更及时的用户体验。

## <a name="azure-cdn-features"></a>Azure CDN 功能
三种 Azure CDN 产品：**来自 Akamai 的标准 Azure CDN**、**来自 Verizon 的标准 Azure CDN** 和**来自 Verizon 的高级 Azure CDN**。  下表列出了每个产品的可用功能。

|  | 标准 Akamai | 标准 Verizon | 高级 Verizon |
| --- | --- | --- | --- |
| __性能特色和优化__ |
| [动态站点加速](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[动态站点加速 - 自适应图像压缩](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-akamai-only) | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[动态站点加速 - 对象预提取](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-akamai-only) | **&#x2713;**  |  |  |
| [视频流式处理优化](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization) | **&#x2713;**  | \* |  \* |
| [大型文件优化](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization) | **&#x2713;**  | \* |  \* |
| [全局服务器负载均衡 (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [快速清除](cdn-purge-endpoint.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [资产预加载](cdn-preload-endpoint.md) | |**&#x2713;** |**&#x2713;** |
| 缓存/标头设置（使用[缓存规则](cdn-caching-rules.md)） |**&#x2713;** |**&#x2713;** | |
| 缓存/标头设置（使用 [规则引擎](cdn-rules-engine.md)） | | |**&#x2713;** |
| [查询字符串缓存](cdn-query-string.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| IPv4/IPv6 双协议栈 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTP/2 支持](cdn-http2.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| __安全性__ |
| 对 CDN 终结点的 HTTPS 支持 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [自定义域 HTTPS](cdn-custom-ssl.md) | |**&#x2713;** |**&#x2713;** |
| [自定义域名支持](cdn-map-content-to-custom-domain.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [地理筛选](cdn-restrict-access-by-country.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [令牌身份验证](cdn-token-auth.md)|  |  |**&#x2713;**| 
| [DDOS 保护](https://www.us-cert.gov/ncas/tips/ST04-015) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| __分析和报告__ |
| [Azure 诊断日志](cdn-azure-diagnostic-logs.md) | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Verizon 中的核心报表](cdn-analyze-usage-patterns.md) | |**&#x2713;** |**&#x2713;** |
| [Verizon 中的自定义报表](cdn-verizon-custom-reports.md) | |**&#x2713;** |**&#x2713;** |
| [高级 HTTP 报告](cdn-advanced-http-reports.md) | | |**&#x2713;** |
| [实时统计信息](cdn-real-time-stats.md) | | |**&#x2713;** |
| [边缘节点性能](cdn-edge-performance.md) | | |**&#x2713;** |
| [实时警报](cdn-real-time-alerts.md) | | |**&#x2713;** |
| __易于使用__ |
| 与[存储](cdn-create-a-storage-account-with-cdn.md)、[云服务](cdn-cloud-service-with-cdn.md)、[Web 应用](../app-service/app-service-web-tutorial-content-delivery-network.md)和[媒体服务](../media-services/media-services-portal-manage-streaming-endpoints.md)等 Azure 服务轻松集成 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| 通过 [REST API](https://msdn.microsoft.com/library/mt634456.aspx)、[.NET](cdn-app-dev-net.md)、[Node.js](cdn-app-dev-node.md) 或 [PowerShell](cdn-manage-powershell.md) 进行管理。 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [可自定义的、基于规则的内容传送引擎](cdn-rules-engine.md) | | |**&#x2713;** |
| URL 重定向/重写（使用[规则引擎](cdn-rules-engine.md)） | | |**&#x2713;** |
| 移动设备规则（使用 [规则引擎](cdn-rules-engine.md)） | | |**&#x2713;** |

\* Verizon 支持直接通过常规 Web 传送交付大型文件和媒体。


> [!TIP]
> 想要 Azure CDN 增加某个功能吗？  [欢迎提供反馈](https://feedback.azure.com/forums/169397-cdn)！ 
> 
> 

## <a name="next-steps"></a>后续步骤
若要开始使用 CDN，请参阅 [Azure CDN 入门](cdn-create-new-endpoint.md)。

如果已经是 CDN 客户，则现在就可以通过 [Microsoft Azure 门户](https://portal.azure.com)或 [PowerShell](cdn-manage-powershell.md) 管理 CDN 终结点。

若要在实际操作中了解 CDN，请观看 [Build 2016 会议视频](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/)。

了解如何通过 [.NET](cdn-app-dev-net.md) 或 [Node.js](cdn-app-dev-node.md) 实现 Azure CDN 的自动化。

如需定价信息，请参阅[内容交付网络定价](https://azure.microsoft.com/pricing/details/cdn/)。

