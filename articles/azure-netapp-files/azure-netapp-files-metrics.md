---
title: Azure NetApp 文件的指标 | Microsoft Docs
description: 介绍 Azure NetApp 文件的指标。
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
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: c8e3b616dee1ab4e6bb6e77c6a8bab5661d4e20b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79460426"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp 文件的指标

Azure NetApp 文件提供了有关已分配存储、实际存储使用情况、卷 IOPS 和延迟的指标。 通过分析这些指标，可以更好地了解 NetApp 帐户的使用模式和卷性能。  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>容量池的使用情况指标

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *分配到卷大小的池*  
    给定容量池中的卷配额（GiB）总数（即，容量池中的卷预配大小的总和）。  
    此大小是在创建卷时选择的大小。  
- *池消耗大小*  
    容量池中的所有卷使用的逻辑空间（GiB）总数。  
<!-- 
- *Pool Consumed Snapshot Size*  
    The total of logical space (GiB) used by snapshots across all volumes in a capacity pool. 
-->

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>卷的使用情况指标

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *卷消耗大小*   
    卷中使用的总逻辑空间（GiB）。  
    此大小包括活动文件系统和快照使用的逻辑空间。  
- *卷快照大小*   
   卷中的快照使用的增量逻辑空间。  

## <a name="performance-metrics-for-volumes"></a>卷的性能指标

- *AverageReadLatency*   
    从卷读取的平均时间（以毫秒为单位）。
- *AverageWriteLatency*   
    从卷写入的平均时间（以毫秒为单位）。
- *ReadIops*   
    每秒读取到卷的次数。
- *WriteIops*   
    每秒向卷进行写入的次数。

## <a name="next-steps"></a>后续步骤

* [了解 Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)
* [设置容量池](azure-netapp-files-set-up-capacity-pool.md)
* [为 Azure NetApp 文件创建卷](azure-netapp-files-create-volumes.md)
