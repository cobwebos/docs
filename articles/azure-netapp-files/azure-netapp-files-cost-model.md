---
title: Azure NetApp 文件的成本模型 |微软文档
description: 描述 Azure NetApp 文件的成本模型，用于管理服务中的费用。
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
ms.openlocfilehash: aea783b818550b8219e1a0498256280f61f678e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "70995118"
---
# <a name="cost-model-for-azure-netapp-files"></a>Azure NetApp 文件的成本模型 

了解 Azure NetApp 文件的成本模型可帮助您从服务中管理费用。

## <a name="calculation-of-capacity-consumption"></a>容量消耗的计算

Azure NetApp 文件根据预配存储容量计费。  通过创建容量池分配预配容量。  容量池按小时增量按 $/预配-GiB/月计费。 单个容量池的最小大小为 4 TiB，容量池随后可以以 1-TiB 增量进行扩展。 卷在容量池中创建。  为每个卷分配一个配额，从池预配的容量中清除。 可分配给卷的配额范围从至少 100 GiB 到最多 100 TiB。  

对于活动卷，针对配额的容量消耗基于逻辑（有效）容量。

如果卷的实际容量消耗超过其存储配额，则卷可以继续增长。 只要实际卷大小小于系统限制 （100 TiB），仍允许写入。  

容量池中与其预配量的已用容量总额是池内所有卷的分配配额或实际消耗量的较大的总和： 

   ![使用容量计算总额](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

下图说明了这些概念。  
* 我们有一个容量池，具有 4 TiB 的预配容量。  池包含三个卷。  
    * 第 1 卷的配额为 2 TiB，并且具有 800 GiB 的消耗量。  
    * 第 2 卷的配额为 1 TiB，并且具有 100 GiB 的消耗量。  
    * 第 3 卷的配额为 500 GiB，但具有 800 GiB 的消耗量（超额）。  
* 容量池按容量的 4 TiB（预配量）进行计量。  
    3.8 容量消耗的 TiB（第 1 卷和第 2 卷的配额为 2 TiB 和 1 TiB，第 3 卷的实际消耗量为 800 GiB）。 剩余产能为 200 GiB。

   ![具有三个卷的容量池](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>容量消耗过剩  

当池的总使用容量超过其预配容量时，仍允许写入数据。  宽限期（一小时）后，如果池的已用容量仍超过其预配容量，则池大小将自动以 1 TiB 的增量增加，直到预配容量大于已配置的总容量。  例如，在上面的插图中，如果卷 3 继续增长，实际消耗量达到 1.2 TiB，则宽限期过后，池将自动调整到 5 TiB。  结果是预配池容量 （5 TiB） 超过使用容量 （4.2 TiB）。  

## <a name="manual-changes-of-the-pool-size"></a>手动更改池大小  

您可以手动增加或减小池大小。 但是，以下约束适用：
* 服务最低和最大限制  
    请参阅有关[资源限制](azure-netapp-files-resource-limits.md)的文章。
* 初始 4-TiB 最低购买后 1-TiB 增量
* 一小时最低计费增量
* 预配池大小不能减少到低于池中的总使用容量。

## <a name="behavior-of-maximum-size-pool-overage"></a>最大规模池超额行为   

您可以创建或调整大小的容量池的最大大小为 500 TiB。  当容量池中的总使用容量超过 500 TiB 时，将发生以下情况：
* 仍允许写入数据（如果卷低于系统最大值 100 TiB）。
* 在一小时的宽限期后，池将自动以 1-TiB 增量调整大小，直到池预配容量超过总使用容量。
* 超过 500 TiB 的额外预配和计费池容量不能用于分配卷配额。 它还不能用于扩展性能 QoS 限制。  
    有关性能限制和 QoS 大小调整的[服务级别](azure-netapp-files-service-levels.md)。

下图说明了以下概念：
* 我们有一个容量池，具有高级存储层和 500-TiB 容量。 池包含九个卷。
    * 第 1 卷到第 8 卷的配额为 60 TiB。  总使用容量为 480 TiB。  
        每个卷的 QoS 限制为 3.75 GiB/s 的吞吐量（60 TiB = 64 MiB/s）。  
    * 第 9 卷的配额为 20 TiB。  
        第 9 卷的 QoS 限制为 1.25 GiB/s 的吞吐量（20 TiB = 64 MiB/s）。
* 第 9 卷是一个超额情况。 它有25 TiB的实际消耗。  
    * 在一小时的宽限期后，容量池将调整到 505 TiB。  
        也就是说，第 1 卷至 8 卷的总使用容量 = 8 = 60-TiB 配额，第 9 卷的实际消耗量为 25 TiB。
    * 计费容量为 505 TiB。
    * 无法增加第 9 卷的卷配额（因为池的总分配配额不得超过 500 TiB）。
    * 可能无法分配其他 QoS 吞吐量（因为池的总 QoS 仍基于 500 TiB）。

   ![具有九个卷的容量池](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>快照的容量消耗 

Azure NetApp 文件中快照的容量消耗根据父卷的配额收费。  因此，它与卷所属的容量池共享相同的计费速率。  但是，与活动卷不同，快照消耗量是根据消耗的增量容量来衡量的。  Azure NetApp 文件快照本质上是有差异的。 根据数据的更改速率，快照消耗的容量通常比活动卷的逻辑容量少得多。 例如，假设您有一个仅包含 10 GiB 差异数据的 500-GiB 卷的快照。 根据该快照的卷配额收取的容量为 10 GiB，而不是 500 GiB。 

## <a name="next-steps"></a>后续步骤

* [Azure NetApp 文件定价页](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
