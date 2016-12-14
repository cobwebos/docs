---
title: "适用于虚拟机的加速网络 - PowerShell | Microsoft 文档"
description: "了解如何使用 PowerShell 为 Azure 虚拟机配置加速网络。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: a0b63599-c23b-40b5-a8ab-23af8b07dded
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 8ae59a99a8397676160c032b744460c0b2bcc1d7


---
# <a name="accelerated-networking-for-a-virtual-machine"></a>适用于虚拟机的加快网络
> [!div class="op_single_selector"]
> * [Azure 门户](virtual-network-accelerated-networking-portal.md)
> * [PowerShell](virtual-network-accelerated-networking-powershell.md)
> 
> 

使用加速网络可以实现对虚拟机 (VM) 的单根 I/O 虚拟化 (SR-IOV)，大幅提升其网络性能。 这种高性能路径会绕过数据路径中的主机，降低延迟、抖动，以及受支持 VM 类型上的最苛刻网络工作负荷的 CPU 利用率。 本文介绍如何使用 Azure PowerShell 在 Azure Resource Manager 部署模型中配置加速网络。 也可以使用 Azure 门户创建具有加速网络的 VM。 若要了解如何操作，请单击本文顶部的“Azure 门户”框。

下图显示了具有和没有加速网络的两个虚拟机 (VM) 之间的通信：

![比较](./media/virtual-network-accelerated-networking-powershell/image1.png)

