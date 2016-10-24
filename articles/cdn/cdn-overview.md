<properties
	pageTitle="Azure CDN 概述 | Microsoft Azure"
	description="了解什么是 Azure 内容交付网络 (CDN)，以及如何使用它通过缓存 blob 和静态内容来交付高带宽内容。"
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/01/2016"
	ms.author="casoper"/>

# Azure 内容交付网络 (CDN) 概述

> [AZURE.NOTE] 本文档介绍 Azure 内容交付网络 (CDN) 内涵、工作原理，以及每个 Azure CDN 产品的功能。如果想要跳过此信息，直接转到有关如何创建 CDN 终结点的教程，请参阅[使用 Azure CDN](cdn-create-new-endpoint.md)。如果想要查看当前 CDN 节点位置的列表，请参阅 [Azure CDN POP 位置](cdn-pop-locations.md)。

Azure 内容交付网络 (CDN) 将静态 Web 内容缓存在按特定策略布置好的位置，以便提供最大的吞吐量，方便将内容交付给用户。CDN 为开发人员提供了一个全局解决方案，通过在世界各地的物理节点缓存内容来交付高带宽内容。

使用 CDN 来缓存网站资产的优点包括：

- 为最终用户提供更好的性能和用户体验，尤其是在使用的应用程序需要多次往返来加载内容时。
- 大幅度缩放以更好地处理瞬间的高负载，例如在产品发布活动开始时。
- 通过分发用户请求并通过边缘服务器来提供内容，可以减少发送到源的流量。


## 工作原理

![CDN 概述](./media/cdn-overview/cdn-overview.png)

1. 用户 (Alice) 使用带有特殊域名（例如 `<endpointname>.azureedge.net`）的 URL 来请求文件（也被称为资产）。DNS 将请求路由到性能最佳的接入点 (POP) 位置。通常是地理位置上最接近用户的 POP。

2. 如果 POP 中的边缘服务器在其缓存中没有文件，则边缘服务器将从原点请求文件。原点可以是 Azure Web 应用、Azure 云服务、Azure 存储帐户或任何公开可用 Web 服务器。

3. 原点将文件返回到边缘服务器，包括描述文件生存时间 (TTL) 的可选 HTTP 标头。

4. 边缘服务器缓存文件并将文件返回到原始请求者 (Alice)。TTL 过期之前，文件仍将缓存在边缘服务器中。如果原点未指定 TTL，默认 TTL 为 7 天。

5. 其他用户则可能会使用相同的 URL 请求同一文件，也可能被引向同一 POP。

6. 如果文件的 TTL 未过期，边缘服务器会从缓存返回文件。这会带来更快、响应更及时的用户体验。


## Azure CDN 功能

三种 Azure CDN 产品：**来自 Akamai 的标准 Azure CDN**、**来自 Verizon 的标准 Azure CDN** 和 **来自 Verizon 的高级 Azure CDN**。下表列出了每个产品的可用功能。

| | 标准 Akamai | 标准 Verizon | 高级 Verizon |
|-------|-----------------|------------------|-----------------|
| 与[存储](cdn-create-a-storage-account-with-cdn.md)、[云服务](cdn-cloud-service-with-cdn.md)、[Web 应用](../app-service-web/cdn-websites-with-cdn.md)和[媒体服务](../media-services/media-services-manage-origins.md#enable_cdn)等 Azure 服务轻松集成 | **&#x2713;** | **&#x2713;** | **&#x2713;**|
| 通过 [REST API](https://msdn.microsoft.com/library/mt634456.aspx)、[.NET](./cdn-app-dev-net.md)、[Node.js](./cdn-app-dev-node.md) 或 [PowerShell](./cdn-manage-powershell.md) 进行管理。 | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| HTTPS 支持 | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| 负载平衡 | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [DDOS](https://www.us-cert.gov/ncas/tips/ST04-015) 保护 | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| IPv4/IPv6 双协议栈 | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [自定义域名支持](cdn-map-content-to-custom-domain.md) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [查询字符串缓存](cdn-query-string.md) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [国家/地区筛选](cdn-restrict-access-by-country.md) | | **&#x2713;** | **&#x2713;** |
| [快速清除](cdn-purge-endpoint.md) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [资产预加载](cdn-preload-endpoint.md) | | **&#x2713;** | **&#x2713;** |
| [核心分析](cdn-analyze-usage-patterns.md) | | **&#x2713;** | **&#x2713;** |
| [HTTP/2 支持](https://msdn.microsoft.com/library/mt762901.aspx) | **&#x2713;** | | |
| [高级 HTTP 报告](cdn-advanced-http-reports.md) | | | **&#x2713;** |
| [实时统计信息](cdn-real-time-stats.md) | | | **&#x2713;** |
| [实时警报](cdn-real-time-alerts.md) | | | **&#x2713;** |
| [可自定义的、基于规则的内容传送引擎](cdn-rules-engine.md) | | | **&#x2713;** |
| 缓存/标头设置（使用[规则引擎](cdn-rules-engine.md)） | | | **&#x2713;** |
| URL 重定向/重写（使用[规则引擎](cdn-rules-engine.md)） | | | **&#x2713;** |
| 移动设备规则（使用[规则引擎](cdn-rules-engine.md)） | | | **&#x2713;** |

>[AZURE.TIP] Azure CDN 中是否有你想看到的功能？ [欢迎提供反馈](https://feedback.azure.com/forums/169397-cdn)！

## 后续步骤

若要开始使用 CDN，请参阅 [使用 Azure CDN](./cdn-create-new-endpoint.md)。

如果已经是 CDN 客户，则现在就可以通过 [Microsoft Azure 门户](https://portal.azure.com)或 [PowerShell](cdn-manage-powershell.md) 管理 CDN 终结点。

若要在实际操作中了解 CDN，请查看 [Build 2016 会议视频](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/)。

了解如何使用 [.NET](./cdn-app-dev-net.md) 或 [Node.js](./cdn-app-dev-node.md) 实现 Azure CDN 自动化。

有关定价信息，请参阅 [CDN 定价](https://azure.microsoft.com/pricing/details/cdn/)。

<!---HONumber=AcomDC_0921_2016-->