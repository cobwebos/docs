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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 7cf382f511d2ba8452d77bf207f36b749cb31e94
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848793"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp 文件的指标

Azure NetApp 文件提供有关已分配的存储、实际存储使用情况、卷吞吐量、IOPS 和延迟的指标。 通过分析这些指标，可以更好地了解 NetApp 帐户的使用模式和卷性能。  

## <a name="capacity_pools"></a>容量池的使用情况指标

<!-- 
- *Volume pool allocated size*  
    The size (GiB) of the provisioned capacity pool  
--> 
- *卷池已用分配容量*  
    给定容量池中的卷配额 (GiB) 总数 (即容量池中的卷预配大小总计)  
    这是创建卷期间选择的大小。  
- *卷池总逻辑大小*  
    容量池中的所有卷使用的逻辑空间 (GiB) 总数  
<!-- 
- *Volume pool total snapshot size*  
    The total of incremental logical space used by the snapshots  
-->

## <a name="volumes"></a>卷的使用情况指标

<!-- 
- *Volume allocated size*   
    The volume size (quota) provisioned in GiB  
--> 
- *卷逻辑大小*   
    卷中使用的总逻辑空间 (GiB)  
    此大小包括活动文件系统和快照使用的逻辑空间。  
- *卷快照大小*   
   卷中的快照使用的增量逻辑空间  

## <a name="performance-metrics-for-volumes"></a>卷的性能指标

- *AverageReadLatency*   
    从卷读取的平均时间 (以毫秒为单位)
- *AverageWriteLatency*   
    从卷写入的平均时间 (以毫秒为单位)
- *ReadIops*   
    每秒读取到卷的次数
- *WriteIops*   
    每秒写入卷的次数

## <a name="next-steps"></a>后续步骤

* [了解 Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)
* [设置容量池](azure-netapp-files-set-up-capacity-pool.md)
* [为 Azure NetApp 文件创建卷](azure-netapp-files-create-volumes.md)
