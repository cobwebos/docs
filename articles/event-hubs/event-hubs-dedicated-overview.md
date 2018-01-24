---
title: "专用 Azure 事件中心容量概述 | Microsoft Docs"
description: "专用 Microsoft Azure 事件中心容量概述。"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: sethm;babanisa
ms.openlocfilehash: 563152a019464f3d0342383ff13e6ee1c87a22fe
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="overview-of-event-hubs-dedicated"></a>专用事件中心概述

针对需求最严苛的客户，专用事件中心容量提供单个租户部署。 完整规模的 Azure 事件中心可每秒传入超过两百万个事件或每秒高达 2 GB 的遥测，并具有完全持久的存储和次秒级的延迟。 通过在相同系统上实时和批处理，它还实现了集成的解决方案。 借助包含在产品中的[事件中心捕获](event-hubs-capture-overview.md)功能，单个流可以同时支持实时和基于批处理的管道，从而降低解决方案的复杂性。

下表比较了事件中心的各可用服务层。 不同于标准事件中心中大部分功能的使用定价，专用事件中心产品每月价格是固定的。 专用层提供标准计划的所有功能，但具有企业规模容量，以满足客户的工作负荷需求。 

| 功能 | 标准 | 专用 |
| --- |:---:|:---:|:---:|
| 入口事件 | 按每百万个事件支付 | 附送 |
| 吞吐量单位（传入为 1 MB/秒，传出为 2 MB/秒） | 按每小时支付 | 附送 |
| 消息大小 | 256 KB | 1 MB |
| 发布者策略 | 是 | 是 |   
| 使用者组 | 20 | 20 |
| 消息重播 | 是 | 是 |
| 最大吞吐量单位 | 20（可灵活调整至 100）   | 1 容量单位 (CU) ≈ 50 |
| 中转连接 | 包括 1000 | 包括 100 K |
| 其他中转连接 | 是 | 是 |
| 消息保留 | 包括 1 天 | 包括最长 7 天 |
| 捕获 | 按每小时支付 | 附送 |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>专用事件中心容量的优点

使用专用事件中心具有以下优点：

* 单个租户托管，免除来自其他租户的干扰。
* 与标准事件中心的 256 KB 相比，消息大小增至 1 MB。
* 每次可重复性能。
* 有保障的容量，满足迸发需求。
* 包括事件中心的[捕获](event-hubs-capture-overview.md)功能，提供与微批处理和长期保留的集成。
* 零维护：该服务负责管理负载均衡、操作系统更新、安全修补程序及分区。
* 固定小时定价。
* 消息保留期长达 7 天，无需支付额外费用。

专用事件中心还会删除一些标准产品的吞吐量限制。 基本层的吞吐量单位可达每秒 1000 个事件，或者每个 TU 每秒 1 MB 的流入量和两倍的流出量。 专用规模产品对入口和出口事件计数不设限制。 这些限制仅由购买的事件中心处理容量管理。

此保留的专用环境提供该层独有的其他功能，例如：

* 控制群集中的命名空间数量。
* 指定每个命名空间的吞吐量限制。
* 配置每个命名空间下事件中心数量。
* 确定分区的数量限制。

该服务针对最大的遥测用户，也可提供给具有企业协议的客户。

## <a name="how-to-onboard"></a>如何加入

通过添加或删除 CU，可以在一个月内随时扩展或缩小容量，满足自身需求。 专用计划独一无二，用户可从事件中心产品团队处获得适合自己的灵活部署，提供一种亲身实践操作体验。 若要加入到此 SKU，请[联系计费支持](https://ms.portal.azure.com/#create/Microsoft.Support)或 Microsoft 代表。

## <a name="next-steps"></a>后续步骤

请与 Microsoft 销售代表或 Microsoft 支持部门联系，以获其他关于事件中心专用容量的详细信息。 还可访问以下链接，了解有关事件中心定价层的详细信息：

- [专用事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。 还可以联系 Microsoft 销售代表或 Microsoft 支持部门，获取关于专用事件中心容量的其他详细信息。
- [事件中心常见问题解答](event-hubs-faq.md)中包含了定价信息并解答了一些有关事件中心的常见问题。 
