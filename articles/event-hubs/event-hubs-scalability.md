---
title: 可伸缩性-Azure 事件中心 |Microsoft Docs
description: 本文提供了有关如何使用分区和吞吐量单位来缩放 Azure 事件中心的信息。
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
ms.openlocfilehash: 2b36faef8c39a8e9b02a056576ae7f5a77b1f6bf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280945"
---
# <a name="scaling-with-event-hubs"></a>利用事件中心进行缩放

有两个因素会影响事件中心的缩放。
*   吞吐量单位
*   “度量值组”

## <a name="throughput-units"></a>吞吐量单位

事件中心的吞吐量容量由“吞吐量单位”控制。 吞吐量单位是预先购买的容量单位。 使用单个吞吐量可以：

* 入口：最高每秒 1 MB 或每秒 1000 个事件（以先达到的限制为准）。
* 出口：最高每秒 2 MB，或每秒 4096 个事件。

超出所购吞吐量单位的容量时，入口受限，返回 [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception)。 出口不会生成限制异常，但仍受限于所购吞吐量单位的容量。 如果收到发布速率异常或者预期会看到更高的出口，请务必检查你为命名空间购买了多少吞吐量单位。 可以在 [Azure 门户](https://portal.azure.com)的命名空间的“规模”边栏选项卡上管理吞吐量单位。 也可使用[事件中心 API](event-hubs-api-overview.md) 以编程方式管理吞吐量单位。

吞吐量单位按小时计费，需提前购买。 购买后，吞吐量单位的最短计费时限为一小时。 最多可以为一个事件中心命名空间购买 20 个吞吐量单位，这些单位跨此命名空间内的所有事件中心进行共享。

事件中心的自动膨胀功能通过增加吞吐量单位数进行自动纵向扩展，以便满足使用量需求。 增加吞吐量单位数可防止出现限制情况，在这些情况下：

- 数据入口速率超过设置的吞吐量单位数。
- 数据出口请求速率超过设置的吞吐量单位数。

当负载的增加超过最小阈值时，事件中心服务会增加吞吐量，不会因服务器繁忙错误导致任何请求失败。 

有关自动扩展功能的详细信息，请参阅[自动缩放吞吐量单位](event-hubs-auto-inflate.md)。

## <a name="partitions"></a>“度量值组”
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>分区键

可以使用[分区键](event-hubs-programming-guide.md#partition-key)将传入事件数据映射到特定分区，以便进行数据组织。 分区键是发送者提供的、要传递给事件中心的值。 该键通过静态哈希函数进行处理，以便分配分区。 如果在发布事件时未指定分区键，则会使用循环分配。

事件发布者只知道其分区密钥，而不知道事件要发布到的分区。 键与分区的这种分离使发送者无需了解有关下游处理的过多信息。 每个设备或用户的唯一标识就可以充当一个适当的分区键，但是，也可以使用其他属性（例如地理位置），以便将相关的事件分组到单个分区中。


## <a name="next-steps"></a>后续步骤
访问以下链接可以了解有关事件中心的详细信息：

- [自动缩放吞吐量单位](event-hubs-auto-inflate.md)
- [事件中心服务概述](event-hubs-what-is-event-hubs.md)
