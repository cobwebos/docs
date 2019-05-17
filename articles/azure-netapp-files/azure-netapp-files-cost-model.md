---
title: 用于 Azure 的 NetApp 文件的成本的模型 |Microsoft Docs
description: 介绍 Azure NetApp 文件用于从服务管理费用的成本模型。
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
ms.openlocfilehash: b06e3366224b90899dd3f9f9439edf897de82794
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524215"
---
# <a name="cost-model-for-azure-netapp-files"></a>Azure NetApp 文件的成本模型 

Azure NetApp 文件了解成本模型可帮助你从服务管理您的费用。

## <a name="calculation-of-capacity-consumption"></a>计算容量消耗量

Azure 的 NetApp 文件是按预配的存储容量计费。  通过创建容量池分配预配的容量。  容量池根据 $/ 预配-GiB/月的 1 小时为增量进行计费。 单个容量池的最小大小是 4 TiB 和 1 TiB 增量可以随后扩展容量池。 容量池内创建卷。  每个卷分配配额，减少从池预配容量。 可以分配给卷范围从最小为 92 TiB 的最大值 100 GiB 的配额。  

对于活动卷，根据配额容量消耗基于逻辑 （有效） 的容量。

如果卷的实际容量消耗超过其存储配额，则该卷可以继续增长。 只要实际卷大小小于系统限制值 (100 TiB)，则仍将允许写入。  

中对其预配量的容量池的总已用的容量是之和大于分配的配额的或实际消耗的池中的所有卷： 

   ![使用的总容量计算](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

下图显示了这些概念。  
* 我们有 4 TiB 的预配容量的容量池。  该池包含三个卷。  
    * 卷 1 分配 2 TiB 的配额，以及使用情况的 800 GiB。  
    * 卷 2 分配 1 TiB 的配额，以及使用情况的 100 GiB。  
    * 第 3 卷已分配的配额为 500 GiB，但具有 800 GiB 消耗 （超额）。  
* 容量池按流量计费的 4 TiB 的容量 （预配量）。  
    3.8 使用 TiB 的容量 （2 TiB 和 1 TiB 的配额从卷 1 和 2，和 800 GiB 的实际使用第三卷）。 和剩余容量的 200 GiB。

   ![有三个卷的容量池](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>在容量消耗的超额  

总使用池容量时超过了其预配的容量，仍受允许的数据写入。  在宽限期 （1 小时），如果使用的池容量仍然超过其预配的容量，然后池大小会自动增加增量为 1 TiB 之前预配的容量大于的总已用容量。  例如，在上述图中，如果第 3 卷的持续发展，实际消耗达到 1.2 TiB，然后在宽限期，池将自动调整为 5 TiB。  结果是预配的池容量 (5 TiB) 超过了的已用的容量 (4.2 TiB)。  

## <a name="manual-changes-of-the-pool-size"></a>池大小的手动更改  

可以手动增加或减少池的大小。 但是，存在以下限制：
* 服务的最小值和最大限制  
    ，请参阅文章[资源限制](azure-netapp-files-resource-limits.md)。
* 初始 4 TiB 最低购买量后 1 TiB 增量
* 一小时的最小计费增量
* 不可能将预配的池大小缩小到小于总池中已使用容量。

## <a name="behavior-of-maximum-size-pool-overage"></a>最大大小池超额行为   

可以创建或重设大小为的容量池的最大大小为 500 TiB。  当总容量池中所用容量超过 500 TiB，将发生以下情况下：
* （如果该卷低于 100 TiB 的系统最大），则仍将允许的数据写入。
* 后一小时宽限期内，该池将自动调整大小以 1 TiB 的增量，直到预配的池容量超过总已用的容量。
* 其他预配，计费池容量超过 500 TiB 不能用于分配卷配额。 它还不能用于展开性能 QoS 限制。  
    请参阅[服务级别](azure-netapp-files-service-levels.md)有关性能限制和 QoS 大小调整。

下图显示了这些概念：
* 我们已使用高级存储层和 500 TiB 容量的容量池。 该池包含九个卷。
    * 1 到 8 的卷分配的配额为 60 TiB。  总已用的容量是 480 TiB。  
        每个卷的 QoS 限制为 3.75 GiB/秒的吞吐量 (60 TiB * 64 MiB/秒)。  
    * 第 9 期被分配的配额为 20 TiB。  
        第 9 期的 QoS 限制为 1.25 GiB/秒的吞吐量 (60 TiB * 64 MiB/秒)。
* 第 9 期是超额方案。 它具有 25 TiB 的实际用电量。  
    * 后一小时宽限期内，容量池将调整为 505 TiB。  
        即，总共使用容量 = 8 * 60-TiB 卷 1 到 8 和 25 TiB 的卷 9 实际消耗的配额。
    * 计费的容量是 505 TiB。
    * （因为该池的总分配的配额不能超过 500 TiB），则不能增加卷 9 卷配额。
    * 将分配额外的 QoS 吞吐量，（因为池的总 QoS 仍基于 500 TiB）。

   ![有九个卷的容量池](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>快照容量消耗量 

Azure NetApp 文件中的快照的容量消耗收费针对父卷的配额。  因此，它会共享相同的计费费率为属于该卷的容量池。  但是，与不同的活动卷，快照使用情况进行测量基于消耗的增量容量。  Azure 的 NetApp 文件快照是在本质上差异。 具体取决于数据的更改速率，快照通常使用要少得多的容量逻辑的活动卷的容量。 例如，假定您有一个 500 GiB 卷，仅包含 10 GiB 的差异数据的快照。 收费针对该快照的卷配额容量为 10 GiB，不为 500 GiB。 

## <a name="next-steps"></a>后续步骤

* [Azure 定价页的 NetApp 文件](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
