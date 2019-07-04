---
title: 有关 Azure NetApp 文件的性能注意事项 |Microsoft Docs
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
ms.openlocfilehash: 97e3c6212edd2ade4eabb96db3543e9b3b68e2ae
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454134"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Azure NetApp 文件的性能考虑因素

[吞吐量限制](azure-netapp-files-service-levels.md)卷的分配给该卷的配额和级别选择的服务的组合决定。 有关 Azure NetApp 文件性能计划时，您需要了解几个注意事项。 

## <a name="quota-and-throughput"></a>配额和吞吐量  

吞吐量限制为只有一个决定因素可实现的实际性能。  

典型的存储性能注意事项，包括读取和写入组合的传输大小、 随机或顺序模式和许多其他因素会提供到所提供的总体性能。  

已在测试中观察到的最大经验吞吐量为 4500 MiB/秒。  在高级存储层、 70.31 TiB 的卷配额将预配足够高，以实现此级别的性能的吞吐量限制。  

如果您正在考虑将卷分配超出 70.31 TiB 的定额，更多的配额可能赋予一个卷用于存储其他数据。 但是，添加了的配额不会进一步提高实际吞吐量。  

请参阅[性能基准 Azure NetApp 文件](azure-netapp-files-performance-benchmarks.md)有关其他信息。

## <a name="overprovisioning-the-volume-quota"></a>过度预配卷配额

如果工作负荷性能吞吐量限制绑定，就可以过度预配卷配额设置更高的吞吐量级别，实现更高的性能。  

例如，如果高级存储层中的卷具有仅 500 GiB 数据，但需要 128 MiB/秒的吞吐量，你可以设置配额为 2 TiB，以便相应地设置的吞吐量级别 (每 TB 64 MiB/秒 * 2 TiB = 128 MiB/秒)。  

如果一致地过度预配的卷实现更高的吞吐量，请考虑改为使用更高的服务级别。  在上面的示例中，您可以通过改为使用超高存储层中实现一半卷配额具有相同的吞吐量限制 (每个 TiB 的 128 MiB/秒 * 1 TiB = 128 MiB/秒)。

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>动态增加或减少卷配额

如果你的性能要求是临时在本质上，或者如果已提高了固定的一段时间内的性能需求，则可以动态增加或减少卷配额在瞬间完成调整吞吐量限制。  请注意以下事项： 

* 卷配额可增加或减少而无需暂停 IO，并且没有中断或受影响的卷的访问权限。  

    在对卷执行 active I/O 事务过程，可以调整配额。  请注意可以永远不会降低该卷配额，低于的逻辑卷中存储的数据量。

* 卷配额更改时，吞吐量限制中的相应更改是几乎即时的。 

    更改不会中断，也不影响的卷访问权限或 I/O。  

* 调整卷配额需要容量池大小的更改。  

    动态并不影响卷的可用性或 I/O 情况下，可以调整容量池大小。

## <a name="next-steps"></a>后续步骤

- [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
- [Azure NetApp 文件的性能基准](azure-netapp-files-performance-benchmarks.md)