在不使用加速网络的情况下，传入和传出 VM 的所有网络流量必须遍历主机和虚拟交换机。 虚拟交换机针对网络流量实施所有策略，例如网络安全组、访问控制列表、隔离和其他网络虚拟化服务。 若要了解详细信息，请阅读 [Hyper-V 网络虚拟化和虚拟交换机](https://technet.microsoft.com/library/jj945275.aspx)一文。

在使用加速网络的情况下，网络流量将抵达网卡 (NIC)，然后转发到 VM。 在不使用加速网络的情况下由虚拟交换机应用的所有网络策略将会卸载，并在硬件中应用。 由于在硬件中应用策略，NIC 可以绕过主机和虚拟交换机将网络流量直接转发到 VM，同时保留它在主机中应用的所有策略。

加速网络的优势仅适用于已启用该功能的 VM。 为获得最佳效果，最好是在连接到同一个 VNet 的最少两个 VM 上启用此功能。  跨 VNet 通信或者在本地连接时，此功能对总体延迟的影响极小。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="benefits"></a>优点
* **更低的延迟/更高的每秒数据包数 (pps)：**从数据路径中去除虚拟交换机可以消除数据包在主机中进行策略处理所花费的时间，同时增大了 VM 中可处理的数据包数。
* **减少抖动：**虚拟交换机处理取决于需要应用的策略数量，以及正在执行处理的 CPU 工作负荷。 将策略实施卸载到硬件消除了这种可变性，因为可以将数据包直接传送到 VM，省去了主机与 VM 之间的通信，以及所有的软件中断和上下文切换。
* **降低了 CPU 利用率：**绕过主机中的虚拟交换机可以减少用于处理网络流量的 CPU 资源。

## <a name="limitations"></a>限制
此功能存在以下限制：

* **网络接口创建：**只能为新网络接口启用加速网络。  无法在现有网络接口上启用它。
* **VM 创建：**已启用加速网络的网络接口只能在创建 VM 时附加到该 VM。 该网络接口无法附加到现有 VM。
* **区域：**仅在美国中西部以及西欧 Azure 区域提供。 推出的区域今后会不断增加。
* **支持的操作系统：**Microsoft Windows Server 2012 R2 和 Windows Server 2016 Technical Preview 5。 不久后即会增加对 Linux 和 Windows Server 2012 的支持。
* **VM 大小：**Standard_D15_v2 和 Standard_DS15_v2 是唯一受支持的 VM 实例大小。 有关详细信息，请参阅 [Windows VM 大小](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)一文。 支持的 VM 实例大小组合今后会不断增加。

当这些限制有变化时，我们会通过 [Azure 虚拟网络更新](https://azure.microsoft.com/updates/accelerated-networking-in-preview)页发出通告。

## <a name="create-a-windows-vm-with-accelerated-networking"></a>创建具有加速网络的 Windows VM
1. 打开 PowerShell 命令提示符，在单个 PowerShell 会话中完成本部分余下的步骤。 如果尚未安装并配置 PowerShell，请先完成[如何安装和配置 Azure PowerShell](../powershell-install-configure.md) 一文中所述的步骤。
2. 若要注册预览版，请向[加速网络订阅](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)发送一封包含订阅 ID 和目标用途的电子邮件。 只有在收到一封告知允许你使用预览版的电子邮件之后，才完成剩余的步骤。
3. 输入以下命令，将该功能注册到订阅：
   
        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
4. 将 *westcentralus* 替换为本文[限制](#limitations)部分中所列的、受此功能支持的另一位置名称（如果需要）。 输入以下命令设置位置的变量：
   
        $locName = "westcentralus"
5. 将 *RG1* 替换为包含新网络接口的资源组的名称，然后输入以下命令创建该资源组：
   
        $rgName = "RG1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
6. 将 *VM1-NIC1* 更改为所需的网络接口名称，然后输入以下命令：
   
        $NICName = "VM1-NIC1"
7. 网络接口必须连接到现有 Azure 虚拟网络 (VNet) 中的子网，该 VNet 所在的位置和[订阅](../azure-glossary-cloud-terminology.md#subscription)必须与该网络接口相同。 如果不熟悉 VNet，请阅读[虚拟网络概述](virtual-networks-overview.md)一文了解详细信息。 若要创建 VNet，请完成 [创建 VNet](virtual-networks-create-vnet-arm-ps.md) 一文中的步骤。 将以下 $Variable 的“值”更改为 VNet 的名称以及网络接口要连接到的子网。
   
        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"
   
    如果你不知道在执行步骤 3 时所选位置中的现有 VNet 的名称，请输入以下命令：
   
        $VirtualNetworks = Get-AzureRmVirtualNetwork
        $VirtualNetworks | Where-Object {$_.Location -eq $locName} | Format-Table Name, Location
   
    如果返回的列表为空，则需要在该位置创建一个 VNet。 若要创建 VNet，请完成[创建虚拟网络](virtual-networks-create-vnet-arm-ps.md)一文中的步骤。
   
    若要获取 VNet 中的子网名称，请键入以下命令并将上述 *Subnet1* 替换为某个子网的名称：
   
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $VNet.Subnets | Format-Table Name, AddressPrefix
8. 输入以下命令检索 VNet 和子网，并将其分配到变量。
   
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }
9. 找到可与网络接口关联的现有公共 IP 地址资源，以便可以通过 Internet 进行连接。 如果不想使用网络接口通过 Internet 访问 VM，可以跳过此步骤。 如果没有公共 IP 地址，必须从连接到同一 VNet 的另一个 VM 来与该 VM 建立连接。 
   
    将 *PIP1* 更改为在创建网络接口的位置中存在的、当前不与其他网络接口关联的现有公共 IP 地址资源的名称。 如果需要，可将 $rgName 更改为公共 IP 地址资源所在的资源组名称，然后输入以下命令：
   
        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $rgName
   
    如果不知道现有公共 IP 地址资源的名称，请输入以下命令：
   
        $PublicIPAddresses = Get-AzureRmPublicIPAddress
        $PublicIPAddresses | Where-Object { $_.Location -eq $locName } |Format-Table Name, Location, IPAddress, IpConfiguration
   
    如果返回的输出中 **IPConfiguration** 列没有任何值，则表示该公共 IP 地址资源不与现有的任何网络接口关联，因此可以使用它。 如果列表为空或没有可用的公共 IP 地址资源，可以使用 New-AzureRmPublicIPAddress 命令创建一个。
   
   > [!NOTE]
   > 公共 IP 地址会产生少许费用。 请阅读 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)页，详细了解价格。
   > 
   > 
10. 如果选择不将公共 IP 地址资源添加到接口，请删除以下命令末尾处的 *-PublicIPAddress $PIP1*。 输入以下命令创建具有加速网络的网络接口：
    
        $nic = New-AzureRmNetworkInterface -Location $locName -Name $NICName -ResourceGroupName $rgName -Subnet $Subnet -EnableAcceleratedNetworking -PublicIpAddress $PIP1 
11. 根据[创建 VM](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 一文中步骤 3 和 6 的说明创建 VM 时，会向 VM 分配网络接口。 在步骤 6-2 中，请将 *Standard_A1* 替换为本文[限制](#limitations)部分中所列的 VM 大小之一。
    
    > [!NOTE]
    > 如果更改了本文中 $locName、$rgName 或 $nic 变量的*名称*，“创建 VM”一文中的步骤 6 将会失败。 但是，可以更改变量的*值*。
    > 
    > 
12. 创建 VM 后，请下载[加速网络驱动程序](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84)，连接到该 VM，然后在其中运行驱动程序安装程序。
13. 右键单击 Windows 按钮，然后单击“**设备管理器**”。 检查“**Mellanox ConnectX-3 Virtual Function Ethernet Adapter**”是否显示在展开的“**网络**”选项下面，如下图所示：
    
    ![设备管理器](./media/virtual-network-accelerated-networking-powershell/image2.png)




<!--HONumber=Nov16_HO3-->


