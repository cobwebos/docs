---
title: 为 Azure NetApp 文件创建 NFS 卷 |微软文档
description: 描述如何为 Azure NetApp 文件创建 NFS 卷。
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
ms.date: 12/01/2019
ms.author: b-juche
ms.openlocfilehash: 9e8817f802ca1d73ca0f6bfa2b32b1b14b37d7da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274081"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>创建用于 Azure NetApp 文件的 NFS 卷

Azure NetApp 文件支持 NFS（NFSv3 和 NFSv4.1）和 SMBv3 卷。 卷的容量消耗是依据其池的预配容量计数的。 本文介绍如何创建 NFS 卷。 如果要创建 SMB 卷，请参阅为[Azure NetApp 文件创建 SMB 卷](azure-netapp-files-create-volumes-smb.md)。 

## <a name="before-you-begin"></a>开始之前 
必须已设置容量池。   
[设置容量池](azure-netapp-files-set-up-capacity-pool.md)   
子网必须委派给 Azure NetApp 文件。  
[将子网委派给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)

## <a name="considerations"></a>注意事项 

* 决定使用哪个 NFS 版本  
  NFSv3 可以处理各种用例，通常部署在大多数企业应用程序中。 您应该验证应用程序需要的版本（NFSv3 或 NFSv4.1），并使用适当的版本创建卷。 例如，如果使用 Apache [ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave)，建议通过 NFSv3 锁定带有 NFSv4.1 的文件。 

* 安全性  
  NFSv3 和 NFSv4.1 支持 UNIX 模式位（读取、写入和执行）。 NFS 客户端上需要根级访问才能装载 NFS 卷。

* 本地用户/组和 LDAP 支持 NFSv4.1  
  目前，NFSv4.1 仅支持对卷的根访问。 请参阅[为 Azure NetApp 文件配置 NFSv4.1 默认域](azure-netapp-files-configure-nfsv41-domain.md)。 

## <a name="best-practice"></a>最佳做法

* 应确保对卷使用正确的装载说明。  请参阅[为 Windows 或 Linux 虚拟机安装或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)。

* NFS 客户端应与 Azure NetApp 文件卷位于同一 VNet 或对等 VNet 中。 支持从 VNet 外部进行连接;但是，它会带来额外的延迟并降低整体性能。

* 应确保 NFS 客户端是最新的，并运行操作系统的最新更新。

## <a name="create-an-nfs-volume"></a>创建 NFS 卷

1.  单击"容量池"边栏选项卡中的 **"卷**"边栏选项卡。 

    ![导航到卷](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  单击“+ 添加卷”**** 以创建卷。  
    将显示"创建卷"窗口。

3.  在"创建卷"窗口中，单击"**创建**并提供有关以下字段的信息"：   
    * **卷名称**      
        指定要创建的卷的名称。   

        卷名称在每个容量池中必须是唯一的。 它的长度必须至少为三个字符。 您可以使用任何字母数字字符。   

        不能用作`default`卷名称。

    * **容量池**  
        指定要创建卷的容量池。

    * **配额**  
        指定分配给卷的逻辑存储量。  

        “可用配额”**** 字段显示了所选容量池中可以用来创建新卷的未使用空间量。 新卷的大小不能超过可用配额。  

    * **虚拟网络**  
        指定要从中访问卷的 Azure 虚拟网络 (Vnet)。  

        你指定的 Vnet 必须已将子网委托给 Azure NetApp 文件。 只能从同一 Vnet 或者从与卷位于同一区域的 Vnet 通过 Vnet 对等互连来访问 Azure NetApp 文件服务。 您还可以通过快速路由从本地网络访问卷。   

    * **子**  
        指定要用于卷的子网。  
        你指定的子网必须委派给 Azure NetApp 文件。 
        
        如果尚未委派子网，可以在“创建卷”页面上单击“新建”。**** 然后，在“创建子网”页面中，指定子网信息，并选择“Microsoft.NetApp/卷”**** 来为 Azure NetApp 文件委派子网。 在每个 Vnet 中，只能将一个子网委派给 Azure NetApp 文件。   
 
        ![创建卷](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![创建子网](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. 单击“协议”****，然后完成以下操作：  
    * 选择“NFS”**** 作为卷的协议类型。   
    * 指定将用于为新卷创建导出路径**的文件路径**。 导出路径用来装载并访问卷。

        文件路径名只能包含字母、数字和连字符 ("-")。 它的长度必须介于 16 到 40 个字符之间。 

        文件路径在每个订阅和每个区域中必须是唯一的。 

    * 选择卷的 NFS 版本（**NFSv3** 或 **NFSv4.1**）。  
    * 或者，[为 NFS 卷配置导出策略](azure-netapp-files-configure-export-policy.md)。

    ![指定 NFS 协议](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. 单击 **"查看 + 创建**"以查看卷详细信息。  然后单击 **"创建**"以创建 NFS 卷。

    您创建的卷将显示在"卷"页中。 
 
    卷从其容量池继承订阅、资源组和位置属性。 若要监视卷部署状态，可以使用“通知”选项卡。


## <a name="next-steps"></a>后续步骤  

* [为 Azure NetApp 文件配置 NFSv 4.1 默认域](azure-netapp-files-configure-nfsv41-domain.md)
* [为 Windows 或 Linux 虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [为 NFS 卷配置导出策略](azure-netapp-files-configure-export-policy.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [了解 Azure 服务的虚拟网络集成](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
