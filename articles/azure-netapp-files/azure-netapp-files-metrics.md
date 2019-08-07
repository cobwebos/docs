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
ms.date: 08/06/2019
ms.author: b-juche
ms.openlocfilehash: 9a56fb27fdf9e196291942041d68b249d7f16648
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839236"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp 文件的指标

Azure NetApp 文件提供有关已分配的存储、实际存储使用情况、卷吞吐量、IOPS 和延迟的指标。 通过分析这些指标，可以更好地了解 NetApp 帐户的使用模式和卷性能。  

## <a name="capacity_pools"></a>容量池的使用情况指标

<!-- 
- *Volume pool allocated size*  
    This is the size (GiB) of the provisioned capacity pool.  
--> 
- *卷池已用分配容量*  
    这是给定容量池中的卷配额总和 (GiB)（即，容量池中卷的预配大小的总和）。 这是创建卷期间选择的大小。  
- *卷池总逻辑大小*  
    这是容量池中各个卷已使用的逻辑空间 (GiB) 的总和。  
<!-- 
- *Volume pool total snapshot size*  
    This is the total of incremental logical space used by the snapshots.  
-->

## <a name="volumes"></a>卷的使用情况指标

<!-- 
- *Volume allocated size*   
    This is the volume size (quota) provisioned in GiB.  
--> 
- *卷逻辑大小*   
    这是卷中已使用的总逻辑空间 (GiB)。 此大小包括活动文件系统和快照使用的逻辑空间。  
- *卷快照大小*   
    这是卷中的快照使用的增量逻辑空间。  

## <a name="next-steps"></a>后续步骤

* [了解 Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)
* [设置容量池](azure-netapp-files-set-up-capacity-pool.md)
* [为 Azure NetApp 文件创建卷](azure-netapp-files-create-volumes.md)
