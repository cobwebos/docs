---
title: 在 Azure 媒体服务中管理 Azure CDN 缓存策略 | Microsoft Docs
description: 了解如何在 Azure 媒体服务中管理 Azure CDN 缓存策略。
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: ''
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: dce8d588a78b028223776e3ade737d4abd26094b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>在 Azure 媒体服务中管理 Azure CDN 缓存策略
Azure 媒体服务提供基于 HTTP 的自适应流式处理和渐进式下载。 基于 HTTP 的流式处理是高度可缩放的功能，具有可以在代理和 CDN 层中缓存以及在客户端缓存的优点。 流式处理终结点提供常用的流式处理功能以及针对 HTTP 缓存标头的配置。 流式处理终结点设置 HTTP Cache-Control: max-age 和 Expires 标头。 可以从 [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html) 获取更多有关 HTTP 缓存标头的信息。

## <a name="default-caching-headers"></a>默认缓存标头
默认情况下，流式处理终结点会对按需流式处理数据（实际的媒体片段/块）和清单（播放列表）应用缓存 3 天的标头。 如果是实时流式播放，流式处理终结点会对数据（实际的媒体片段/块）应用缓存 3 天的标头，对清单（播放列表）请求应用缓存 2 秒的标头。 当实时播放节目转为按需（实时存档）播放节目时，则会应用按需流式处理缓存标头。

## <a name="azure-cdn-integration"></a>Azure CDN 集成
Azure 媒体服务为流式处理终结点提供[集成 CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/)。 Cache-control 标头的应用方式与流式处理终结点到启用 CDN 的流式处理终结点的应用方式相同。 Azure CDN 使用流式处理终结点配置的值来定义内部缓存对象的生存期，并使用该值来设置交付缓存标头。 使用启用了 CDN 的流式处理终结点时，建议不要设置小的缓存值。 设置的值过小会降低性能，导致无法充分利用 CDN。 不允许将启用了 CDN 的流式处理终结点的缓存标头设置为小于 600 秒。

> [!IMPORTANT]
>Azure 媒体服务支持与 Azure CDN 的完整集成。 只需单击一下即可将所有可用的 Azure CDN 提供商集成到流式处理终结点（包括标准版和高级版产品）。 有关详细信息，请参阅此[公告](https://azure.microsoft.com/blog/standardstreamingendpoint/)。
> 
> 只有在通过流式处理终结点 API 启用 CDN 或使用 Azure 门户的流式处理终结点部分时，才会禁用流式处理终结点到 CDN 的数据费用。 如果进行手动集成，或者使用 CDN API 或门户部分直接创建 CDN 终结点，则不会禁用数据费用。

## <a name="configuring-cache-headers-with-azure-media-services"></a>使用 Azure 媒体服务配置缓存标头
可以使用 Azure 门户或 Azure 媒体服务 API 来配置缓存标头值。

1. 若要使用 Azure 门户配置缓存标头，请参阅[如何管理流式处理终结点](../media-services/previous/media-services-portal-manage-streaming-endpoints.md)部分配置流式处理终结点。
2. Azure 媒体服务 REST API，[StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl)。
3. Azure 媒体服务 .NET SDK，[StreamingEndpointCacheControl Properties](http://go.microsoft.com/fwlink/?LinkId=615302)。

## <a name="cache-configuration-precedence-order"></a>缓存配置优先顺序
1. Azure 媒体服务配置的缓存值将覆盖默认值。
2. 如果没有进行手动配置，将应用默认值。
3. 默认情况下，不管 Azure 媒体或 Azure 存储的配置如何，缓存 2 秒的标头适用于实时流式处理清单（播放列表），无法覆盖此值。

