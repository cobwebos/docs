---
title: 显示 Azure NetApp 文件复制关系的运行状况状态 |Microsoft Docs
description: 描述如何在 Azure NetApp 文件的源卷或目标卷上查看复制状态。
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
ms.topic: how-to
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 95c1202fb56e882554d40926e9d5ecec7be49086
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708552"
---
# <a name="display-health-status-of-replication-relationship"></a>显示复制关系的运行状况 

可以查看源卷或目标卷上的复制状态。  

## <a name="display-replication-status"></a>显示复制状态

1. 在源卷或目标卷中，单击 "存储服务" 下的 " **复制** "。

    将显示以下复制状态和运行状况信息：  
    * **终结点类型** –标识卷是复制的源或目标。
    * **运行状况** –显示复制关系的运行状况状态。
    * **镜像状态** –显示以下值之一：
        * *未初始化*：  
            这是创建对等关系时的初始状态和默认状态。 在初始化成功完成之前，状态将保持未初始化状态。
        * *镜像*：   
            目标卷已初始化并准备好接收镜像更新。
        * *损坏*：   
            这是在中断对等关系之后的状态。 目标卷为 `‘RW’` ，并且存在快照。
    * **关系状态** –显示以下值之一： 
        * *空闲*：  
            没有正在进行的传输操作，将来未禁用传输。
        * 正在*传输*：  
            传输操作正在进行中，但未禁用未来传输。
    * **复制计划** -显示初始化 (基准复制) 完成时，将执行增量镜像更新的频率。

    * **总体进度** -显示当前传输操作传输的总数据量（以字节为单位）。 此量是实际传输的位数，可能不同于源卷和目标卷报告的逻辑空间。  

    ![复制运行状况](../media/azure-netapp-files/cross-region-replication-health-status.png)

> [!NOTE] 
> 如果以前的复制作业未完成，则复制关系会将运行状况状态显示为不 *正常* 。 此状态是由较低传输时段传输的大型卷的结果 (例如，大型卷) 的传输时间为10分钟。 在这种情况下，关系状态显示 " *传输* " 和 "运行状况状态显示不 *正常*"。

## <a name="next-steps"></a>后续步骤  

* [跨区域复制](cross-region-replication-introduction.md)
* [管理灾难恢复](cross-region-replication-manage-disaster-recovery.md)
* [卷复制指标](azure-netapp-files-metrics.md#replication)
* [跨区域复制故障排除](troubleshoot-cross-region-replication.md)

