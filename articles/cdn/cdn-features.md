---
title: 比较 Azure 内容分发网络 (CDN) 产品功能 | Microsoft Docs
description: 了解每个 Azure 内容分发网络 (CDN) 产品支持的功能。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/25/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 0d0b3b38fe39e32c6a3c7c39de5cbed4f7400d92
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58435886"
---
# <a name="compare-azure-cdn-product-features"></a>比较 Azure CDN 产品功能

Azure 内容分发网络 (CDN) 包括四个产品：“来自 Microsoft 的 Azure CDN Standard”、“来自 Akamai 的 Azure CDN Standard”、“来自 Verizon 的 Azure CDN Standard”和“来自 Verizon 的 Azure CDN Premium”。 有关将**来自 Verizon 的标准 Azure CDN** 配置文件迁移到**来自 Verizon 的高级 Azure CDN** 的信息，请参阅[将 Azure CDN 配置文件从标准 Verizon 迁移到高级 Verizon](cdn-migrate.md)。

下表比较了每个产品的可用功能。

| **性能特色和优化** | **标准版 Microsoft** | **标准 Akamai** | **标准 Verizon** | **高级 Verizon** |
| --- | --- | --- | --- | --- |
| [动态站点加速](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | 通过 [Azure Front Door 服务](https://docs.microsoft.com/azure/frontdoor/front-door-overview)提供 | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[动态站点加速 - 自适应图像压缩](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[动态站点加速 - 对象预提取](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [常规 Web 传送优化](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;**，如果平均文件大小小于 10 MB，请选择此优化类型  | **&#x2713;** |  **&#x2713;** |
| [视频流式处理优化](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | 通过常规 Web 传送 | **&#x2713;**  | 通过常规 Web 传送 |  通过常规 Web 传送 |
| [大型文件优化](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | 通过常规 Web 传送 | **&#x2713;**，如果平均文件大小大于 10 MB，请选择此优化类型   | 通过常规 Web 传送 |  通过常规 Web 传送 |
| 更改优化类型 | |**&#x2713;** | | |
| 原点端口 |所有 TCP 端口 |[允许的原点端口](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |所有 TCP 端口 |所有 TCP 端口 |
| [全局服务器负载均衡 (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [快速清除](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;**，目前 Akamai 的 Azure CDN 不支持全部清除和通配符清除 |**&#x2713;** |**&#x2713;** |
| [资产预加载](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| 缓存/标头设置（使用[缓存规则](cdn-caching-rules.md)）  |  |**&#x2713;** |**&#x2713;** | |
| 可自定义的基于规则的内容交付引擎（使用[规则引擎](cdn-rules-engine.md)）  |  | | |**&#x2713;** |
| 缓存/标头设置（使用 [规则引擎](cdn-rules-engine.md)）  |  | | |**&#x2713;** |
| URL 重定向/重写（使用 [规则引擎](cdn-rules-engine.md)）  |  | | |**&#x2713;** |
| 移动设备规则（使用 [规则引擎](cdn-rules-engine.md)）  |  | | |**&#x2713;** |
| [查询字符串缓存](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| IPv4/IPv6 双协议栈 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTP/2 支持](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **安全性** | **标准版 Microsoft** | **标准 Akamai** | **标准 Verizon** | **高级 Verizon** | 
| 对 CDN 终结点的 HTTPS 支持 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [自定义域 HTTPS](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [自定义域名支持](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [地理筛选](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [令牌身份验证](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [DDOS 保护](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [使用自己的证书](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  |  |  |
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
| 轻松与[存储](cdn-create-a-storage-account-with-cdn.md)、[Web 应用](cdn-add-to-web-app.md)和[媒体服务](../media-services/media-services-portal-manage-streaming-endpoints.md)等 Azure 服务集成  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| 通过 [REST API](https://msdn.microsoft.com/library/mt634456.aspx)、[.NET](cdn-app-dev-net.md)、[Node.js](cdn-app-dev-node.md) 或 [PowerShell](cdn-manage-powershell.md) 进行管理  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [压缩 MIME 类型](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |仅限默认值 |可配置性 |可配置性  |可配置性  |
| 压缩编码  |gzip、brotli |gzip |gzip、deflate、bzip2、brotili  |gzip、deflate、bzip2、brotili  |






