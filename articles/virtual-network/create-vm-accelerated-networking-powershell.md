---
title: "创建具有加速网络的 Azure 虚拟机 | Microsoft Docs"
description: "了解如何创建具有加速网络的 Linux 虚拟机。"
services: virtual-network
documentationcenter: 
author: jdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: jimdial
ms.openlocfilehash: f4908963e0650be9b12b745f6868a1ba6ad933e4
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>创建具有加速网络的 Windows 虚拟机

> [!IMPORTANT] 
> 必须创建启用加速网络的虚拟机。 现有虚拟机不能启用此功能。 可按照以下步骤启用加速网络
>   1. 删除虚拟机
>   2. 重新创建启用加速网络的虚拟机
>

本教程介绍如何创建具有加速网络的 Windows 虚拟机 (VM)。 使用加速网络可以实现对 VM 的单根 I/O 虚拟化 (SR-IOV)，大幅提升其网络性能。 这种高性能路径会绕过数据路径中的主机，降低延迟、抖动，以及受支持 VM 类型上的最苛刻网络工作负荷的 CPU 利用率。 下图显示了具有和没有加速网络的两个 VM 之间的通信：

![比较](./media/create-vm-accelerated-networking/accelerated-networking.png)

在不使用加速网络的情况下，传入和传出 VM 的所有网络流量必须遍历主机和虚拟交换机。 虚拟交换机针对网络流量实施所有策略，例如网络安全组、访问控制列表、隔离和其他网络虚拟化服务。 若要详细了解虚拟交换机，请阅读 [Hyper-V 网络虚拟化和虚拟交换机](https://technet.microsoft.com/library/jj945275.aspx)一文。

在使用加速网络的情况下，网络流量将抵达 VM 的网络接口 (NIC)，然后转发到 VM。 由虚拟交换机应用的所有网络策略现在都会卸载，并在硬件中应用。 由于在硬件中应用策略，NIC 可以绕过主机和虚拟交换机将网络流量直接转发到 VM，同时保留它在主机中应用的所有策略。

加速网络的优势仅适用于已启用该功能的 VM。 为获得最佳效果，最好是在连接到同一个 Azure 虚拟网络 (VNet) 的最少两个 VM 上启用此功能。 跨 VNet 通信或者在本地连接时，此功能对总体延迟的影响极小。

## <a name="benefits"></a>优点
* **更低的延迟/更高的每秒数据包数 (pps)：**从数据路径中去除虚拟交换机可以消除数据包在主机中进行策略处理所花费的时间，同时增大了 VM 中可处理的数据包数。
* **减少抖动：**虚拟交换机处理取决于需要应用的策略数量，以及正在执行处理的 CPU 工作负荷。 将策略实施卸载到硬件消除了这种可变性，因为可以将数据包直接传送到 VM，省去了主机与 VM 之间的通信，以及所有的软件中断和上下文切换。
* **降低了 CPU 利用率：**绕过主机中的虚拟交换机可以减少用于处理网络流量的 CPU 资源。

## <a name="supported-operating-systems"></a>支持的操作系统
Microsoft Windows Server 2012 R2 Datacenter 和 Windows Server 2016。

## <a name="supported-vm-instances"></a>支持的 VM 实例
大多数常规用途实例以及具有 4 个或更多 vCPU 的计算优化实例都支持加速网络。 在支持超线程的实例上（如 D/DSv3 或 E/ESv3），具有 8 个或更多 vCPU 的 VM 实例支持加速网络。 支持的系列包含 D/DSv2、D/DSv3、E/ESv3、F/Fs/Fsv2 和 Ms/Mms。

有关 VM 实例的详细信息，请参阅[Windows VM 大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="regions"></a>区域
在所有公共 Azure 区域和 Azure 政府云中均可用。 

## <a name="limitations"></a>限制
此功能存在以下限制：

* **网络接口创建：**只能为新 NIC 启用加速网络。 不能为现有 NIC 启用。
* **VM 创建：**已启用加速网络的 NIC 只能在创建 VM 时附加到该 VM。 该 NIC 无法附加到现有 VM。 如果将 VM 添加到现有可用性集，可用性集中的所有 VM 也必须启用加速网络。
* **仅通过 Azure 资源管理器部署：**不能部署具有加速网络的虚拟机（经典）。

## <a name="create-a-virtual-network"></a>创建虚拟网络

安装 [Azure PowerShell](/powershell/azure/install-azurerm-ps) 5.1.1 版或更高版本。 要查找当前安装的版本，请运行 `Get-Module -ListAvailable AzureRM`。 如果需要进行安装或升级，请从 [PowerShell 库](https://www.powershellgallery.com/packages/AzureRM)安装最新版本的 AzureRM 模块。 在 PowerShell 会话中，使用 [Add-AzureRmAccount](/powershell/module/AzureRM.Profile/Add-AzureRmAccount) 登录到 Azure 帐户。

在以下示例中，请将示例参数名称替换为自己的值。 参数名称示例包括 myResourceGroup、myNic 和 myVM。

使用 [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup) 创建资源组。 以下示例在 centralus 位置创建名为 myResourceGroup 的资源组：

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "centralus"
```

首先，使用 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetworkSubnetConfig) 创建子网配置。 以下示例创建名为 mySubnet 的子网：

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

使用 [New-AzureRmVirtualNetwork](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetwork) 创建虚拟网络，带 mySubnet 子网。

```powershell
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>创建网络安全组

首先，使用 [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityRuleConfig) 创建网络安全组规则。

```powershell
$rdp = New-AzureRmNetworkSecurityRuleConfig `
    -Name 'Allow-RDP-All' `
    -Description 'Allow RDP' `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 100 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389
```

使用 [New-AzureRmNetworkSecurityGroup](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityGroup) 创建网络安全组，并向其分配 Allow-RDP-All 安全规则。 除 Allow-RDP-All 规则外，该网络安全组还包含多个默认规则。 其中一个默认规则会禁用所有来自 Internet 的入站访问，这正是向网络安全组分配 Allow-RDP-All 规则的原因，为的是在虚拟机创建后能够远程连接到虚拟机。

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

使用 *Set-AzureRmVirtualNetworkSubnetConfig* 将网络安全组关联到 mySubnet 子网[](/powershell/module/AzureRM.Network/Set-AzureRmVirtualNetworkSubnetConfig)。 网络安全组中的规则对子网中部署的所有资源都是有效的。

```powershell
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>创建具有加速网络的网络接口
使用 [New-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/New-AzureRmPublicIpAddress) 创建一个公共 IP 地址。 如果不打算从 Internet 访问虚拟机，则不需要公共 IP 地址，但必须完成本文中的步骤。

```powershell
$publicIp = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

使用 [New-AzureRmNetworkInterface](/powershell/module/AzureRM.Network/New-AzureRmNetworkInterface) 创建启用了加速网络的网络接口，并将公共 IP 地址分配给该网络接口。 下面的示例在 myVnet 虚拟网络的 mySubnet 子网中创建名为 myNic 的网络接口，并向其分配 myPublicIp 公共 IP 地址：

```powershell
$nic = New-AzureRmNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>创建虚拟机

使用 [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) 将 VM 凭据设置为 `$cred` 变量：

```powershell
$cred = Get-Credential
```

首先，通过 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) 定义 VM。 以下示例定义名为 myVM 的 VM，其大小支持加速网络 (*Standard_DS4_v2*)：

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

若要获取所有 VM 大小和特性列表，请参阅 [Windows VM 大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

通过 [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) 和 [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) 创建 VM 配置的其余部分。 以下示例创建一个 Windows Server 2016 VM：

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig `
    -Windows `
    -ComputerName "myVM" `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig `
    -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" `
    -Skus "2016-Datacenter" `
    -Version "latest"
```

通过 [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) 附加之前创建的网络接口：

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

最后，通过 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建你的 VM：

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>确认操作系统中安装了驱动程序

在 Azure 中创建 VM 后，立即连接到 VM 并确认 Windows 中安装了驱动程序。 

1. 在 Internet 浏览器中打开 Azure [门户](https://portal.azure.com)并使用 Azure 帐户登录。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入 myVm。 当“myVm”出现在搜索结果中时，请单击它。 如果“连接”按钮下方显示“正在创建”，则表示 Azure 尚未完成创建 VM。 仅在“连接”按钮下方不再显示“正在创建”时，才在概述的左上角单击“连接”。
3. 输入在[创建虚拟机](#create-the-virtual-machine)中输入的用户名和密码。 如果从未连接到 Azure 中的 Windows VM，请参阅[连接到虚拟机](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine)。
4. 右键单击 Windows“开始”按钮，并单击“设备管理器”。 展开“网络适配器”节点。 确认已显示“Mellanox ConnectX-3 Virtual Function Ethernet Adapter”，如下图所示：
   
    ![设备管理器](./media/create-vm-accelerated-networking/device-manager.png)

现在已为 VM 启用加速网络。
