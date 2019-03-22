---
title: 使用 Azure NetApp 文件管理快照 | Microsoft Docs
description: 介绍如何使用 Azure NetApp 文件为卷创建快照或从快照还原到新卷。
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
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 9154add96084142ca1f93ad61c6418e3b487fb83
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090910"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>使用 Azure NetApp 文件管理快照

可以使用 Azure NetApp 文件为卷创建按需快照，或从快照还原到新卷。

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>为卷创建按需快照

可以仅按需创建快照。 目前不支持快照策略。

1.  在“卷”边栏选项卡中，单击“快照”。

    ![导航到快照](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  单击“+ 添加快照”，为卷创建按需快照。

    ![添加快照](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  在“新建快照”窗口中，为要创建的新快照提供名称。   

    ![新建快照](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. 单击“确定”。 

## <a name="restore-a-snapshot-to-a-new-volume"></a>将快照还原到新卷

目前，只能将快照还原到新卷。 
1. 从“卷”边栏选项卡转到“管理快照”边栏选项卡以显示快照列表。 
2. 选择要还原的快照。  
3. 右键单击快照名称，然后从菜单选项中选择“还原到新卷”。  

    ![将快照还原到新卷](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. 在“新建卷”窗口中，提供新卷的信息：  
    * **名称**   
        指定要创建的卷的名称。  
        
        名称在资源组中必须唯一。 它的长度必须至少为三个字符。  它可以使用任何字母数字字符。

    * **文件路径**     
        指定将用来创建新卷的导出路径的文件路径。 导出路径用来装载并访问卷。   
        
        装载目标是 NFS 服务 IP 地址的终结点。 它是自动生成的。   
        
        文件路径名只能包含字母、数字和连字符 ("-")。 它的长度必须介于 16 到 40 个字符之间。 

    * **配额**  
        指定分配给卷的逻辑存储量。  

        “可用配额”字段显示了所选容量池中可以用来创建新卷的未使用空间量。 新卷的大小不能超过可用配额。

    *   **虚拟网络**  
        指定要从中访问卷的 Azure 虚拟网络 (Vnet)。  
        你指定的 Vnet 必须已将子网委托给 Azure NetApp 文件。 只能从同一 Vnet 或者从与卷位于同一区域的 Vnet 通过 Vnet 对等互连来访问 Azure NetApp 文件。 可以从本地网络通过 Express Route 来访问卷。 

    * **子网**  
        指定要用于卷的子网。  
        你指定的子网必须已委托给 Azure NetApp 文件服务。 可以通过选择“子网”字段下的“新建”来创建新子网。  
   <!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
   -->

5. 单击“确定”。   
    快照还原到的新卷将显示在“卷”边栏选项卡中。

## <a name="next-steps"></a>后续步骤

[了解 Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)