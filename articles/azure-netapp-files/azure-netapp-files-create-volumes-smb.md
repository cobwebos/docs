---
title: 为 Azure NetApp 文件创建 SMB 卷 |Microsoft Docs
description: 介绍如何为 Azure NetApp 文件创建 SMB 卷。
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
ms.date: 10/12/2019
ms.author: b-juche
ms.openlocfilehash: 94fc4906478e44365d03e9c8eeadd7cb1946a43a
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300541"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>创建用于 Azure NetApp 文件的 SMB 卷

Azure NetApp 文件支持 NFS 和 SMBv3 卷。 卷的容量消耗是依据其池的预配容量计数的。 本文介绍如何创建 SMBv3 卷。 若要创建 NFS 卷，请参阅[为 Azure NetApp 文件创建 NFS 卷](azure-netapp-files-create-volumes.md)。 

## <a name="before-you-begin"></a>开始之前 
必须已设置容量池。   
[设置容量池](azure-netapp-files-set-up-capacity-pool.md)   
子网必须委派给 Azure NetApp 文件。  
[将子网委派给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Active Directory 连接的要求

 创建 SMB 卷之前，需要创建 Active Directory 连接。 Active Directory 连接的要求如下： 

* 你使用的管理员帐户必须能够在你将指定的组织单位（OU）路径中创建计算机帐户。  

* 在适用的 Windows Active Directory （AD）服务器上必须打开正确的端口。  
    所需的端口如下： 

    |     服务           |     Port     |     Protocol     |
    |-----------------------|--------------|------------------|
    |    AD Web 服务    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    不可用       |    回显回复    |
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

* 目标 Active Directory 域服务的站点拓扑必须遵循最佳做法，尤其是 azure VNet 部署 azure NetApp 文件的位置。  

    必须将 Azure NetApp 文件部署到的虚拟网络的地址空间添加到新的或现有的 Active Directory 站点（其中，Azure NetApp 文件可访问的域控制器）。 

* 指定的 DNS 服务器必须可从 Azure NetApp 文件的[委托子网](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)访问。  

    请参阅[Azure NetApp 文件的准则网络规划](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)以获取支持的网络拓扑。

    网络安全组（Nsg）和防火墙必须具有适当配置的规则，以允许 Active Directory 和 DNS 流量请求。 

* Azure NetApp 文件委托子网必须能够访问域中的所有 Active Directory 域服务（添加）域控制器，包括所有本地和远程域控制器。 否则，可能会发生服务中断。  

    如果你的域控制器无法通过 Azure NetApp 文件委托子网访问，你可以提交 Azure 支持请求，将作用域从**全局**（默认）更改为**站点**。  Azure NetApp 文件只需与 Azure NetApp 文件委托子网地址空间所在的站点中的域控制器进行通信。

    请参阅设计有关 AD 站点和服务[的站点拓扑](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology)。 

## <a name="create-an-active-directory-connection"></a>创建 Active Directory 连接

1. 在你的 NetApp 帐户中，单击 " **Active Directory 连接**"，然后单击 "**加入**"。  

    ![Active Directory 连接](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. 在 "联接 Active Directory" 窗口中提供以下信息：

    * **主 DNS**  
        这是 Active Directory 域加入和 SMB 身份验证操作所需的 DNS。 
    * **辅助 DNS**   
        这是确保冗余名称服务的辅助 DNS 服务器。 
    * **域**  
        这是你要加入的 Active Directory 域服务的域名。
    * **SMB 服务器（计算机帐户）前缀**  
        这是 Azure NetApp 文件将用于创建新帐户 Active Directory 中的计算机帐户的命名前缀。

        例如，如果你的组织用于文件服务器的命名标准为 "NAS-01，NAS-02 ...，045"，则你将输入 "NAS" 作为前缀。 

        该服务将根据需要在 Active Directory 中创建其他计算机帐户。

    * **组织单位路径**  
        这是将在其中创建 SMB 服务器计算机帐户的组织单位（OU）的 LDAP 路径。 即 OU = 第二级，OU = 第一级。 

        如果将 Azure NetApp 文件与 Azure Active Directory 域服务一起使用，则在为 NetApp 帐户配置 Active Directory 时，组织单位路径 @no__t 为0。
        
    * 凭据，包括**用户名**和**密码**

    ![联接 Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. 单击“加入”。  

    此时将显示创建的 Active Directory 连接。

    ![Active Directory 连接](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> 保存 Active Directory 连接后，可以编辑 "用户名" 和 "密码" 字段。 保存连接后，不能再编辑其他值。 如果需要更改其他任何值，则必须先删除所有已部署的 SMB 卷，然后删除并重新创建 Active Directory 连接。

## <a name="add-an-smb-volume"></a>添加 SMB 卷

1. 单击 "容量池" 边栏选项卡中的 "**卷**" 边栏选项卡。 

    ![导航到卷](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. 单击“+ 添加卷”以创建卷。  
    此时将显示 "创建卷" 窗口。

3. 在 "创建卷" 窗口中，单击 "**创建**"，并为以下字段提供信息：   
    * **卷名**      
        指定要创建的卷的名称。   

        卷名称在每个容量池中必须是唯一的。 它的长度必须至少为三个字符。 您可以使用任何字母数字字符。   

        不能使用 `default` 作为卷名。

    * **容量池**  
        指定要在其中创建卷的容量池。

    * **配额**  
        指定分配给卷的逻辑存储量。  

        “可用配额”字段显示了所选容量池中可以用来创建新卷的未使用空间量。 新卷的大小不能超过可用配额。  

    * **虚拟网络**  
        指定要从中访问卷的 Azure 虚拟网络（VNet）。  

        指定的 VNet 必须具有委托给 Azure NetApp 文件的子网。 只能从同一 VNet 或通过 VNet 对等互连中的同一区域中的 VNet 访问 Azure NetApp 文件服务。 还可以通过 Express Route 从本地网络访问该卷。   

    * **子网**  
        指定要用于卷的子网。  
        你指定的子网必须委派给 Azure NetApp 文件。 
        
        如果尚未委派子网，可以在“创建卷”页面上单击“新建”。 然后，在“创建子网”页面中，指定子网信息，并选择“Microsoft.NetApp/卷”来为 Azure NetApp 文件委派子网。 在每个 VNet 中，只能将一个子网委托给 Azure NetApp 文件。   
 
        ![创建卷](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![创建子网](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. 单击 "**协议**" 并完成以下信息：  
    * 选择**SMB**作为卷的协议类型。 
    * 从下拉列表中选择**Active Directory**连接。
    * 在 "**共享名**" 中指定共享卷的名称。

    ![指定 SMB 协议](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. 单击 "**查看 + 创建**" 查看卷详细信息。  然后单击 "**创建**" 创建 SMB 卷。

    你创建的卷将出现在 "卷" 页中。 
 
    卷从其容量池继承订阅、资源组和位置属性。 若要监视卷部署状态，可以使用“通知”选项卡。

## <a name="next-steps"></a>后续步骤  

* [为 Windows 或 Linux 虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [SMB 常见问题解答](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [了解 Azure 服务的虚拟网络集成](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [使用 Azure CLI 安装新的 Active Directory 林](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
