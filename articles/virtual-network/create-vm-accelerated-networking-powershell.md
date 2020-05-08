---
title: 创建具有加速网络的 Windows VM-Azure PowerShell
description: 了解如何创建具有加速网络的 Linux 虚拟机。
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/15/2020
ms.author: gsilva
ms.openlocfilehash: 202acff5bae87174781dc6c914bebf0494dfcf05
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871452"
---
# <a name="create-a-windows-vm-with-accelerated-networking-using-azure-powershell"></a>使用 Azure PowerShell 创建具有加速网络的 Windows VM

本教程介绍如何创建具有加速网络的 Windows 虚拟机（VM）。

> [!NOTE]
> 若要在 Linux 虚拟机中使用加速网络，请参阅[创建具有加速网络的 LINUX VM](create-vm-accelerated-networking-cli.md)。

使用加速网络可以实现对 VM 的单根 I/O 虚拟化 (SR-IOV)，大幅提升其网络性能。 这种高性能路径会绕过数据路径中的主机，从而减少支持的 VM 类型上要求最苛刻的网络工作负荷的延迟、抖动和 CPU 使用率。 下图说明了两个 Vm 如何与/不具有加速网络通信：

![具有和没有加速网络的 Azure 虚拟机之间的通信](./media/create-vm-accelerated-networking/accelerated-networking.png)

在不使用加速网络的情况下，传入和传出 VM 的所有网络流量必须遍历主机和虚拟交换机。 虚拟交换机针对网络流量实施所有策略，例如网络安全组、访问控制列表、隔离和其他网络虚拟化服务。

> [!NOTE]
> 若要详细了解虚拟交换机，请参阅[Hyper-v 虚拟交换机](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch)。

对于加速网络，网络流量到达 VM 的网络接口（NIC），然后转发到 VM。 由虚拟交换机应用的所有网络策略现在都会卸载，并在硬件中应用。 由于在硬件中应用策略，NIC 可以将网络流量直接转发到 VM。 NIC 会绕过主机和虚拟交换机，同时维护主机中应用的所有策略。

加速网络的优势仅适用于启用了该功能的 VM。 为获得最佳结果，请在连接到同一 Azure 虚拟网络的至少两个 Vm 上启用此功能。 当跨虚拟网络进行通信或者在本地连接时，此功能对总体延迟的影响最小。

## <a name="benefits"></a>优点

- **延迟/更高的每秒数据包数（pps）**：从数据路径中消除虚拟交换机将删除数据包用于策略处理的时间。 它还增加了 VM 中可处理的数据包数。

- **减少抖动**：虚拟交换机处理取决于需要应用的策略量。 它还取决于执行处理的 CPU 工作负荷。 将策略实施卸载到硬件会将数据包直接传送到 VM，从而消除了这种可变性。 卸载还会删除主机到 VM 的通信、所有的软件中断和所有上下文切换。

- **降低了 cpu 利用率**：绕过主机中的虚拟交换机可以减少处理网络流量的 CPU 利用率。

## <a name="supported-operating-systems"></a>支持的操作系统

直接从 Azure 库支持以下分发版：

- **Windows Server 2019 Datacenter**
- **Windows Server 2016 Datacenter** 
- **Windows Server 2012 R2 Datacenter**

## <a name="limitations-and-constraints"></a>限制和约束

### <a name="supported-vm-instances"></a>支持的 VM 实例

对于包含两个或多个虚拟 Cpu （个 vcpu）的最常见用途和计算优化实例大小支持加速网络。  这些受支持的系列包括： Dv2/DSv2 和 F/Fs。

在支持超线程的实例上，在具有四个或更多个 vcpu 的 VM 实例上支持加速网络。 支持的系列包括： D/Dsv3、D/Dsv4、E/Esv3、Ea/Easv4、Fsv2、Lsv2、Ms/Mms 和 Ms/Mmsv2。

