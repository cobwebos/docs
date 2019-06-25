---
title: 可伸缩性-Azure 事件中心 |Microsoft Docs
description: 提供有关如何缩放 Azure 事件中心信息。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 06/18/2019
ms.author: shvija
ms.openlocfilehash: 3eb20013a6b3afaddce10f2e4652add0edf22a9a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276777"
---
# <a name="scaling-with-event-hubs"></a>通过事件中心进行缩放

有两个因素会影响通过事件中心进行的缩放。
*   吞吐量单位
*   分区

## <a name="throughput-units"></a>吞吐量单位

事件中心的吞吐量容量由“吞吐量单位”控制。  吞吐量单位是预先购买的容量单位。 单个吞吐量是指：

* 入口：最高每秒 1 MB 或每秒 1000 个事件（以先达到的限制为准）。
* 出口：最高每秒 2 MB，或每秒 4096 个事件。

超出所购吞吐量单位的容量时，入口受限，返回 [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception)。 出口不会生成限制异常，但仍受限于所购吞吐量单位的容量。 如果收到发布速率异常或者预期会看到更高的出口，请务必检查为命名空间购买了多少吞吐量单位。 可以在 [Azure 门户](https://portal.azure.com)的命名空间的“规模”边栏选项卡上管理吞吐量单位。  也可使用[事件中心 API](event-hubs-api-overview.md) 以编程方式管理吞吐量单位。

吞吐量单位按小时计费，需提前购买。 购买后，吞吐量单位的最短计费时限为一小时。 最多可以为一个事件中心命名空间购买 20 个吞吐量单位，这些单位跨此命名空间内的所有事件中心进行共享。

事件中心的自动膨胀功能通过增加吞吐量单位数进行自动纵向扩展，以便满足使用量需求  。 增加吞吐量单位数可防止出现限制情况，在这些情况下：

- 数据入口速率超过设置的吞吐量单位数。
- 数据出口请求速率超过设置的吞吐量单位数。

当负载的增加超过最小阈值时，事件中心服务会增加吞吐量，不会因服务器繁忙错误导致任何请求失败。 

有关详细信息自动膨胀功能，请参阅[自动缩放吞吐量单位](event-hubs-auto-inflate.md)。

## <a name="partitions"></a>分区

可以通过分区进行缩放，方便下游处理。 由于事件中心为分区使用者模型提供分区，因此你可以在并发处理事件时横向扩展。 一个事件中心最多可以有 32 个分区。

建议以 1:1 的比例来平衡吞吐量单位和分区数目，实现最佳缩放。 一个分区最多只能保证一个吞吐量单位的入口和出口。 虽然你也许可以在一个分区实现更高的吞吐量，但性能无法得到保证。 这就是我们强烈建议一个事件中心的分区数大于或等于吞吐量单位数的原因。

如果已确定所需总吞吐量，则可以知道所需吞吐量单位数和最小分区数，但应该有多少分区呢？ 选择的分区数取决于要实现的下游并行度以及未来的吞吐量需求。 我们不根据事件中心的分区数收费。

如需事件中心的详细定价信息，请参阅[事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。

## <a name="next-steps"></a>后续步骤
访问以下链接可以了解有关事件中心的详细信息：

- [自动缩放吞吐量单位](event-hubs-auto-inflate.md)
- [事件中心服务概述](event-hubs-what-is-event-hubs.md)
