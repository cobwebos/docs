---
title: "使用虚拟网络对等互连连接虚拟网络 - Azure 门户 | Microsoft Docs"
description: "了解如何使用虚拟网络对等互连连接虚拟网络。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ac0c1033546758a77b43298a5fa8cba5f5204650
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>通过 Azure 门户使用虚拟网络对等互连连接虚拟网络

可以使用虚拟网络对等互连将虚拟网络互相连接。 将虚拟网络对等互连后，两个虚拟网络中的资源将能够以相同的延迟和带宽相互通信，就像这些资源位于同一个虚拟网络中一样。 本文介绍如何创建两个虚拟网络并将其对等互连。 学习如何：

> [!div class="checklist"]
> * 创建两个虚拟网络
> * 创建虚拟网络间的对等互连
> * 测试对等互连

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="log-in-to-azure"></a>登录 Azure 

通过 http://portal.azure.com 登录到 Azure 门户。

## <a name="create-virtual-networks"></a>创建虚拟网络

1. 选择 Azure 门户左上角的“+ 创建资源”。
2. 选择“网络”，然后选择“虚拟网络”。
3. 如下图所示，对于“名称”，输入“myVirtualNetwork1”，对于“地址空间”，输入“10.0.0.0/16”，对于“资源组”，输入“myResourceGroup”，对于子网“名称”，输入“Subnet1”，对于子网“地址范围”，输入 10.0.0.0/24，选择一个位置和你的订阅，接受其余默认设置，然后选择“创建”：

    ![创建虚拟网络](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. 再次完成步骤 1-3，并做出以下更改：
    - **名称**：*myVirtualNetwork2*
    - **资源组**：选择“使用现有”，然后选择“myResourceGroup”。
    - **地址空间**：*10.1.0.0/16*
    - **子网地址范围**：*10.1.0.0/24*

    *myVirtualNetwork2* 虚拟网络的地址前缀与 *myVirtualNetwork1* 虚拟网络的地址空间不重叠。 不能对等互连地址空间重叠的虚拟网络。

## <a name="peer-virtual-networks"></a>将虚拟网络对等互连

1. 在 Azure 门户顶部的“搜索”框中，开始键入“MyVirtualNetwork1”。 当“myVirtualNetwork1”出现在搜索结果中时，将其选中。
2. 在“设置”下选择“对等互连”，然后选择“+ 添加”，如下图所示：

    ![创建对等互连](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. 输入或选择下图中所示的信息，然后选择“确定”。 若要选择 *myVirtualNetwork2* 虚拟网络，请依次选择“虚拟网络”、“myVirtualNetwork2”。

    ![对等互连设置](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    “对等互连状态”为“已启动”，如下图所示：

    ![对等互连状态](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    如果看不到状态，请刷新浏览器。

4. 搜索 *myVirtualNetwork2* 虚拟网络。 搜索结果中返回它后，请选择它。
5. 再次完成步骤 1-3，并做出以下更改，然后选择“确定”：
    - **名称**：*myVirtualNetwork2-myVirtualNetwork1*
    - **虚拟网络**：*myVirtualNetwork1*

    “对等互连状态”为“已连接”。 Azure 还将 *myVirtualNetwork2-myVirtualNetwork1* 对等互连的对等互连状态从“已启动”更改为“已连接”。 直到两个虚拟网络的对等互连状态均为“已连接”时，虚拟网络对等互连才完全建立。 

对等互连在两个虚拟网络之间进行，但不可传递。 因此，举例来说，如果还想要将 *myVirtualNetwork2* 对等互连到 *myVirtualNetwork3*，则需要另外在虚拟网络 *myVirtualNetwork2* 和 *myVirtualNetwork3* 之间创建对等互连。 即使 *myVirtualNetwork1* 已与 *myVirtualNetwork2* 对等互连，也仅当 *myVirtualNetwork1* 也与 *myVirtualNetwork3* 对等互连时，*myVirtualNetwork1* 中的资源才能访问 *myVirtualNetwork3* 中的资源。 

将生产虚拟网络对等互连之前，建议全面了解[对等互连概述](virtual-network-peering-overview.md)、[管理对等互连](virtual-network-manage-peering.md)和[虚拟网络限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 虽然本文以同一订阅和位置中两个虚拟网络之间的对等互连举例说明，但也可将[不同区域](#register)和[不同 Azure 订阅](create-peering-different-subscriptions.md#portal)中的虚拟网络对等互连。 还可以使用对等互连创建[中心辐射型网络设计](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)。

## <a name="test-peering"></a>测试对等互连

若要通过对等互连测试不同虚拟网络中的虚拟机之间的网络通信，请将虚拟机部署到每个子网，然后在虚拟机之间进行通信。 

### <a name="create-virtual-machines"></a>创建虚拟机

在每个虚拟网络中创建虚拟机，以便在稍后的步骤中可以验证它们之间的通信。

### <a name="create-virtual-machines"></a>创建虚拟机

1. 选择 Azure 门户左上角的“+ 创建资源”。
2. 选择“计算”，然后选择“Windows Server 2016 Datacenter”。 可以选择不同的操作系统，但剩余步骤假定你选择了“Windows Server 2016 Datacenter”。 
3. 对于“基本信息”，选择或输入以下信息，然后选择“确定”：
    - **名称**：*myVm1*
    - **资源组**：选择“使用现有”，然后选择“myResourceGroup”。
    - **位置**：选择“美国东部”。

    稍后的步骤中将使用你输入的“用户名”和“密码”。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。 所选的“位置”和“订阅”必须与虚拟网络所在的位置和订阅相同。 不要求选择在其中创建了虚拟网络的同一资源组，但本文中选择了同一资源组。
4. 在“选择大小”下选择 VM 大小。
5. 对于“设置”，选择或输入以下信息，然后选择“确定”：
    - **虚拟网络**：确保选择“myVirtualNetwork1”。 如果没有，请选择“虚拟网络”，然后在“选择虚拟网络”下选择“myVirtualNetwork1”。
    - **子网**：确保选择“Subnet1”。 如果没有，请选择“子网”，然后在“选择子网”下选择“Subnet1”，如下图所示：
    
        ![虚拟机设置](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. 在“摘要”中的“创建”下，选择“创建”以启动虚拟机部署。
7. 再次完成步骤 1-6，但对于虚拟机的“名称”，输入“myVm2”，对于“虚拟网络”，选择“myVirtualNetwork2”。

Azure 已分配 *10.0.0.4* 作为 *myVm1* 虚拟机的专用 IP 地址，并已将 10.1.0.4 分配到 *myVm2* 虚拟机，因为它们分别是 *myVirtualNetwork1* 和 *myVirtualNetwork2* 虚拟网络的 *Subnet1* 中的第一个可用 IP 地址。

创建虚拟机需花费几分钟的时间。 在两个虚拟机完成创建之前，不要继续执行剩余的步骤。

### <a name="test-virtual-machine-communication"></a>测试虚拟机通信

1. 在门户顶部的“搜索”框中，开始键入“myVm1”。 当“myVm1”出现在搜索结果中时，请选择它。
2. 通过选择“连接”创建到 *myVm1* 虚拟机的远程桌面连接，如下图中所示：

    ![连接到虚拟机](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. 若要连接到 VM，请打开已下载的 RDP 文件。 出现提示时，选择“连接”。
4. 输入在创建虚拟机时指定的用户名和密码（可能需要选择“更多选择”，然后选择“使用其他帐户”，以便指定在创建虚拟机时输入过的凭据），然后选择“确定”。
5. 你可能会在登录过程中收到证书警告。 选择“是”以继续进行连接。
6. 在后面的步骤中，将使用 ping 从 *myVmWeb* 虚拟机与 *myVm2* 虚拟机进行通信。 Ping 使用默认情况下无法通过 Windows 防火墙的 ICMP。 在命令提示符处输入以下命令，允许 ICMP 通过 Windows 防火墙：

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    虽然本文中使用 ping 进行测试，但是对于生产部署，建议不要允许 ICMP 穿过 Windows 防火墙。

7. 若要连接到 *myVm2* 虚拟机，请在 *myVm1* 虚拟机上通过命令提示符输入以下命令：

    ```
    mstsc /v:10.1.0.4
    ```
    
8. 由于启用了对 *myVm1* 的 ping，现在可以按 IP 地址 ping 它：

    ```
    ping 10.0.0.4
    ```
    
    会收到四条回复。 如果按虚拟机的名称 (*myVm1*) 而不是其 IP 地址进行 ping 操作，ping 操作会失败，因为 *myVm1* 是未知的主机名。 Azure 的默认名称解析在同一虚拟网络中的虚拟机之间可以正常工作，但在不同虚拟网络中的虚拟机之间无法正常工作。 若要跨虚拟网络解析名称，必须[部署自己的 DNS 服务器](virtual-networks-name-resolution-for-vms-and-role-instances.md)或使用 [Azure DNS 专用域](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

9. 断开到 *myVm1* 和 *myVm2* 的 RDP 会话。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将资源组及其包含的所有资源一并删除： 

1. 在门户顶部的“搜索”框中输入“myResourceGroup”。 当在搜索结果中看到“myResourceGroup”时，将其选中。
2. 选择“删除资源组”。
3. 对于“键入资源组名称:”，输入“myResourceGroup”，然后选择“删除”。

**<a name="register"></a>注册全局虚拟网络对等互连（预览版）**

在同一区域中的虚拟网络之间建立对等互连的功能已推出正式版。 在不同区域的虚拟网络之间建立对等互连目前处于预览版状态。 有关可用区域，请参阅[虚拟网络更新](https://azure.microsoft.com/updates/?product=virtual-network)。 若要跨区域对立虚拟网络对等互连，必须先注册预览版。 无法使用门户注册，但可以使用 [PowerShell](tutorial-connect-virtual-networks-powershell.md#register) 或 [Azure CLI](tutorial-connect-virtual-networks-cli.md#register) 注册。 如果在注册功能之前尝试为不同区域中的虚拟网络建立对等互连，对等互连将会失败。

## <a name="next-steps"></a>后续步骤

本文已介绍如何使用虚拟网络对等互连来连接两个网络。

请继续通过 VPN 将自己的计算机连接到虚拟网络，并与虚拟网络或对等互连的虚拟网络中的资源进行交互。

> [!div class="nextstepaction"]
> [将计算机连接到虚拟网络](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
