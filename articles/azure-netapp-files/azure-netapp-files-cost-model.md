---
title: Azure NetApp 文件的成本模型 |Microsoft Docs
description: 描述用于从服务管理费用的 Azure NetApp 文件的成本模型。
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
ms.date: 05/01/2019
ms.author: b-juche
ms.openlocfilehash: 563416418b3f387f103fddc88b3ba9ad4c93fdd4
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030802"
---
# <a name="cost-model-for-azure-netapp-files"></a>Azure NetApp 文件的成本模型 

了解 Azure NetApp 文件的成本模型可帮助你管理服务的费用。

## <a name="calculation-of-capacity-consumption"></a>计算容量消耗

Azure NetApp 文件在预配的存储容量上计费。  预配的容量通过创建容量池进行分配。  容量池根据每小时的 $/provisioned-GiB/month 进行计费。 单个容量池的最小大小为 4 TiB, 容量池可以在 1-TiB 增量扩展。 在容量池中创建卷。  为每个卷分配一个从池预配容量中减去的配额。 可分配给卷的配额范围介于最小 100 GiB 到 100 TiB 之间。  

对于活动卷, 针对配额的容量消耗基于逻辑 (有效) 容量。

如果卷的实际容量消耗超出了其存储配额, 则该卷可以继续增长。 只要实际的卷大小小于系统限制 (100 TiB), 仍将允许写入。  

容量池中根据其预配量的已用总容量是该池内所有卷的已分配配额或实际消耗的最大值的总和: 

   ![使用的总容量计算](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

下图演示了这些概念。  
* 我们有一个容量池, 其中包含 4 TiB 的预配容量。  池包含三个卷。  
    * 卷1分配有2个 TiB 的配额, 并且具有 800 GiB 的消耗。  
    * 卷2分配配额为 1 TiB, 具有 100 GiB 的消耗。  
    * 向第3卷分配配额 500 GiB, 但其用量为 800 GiB (超额)。  
* 容量池的容量为 4 TiB (预配量)。  
    3.8 TiB 的容量已使用 (2 TiB 和1个 TiB, 其中卷1和2的配额, 以及第3卷的实际消耗 800 GiB)。 还有200的 GiB。

   ![容量池包含三个卷](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>容量消耗超额  

如果池的总已用容量超出了其预配的容量, 则仍允许进行数据写入。  宽限期 (一小时) 后, 如果池的已用容量仍超过其预配的容量, 则池大小将自动增加1个 TiB, 直到预配的容量大于使用的总容量。  例如, 在上图中, 如果 Volume 3 持续增长, 实际消耗达到 1.2 TiB, 则在宽限期后, 池将自动调整大小为 5 TiB。  结果是预配的池容量 (5 TiB) 超过了已用容量 (4.2 TiB)。  

## <a name="manual-changes-of-the-pool-size"></a>池大小的手动更改  

你可以手动增加或减少池大小。 但是, 以下约束适用:
* 最小服务和最大限制  
    请参阅有关[资源限制](azure-netapp-files-resource-limits.md)的文章。
* 第一次 TiB 最小购买后的 TiB 增量
* 一小时的最小计费增量
* 预配的池大小不能减少到池中已用的总容量。

## <a name="behavior-of-maximum-size-pool-overage"></a>最大大小池超额的行为   

可以创建或调整大小的容量池的最大大小为 500 TiB。  如果容量池中使用的总容量超过 500 TiB, 将发生以下情况:
* 仍允许写入数据 (如果卷低于系统最大值 100 TiB)。
* 在一小时的宽限期后, 池将以1个 TiB 的增量自动调整大小, 直到池预配的容量超出了使用的总容量。
* 不能使用超过 500 TiB 的其他预配和已计费池容量来分配卷配额。 它也不能用于扩展性能 QoS 限制。  
    请参阅[服务级别](azure-netapp-files-service-levels.md)关于性能限制和 QoS 大小调整。

下图演示了这些概念:
* 我们有一个具有高级存储层和 500 TiB 容量的容量池。 池包含9个卷。
    * 将每个卷1到8分配配额为 60 TiB。  使用的总容量为 480 TiB。  
        每个卷的 GiB (60 TiB * 64 MiB/秒) 的 QoS 限制为3.75。  
    * 卷9分配配额为 20 TiB。  
        卷9的 QoS 限制为 1.25 GiB/秒 (60 TiB * 64 MiB/秒)。
* 卷9是一种超额方案。 它有25个 TiB 的实际消耗。  
    * 在一小时的宽限期后, 容量池将调整为 505 TiB。  
        也就是说, 已用总容量 = 8 * 60-TiB 配额 (适用于卷1到 8) 和 25 TiB (适用于卷9的实际消耗)。
    * 计费的容量为 505 TiB。
    * 卷9的卷配额不能增加 (因为池的总分配配额不能超过 500 TiB)。
    * 可能无法分配额外的 QoS 吞吐量 (因为池的总 QoS 仍基于 500 TiB)。

   ![容量池包含九个卷](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>快照的容量消耗 

Azure NetApp 文件中的快照容量消耗根据父卷的配额收费。  因此, 它与卷所属的容量池共用相同的计费费率。  但是, 与活动卷不同, 根据使用的增量容量来度量快照消耗。  Azure NetApp 文件快照本质上是差异的。 根据数据的更改率, 快照通常占用的容量比活动卷的逻辑容量少得多。 例如, 假设有一个 500 GiB 卷的快照, 该卷只包含 10 GiB 的差异数据。 针对该快照的卷配额收费的容量为 10 GiB, 而不是 500 GiB。 

## <a name="next-steps"></a>后续步骤

* [Azure NetApp 文件定价页](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
