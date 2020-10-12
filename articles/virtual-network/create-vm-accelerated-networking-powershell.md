---
title: 创建具有加速网络的 Windows VM - Azure PowerShell
description: 创建具有加速网络的 Windows 虚拟机 () ，以大幅提高其网络性能。
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/15/2020
ms.author: gsilva
ms.openlocfilehash: fd50af98fe0d7f20273c45e2b86c18215a3626f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87289629"
---
# <a name="create-a-windows-vm-with-accelerated-networking-using-azure-powershell"></a>使用 Azure PowerShell 创建具有加速网络的 Windows VM

本教程介绍如何创建具有加速网络的 Windows 虚拟机 (VM)。

> [!NOTE]
> 若要将加速网络用于 Linux 虚拟机，请参阅[创建具有加速网络的 Linux VM](create-vm-accelerated-networking-cli.md)。

使用加速网络可以实现对 VM 的单根 I/O 虚拟化 (SR-IOV)，大幅提升其网络性能。 这种高性能路径会绕过数据路径中的主机，为受支持 VM 类型上最苛刻的网络工作负载降低延迟、抖动和 CPU 利用率。 下图说明了在使用和不使用加速网络的情况下，两个 VM 如何通信：

![在使用和不使用加速网络的情况下，Azure 虚拟机之间的通信](./media/create-vm-accelerated-networking/accelerated-networking.png)

在不使用加速网络的情况下，传入和传出 VM 的所有网络流量必须遍历主机和虚拟交换机。 虚拟交换机针对网络流量实施所有策略，例如网络安全组、访问控制列表、隔离和其他网络虚拟化服务。

> [!NOTE]
> 若要详细了解虚拟交换机，请参阅 [Hyper-V 虚拟交换机](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch)。

在使用加速网络的情况下，网络流量将抵达 VM 的网络接口 (NIC)，然后转发到 VM。 由虚拟交换机应用的所有网络策略现在都会卸载，并在硬件中应用。 由于在硬件中应用策略，NIC 可以将网络流量直接转发到 VM。 NIC 会绕过主机和虚拟交换机，同时维护主机中应用的所有策略。

加速网络的优势仅适用于已启用该功能的 VM。 为获得最佳效果，请在连接到同一个 Azure 虚拟网络的至少两个 VM 上启用此功能。 跨虚拟网络通信或者在本地连接时，此功能对总体延迟的影响极小。

## <a name="benefits"></a>优点

- **降低延迟/提高每秒数据包数 (pps)** ：消除数据路径中的虚拟交换机可以减少数据包在主机中进行策略处理花费的时间。 还会增加 VM 中可处理的数据包数。

- **减少波动**：虚拟交换机处理取决于需要应用的策略数量。 还取决于执行处理的 CPU 工作负载。 将策略实施卸载到硬件，可将数据包直接传送到 VM，从而消除这种可变性。 卸载还会消除主机到 VM 的通信、所有软件中断和所有上下文切换。

- **降低 CPU 利用率**：绕过主机中的虚拟交换机可以减少用于处理流量的 CPU 资源。

## <a name="supported-operating-systems"></a>支持的操作系统

由 Azure 库直接支持以下发行版：

- **Windows Server 2019 Datacenter**
- **Windows Server 2016 Datacenter** 
- **Windows Server 2012 R2 Datacenter**

## <a name="limitations-and-constraints"></a>限制和约束

### <a name="supported-vm-instances"></a>支持的 VM 实例

大多数常规用途实例以及具有两个或更多虚拟 CPU (vCPU) 的计算优化实例都支持加速网络。  这些受支持的系列包括：Dv2/DSv2 和 F/Fs。

在支持超线程的实例上，具有四个或更多个 vCPU 的 VM 实例支持加速网络。 支持的系列包括： D/Dsv3、D/Dsv4、Da/Dasv4、E/Esv3、Ea/Easv4、Fsv2、Lsv2、Ms/Mms 和 Ms/Mmsv2。

