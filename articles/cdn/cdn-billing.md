---
title: 了解 Azure CDN 计费 | Microsoft Docs
description: 本常见问题解答介绍了 Azure CDN 计费原理。
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: v-deasim
ms.openlocfilehash: 218c493c772dc8fd212efaf60a0599fa2e896411
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32161280"
---
# <a name="understanding-azure-cdn-billing"></a>了解 Azure CDN 计费

本常见问题解答介绍了通过 Azure 内容分发网络 (CDN) 托管的内容的计费结构。

## <a name="what-is-a-billing-region"></a>什么是计费区域？
计费区域是指在从 Azure CDN 分发对象时用于确定费率的地理区域。 目前的计费区域及其地理区域如下所示：

- 区域 1：北美、欧洲、中东和非洲

- 区域 2：亚太（包括日本）

- 区域 3：南美

- 区域 4：澳大利亚和新西兰

- 区域 5：印度

有关接入点 (POP) 区域的信息，请参阅[按区域列出的 Azure CDN 的 POP 位置](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)。 例如，位于墨西哥的 POP 属于北美区域，因此包括在区域 1 中。 

有关 Azure CDN 定价的信息，请参阅[内容分发网络定价](https://azure.microsoft.com/is-is/pricing/details/cdn/)。

## <a name="how-are-delivery-charges-calculated-by-region"></a>如何按区域计算分发费用？
Azure CDN 计费区域取决于将内容分发到最终用户的源服务器的位置。 系统不将客户端的目标（物理位置）视为计费区域。

例如，如果某位墨西哥用户发出了一个请求，而该请求因受对等互连或通信条件影响而由位于美国 POP 的服务器处理，则计费区域为美国。

## <a name="what-is-a-billable-azure-cdn-transaction"></a>什么是 Azure CDN 计费事务？
任何在 CDN 上终止的 HTTP(S) 请求都是计费事件，其中包括所有响应类型：成功、失败或其他。 但是，不同的响应可能生成不同的流量。 例如，“304 未修改”和其他仅标头响应会生成很少的流量，因为它们是小型标头响应；同样，错误响应（例如，“404 未找到”）是计费的，但产生的费用很少，因为响应有效负载很小。

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>与 Azure CDN 使用相关联的其他 Azure 费用有哪些？
使用 Azure CDN 时，还会对用作对象的源的服务造成一些使用费用。 这些费用通常只占全部 CDN 使用费用的一小部分。

如果使用 Azure Blob 存储作为内容的源，则还会产生下述针对缓存填充的存储费用：

- 实际使用的 GB：源对象的实际存储。

- 以 GB 为单位的传输量：通过传输来填充 CDN 缓存的数据量。

- 事务数：填充缓存所需。

有关 Azure 存储计费的详细信息，请参阅 [Understanding Azure Storage Billing – Bandwidth, Transactions, and Capacity](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/)（了解 Azure 存储计费 - 带宽、事务和容量）。

如果使用托管服务分发，则会产生下述费用：

- Azure 计算时间：充当源的计算实例。

- Azure 计算传输：源自计算实例的数据传输，用于填充 Azure CDN 缓存。

如果客户端使用字节范围请求（不考虑源服务），请注意以下事项：

- 字节范围请求是 CDN 上的计费事务。 当客户端发出字节范围请求时，该请示是针对一部分（一系列）对象的。 CDN 只使用一部分请求的内容进行响应。 此部分响应是计费事务，传输量以范围响应（加上标头）的大小为限。

- 当某个到达的请求针对的只是对象的一部分时（通过指定字节范围标头来实现这一点），CDN 可能会将整个对象提取到其缓存中。 因此，即使来自 CDN 的计费事务是针对部分响应的，来自源的计费事务也会涉及完整大小的对象。

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>若要为缓存提供支持，会出现多少传输活动？
CDN POP 会在每次需要填充其缓存时向要缓存的对象的源提出请求。 因此，每次出现缓存失误时，源就会产生计费事务。 缓存失误数取决于许多因素：

- 内容的可缓存性如何：如果内容的 TTL（生存时间）/过期值高且访问频率也高，因此需经常放置在缓存中，则由 CDN 处理负载的绝大部分。 通常情况下，合格的缓存命中率远高于 90%，这意味着必须返回到源的客户端请求不到 10%，不管是因为缓存失误还是因为需要进行对象刷新。

- 多少节点需要加载对象：节点在每次从源加载对象时，都会产生计费事务。 因此，全局内容越多（需要从更多的节点访问），计费事务也越多。

- TTL 影响：对象的 TTL 越高，意味着需要从源提取该对象的频率越低， 同时还意味着客户端（例如浏览器）能够缓存该对象的时间越长，因此可以减少 CDN 的事务。

## <a name="how-do-i-manage-my-costs-most-effectively"></a>如何才能最有效地管理费用？
将内容的 TTL 尽可能设置得长一点。 
