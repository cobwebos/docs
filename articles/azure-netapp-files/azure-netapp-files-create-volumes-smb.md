---
title: 为 Azure NetApp 文件创建 SMB 卷 |微软文档
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
ms.date: 03/13/2020
ms.author: b-juche
ms.openlocfilehash: b2000c3fd3d64793f797e997d8f3c10eaed5d7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409571"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>创建用于 Azure NetApp 文件的 SMB 卷

Azure NetApp 文件支持 NFS 和 SMBv3 卷。 卷的容量消耗是依据其池的预配容量计数的。 本文介绍如何创建 SMBv3 卷。 如果要创建 NFS 卷，请参阅为[Azure NetApp 文件创建 NFS 卷](azure-netapp-files-create-volumes.md)。 

## <a name="before-you-begin"></a>开始之前 
必须已设置容量池。   
[设置容量池](azure-netapp-files-set-up-capacity-pool.md)   
子网必须委派给 Azure NetApp 文件。  
[将子网委派给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>活动目录连接的要求

 在创建 SMB 卷之前，您需要创建活动目录连接。 活动目录连接的要求如下： 

* 您使用的管理员帐户必须能够创建要指定的组织单位 （OU） 路径中的计算机帐户。  

* 必须在适用的 Windows 活动目录 （AD） 服务器上打开正确的端口。  
    所需的端口如下所示： 

    |     服务           |     端口     |     协议     |
    |-----------------------|--------------|------------------|
    |    AD Web 服务    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    空值       |    回声回复    |
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
    |    w32time            |    123       |    UDP           |

* 目标活动目录域服务的站点拓扑必须符合最佳做法，尤其是部署 Azure NetApp 文件的 Azure VNet。  

    部署 Azure NetApp 文件虚拟网络的地址空间必须添加到新的或现有的活动目录站点（Azure NetApp 文件可访问域控制器所在的站点）。 

* 指定的 DNS 服务器必须从 Azure NetApp 文件的[委派子网](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)进行。  

    请参阅[Azure NetApp 文件网络规划指南](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)，了解支持的网络拓扑。

    网络安全组 （NSG） 和防火墙必须具有适当配置的规则，以允许活动目录和 DNS 流量请求。 

* Azure NetApp 文件委派子网必须能够覆盖域中的所有活动目录域服务 （ADDS） 域控制器，包括所有本地域和远程域控制器。 否则，可能会发生服务中断。  

    如果 Azure NetApp 文件委派子网无法访问的域控制器，则可以在创建活动目录连接期间指定活动目录站点。  Azure NetApp 文件只需要与 Azure NetApp 文件委派子网地址空间所在的站点中的域控制器通信。

    请参阅设计有关 AD 站点和服务[的站点拓扑](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology)。 
    
有关其他 AD 信息，请参阅 Azure NetApp 文件[SMB 常见问题解答](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)。 

## <a name="decide-which-domain-services-to-use"></a>决定使用哪些域服务 

Azure NetApp 文件支持 AD 连接[的活动目录域服务](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology)（ADDS） 和 Azure 活动目录域服务 （AADDS）。  在创建 AD 连接之前，您需要决定是使用 ADDS 还是 AADDS。  

有关详细信息，请参阅[比较自管理的活动目录域服务、Azure 活动目录和托管 Azure 活动目录域服务](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions)。 

### <a name="active-directory-domain-services"></a>Active Directory 域服务

您可以将首选[的活动目录站点和服务](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology)范围用于 Azure NetApp 文件。 此选项支持读取和写入[Azure NetApp 文件可访问的](azure-netapp-files-network-topologies.md)活动目录域服务 （ADDS） 域控制器。 它还阻止服务与不在指定活动目录站点和服务站点中的域控制器通信。 

要在使用 ADDS 时查找站点名称，可以联系组织中负责活动目录域服务的管理组。 下面的示例显示了显示站点名称的活动目录站点和服务插件： 

![Active Directory 站点和服务](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-and-services.png)

为 Azure NetApp 文件配置 AD 连接时，请在**AD 站点名称**字段的作用域中指定站点名称。

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory 域服务 

有关 Azure 活动目录域服务 （AADDS） 配置和指导，请参阅[Azure AD 域服务文档](https://docs.microsoft.com/azure/active-directory-domain-services/)。

其他 AADDS 注意事项适用于 Azure NetApp 文件： 

* 确保部署 AADDS 的 VNet 或子网与 Azure NetApp 文件部署位于相同的 Azure 区域中。
* 如果在部署 Azure NetApp 文件的区域中使用另一个 VNet，则应在两个 VNet 之间创建对等互连。
* Azure NetApp`user`文件`resource forest`支持和类型。
* 对于同步类型，您可以选择`All`或`Scoped`。   
    如果选择`Scoped`，请确保为访问 SMB 共享选择了正确的 Azure AD 组。  如果不确定，可以使用`All`同步类型。
* 需要使用企业版或高级 SKU。 不支持标准 SKU。

创建活动目录连接时，请注意 AADDS 的以下细节：

* 您可以在 AADDS 菜单中找到**主 DNS、****辅助 DNS**和 AD **DNS 域名**的信息。  
对于 DNS 服务器，将使用两个 IP 地址来配置活动目录连接。 
* **组织单位路径**为`OU=AADDC Computers`。  
此设置在**NetApp 帐户**下的 **"活动目录连接**"中配置：

  ![组织单位路径](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **用户名**凭据可以是 Azure AD 组**Azure AD DC 管理员**的成员的任何用户。


## <a name="create-an-active-directory-connection"></a>创建活动目录连接

1. 从 NetApp 帐户中，单击 **"活动目录连接**"，然后单击"**加入**"。  

    ![活动目录连接](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. 在"加入活动目录"窗口中，根据要使用的域服务提供以下信息：  

    有关特定于您使用的域服务的信息，请参阅[决定使用哪些域服务](#decide-which-domain-services-to-use)。 

    * **主 DNS**  
        这是活动目录域联接和 SMB 身份验证操作所需的 DNS。 
    * **辅助 DNS**   
        这是用于确保冗余名称服务的辅助 DNS 服务器。 
    * **AD DNS 域名**  
        这是要加入的活动目录域服务的域名。
    * **AD 网站名称**  
        这是域控制器发现将限制的站点名称。
    * **SMB 服务器（计算机帐户）前缀**  
        这是 Azure NetApp 文件将用于创建新帐户的活动目录中的计算机帐户的命名前缀。

        例如，如果组织用于文件服务器的命名标准为 NAS-01、NAS-02...、NAS-045，则前缀将输入"NAS"。 

        该服务将根据需要在活动目录中创建其他计算机帐户。

    * **组织单位路径**  
        这是将创建 SMB 服务器计算机帐户的组织单位 （OU） 的 LDAP 路径。 也就是说，OU=第二级，OU=第一级。 

        如果将 Azure NetApp 文件与 Azure 活动目录域服务一起使用，则`OU=AADDC Computers`组织单位路径是为 NetApp 帐户配置活动目录时。
        
    * 凭据，包括**您的用户名和密码****password**

    ![加入活动目录](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. 单击“加入” ****。  

    将显示您创建的活动目录连接。

    ![活动目录连接](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> 保存活动目录连接后，可以编辑用户名和密码字段。 保存连接后，无法编辑任何其他值。 如果需要更改任何其他值，必须首先删除任何已部署的 SMB 卷，然后删除并重新创建 Active Directory 连接。

## <a name="add-an-smb-volume"></a>添加 SMB 卷

1. 单击"容量池"边栏选项卡中的 **"卷**"边栏选项卡。 

    ![导航到卷](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. 单击“+ 添加卷”**** 以创建卷。  
    将显示"创建卷"窗口。

3. 在"创建卷"窗口中，单击"**创建**并提供有关以下字段的信息"：   
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
        指定要从中访问卷的 Azure 虚拟网络 （VNet）。  

        指定的 VNet 必须将子网委派给 Azure NetApp 文件。 Azure NetApp 文件服务只能从同一 VNet 或通过 VNet 对等互连的卷位于与卷相同的区域的 VNet 访问。 您还可以通过快速路由从本地网络访问卷。   

    * **子**  
        指定要用于卷的子网。  
        你指定的子网必须委派给 Azure NetApp 文件。 
        
        如果尚未委派子网，则可以在"创建卷"页上单击 **"创建新**"。 然后，在“创建子网”页面中，指定子网信息，并选择“Microsoft.NetApp/卷”**** 来为 Azure NetApp 文件委派子网。 在每个 VNet 中，只能将一个子网委派给 Azure NetApp 文件。   
 
        ![创建卷](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![创建子网](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. 单击 **"协议"** 并完成以下信息：  
    * 选择**SMB**作为卷的协议类型。 
    * 从下拉列表中选择**活动目录**连接。
    * 在**共享名称**中指定共享卷的名称。

    ![指定 SMB 协议](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. 单击 **"查看 + 创建**"以查看卷详细信息。  然后单击"**创建**"以创建 SMB 卷。

    您创建的卷将显示在"卷"页中。 
 
    卷从其容量池继承订阅、资源组和位置属性。 若要监视卷部署状态，可以使用“通知”选项卡。

## <a name="next-steps"></a>后续步骤  

* [为 Windows 或 Linux 虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [中小企业常见问题解答](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [了解 Azure 服务的虚拟网络集成](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [使用 Azure CLI 安装新的活动目录林](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