有关 VM 实例的详细信息，请参阅 [Azure 中的 Windows 虚拟机大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

### <a name="custom-images"></a>自定义映像

如果你使用的是自定义映像，并且映像支持加速网络，请确保安装了可以在 Azure 上与 Mellanox 的 ConnectX-3 和 ConnectX-4 Lx NIC 配合使用的必需驱动程序。

### <a name="regions"></a>区域

所有全球 Azure 区域和 Azure 政府云中都提供加速网络。

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>在正在运行的 VM 上启用加速网络

未启用加速网络的受支持 VM 大小只有在停止和解除分配时才能启用该功能。

### <a name="deployment-through-azure-resource-manager"></a>通过 Azure 资源管理器部署

虚拟机（经典）无法部署加速网络。

## <a name="vm-creation-using-the-portal"></a>使用门户创建 VM

尽管本文提供了使用 Azure Powershell 创建具有加速网络的 VM 的步骤，但也可以[使用 Azure 门户创建启用加速网络的虚拟机](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 在门户中创建 VM 时，在“创建虚拟机”页中，选择“网络”选项卡 。此选项卡具有“加速网络”的选项。 如果已选择[支持的操作系统](#supported-operating-systems)和 [VM 大小](#supported-vm-instances)，此选项会自动设置为“打开”。 否则，该选项会设置为“关”，Azure 将显示无法启用它的原因。

> [!NOTE]
> 只有受支持的操作系统才能通过门户启用。 如果你使用的是自定义映像，并且该映像支持加速网络，请使用 CLI 或 PowerShell 创建 VM。 

创建 VM 后，可以确认是否已启用加速网络。 按照以下说明操作：

1. 转到 [Azure 门户](https://portal.azure.com)来管理 VM。 搜索并选择“虚拟机”。

2. 在虚拟机列表中，选择新的 VM。

3. 在 VM 菜单栏中选择“网络”。

在网络接口信息中的“加速网络”标签旁边，门户会显示加速网络状态为“已禁用”或“已启用”  。

## <a name="vm-creation-using-powershell"></a>使用 PowerShell 创建 VM

请先安装 [Azure PowerShell](/powershell/azure/install-az-ps) 1.0.0 版或更高版本。 要查找当前安装的版本，请运行 `Get-Module -ListAvailable Az`。 如果需要进行安装或升级，请从 [PowerShell 库](https://www.powershellgallery.com/packages/Az)安装最新版本的 Az 模块。 在 PowerShell 会话中，使用 [AzAccount](/powershell/module/az.accounts/connect-azaccount)登录到 Azure 帐户。

在以下示例中，请将示例参数名称替换成自己的值。 参数名称示例包括 myResourceGroup、myNic 和 myVM。  

### <a name="create-a-virtual-network"></a>创建虚拟网络

1. 使用 [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) 创建资源组。 以下命令在 " *centralus* " 位置创建名为 " *myResourceGroup* " 的资源组：

    ```azurepowershell
    New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
    ```

2. 使用 [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig) 创建子网配置。 以下命令创建名为 mySubnet 的子网：

    ```azurepowershell
    $subnet = New-AzVirtualNetworkSubnetConfig `
        -Name "mySubnet" `
        -AddressPrefix "192.168.1.0/24"
    ```

3. 使用 [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork) 创建带 mySubnet 子网的虚拟网络。

    ```azurepowershell
    $vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "centralus" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $Subnet
    ```

### <a name="create-a-network-security-group"></a>创建网络安全组

1. 使用 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig) 创建网络安全组规则。

    ```azurepowershell
    $rdp = New-AzNetworkSecurityRuleConfig `
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

2. 使用 [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) 创建网络安全组，并向其分配 Allow-RDP-All 安全规则。 除 Allow-RDP-All 规则外，该网络安全组还包含多个默认规则。 某个默认规则禁用来自 Internet 的所有入站访问。 创建后，Allow-RDP-All 规则分配给网络安全组，以便可以远程连接到 VM。

    ```azurepowershell
    $nsg = New-AzNetworkSecurityGroup `
        -ResourceGroupName myResourceGroup `
        -Location centralus `
        -Name "myNsg" `
        -SecurityRules $rdp
    ```

3. 使用 [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig) 将网络安全组关联到 *mySubnet* 子网。 网络安全组中的规则对子网中部署的所有资源都是有效的。

    ```azurepowershell
    Set-AzVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name 'mySubnet' `
        -AddressPrefix "192.168.1.0/24" `
        -NetworkSecurityGroup $nsg
    ```

### <a name="create-a-network-interface-with-accelerated-networking"></a>创建具有加速网络的网络接口

1. 使用 [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress) 创建一个公共 IP 地址。 如果不打算从 Internet 访问 VM，则不需要公共 IP 地址。 但是需要完成本文中的步骤。

    ```azurepowershell
    $publicIp = New-AzPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name 'myPublicIp' `
        -location centralus `
        -AllocationMethod Dynamic
    ```

2. 使用 [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) 创建启用了加速网络的网络接口，并将公共 IP 地址分配给该网络接口。 下面的示例在 myVnet 虚拟网络的 mySubnet 子网中创建名为 myNic 的网络接口，向其分配 myPublicIp 公共 IP 地址   ：

    ```azurepowershell
    $nic = New-AzNetworkInterface `
        -ResourceGroupName "myResourceGroup" `
        -Name "myNic" `
        -Location "centralus" `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $publicIp.Id `
        -EnableAcceleratedNetworking
    ```

### <a name="create-a-vm-and-attach-the-network-interface"></a>创建 VM 并附加网络接口

1. 使用 [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) 将 VM 凭据设置为 `$cred` 变量，这样会提示你登录：

    ```azurepowershell
    $cred = Get-Credential
    ```

2. 通过 [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) 定义你的 VM。 以下命令定义名为 myVM 的 VM，其大小支持加速网络 (Standard_DS4_v2) ：

    ```azurepowershell
    $vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
    ```

    若要获取所有 VM 大小和特性列表，请参阅 [Windows VM 大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

3. 通过 [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) 和 [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) 创建 VM 配置的其余部分。 以下命令将创建 Windows Server 2016 VM：

    ```azurepowershell
    $vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
    ```

4. 使用 [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) 附加之前创建的网络接口：

    ```azurepowershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

5. 使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 创建 VM。

    ```azurepowershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
    ```

### <a name="confirm-the-ethernet-controller-is-installed-in-the-windows-vm"></a>确认 Windows VM 中已安装以太网控制器

在 Azure 中创建 VM 后，立即连接到 VM 并确认 Windows 中安装了以太网控制器。

1. 转到 [Azure 门户](https://portal.azure.com)来管理 VM。 搜索并选择“虚拟机”。

2. 在虚拟机列表中，选择新的 VM。

3. 在 VM 概述页中，如果 VM 的“状态”列为“正在创建”，请等到 Azure 完成 VM 创建 。 VM 创建完成后，“状态”将更改为“正在运行” 。

4. 在 VM 概述工具栏中，选择“连接” > “RDP” > “下载 RDP 文件”  。

5. 打开 .rdp 文件，然后使用在[创建 VM 并附加网络接口](#create-a-vm-and-attach-the-network-interface)部分中输入的凭据登录到 VM。 如果从未连接到 Azure 中的 Windows VM，请参阅[连接到虚拟机](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine)。

6. 显示 VM 的远程桌面会话后，右键单击 Windows“开始”按钮，然后选择“设备管理器”。

7. 在“设备管理器”窗口中，展开“网络适配器”节点 。

8. 确认已显示“Mellanox ConnectX-3 虚函数以太网适配器”，如下图所示：

    ![Mellanox ConnectX-3 虚函数以太网适配器，适用于加速网络的新网络适配器，设备管理器](./media/create-vm-accelerated-networking/device-manager.png)

现在已为 VM 启用加速网络。

> [!NOTE]
> 如果 Mellanox 适配器启动失败，请在远程桌面会话中打开管理员提示符，然后输入以下命令：
>
> `netsh int tcp set global rss = enabled`

## <a name="enable-accelerated-networking-on-existing-vms"></a>在现有 VM 上启用加速网络

如果创建的 VM 没有加速网络，则可在现有 VM 上启用此功能。 VM 必须支持加速网络，前提是满足以下先决条件（上文亦有列出）：

* VM 必须是加速网络支持的大小。
* VM 必须是受支持的 Azure 库映像（以及适用于 Linux 的内核版本）。
* 在任何 NIC 上启用加速网络前，必须停止或解除分配可用性集或虚拟机规模集中的所有 VM。

### <a name="individual-vms-and-vms-in-an-availability-set"></a>单个 VM 与可用性集中的 VM

1. 停止或解除分配 VM，或集合中的所有 VM（如果是可用性集）：

    ```azurepowershell
    Stop-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

    > [!NOTE]
    > 单独创建 VM 而没有可用性集时，只需停止或解除分配单个 VM 即可启用加速网络。 如果 VM 是使用可用性集创建的，则在任何 NIC 上启用加速网络之前，必须停止或解除分配可用性集中包含的所有 VM，以便 VM 最终位于支持加速网络的群集上。 如果禁用加速网络，则无需停止或解除分配要求，因为支持加速网络的群集也可与不使用加速网络的 NIC 一起正常工作。

2. 在 VM 的 NIC 上启用加速网络：

    ```azurepowershell
    $nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic"
    
    $nic.EnableAcceleratedNetworking = $true
    
    $nic | Set-AzNetworkInterface
    ```

3. 重启 VM（如果在可用性集中，则重启其中的所有 VM），并确认已启用加速网络：

    ```azurepowershell
    Start-AzVM -ResourceGroup "myResourceGroup" `
        -Name "myVM"
    ```

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

虚拟机规模集略有不同，但它遵循相同的工作流。

1. 停止 VM：

    ```azurepowershell
    Stop-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

2. 更新网络接口下的加速网络属性：

    ```azurepowershell
    $vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

3. 将应用的更新设置为自动，以便立即应用更改：

    ```azurepowershell
    $vmss.UpgradePolicy.Mode = "Automatic"
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

    > [!NOTE]
    > 规模集具有 VM 升级功能，可使用三种不同的设置（自动、滚动和手动）应用更新。 在这些说明中，策略设置为自动，因此规模集会在重启后立即应用更改。

4. 重启规模集：

    ```azurepowershell
    Start-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

重启后，请等待升级完成。 升级完成后，虚函数 (VF) 会显示在 VM 内。 确保使用的是支持的 OS 和 VM 大小。

### <a name="resizing-existing-vms-with-accelerated-networking"></a>调整具有加速网络的现有 VM 的大小

如果 VM 启用了加速网络，则只能将其调整为支持加速网络的 VM 大小。  

启用加速网络的 VM 不能使用调整大小操作调整为不支持加速网络的 VM 实例的大小。 相反，若要调整其中一个 VM 的大小，请执行以下操作：

1. 停止或解除分配 VM。 对于可用性集或规模集，停止或解除分配可用性集或规模集中的所有 VM。

2. 在 VM 的 NIC 上禁用加速网络。 对于可用性集或规模集，请在可用性集或规模集中的所有 VM 的 NIC 上禁用加速网络。

3. 禁用加速网络后，将 VM、可用性集或规模集移至不支持加速网络的新大小，然后重启。
