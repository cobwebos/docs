---
title: "在 Azure 媒体服务中管理 Azure CDN 缓存策略 | Microsoft Docs"
description: "了解如何在 Azure 媒体服务中管理 Azure CDN 缓存策略。"
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: 
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 06bd0112eab46f3347dfb039a99641a37c2b0197
ms.openlocfilehash: 4a225332995bdf7ed03817faf5da89a98eaf1967


---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>在 Azure 媒体服务中管理 Azure CDN 缓存策略
Azure 媒体服务提供基于 HTTP 的自适应流式处理和渐进式下载。 基于 HTTP 的流式处理是高度可伸缩的功能，具有可以在代理和 CDN 层中缓存以及在客户端缓存的优点。 流式处理终结点提供常用的流式处理功能以及针对 HTTP 缓存标头的配置。 流式处理终结点设置 HTTP Cache-Control: max-age 和 Expires 标头。 你可以从 [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html) 获取更多有关 HTTP 缓存标头的信息。

## <a name="default-caching-headers"></a>默认缓存标头
默认情况下，流式处理终结点会对按需流式处理数据（实际的媒体片段/块）和清单（播放列表）应用缓存 3 天的标头。 如果是实时流式播放，流式处理终结点会对数据（实际的媒体片段/块）应用缓存 3 天的标头，对清单（播放列表）请求应用缓存 2 秒的标头。 当实时播放节目转为按需（实时存档）播放节目时，则会应用按需流式处理缓存标头。

## <a name="azure-cdn-integration"></a>Azure CDN 集成
Azure 媒体服务为流式处理终结点提供[集成 CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/)。 Cache-control 标头的应用方式与流式处理终结点到启用 CDN 的流式处理终结点的应用方式相同。 Azure CDN 使用流式处理终结点配置的值来定义内部缓存对象的生存期，并使用该值来设置交付缓存标头。 使用启用了 CDN 的流式处理终结点时，建议你不要设置小的缓存值。 设置的值过小会降低性能，导致无法充分利用 CDN。 不允许将启用了 CDN 的流式处理终结点的缓存标头设置为小于 600 秒。

> [!IMPORTANT]
> 可在 **Verizon 提供的 Azure CDN** 上实现 Azure 媒体服务与 Azure CDN 的集成。  如果希望将 **Akamai 提供的 Azure CDN** 用于 Azure 媒体服务，则必须 [手动配置终结点](cdn-create-new-endpoint.md)。  有关 Azure CDN 功能的详细信息，请参阅 [CDN 概述](cdn-overview.md)。
> 
> 

## <a name="configuring-cache-headers-with-azure-media-services"></a>使用 Azure 媒体服务配置缓存标头
你可以使用 Azure 管理门户或 Azure 媒体服务 API 来配置缓存标头值。

1. 若要使用管理门户配置缓存标头，请参阅[如何管理流式处理终结点](../media-services/media-services-portal-manage-streaming-endpoints.md)的“配置流式处理终结点”部分。
2. Azure 媒体服务 REST API，[StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl)。
3. Azure 媒体服务 .NET SDK，[StreamingEndpointCacheControl Properties](http://go.microsoft.com/fwlink/?LinkId=615302)。

## <a name="cache-configuration-precedence-order"></a>缓存配置优先顺序
1. Azure 媒体服务配置的缓存值将覆盖默认值。
2. 如果没有进行手动配置，将应用默认值。
3. 默认情况下，不管 Azure 媒体或 Azure 存储空间的配置如何，缓存 2 秒的标头适用于实时流式处理清单（播放列表），无法覆盖此值。




<!--HONumber=Jan17_HO4-->