有关 VM 实例的详细信息，请参阅[Azure 中 Windows 虚拟机的大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

### <a name="regions"></a>区域

所有全球 Azure 区域和 Azure 政府云中都提供加速网络。

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>在正在运行的 VM 上启用加速网络

在未启用加速网络的情况下，受支持的 VM 大小只能在停止并解除分配时启用。

### <a name="deployment-through-azure-resource-manager"></a>通过 Azure 资源管理器部署

不能通过加速网络部署虚拟机（经典）。

## <a name="vm-creation-using-the-portal"></a>使用门户创建 VM

尽管本文提供了使用 Azure PowerShell 创建具有加速网络的 VM 的步骤，但也可[使用 Azure 门户创建](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)启用加速网络的虚拟机。 在门户中创建 VM 时，请在 "**创建虚拟机**" 页中选择 "**网络**" 选项卡。此选项卡具有用于**加速网络**的选项。 如果你选择了[受支持的操作系统](#supported-operating-systems)和[VM 大小](#supported-vm-instances)，则此选项会自动设置为**On**。 否则，该选项设置为**Off**，Azure 将显示无法启用它的原因。

> [!NOTE]
> 只有受支持的操作系统才能通过门户启用。 如果你使用的是自定义映像，并且映像支持加速网络，请使用 CLI 或 PowerShell 创建 VM。 

创建 VM 后，可以确认是否已启用加速网络。 请按照以下说明执行操作：

1. 转到 [Azure 门户](https://portal.azure.com)来管理 VM。 搜索并选择“虚拟机”  。

2. 在 "虚拟机" 列表中，选择新的 VM。

3. 在 VM 菜单栏中选择“网络”。 

在 "网络接口信息" 中，在 "**加速**网络" 标签旁，门户将显示 "**已禁用**" 或 "**已启用**" 作为 "加速网络" 状态。

## <a name="vm-creation-using-powershell"></a>使用 PowerShell 创建 VM

在继续之前，请安装[Azure PowerShell](/powershell/azure/install-az-ps) 1.0.0 或更高版本。 要查找当前安装的版本，请运行 `Get-Module -ListAvailable Az`。 如果需要进行安装或升级，请从 [PowerShell 库](https://www.powershellgallery.com/packages/Az)安装最新版本的 Az 模块。 在 PowerShell 会话中，使用[AzAccount](/powershell/module/az.accounts/connect-azaccount)登录到 Azure 帐户。

在以下示例中，请将示例参数名称替换成自己的值。 示例参数名称包括*myResourceGroup*、 *myNic*和*myVM*。

### <a name="create-a-virtual-network"></a>创建虚拟网络

1. 使用 [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) 创建资源组。 以下命令在 " *centralus* " 位置创建名为 " *myResourceGroup* " 的资源组：

    ```azurepowershell
    New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
    ```

2. 使用 [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig) 创建子网配置。 以下命令将创建名为*mySubnet*的子网：

    ```azurepowershell
    $subnet = New-AzVirtualNetworkSubnetConfig `
        -Name "mySubnet" `
        -AddressPrefix "192.168.1.0/24"
    ```

3. 使用 [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork) 创建带 mySubnet** 子网的虚拟网络。

    ```azurepowershell
    $vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "centralus" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $Subnet
    ```

### <a name="create-a-network-security-group"></a>创建网络安全组

1. 使用[AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig)创建网络安全组规则。

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

2. 使用 [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) 创建网络安全组，并向其分配 Allow-RDP-All** 安全规则。 除了*Allow-All*规则之外，网络安全组包含多个默认规则。 一个默认规则禁用来自 internet 的所有入站访问。 创建后，"*允许-RDP-All* " 规则将分配给网络安全组，以便可以远程连接到 VM。

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

1. 使用 [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress) 创建一个公共 IP 地址。 如果不打算从 internet 访问 VM，则不需要公共 IP 地址。 但是，必须完成本文中的步骤。

    ```azurepowershell
    $publicIp = New-AzPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name 'myPublicIp' `
        -location centralus `
        -AllocationMethod Dynamic
    ```

2. 使用启用了加速网络的[AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface)创建网络接口，并将公共 IP 地址分配给网络接口。 以下示例在*myVnet*虚拟网络的*mySubnet*子网中创建名为*myNic*的网络接口，并将*myPublicIp*公共 IP 地址分配给该网络：

    ```azurepowershell
    $nic = New-AzNetworkInterface `
        -ResourceGroupName "myResourceGroup" `
        -Name "myNic" `
        -Location "centralus" `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $publicIp.Id `
        -EnableAcceleratedNetworking
    ```

### <a name="create-a-vm-and-attach-the-network-interface"></a>创建 VM 并连接到网络接口

1. 使用[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)将 VM `$cred`凭据设置为变量，这会提示你登录：

    ```azurepowershell
    $cred = Get-Credential
    ```

2. 通过 [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) 定义你的 VM。 以下命令定义了一个名为*myVM*的 vm，其 vm 大小支持加速网络（*Standard_DS4_v2*）：

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

5. 通过[new-azvm](/powershell/module/az.compute/new-azvm)创建 VM。

    ```azurepowershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
    ```

### <a name="confirm-the-ethernet-controller-is-installed-in-the-windows-vm"></a>确认 Windows VM 中已安装以太网控制器

在 Azure 中创建 VM 后，连接到 VM，并确认已在 Windows 中安装了以太网控制器。

1. 转到 [Azure 门户](https://portal.azure.com)来管理 VM。 搜索并选择“虚拟机”  。

2. 在 "虚拟机" 列表中，选择新的 VM。

3. 在 "VM 概述" 页中，如果 VM 的**状态**列为 "**正在创建**"，请等到 Azure 完成创建 VM。 VM 创建完成后，**状态**将更改为 "**正在运行**"。

4. 在 VM 概述工具栏中，选择 "**连接** > **rdp** > **下载 rdp 文件**"。

5. 打开 .rdp 文件，然后使用你在 "[创建 VM" 和 "附加网络接口](#create-a-vm-and-attach-the-network-interface)" 部分中输入的凭据登录到 VM。 如果从未连接到 Azure 中的 Windows VM，请参阅[连接到虚拟机](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine)。

6. 显示 VM 的远程桌面会话后，右键单击 Windows "开始" 按钮，然后选择 "**设备管理器**"。

7. 在 "**设备管理器**" 窗口中，展开 "**网络适配器**" 节点。

8. 确认已显示 " **Mellanox ConnectX-3" 虚拟功能 "以太网适配器**"，如下图所示：

    ![Mellanox ConnectX 3 虚函数以太网适配器，用于加速网络的新网络适配器，设备管理器](./media/create-vm-accelerated-networking/device-manager.png)

现在已为 VM 启用加速网络。

> [!NOTE]
> 如果 Mellanox 适配器无法启动，请在远程桌面会话中打开管理员提示符，然后输入以下命令：
>
> `netsh int tcp set global rss = enabled`

## <a name="enable-accelerated-networking-on-existing-vms"></a>在现有 Vm 上启用加速网络

如果你创建了没有加速网络的 VM，则可以在现有 VM 上启用此功能。 VM 必须支持加速网络，方法是满足以下先决条件：

* 对于加速网络，VM 必须是受支持的大小。
* VM 必须是受支持的 Azure 库映像（适用于 Linux 的内核版本）。
* 在任何 NIC 上启用加速网络之前，必须停止或释放可用性集或虚拟机规模集中的所有 Vm。

### <a name="individual-vms-and-vms-in-an-availability-set"></a>可用性集中的单个 Vm 和 Vm

1. 停止或解除分配 VM，或者如果某个可用性集，则为集中的所有 Vm：

    ```azurepowershell
    Stop-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

    > [!NOTE]
    > 在不使用可用性集的情况下单独创建 VM 时，只需停止或解除分配单个 VM 即可启用加速网络。 如果 VM 是使用可用性集创建的，则在任何 Nic 上启用加速网络之前，必须停止或解除分配可用性集中包含的所有 Vm，以便 Vm 在支持加速网络的群集上结束。 如果禁用加速网络，则无需停止或释放要求，因为支持加速网络的群集也可用于不使用加速网络的 Nic。

2. 在 VM 的 NIC 上启用加速网络：

    ```azurepowershell
    $nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic"
    
    $nic.EnableAcceleratedNetworking = $true
    
    $nic | Set-AzNetworkInterface
    ```

3. 重新启动 VM，或者，如果在可用性集中，则为集中的所有 Vm，并确认已启用加速网络：

    ```azurepowershell
    Start-AzVM -ResourceGroup "myResourceGroup" `
        -Name "myVM"
    ```

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

虚拟机规模集略有不同，但它遵循相同的工作流。

1. 停止 Vm：

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

3. 将应用的更新设置为 "自动"，以便立即选取更改：

    ```azurepowershell
    $vmss.UpgradePolicy.AutomaticOSUpgrade = $true
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

    > [!NOTE]
    > 规模集具有使用三个不同的设置应用更新的 VM 升级：自动、滚动和手动。 在这些说明中，策略设置为自动，因此规模集会在重新启动后立即选取更改。

4. 重新启动规模集：

    ```azurepowershell
    Start-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

重新启动后，请等待升级完成。 完成升级后，虚拟功能（VF）会显示在 VM 内。 请确保使用的是受支持的操作系统和 VM 大小。

### <a name="resizing-existing-vms-with-accelerated-networking"></a>通过加速网络调整现有 Vm 的大小

如果 VM 启用了加速网络，则只能将其重设为支持加速网络的 VM。  

启用加速网络的 VM 无法调整为不支持使用调整大小操作加速网络的 VM 实例。 相反，若要调整其中一个 VM 的大小，请执行以下操作：

1. 停止或解除分配 VM。 对于可用性集或规模集，停止或释放可用性集中的所有 Vm 或规模集。

2. 在 VM 的 NIC 上禁用加速网络。 对于可用性集或规模集，请在可用性集或规模集中的所有 Vm 的 Nic 上禁用加速网络。

3. 禁用加速网络后，将 VM、可用性集或规模集移动到不支持加速网络的新大小，然后重新启动它们。
