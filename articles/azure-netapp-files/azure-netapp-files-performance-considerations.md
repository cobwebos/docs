---
title: Azure NetApp 文件的性能注意事项 |Microsoft Docs
description: 介绍 Azure NetApp 文件的性能注意事项。
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
ms.date: 06/25/2019
ms.author: b-juche
ms.openlocfilehash: 4badf550809df6cc63711c47e6e6f5b3690fdfca
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691321"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Azure NetApp 文件的性能考虑因素

卷的[吞吐量限制](azure-netapp-files-service-levels.md)由分配给卷的配额和所选服务级别的组合决定。 当你制定有关 Azure NetApp 文件的性能计划时，需要了解几个注意事项。 

## <a name="quota-and-throughput"></a>配额和吞吐量  

吞吐量限制只是将实现的实际性能的一个决定。  

典型的存储性能注意事项，包括读取和写入混合、传输大小、随机或顺序模式以及许多其他因素将影响提供的总性能。  

测试中已观察到的最大实际吞吐量为 4500 MiB/s。  在高级存储层，70.31 TiB 的卷配额将预配吞吐量限制，此限制足以实现此性能级别。  

如果考虑将卷配额量分配到 70.31 TiB 以上，可以为卷分配额外的配额，以存储其他数据。 但是，增加的配额不会导致实际吞吐量增加。  

## <a name="overprovisioning-the-volume-quota"></a>过度预配卷配额

如果工作负荷的性能限制为吞吐量限制，则可以 overprovision 卷配额，以设置更高的吞吐量级别并实现更高的性能。  

例如，如果高级存储层中的某个卷只有 500 GiB 的数据，但需要 128 MiB/秒的吞吐量，则可以将配额设置为 2 TiB，以便相应地设置吞吐量级别（64 MiB/每 TB * 2 TiB = 128 MiB/秒）。  

如果你一直 overprovision 一个卷来实现更高的吞吐量，请考虑改用较高的服务级别。  在上面的示例中，你可以通过使用 Ultra 存储层（128 MiB/s，每个 TiB * 1 TiB = 128 MiB/秒）实现与卷配额一半相同的吞吐量限制。

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>动态增加或减少卷配额

如果性能要求在本质上是暂时的，或者如果在固定时间段内增加了性能需求，则可以动态增加或减少卷配额，以即时调整吞吐量限制。  请注意以下事项： 

* 卷配额可以增加或减少，无需暂停 IO，并且卷的访问不会中断或影响。  

    你可以根据卷调整活动 i/o 事务期间的配额。  请注意，卷配额永远不会降低到卷中存储的逻辑数据量。

* 更改卷配额时，吞吐量限制的相应更改几乎是瞬时的。 

    此更改不会中断或影响卷访问或 i/o。  

* 调整卷配额需要更改容量池大小。  

    可以动态调整容量池大小，而不会影响卷的可用性或 i/o。

## <a name="next-steps"></a>后续步骤

- [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
- [Linux 性能基准](performance-benchmarks-linux.md)