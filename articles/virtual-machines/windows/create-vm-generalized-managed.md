---
title: "在 Azure 中从托管 VM 映像创建 VM | Microsoft Docs"
description: "在 Resource Manager 部署模型中，使用 Azure PowerShell 从通用托管 VM 映像创建 Windows 虚拟机。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: cynthn
ms.openlocfilehash: 8157b77976a2152cc0b012fe6ad5fa116223bef6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-from-a-managed-image"></a>从托管的映像创建 VM

可在 Azure 中从托管 VM 映像创建多个 VM。 托管 VM 映像包含创建 VM（包括 OS 和数据磁盘）所需的信息。 构成映像的 VHD（包括 OS 磁盘和任何数据磁盘）均作为托管磁盘存储。 


## <a name="prerequisites"></a>先决条件

必须已[创建托管 VM 映像](capture-image-resource.md)以用于创建新 VM。 

请确保具有最新版本的 AzureRM.Compute 和 AzureRM.Network PowerShell 模块。 以管理员身份打开 PowerShell 提示符，并运行以下命令进行安装。

```azurepowershell-interactive
Install-Module AzureRM.Compute,AzureRM.Network
```
有关详细信息，请参阅 [Azure PowerShell 版本控制](/powershell/azure/overview)。



## <a name="collect-information-about-the-image"></a>收集有关映像的信息

首先需要收集有关映像的基本信息并创建映像的变量。 此示例使用**美国中西部**位置中 **myResourceGroup** 资源组中的托管 VM 映像，名为 **myImage**。 

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

## <a name="create-a-virtual-network"></a>创建虚拟网络
创建[虚拟网络](../../virtual-network/virtual-networks-overview.md)的 vNet 和子网。

1. 创建子网。 此示例创建名为 **mySubnet** 的子网，其地址前缀为 **10.0.0.0/24**。  
   
    ```azurepowershell-interactive
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. 创建虚拟网络。 此示例创建名为 **myVnet** 的虚拟网络，其地址前缀为 **10.0.0.0/16**。  
   
    ```azurepowershell-interactive
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>创建公共 IP 地址和网络接口

若要与虚拟网络中的虚拟机通信，需要一个 [公共 IP 地址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) 和网络接口。

1. 创建公共 IP 地址。 此示例创建名为 **myPip** 的公共 IP 地址。 
   
    ```azurepowershell-interactive
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. 创建 NIC。 此示例创建名为 **myNic** 的 NIC。 
   
    ```azurepowershell-interactive
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>创建网络安全组和 RDP 规则

若要使用 RDP 登录到 VM，需要创建一个允许在端口 3389 上进行 RDP 访问的网络安全规则 (NSG)。 

此示例创建名为 **myNsg** 的 NSG，其中包含一个允许通过端口 3389 传输 RDP 流量的、名为 **myRdpRule** 的规则。 有关 NSG 的详细信息，请参阅 [Opening ports to a VM in Azure using PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)（使用 PowerShell 在 Azure 中打开 VM 端口）。

```azurepowershell-interactive
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>为虚拟网络创建变量

为完成的虚拟网络创建变量。 

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

## <a name="get-the-credentials-for-the-vm"></a>获取 VM 的凭据

以下 cmdlet 将打开一个窗口，需在其中输入远程访问 VM 所用的本地管理员帐户的新用户名和密码。 

```azurepowershell-interactive
$cred = Get-Credential
```

## <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>设置 VM 名称和计算机名称的变量以及 VM 的大小

1. 创建 VM 名称与计算机名称的变量。 此示例将 VM 名称设置为 **myVM**，将计算机名称设置为 **myComputer**。

    ```azurepowershell-interactive
    $vmName = "myVM"
    $computerName = "myComputer"
    ```
2. 设置虚拟机的大小。 此示例创建 **Standard_DS1_v2** 大小的 VM。 有关详细信息，请参阅 [VM 大小](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/)文档。

    ```azurepowershell-interactive
    $vmSize = "Standard_DS1_v2"
    ```

3. 向 VM 配置中添加 VM 的名称和大小。

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

## <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>将 VM 映像设置为新 VM 的源映像

使用托管 VM 映像的 ID 设置源映像。

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

## <a name="set-the-os-configuration-and-add-the-nic"></a>设置 OS 配置并添加 NIC。

输入 OS 磁盘的存储类型（PremiumLRS 或 StandardLRS）和大小。 此示例将帐户类型设置为 **PremiumLRS**，将磁盘大小设置为 **128 GB**，将磁盘缓存设置为 **ReadWrite**。

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk -VM $vm  -StorageAccountType PremiumLRS -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

## <a name="create-the-vm"></a>创建 VM

使用在 **$vm** 变量中生成和存储的配置创建新 VM。

```azurepowershell-interactive
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## <a name="verify-that-the-vm-was-created"></a>验证是否已创建 VM
完成后，应会在 [Azure 门户](https://portal.azure.com)的“浏览” > “虚拟机”下看到新建的 VM，也可以使用以下 PowerShell 命令查看该 VM：

```azurepowershell-interactive
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>后续步骤
若要使用 Azure PowerShell 管理新虚拟机，请参阅[使用 Azure PowerShell 模块创建和管理 Windows VM](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

