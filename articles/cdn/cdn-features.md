---
title: Azure 内容分发网络 (CDN) 产品功能 | Microsoft Docs
description: 了解每个 Azure 内容分发网络 (CDN) 产品支持的功能。
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
ms.topic: overview
ms.date: 04/25/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: ae57d4e9feb23f67bb4f05148d87fec96f055a7f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cdn-product-features"></a>Azure CDN 产品特征

Azure 内容分发网络 (CDN) 包括四种产品：“来自 Microsoft 的 Azure CDN Standard”、“来自 Akamai 的 Azure CDN Standard”、“来自 Verizon 的 Azure CDN Standard”和“来自 Verizon 的 Azure CDN Premium”。 下表比较了每个产品的可用功能。

| **性能特色和优化** | **标准版 Microsoft** | **标准 Akamai** | **标准 Verizon** | **高级 Verizon** |
| --- | --- | --- | --- | --- |
| [动态站点加速](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  |  | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[动态站点加速 - 自适应图像压缩](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[动态站点加速 - 对象预提取](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-akamai-only)  |  | **&#x2713;**  |  |  |
| [视频流式处理优化](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | \* | **&#x2713;**  | \* |  \* |
| [大型文件优化](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | \* | **&#x2713;**  | \* |  \* |
| [全局服务器负载均衡 (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [快速清除](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [资产预加载](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| 缓存/标头设置（使用[缓存规则](cdn-caching-rules.md)）  |  |**&#x2713;** |**&#x2713;** | |
| 缓存/标头设置（使用 [规则引擎](cdn-rules-engine.md)）  |  | | |**&#x2713;** |
| [查询字符串缓存](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| 区域缓存  |**&#x2713;** |  |  |  |
| IPv4/IPv6 双协议栈 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTP/2 支持](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **安全性** | **标准版 Microsoft** | **标准 Akamai** | **标准 Verizon** | **高级 Verizon** | 
| 对 CDN 终结点的 HTTPS 支持 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [自定义域 HTTPS](cdn-custom-ssl.md)  | **&#x2713;** | |**&#x2713;** |**&#x2713;** |
| [自定义域名支持](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [地理筛选](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [令牌身份验证](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [DDOS 保护](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [使用自己的证书](cdn-custom-ssl.md#option-2-enable-the-https-feature-with-your-own-certificate) |**&#x2713;** |  |  |  |
||||
| **分析和报告** | **标准版 Microsoft** | **标准 Akamai** | **标准 Verizon** | **高级 Verizon** | 
| [Azure 诊断日志](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Verizon 中的核心报表](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Verizon 中的自定义报表](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [高级 HTTP 报告](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [实时统计信息](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [边缘节点性能](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [实时警报](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **易于使用** | **标准版 Microsoft** | **标准 Akamai** | **标准 Verizon** | **高级 Verizon** | 
| 与[存储](cdn-create-a-storage-account-with-cdn.md)、[云服务](cdn-cloud-service-with-cdn.md)、[Web 应用](../app-service/app-service-web-tutorial-content-delivery-network.md)和[媒体服务](../media-services/media-services-portal-manage-streaming-endpoints.md)等 Azure 服务轻松集成  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| 通过 [REST API](https://msdn.microsoft.com/library/mt634456.aspx)、[.NET](cdn-app-dev-net.md)、[Node.js](cdn-app-dev-node.md) 或 [PowerShell](cdn-manage-powershell.md) 进行管理  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [可自定义的、基于规则的内容传送引擎](cdn-rules-engine.md)  |  | | |**&#x2713;** |
| URL 重定向/重写（使用[规则引擎](cdn-rules-engine.md)）  |  | | |**&#x2713;** |
| 移动设备规则（使用 [规则引擎](cdn-rules-engine.md)）  |  | | |**&#x2713;** |

\* Microsoft 和 Verizon 支持直接通过常规 Web 传送优化交付大型文件和媒体。



