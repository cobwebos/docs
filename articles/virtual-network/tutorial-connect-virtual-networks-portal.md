---
title: 使用 VNet 对等互连连接虚拟网络 - 教程 - Azure 门户
description: 本教程介绍如何使用 Azure 门户通过虚拟网络对等互连来连接虚拟网络。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: e95441aab6c8ce7de37ba5f6b08d5f7d54e13347
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "77201292"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>教程：通过 Azure 门户使用虚拟网络对等互连连接虚拟网络

可以使用虚拟网络对等互连将虚拟网络互相连接。 这些虚拟网络可以位于相同区域或不同区域中（也称为全局 VNet 对等互连）。 将虚拟网络对等互连后，两个虚拟网络中的资源将能够以相同的延迟和带宽相互通信，就像这些资源位于同一个虚拟网络中一样。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建两个虚拟网络
> * 使用虚拟网络对等互连连接两个虚拟网络。
> * 将虚拟机 (VM) 部署到每个虚拟网络
> * VM 之间进行通信

如果你愿意，可以使用 [Azure CLI](tutorial-connect-virtual-networks-cli.md) 或 [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md) 完成本教程中的步骤。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="log-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-virtual-networks"></a>创建虚拟网络

1. 在 Azure 门户中，选择“创建资源”。 
2. 选择“网络”，然后选择“虚拟网络”   。
3. 在“基本信息”  选项卡上，输入或选择以下信息并接受其余设置的默认值：

    |设置|值|
    |---|---|
    |订阅| 选择订阅。|
    |资源组| 选择“新建”，并输入 myResourceGroup  |
    |区域| 选择“美国东部”  。|
    |名称|myVirtualNetwork1|

4. 在“IP 地址”  选项卡上，为“地址空间”  字段输入 10.0.0.0/16。 单击下面的“添加子网”  按钮，并输入 *Subnet1* 作为**子网名称**，输入 10.0.0.0/24 作为**子网地址范围**。
5. 选择“查看 + 创建”，然后选择“创建”。  
   
5. 再次完成步骤 1-5，但需要做出以下更改：

    |设置|值|
    |---|---|
    |名称|myVirtualNetwork2|
    |地址空间|10.1.0.0/16|
    |资源组| 选择“使用现有”，然后选择“myResourceGroup”   。|
    |子网名称 | Subnet2|
    |子网地址范围|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>将虚拟网络对等互连

