---
title: "在 Azure 虚拟机上安装本地 Active Directory 域的副本域控制器 | Microsoft Docs"
description: "如何在 Azure 虚拟网络中的 Azure 虚拟机 (VM) 上安装本地 Active Directory 域的副本 DC。"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 8c9ebf1b-289a-4dd6-9567-a946450005c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: f4e64fbc6c2fda026297b69bd54471d49b6785a1
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>在 Azure 虚拟网络中安装副本 Active Directory 域控制器
本文介绍如何在 Azure 虚拟网络中的 Azure 虚拟机 (VM) 上为本地 Active Directory 域安装用作副本 DC 的域控制器 (DC)。 也可以[在 Azure 虚拟网络中安装 Windows Server Active Directory 林](active-directory-new-forest-virtual-machine.md)。 有关如何在 Azure 虚拟网络上安装 Active Directory 域服务 (AD DS)，请参阅[在 Azure 虚拟机上部署 Windows Server Active Directory 的指南](https://msdn.microsoft.com/library/azure/jj156090.aspx)。

## <a name="scenario-diagram"></a>方案示意图
在此案例中，外部用户需要访问在添加域的服务器上运行的应用程序。 运行应用程序服务器和副本 DC 的 VM 安装在 Azure 虚拟网络中。 可以使用 [ExpressRoute](../expressroute/expressroute-locations-providers.md) 或[站点到站点 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) 将虚拟网络连接到本地网络，如下所示： 

![用图解法表示 pf Active Directory 域控制器 Azure vnet][1]

应用程序服务器和 DC 将部署在独立的云服务中以分散计算处理，并部署在可用性集中改进容错功能。
DC 将使用 Active Directory 复制功能在彼此之间以及与本地 DC 相互复制。 不需要任何同步工具。

## <a name="create-an-on-premises-active-directory-site-for-the-azure-virtual-network"></a>创建 Azure 虚拟网络的本地 Active Directory 站点
可以在 Active Directory 中创建一个站点来表示对应于虚拟网络的网络区域。 此站点有助于优化身份验证、复制及其他 DC 位置操作。 以下步骤说明了如何创建站点。有关更多背景信息，请参阅 [Adding a New Site](https://technet.microsoft.com/library/cc781496.aspx)（添加新站点）。

1. 打开 Active Directory 站点和服务：“服务器管理器” > “工具” > “Active Directory 站点和服务”。
2. 创建一个站点，用于表示创建的 Azure 虚拟网络所在的区域：单击“站点” > “操作” > “新建站点”> 键入新站点的名称（例如“Azure 美国西部”）> 选择站点链接 > 单击“确定”。
3. 创建子网并将其与新站点关联：双击“站点”> 右键单击“子网” > “新建子网”> 键入虚拟网络的 IP 地址范围（例如方案示意图中的 10.1.0.0/16）> 选择新的 Azure 站点 > 单击“确定”。

## <a name="create-an-azure-virtual-network"></a>创建 Azure 虚拟网络
若要创建 Azure 虚拟网络并设置站点到站点 VPN，请遵循[创建站点到站点连接](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)一文中的步骤。 

还可以配置虚拟网络网关，以便创建安全的站点到站点 VPN 连接。 在新的虚拟网络与本地 VPN 设备之间创建站点到站点 VPN 连接。 有关说明，请参阅[配置虚拟网络网关](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md)。

## <a name="create-azure-vms-for-the-dc-roles"></a>为 DC 角色创建 Azure VM
若要创建用于托管 DC 角色的 VM，请根据需要重复[使用 Azure 门户创建 Windows 虚拟机](../virtual-machines/windows/quick-create-portal.md)中的步骤。 至少部署两个虚拟域控制器来提供容错和冗余。 如果 Azure 虚拟网络包含至少两个采用类似配置的 DC，则可将运行这些 DC 的 VM 放在可用性集中，以获得更高的容错能力。

若要使用 Windows PowerShell 而不是 Azure 门户创建 VM，请参阅[使用 Azure PowerShell 创建和预配置基于 Windows 的虚拟机](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

为要运行 DC 角色的 VM 保留静态 IP 地址。 若要保留静态 IP 地址，请下载 Microsoft Web 平台安装程序，[安装 Azure PowerShell](/powershell/azure/overview) 并运行 Set-AzureStaticVNetIP cmdlet。 例如：

````
Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM
````
有关如何设置静态 IP 地址的详细信息，请参阅[为 VM 配置静态内部 IP 地址](../virtual-network/virtual-networks-reserved-private-ip.md)。

## <a name="install-ad-ds-on-azure-vms"></a>在 Azure VM 上安装 AD DS
登录 VM，并确认已通过站点到站点 VPN 建立连接，或者与本地网络上的资源建立了 ExpressRoute 连接。 然后在 Azure VM 上安装 AD DS。 可以使用在本地网络上安装其他域控制器的相同过程（UI、Windows PowerShell 或应答文件）。 安装 AD DS 时，请务必指定新卷的 AD 数据库、日志和 SYSVOL 的位置。 如需在 AD DS 安装中使用刷新程序，请参阅[安装 Active Directory 域服务（级别 100）](https://technet.microsoft.com/library/hh472162.aspx)或[在现有域中安装副本 Windows Server 2012 域控制器（级别 200）](https://technet.microsoft.com/library/jj574134.aspx)。

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>重新配置虚拟网络的 DNS 服务器
1. 若要获取虚拟网络名称的列表，请在 [Azure 门户](https://portal.azure.com)中搜索“虚拟网络”，然后选择“虚拟网络”查看该列表。 
2. 打开想要管理的虚拟网络，然后[重新配置虚拟网络的 DNS 服务器 IP 地址](../virtual-network/manage-virtual-network.md#change-dns-servers)，以便使用分配到副本 DC 的静态 IP 地址，而不是本地 DNS 服务器的 IP 地址。
3. 若要确保虚拟网络中的所有副本 DC VM 配置为使用虚拟网络上的 DNS 服务器：
  1. 选择“虚拟机”。
  2. 选择 VM，然后选择“重启”。 
  3. 等到 VM 的状态再次变为“正在运行”后登录到其中。

## <a name="create-vms-for-application-servers"></a>为应用程序服务器创建 VM

若要创建用于托管应用程序服务器角色的 VM，请根据需要重复[使用 Azure 门户创建 Windows 虚拟机](../virtual-machines/windows/quick-create-portal.md)中的步骤。 若要使用 Microsoft PowerShell 而不是 Azure 门户创建 VM，请参阅[使用 Azure PowerShell 创建和配置基于 Windows 的虚拟机](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。 下表包含建议的设置。

| 设置 | 值 |
| --- | --- |
|  **选择映像** | Windows Server 2012 R2 Datacenter |
|  **虚拟机配置** |<p>虚拟机名称：键入单个标签名称（例如 AppServer1）。</p><p>新用户名：键入用户的名称。 此用户将是 VM 上本地管理员组的成员。 在首次登录 VM 时，需要使用此名称。 名为“管理员”的内置帐户将无法使用。</p><p>新密码/确认：键入密码</p> |
|  **虚拟机配置** |<p>云服务：针对第一个 VM 选择“创建新云服务”，并在创建更多用于托管应用程序的 VM 时选择该云服务名称。</p><p>云服务 DNS 名称：指定一个全局唯一的名称</p><p>区域/地缘组/虚拟网络：指定虚拟网络名称（例如 WestUSVNet）。</p><p>存储帐户：针对第一个 VM 选择“使用自动生成的存储帐户”，并在创建更多用于托管应用程序的 VM 时选择该存储帐户名称。</p><p>可用性集：选择“创建可用性集”。</p><p>可用性集名称：键入创建第一个 VM 时的可用性集的名称，并在创建更多 VM 时选择该名称。</p> |
|  **虚拟机配置** |<p>选择“安装 VM 代理”，以及所需的任何其他扩展。<b></b></p> |
  
预配每个 VM 之后，登录 VM 并将其加入域。 
1. 在“服务器管理器”&gt;“本地服务器”&gt;“工作组”&gt;“更改...”中，选择“域”。
2. 输入本地域的名称。 
3. 提供域用户的凭据。
4. 重启 VM。

## <a name="additional-resources"></a>其他资源

* 有关如何使用 Windows PowerShell 的详细信息，请参阅 [Get Started with Azure Cmdlets](/powershell/azure/overview)（Azure Cmdlet 入门）和 [Azure Cmdlet 参考](/powershell/azure/get-started-azureps)。
* [在 Azure 虚拟机上部署 Windows Server Active Directory 的指南](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [如何通过使用 Azure PowerShell 上传到 Azure 的现有内部部署 Hyper-V 域控制器](http://support.microsoft.com/kb/2904015)
* [在 Azure 虚拟网络中安装新的 Active Directory 林](active-directory-new-forest-virtual-machine.md)
* [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)
* [Microsoft Azure IT Pro IaaS: (01) Virtual Machine Fundamentals](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)（Microsoft Azure IT Pro IaaS：(01) 虚拟机基础知识）
* [Microsoft Azure IT Pro IaaS: (05) Creating Virtual Networks and Cross-Premises Connectivity](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)（Microsoft Azure IT Pro IaaS：(05) 创建虚拟网络和跨界连接）
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Management Cmdlets](/powershell/module/azurerm.compute/#virtual_machines)（Azure 管理 Cmdlet）

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
