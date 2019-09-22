---
title: 为 Azure NetApp 文件创建 NFS 卷 |Microsoft Docs
description: 介绍如何为 Azure NetApp 文件创建 NFS 卷。
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
ms.date: 7/9/2019
ms.author: b-juche
ms.openlocfilehash: 45164acd89fc9634d6929bafb35e64a5dc9f2b86
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178218"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>创建用于 Azure NetApp 文件的 NFS 卷

Azure NetApp 文件支持 NFS 和 SMBv3 卷。 卷的容量消耗是依据其池的预配容量计数的。 本文介绍如何创建 NFS 卷。 若要创建 SMB 卷, 请参阅[为 Azure NetApp 文件创建 smb 卷](azure-netapp-files-create-volumes-smb.md)。 

## <a name="before-you-begin"></a>开始之前 
必须已设置容量池。   
[设置容量池](azure-netapp-files-set-up-capacity-pool.md)   
子网必须委派给 Azure NetApp 文件。  
[将子网委派给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)

## <a name="create-an-nfs-volume"></a>创建 NFS 卷

1.  单击 "容量池" 边栏选项卡中的 "**卷**" 边栏选项卡。 

    ![导航到卷](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  单击“+ 添加卷”以创建卷。  
    此时将显示 "创建卷" 窗口。

3.  在 "创建卷" 窗口中, 单击 "**创建**", 并为以下字段提供信息:   
    * **卷名**      
        指定要创建的卷的名称。   

        卷名称在每个容量池中必须是唯一的。 它的长度必须至少为三个字符。 您可以使用任何字母数字字符。   

        不能将`default`用作卷名。

    * **容量池**  
        指定要在其中创建卷的容量池。

    * **配额**  
        指定分配给卷的逻辑存储量。  

        “可用配额”字段显示了所选容量池中可以用来创建新卷的未使用空间量。 新卷的大小不能超过可用配额。  

    * **虚拟网络**  
        指定要从中访问卷的 Azure 虚拟网络 (Vnet)。  

        你指定的 Vnet 必须已将子网委托给 Azure NetApp 文件。 只能从同一 Vnet 或者从与卷位于同一区域的 Vnet 通过 Vnet 对等互连来访问 Azure NetApp 文件服务。 还可以通过 Express Route 从本地网络访问该卷。   

    * **子网**  
        指定要用于卷的子网。  
        你指定的子网必须委派给 Azure NetApp 文件。 
        
        如果尚未委派子网，可以在“创建卷”页面上单击“新建”。 然后，在“创建子网”页面中，指定子网信息，并选择“Microsoft.NetApp/卷”来为 Azure NetApp 文件委派子网。 在每个 Vnet 中, 只能将一个子网委托给 Azure NetApp 文件。   
 
        ![创建卷](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![创建子网](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. 单击“协议”，然后选择“NFS”作为该卷的协议类型。   
    * 指定将用于创建新卷的导出路径的**文件路径**。 导出路径用来装载并访问卷。

        文件路径名只能包含字母、数字和连字符 ("-")。 它的长度必须介于 16 到 40 个字符之间。 

        文件路径在每个订阅和每个区域中必须是唯一的。 

    * (可选)[配置 NFS 卷的导出策略](azure-netapp-files-configure-export-policy.md)

    ![指定 NFS 协议](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. 单击 "**查看 + 创建**" 查看卷详细信息。  然后单击 "**创建**" 创建 NFS 卷。

    你创建的卷将出现在 "卷" 页中。 
 
    卷从其容量池继承订阅、资源组和位置属性。 若要监视卷部署状态，可以使用“通知”选项卡。


## <a name="next-steps"></a>后续步骤  

* [为 Windows 或 Linux 虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [为 NFS 卷配置导出策略](azure-netapp-files-configure-export-policy.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [了解 Azure 服务的虚拟网络集成](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