1. 在 Azure 门户顶部的“搜索”  框中，开始键入“MyVirtualNetwork1”。 当“myVirtualNetwork1”出现在搜索结果中时，将其选中。 
2. 在“设置”下选择“对等互连”，然后选择“添加”，如下图所示：   

    ![创建对等互连](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. 输入或选择以下信息，接受剩下的默认设置，然后选择“确定”  。

    |设置|值|
    |---|---|
    |从 myVirtualNetwork1 到远程虚拟网络的对等互连的名称|myVirtualNetwork1-myVirtualNetwork2 - 当页面首次加载时，将在此处看到短语“remote virtual network”。 选择远程虚拟网络后，短语“远程虚拟网络”将替换为远程虚拟网络的名称。|
    |订阅| 选择订阅。|
    |虚拟网络|myVirtualNetwork2 - 若要选择 *myVirtualNetwork2* 虚拟网络，请依次选择“虚拟网络”、“myVirtualNetwork2 (myResourceGroup)”   。 可以在相同区域或不同区域中选择虚拟网络。|
    |创建从 myVirtualNetwork2 到 myVirtualNetwork1 的对等互连名称|myVirtualNetwork2-myVirtualNetwork1|

    ![对等互连设置](./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional.png)

    “对等互连状态”为“已连接”，如下图所示：  

    ![对等互连状态](./media/tutorial-connect-virtual-networks-portal/peering-status-connected.png)

    如果看不到状态，请刷新浏览器。

## <a name="create-virtual-machines"></a>创建虚拟机

在稍后的步骤中，会在每个虚拟网络中创建一个 VM，以便可以在它们之间进行通信。

### <a name="create-the-first-vm"></a>创建第一个 VM

1. 在 Azure 门户中，选择“创建资源”。 
2. 选择“计算”，然后选择“Windows Server 2016 Datacenter”。   可以选择不同的操作系统，但剩余步骤假定你选择了“Windows Server 2016 Datacenter”。  
3. 对于“基本信息”输入或选择以下信息，接受剩下的默认设置，然后选择“创建”   ：

    |设置|值|
    |---|---|
    |资源组| 选择“使用现有”，然后选择“myResourceGroup”   。|
    |名称|myVM1|
    |位置| 选择“美国东部”  。|
    |用户名| 输入所选用户名。|
    |密码| 输入所选密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
   
4. 为“大小”  选项选择 VM 大小。
5. 在“网络”  下选择以下值：

    |设置|值|
    |---|---|
    |虚拟网络| myVirtualNetwork1 - 如果尚未选择它，请选择“虚拟网络”，然后选择“myVirtualNetwork1”。  |
    |子网| Subnet1 - 如果尚未选择它，请选择“子网”  ，然后选择“Subnet1”  。|
   
6. 选择“网络”  。 为“公共入站端口”选项选择“允许选定端口”。 为此下面的“选择入站端口”选项选择 **RDP**。 

7. 选择左下角的“查看 + 创建”  按钮开始 VM 部署。

### <a name="create-the-second-vm"></a>创建第二个 VM

再次完成步骤 1-6，并做出以下更改：

|设置|值|
|---|---|
|名称 | myVm2|
|虚拟网络 | myVirtualNetwork2|

创建 VM 可能需要数分钟的时间。 在两个 VM 完成创建之前，不要继续执行剩余的步骤。

## <a name="communicate-between-vms"></a>VM 之间进行通信

1. 在门户顶部的“搜索”  框中，开始键入“myVm1”。  当“myVm1”出现在搜索结果中时，请选择它。 
2. 通过选择“连接”创建到 *myVm1* VM 的远程桌面连接  ，如下图中所示：

    ![连接到虚拟机](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. 若要连接到 VM，请打开已下载的 RDP 文件。 出现提示时，选择“连接”  。
4. 输入在创建 VM 时指定的用户名和密码（可能需要选择“更多选择”，然后选择“使用其他帐户”，以便指定在创建 VM 时输入的凭据），然后选择“确定”。   
5. 你可能会在登录过程中收到证书警告。 选择“是”以继续进行连接。 
6. 在后面的步骤中，将使用 ping 从 *myVm1* VM 与 *myVm2* VM 进行通信。 Ping 使用 Internet 控制消息协议 (ICMP)，默认情况下会拒绝 ICMP 通过 Windows 防火墙。 在 *myVm1* VM 上，允许 ICMP 穿过 Windows 防火墙，以便在稍后的步骤中可以使用 PowerShell 从 *myVm2* 对此 VM 执行 ping 命令：

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```
    
    虽然本教程中使用 ping 在 VM 之间进行通信，但在进行生产部署时，不建议允许 ICMP 通过 Windows 防火墙。

7. 若要连接到 *myVm2* VM，请在 *myVm1* VM 上通过命令提示符输入以下命令：

    ```
    mstsc /v:10.1.0.4
    ```
    
8. 由于启用了对 *myVm1* 的 ping，现在可以按 IP 地址 ping 它：

    ```
    ping 10.0.0.4
    ```
    
9. 断开到 *myVm1* 和 *myVm2* 的 RDP 会话。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将资源组及其包含的所有资源一并删除： 

1. 在门户顶部的“搜索”框中输入“myResourceGroup”   。 当在搜索结果中看到“myResourceGroup”时，将其选中。 
2. 选择“删除资源组”  。
3. 对于“键入资源组名称:”，输入“myResourceGroup”，然后选择“删除”。   

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用虚拟网络对等互连来连接同一 Azure 区域中的两个网络。 还可以将不同[受支持的区域](virtual-network-manage-peering.md#cross-region)、[不同 Azure 订阅](create-peering-different-subscriptions.md#portal)中的虚拟网络对等互连，并且可以使用对等互连创建[中心辐射型网络设计](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering)。 若要详细了解虚拟网络对等互连，请参阅[虚拟网络对等互连概述](virtual-network-peering-overview.md)和[管理虚拟网络对等互连](virtual-network-manage-peering.md)。

若要通过 VPN 将自己的计算机连接到虚拟网络，并与虚拟网络或对等互连的虚拟网络中的资源进行交互，请参阅[将计算机连接到虚拟网络](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
