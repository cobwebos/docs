---
title: Azure NetApp 文件的性能注意事项 |Microsoft Docs
description: 了解 Azure NetApp 文件的性能，包括配额和吞吐量限制的关系，以及如何动态增加/减少卷配额。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: edfebe3d9470defbe70b3694d5574e58ca3b5938
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325515"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Azure NetApp 文件的性能考虑因素

具有自动 QoS 的卷的 [吞吐量限制](azure-netapp-files-service-levels.md) 是通过分配给卷的配额和所选服务级别的组合确定的。 对于具有手动 QoS 的卷，可以单独定义吞吐量限制。 当你制定有关 Azure NetApp 文件的性能计划时，需要了解几个注意事项。 

## <a name="quota-and-throughput"></a>配额和吞吐量  

吞吐量限制只是将实现的实际性能的一个决定。  

典型的存储性能注意事项，包括读取和写入混合、传输大小、随机或顺序模式以及许多其他因素将影响提供的总性能。  

测试中已观察到的最大实际吞吐量为 4500 MiB/s。  在高级存储层，70.31 TiB 的自动 QoS 卷配额将预配吞吐量限制，此限制足以实现此性能级别。  

在自动 QoS 卷的情况下，如果考虑将卷配额量分配到 70.31 TiB 以上，则可以将额外的配额分配给卷以存储额外的数据。 但是，增加的配额不会导致实际吞吐量增加。  

同一经验率上限适用于具有手动 QoS 的卷。 可分配给卷的最大吞吐量为 4500 MiB/s。

## <a name="automatic-qos-volume-quota-and-throughput"></a>自动 QoS 卷配额和吞吐量

本部分介绍具有自动 QoS 类型的卷的配额管理和吞吐量。

### <a name="overprovisioning-the-volume-quota"></a>过度预配卷配额

如果工作负荷的性能限制为吞吐量限制，则可以 overprovision 自动 QoS 卷配额，以设置更高的吞吐量级别并实现更高的性能。  

例如，如果高级存储层中的自动 QoS 卷只包含 500 GiB 的数据，但需要 128 MiB/秒的吞吐量，则可以将配额设置为 2 TiB，以便相应地设置吞吐量级别 (64 MiB/每 TB * 2 TiB = 128 MiB/秒) 。  

如果你一直 overprovision 一个卷来实现更高的吞吐量，请考虑改用手动 QoS 卷或使用更高的服务级别。  在上面的示例中，你可以通过使用 Ultra 存储层来实现与自动 QoS 卷配额的一半相同的吞吐量限制，方法是使用 TiB * 1 TiB = 128 MiB/s)  (128 MiB/s。

### <a name="dynamically-increasing-or-decreasing-volume-quota"></a>动态增加或减少卷配额

如果性能要求在本质上是暂时的，或者如果在固定时间段内增加了性能需求，则可以动态增加或减少卷配额，以即时调整吞吐量限制。  请注意以下事项： 

* 卷配额可以增加或减少，无需暂停 IO，并且卷的访问不会中断或影响。  

    你可以根据卷调整活动 i/o 事务期间的配额。  请注意，卷配额永远不会降低到卷中存储的逻辑数据量。

* 更改卷配额时，吞吐量限制的相应更改几乎是瞬时的。 

    此更改不会中断或影响卷访问或 i/o。  

* 调整卷配额可能需要更改容量池大小。  

    可以动态调整容量池大小，而不会影响卷的可用性或 i/o。

## <a name="manual-qos-volume-quota-and-throughput"></a>手动 QoS 卷配额和吞吐量 

如果使用手动 QoS 卷，则不必 overprovision 卷配额来实现更高的吞吐量，因为可以独立地将吞吐量分配给每个卷。 但仍需确保容量池预配有足够的吞吐量，以满足你的性能需求。 容量池的吞吐量是根据其大小和服务级别进行预配的。 有关更多详细信息，请参阅 [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md) 。


## <a name="next-steps"></a>后续步骤

- [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
- [Linux 性能基准](performance-benchmarks-linux.md)