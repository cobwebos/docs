---
title: 了解预留折扣如何应用于 Azure Cache for Redis | Microsoft Docs
description: 了解预留折扣如何应用于 Azure Cache for Redis 实例。
author: yegu-ms
manager: maiye
ms.service: cache
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: yegu
ms.openlocfilehash: 4ad28bd65fe9bc240e963f103ca5bd658e90fdfd
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77529613"
---
# <a name="how-the-reservation-discount-is-applied-to-azure-cache-for-redis"></a>预留折扣如何应用于 Azure Cache for Redis

购买 Azure Cache for Redis 预留容量后，预留折扣会自动应用于与预留属性和数量匹配的缓存实例。 预留仅涵盖 Azure Cache for Redis 的计算成本。 按标准费率收取存储和网络费用。 预留容量仅适用于[高级层](/azure/azure-cache-for-redis/cache-premium-tier-intro)缓存。

## <a name="how-reservation-discount-is-applied"></a>如何应用预留折扣

预留折扣的性质是“不用就会失效”。 因此，如果你在任何小时内没有匹配资源，那么你将丢失该小时的预留数量。 不能结转未使用的预留小时数。

关闭资源时，预留折扣将自动应用于指定范围内的另一个匹配资源。 如果在指定的范围内找不到匹配的资源，则预留小时数将丢失。

## <a name="discount-applied-to-azure-cache-for-redis"></a>应用于 Azure Cache for Redis 的折扣

Azure Cache for Redis 预留容量折扣每小时应用于缓存。 购买的预留项将与运行中的缓存产生的计算用量进行匹配。 如果这些缓存不是整小时运行，预留项将自动应用于与预留属性匹配其他缓存。 折扣可以应用于同时运行的缓存。 如果与预留属性匹配的缓存不是整小时运行，则无法获得该小时的完整预留折扣权益。

以下示例说明如何根据购买的缓存数目及其运行时间，来应用 Azure Cache for Redis 预留容量折扣。

* **示例 1**：你为 6 GB 缓存购买了 Azure Cache for Redis 预留容量。 如果运行的 13 GB 缓存与其他预留属性匹配，则按照即用即付价格为 7 GB Azure Cache for Redis 计算用量付费，并获得一小时的 6 GB 缓存计算用量的预留折扣。

剩余的示例假设购买的 Azure Cache for Redis 预留容量用于 26 GB 缓存，并且其他预留属性与正在运行的缓存相匹配。

* **示例 2**：运行 2 个 13 GB 缓存一小时。 26 GB 预留折扣将应用于这两个缓存的计算用量。

* **示例 3**：从下午 1:00 到 1:30 运行 1 个 26 GB 缓存。 从下午 1:30 到 2:00 运行另一个 26 GB 缓存。 预留折扣同时涵盖这两个数据库。

* **示例 4**：从下午 1:00 到 1:45 运行 1 个 26 GB 缓存。 从下午 1:30 到 2:00 运行另一个 26 GB 缓存。 将收取 15 分钟重叠期的即用即付费用。 预留折扣将应用到剩余时间的计算用量。

若要了解 Azure 预留的应用情况并在计费使用情况报告中查看该信息，请参阅[了解 Azure 预留使用情况](/azure/billing/billing-understand-reserved-instance-usage-ea)。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们
如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。
