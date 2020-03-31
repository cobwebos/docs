---
title: Azure NetApp 文件的性能注意事项 |微软文档
description: 描述 Azure NetApp 文件的性能注意事项。
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
ms.openlocfilehash: 97e3c6212edd2ade4eabb96db3543e9b3b68e2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "67454134"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Azure NetApp 文件的性能考虑因素

卷的[吞吐量限制](azure-netapp-files-service-levels.md)由分配给卷的配额和所选服务级别的组合决定。 在创建有关 Azure NetApp 文件的性能计划时，需要了解几个注意事项。 

## <a name="quota-and-throughput"></a>配额和吞吐量  

吞吐量限制只是实际性能的一个决定因素。  

典型的存储性能注意事项，包括读写混合、传输大小、随机或顺序模式以及许多其他因素，都将有助于交付的总体性能。  

在测试中观察到的最大经验吞吐量为 4，500 MiB/s。  在高级存储层中，容量配额为 70.31 TiB 将预配足以达到此性能级别的吞吐量限制。  

如果您正在考虑分配超出 70.31 TiB 的卷配额金额，则可以为存储其他数据的卷分配额外的配额。 但是，添加的配额不会导致实际吞吐量的进一步增加。  

有关其他信息，请参阅[Azure NetApp 文件的性能基准](azure-netapp-files-performance-benchmarks.md)。

## <a name="overprovisioning-the-volume-quota"></a>过度预配卷配额

如果工作负载的性能是吞吐量限制绑定的，则可能会过度调配卷配额以设置更高的吞吐量级别并实现更高的性能。  

例如，如果高级存储层中的卷只有 500 GiB 的数据，但需要 128 MiB/s 的吞吐量，则可以将配额设置为 2 TiB，以便相应地设置吞吐量级别（每个 TB 64 MiB/s = 2 TiB = 128 MiB/s）。  

如果始终过度调配卷以实现更高的吞吐量，请考虑改用更高的服务级别。  在上面的示例中，您可以使用 Ultra 存储层（每个 TiB 128 MiB/s = 1 TiB = 128 MiB/s），实现相同吞吐量限制，使用一半的卷配额。

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>动态增加或减少卷配额

如果性能要求是临时性的，或者在固定时间段内提高了性能需求，则可以动态增加或减少音量配额以即时调整吞吐量限制。  请注意以下事项： 

* 无需暂停 IO 即可增加或减少卷配额，并且对卷的访问不会中断或受到影响。  

    您可以在活动 I/O 事务期间针对卷调整配额。  请注意，卷配额永远不会降低到存储在卷中的逻辑数据量以下。

* 当音量配额更改时，吞吐量限制的相应变化几乎是瞬时的。 

    更改不会中断或影响卷访问或 I/O。  

* 调整容量配额需要更改容量池大小。  

    容量池大小可以动态调整，而不会影响容量可用性或 I/O。

## <a name="next-steps"></a>后续步骤

- [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
- [Azure NetApp 文件的性能基准](azure-netapp-files-performance-benchmarks.md)