---
title: 为 Azure NetApp 文件创建卷 | Microsoft Docs
description: 介绍了如何为 Azure NetApp 文件创建卷。
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
ms.date: 6/6/2019
ms.author: b-juche
ms.openlocfilehash: 657bacc153b5721d5a9f34792eaf4796cb477755
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808876"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>为 Azure NetApp 文件创建卷

每个容量池最多可以有 500 个卷。 卷的容量消耗是依据其池的预配容量计数的。 Azure 的 NetApp 文件支持 NFS 和 SMBv3 卷。 

## <a name="before-you-begin"></a>开始之前 
必须已设置容量池。   
[设置容量池](azure-netapp-files-set-up-capacity-pool.md)   
子网必须委派给 Azure NetApp 文件。  
[将子网委派给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)

## <a name="create-an-nfs-volume"></a>创建 NFS 卷

1.  单击**卷**边栏选项卡中的容量池边栏选项卡。 

    ![导航到卷](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  单击“+ 添加卷”  以创建卷。  
    将显示一个卷窗口创建。

3.  在创建卷窗口中，单击**创建**，并提供以下字段的信息：   
    * **卷名**      
        指定要创建的卷的名称。   

        卷名必须是每个容量池内唯一的。 它的长度必须至少为三个字符。 可以使用任何字母数字字符。

    * **容量池**  
        指定想要创建的卷的容量池。

    * **配额**  
        指定分配给卷的逻辑存储量。  

        “可用配额”  字段显示了所选容量池中可以用来创建新卷的未使用空间量。 新卷的大小不能超过可用配额。  

    * **虚拟网络**  
        指定要从中访问卷的 Azure 虚拟网络 (Vnet)。  

        你指定的 Vnet 必须已将子网委托给 Azure NetApp 文件。 只能从同一 Vnet 或者从与卷位于同一区域的 Vnet 通过 Vnet 对等互连来访问 Azure NetApp 文件服务。 此外可以从本地网络通过 Expressroute 访问该卷。   

    * **子网**  
        指定要用于卷的子网。  
        你指定的子网必须委派给 Azure NetApp 文件。 
        
        如果尚未委派子网，可以在“创建卷”页面上单击“新建”。  然后，在“创建子网”页面中，指定子网信息，并选择“Microsoft.NetApp/卷”  来为 Azure NetApp 文件委派子网。 每个 Vnet 中只有一个子网可以委派给 Azure NetApp 文件。   
 
        ![创建卷](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![创建子网](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. 单击“协议”  ，然后选择“NFS”  作为该卷的协议类型。   
    * 指定**文件路径**将用于创建新卷导出路径。 导出路径用来装载并访问卷。

        文件路径名只能包含字母、数字和连字符 ("-")。 它的长度必须介于 16 到 40 个字符之间。 

        文件路径必须是在每个订阅和每个区域中唯一的。 

    * （可选）[配置导出策略的 NFS 卷](azure-netapp-files-configure-export-policy.md)

    ![指定 NFS 协议](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. 单击**查看 + 创建**若要查看卷的详细信息。  然后单击**创建**创建 NFS 卷。

    您创建的卷将显示在卷页。 
 
    卷从其容量池继承订阅、资源组和位置属性。 若要监视卷部署状态，可以使用“通知”选项卡。

## <a name="create-an-smb-volume"></a>创建 SMB 卷

Azure 的 NetApp 文件支持 SMBv3 卷。 您需要添加的 SMB 卷之前创建 Active Directory 连接。 

### <a name="requirements-for-active-directory-connections"></a>Active Directory 连接的要求

 Active Directory 连接的要求如下所示： 

* 所使用的管理员帐户必须能够在您将指定的组织单位 (OU) 路径中创建计算机帐户。  

* 必须在适用的 Windows Active Directory (AD) 服务器上打开正确的端口。  
    所需的端口是按如下所示： 

    |     服务           |     Port     |     Protocol     |
    |-----------------------|--------------|------------------|
    |    AD Web 服务    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    不适用       |    回送答复    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS 名称       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    安全 LDAP        |    636       |    TCP           |
    |    安全 LDAP        |    3269      |    TCP           |
    |    w32time            |    123       |    UDP           |

### <a name="create-an-active-directory-connection"></a>创建 Active Directory 连接

1. 在 NetApp 帐户中，单击**Active Directory 连接**，然后单击**加入**。  

    ![Active Directory 连接](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. 在加入 Active Directory 窗口中，提供以下信息：

    * **主 DNS**  
        这是 Active Directory 域加入和 SMB 身份验证操作所需的 DNS。 
    * **辅助 DNS**   
        这是确保冗余名称服务的辅助 DNS 服务器。 
    * **域**  
        这是你想要加入的 Active Directory 域服务的域名。
    * **SMB 服务器 （计算机帐户） 前缀**  
        这是 Azure NetApp 文件都将使用新的帐户中创建的 Active Directory 中的计算机帐户的命名前缀。

        例如，如果你的组织的文件服务器使用的命名标准为 NAS-01、 02...，NAS NAS 045，则输入"NAS"为前缀。 

        根据需要该服务将在 Active Directory 中创建其他的计算机帐户。

    * **组织单位路径**  
        这是在其中创建 SMB 服务器计算机帐户的组织单位 (OU) 的 LDAP 路径。 也就是说，OU = 第二个级别，OU = 第一个级别。 
    * 凭据，包括你**用户名**和**密码**

    ![Join Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. 单击“加入”  。  

    将显示你创建的 Active Directory 连接。

    ![Active Directory 连接](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

### <a name="add-an-smb-volume"></a>添加 SMB 卷

1. 单击**卷**边栏选项卡中的容量池边栏选项卡。 

    ![导航到卷](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. 单击“+ 添加卷”  以创建卷。  
    将显示一个卷窗口创建。

3. 在创建卷窗口中，单击**创建**，并提供以下字段的信息：   
    * **卷名**      
        指定要创建的卷的名称。   

        卷名必须是每个容量池内唯一的。 它的长度必须至少为三个字符。 可以使用任何字母数字字符。

    * **容量池**  
        指定想要创建的卷的容量池。

    * **配额**  
        指定分配给卷的逻辑存储量。  

        “可用配额”  字段显示了所选容量池中可以用来创建新卷的未使用空间量。 新卷的大小不能超过可用配额。  

    * **虚拟网络**  
        指定要从中访问卷的 Azure 虚拟网络 (Vnet)。  

        你指定的 Vnet 必须已将子网委托给 Azure NetApp 文件。 只能从同一 Vnet 或者从与卷位于同一区域的 Vnet 通过 Vnet 对等互连来访问 Azure NetApp 文件服务。 此外可以从本地网络通过 Expressroute 访问该卷。   

    * **子网**  
        指定要用于卷的子网。  
        你指定的子网必须委派给 Azure NetApp 文件。 
        
        如果尚未委派子网，可以在“创建卷”页面上单击“新建”。  然后，在“创建子网”页面中，指定子网信息，并选择“Microsoft.NetApp/卷”  来为 Azure NetApp 文件委派子网。 每个 Vnet 中只有一个子网可以委派给 Azure NetApp 文件。   
 
        ![创建卷](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![创建子网](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. 单击**协议**并完成以下信息：  
    * 选择**SMB**作为该卷的协议类型。 
    * 选择你**Active Directory**下拉列表中的连接。
    * 指定的名称中的共享卷**共享名**。

    ![指定 SMB 协议](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. 单击**查看 + 创建**若要查看卷的详细信息。  然后单击**创建**SMB 卷创建操作。

    您创建的卷将显示在卷页。 
 
    卷从其容量池继承订阅、资源组和位置属性。 若要监视卷部署状态，可以使用“通知”选项卡。

## <a name="next-steps"></a>后续步骤  

* [装载或卸载 Windows 或 Linux 虚拟机的卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [为 NFS 卷配置导出策略](azure-netapp-files-configure-export-policy.md)
* [了解 Azure 服务的虚拟网络集成](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
