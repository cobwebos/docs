---
title: Azure NetApp 文件的指标 | Microsoft Docs
description: Azure NetApp 文件提供了有关已分配存储、实际存储使用情况、卷 IOPS 和延迟的指标。 使用这些指标来了解使用情况和性能。
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
ms.date: 09/10/2020
ms.author: b-juche
ms.openlocfilehash: 1690a844ff700a2975be8e972fd90ba71eeb937c
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707775"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp 文件的指标

Azure NetApp 文件提供了有关已分配存储、实际存储使用情况、卷 IOPS 和延迟的指标。 通过分析这些指标，可以更好地了解 NetApp 帐户的使用模式和卷性能。  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>容量池的使用情况指标

- *池分配的大小*   
    池的预配大小。

- *分配到卷大小的池*  
    卷配额 (GiB) 在给定的容量池中， (也就是说，容量池中的卷预配大小总计) 。  
    此大小是在创建卷时选择的大小。  

- *池消耗大小*  
    容量池中的所有卷使用的逻辑空间 () 。  

- *池的总快照大小*    
    池中所有卷的快照大小之和。

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>卷的使用情况指标

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *卷消耗大小*   
    卷中使用的总逻辑空间 (GiB) 。  
    此大小包括活动文件系统和快照使用的逻辑空间。  
- *卷快照大小*   
   卷中的快照使用的增量逻辑空间。  

## <a name="performance-metrics-for-volumes"></a>卷的性能指标

- *平均读取延迟*   
    从卷读取的平均时间（以毫秒为单位）。
- *平均写入延迟*   
    从卷写入的平均时间（以毫秒为单位）。
- *读取 IOPS*   
    每秒读取到卷的次数。
- *写入 IOPS*   
    每秒向卷进行写入的次数。

## <a name="volume-replication-metrics"></a><a name="replication"></a>卷复制指标

- *卷复制状态是否正常*   
    复制关系的条件。 

- *卷复制传输*    
    卷复制的状态是否为 "正在传输"。 
 
- *卷复制滞后时间*   
    镜像上的数据落后于源的时间（秒）。 

- *卷复制上次传输持续时间*   
    上次传输完成所用的时间（秒）。 

- *卷复制的上次传输大小*    
    作为上次传输的一部分传输的总字节数。 

- *卷复制进度*    
    当前传输操作传输的总数据量。 

- *卷复制总传输*   
    为关系传输的累计字节数。 

## <a name="next-steps"></a>后续步骤

* [了解 Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)
* [设置容量池](azure-netapp-files-set-up-capacity-pool.md)
* [为 Azure NetApp 文件创建卷](azure-netapp-files-create-volumes.md)
