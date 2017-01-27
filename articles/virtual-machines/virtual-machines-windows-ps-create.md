---
title: "使用 PowerShell 创建 Azure VM | Microsoft Docs"
description: "使用 Azure PowerShell 和 Azure Resource Manager 轻松创建运行 Windows Server 的新 VM。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 14fe9ca9-e228-4d3b-a5d8-3101e9478f6e
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/21/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 94c18aa0c4fe38fb74931d5ed61fece207c8b5ce
ms.openlocfilehash: 701a5515cd1d52f7ca8d3562dabcdf0e4d31183d


---
# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>使用 Resource Manager 和 PowerShell 创建 Windows VM
本文介绍如何使用 [Resource Manager](../azure-resource-manager/resource-group-overview.md) 和 PowerShell 快速创建运行 Windows Server 的 Azure 虚拟机及其所需的资源。 

若要创建虚拟机，必须执行本文中的所有步骤，这需要花费大约 30 分钟时间。 请将命令中的示例参数值替换为适合自己环境的名称。

## <a name="step-1-install-azure-powershell"></a>步骤 1：安装 Azure PowerShell
有关安装最新版 Azure PowerShell、选择订阅和登录到帐户的信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

## <a name="step-2-create-a-resource-group"></a>步骤 2：创建资源组
所有资源都必须包含在资源组中，因此先创建资源组。  

1. 获取可以创建资源的可用位置列表。
   
    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```
2. 设置资源的位置。 此命令将位置设置为 **centralus**。
   
    ```powershell
    $location = "centralus"
    ```
3. 创建资源组。 此命令在用户设置的位置创建名为 **myResourceGroup** 的资源组。
   
    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```

## <a name="step-3-create-a-storage-account"></a>步骤 3：创建存储帐户
需要一个 [存储帐户](../storage/storage-introduction.md) 存储你创建的虚拟机所使用的虚拟硬盘。 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。

1. 测试存储帐户名称是否唯一。 此命令测试名称 **myStorageAccount**。
   
    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
   
    如果此命令返回 **True**，则表示建议的名称在 Azure 中是唯一的。 
2. 现在创建存储帐户。
   
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup `
        -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```

## <a name="step-4-create-a-virtual-network"></a>步骤 4：创建虚拟网络
所有虚拟机都是 [虚拟网络](../virtual-network/virtual-networks-overview.md)的一部分。

1. 创建虚拟网络的子网。 此命令创建名为 **mySubnet** 的子网，其地址前缀为 10.0.0.0/24。
   
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```
2. 现在创建虚拟网络。 此命令使用已创建的子网创建名为 **myVnet** 的虚拟网络，其地址前缀为 **10.0.0.0/16**。
   
    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup `
        -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```

## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>步骤 5：创建公共 IP 地址和网络接口
若要与虚拟网络中的虚拟机通信，需要一个 [公共 IP 地址](../virtual-network/virtual-network-ip-addresses-overview-arm.md) 和网络接口。

1. 创建公共 IP 地址。 此命令创建名为 **myPublicIp** 的公共 IP 地址，使用“动态”分配方法。
   
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup `
        -Location $location -AllocationMethod Dynamic
    ```
2. 创建网络接口。 此命令创建名为 **myNIC** 的网络接口。
   
    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup `
        -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```

## <a name="step-6-create-a-virtual-machine"></a>步骤 6：创建虚拟机
现已做好所有的准备，接下来可以开始创建虚拟机。

1. 运行以下命令，设置虚拟机的管理员帐户名和密码。

    ```powershell
    $cred = Get-Credential -Message "Type the name and password of the local administrator account."
    ```
   
    密码的长度必须为 12 到 123 个字符，并且至少包含 1 个小写字符、1 个大写字符、1 个数字和 1 个特殊字符。 
2. 创建虚拟机的配置对象。 此命令创建名为 **myVmConfig** 的配置对象，用于定义 VM 的名称和大小。
   
    ```powershell
    $myVm = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
   
    有关虚拟机的可用大小列表，请参阅 [Azure 中的虚拟机大小](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 。
3. 配置 VM 的操作系统设置。 此命令设置 VM 的计算机名称、操作系统类型和帐户凭据。
   
    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred `
        -ProvisionVMAgent -EnableAutoUpdate
    ```
4. 定义用于预配 VM 的映像。 此命令定义用于 VM 的 Windows Server 映像。 
   
    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```
   
    如需详细了解如何选择要使用的映像，请参阅[使用 PowerShell 或 CLI 在 Azure 中导航和选择 Windows 虚拟机映像](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
5. 将创建的网络接口添加到配置。
   
    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```
6. 定义 VM 硬盘的名称和位置。 虚拟硬盘文件存储在容器中。 以下命令在你创建的存储帐户中名为 **vhds/myOsDisk1.vhd** 的容器中创建该磁盘。
   
    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```
7. 将操作系统磁盘信息添加到 VM 配置。 将 **$diskName** 的值替换为操作系统磁盘的名称。 创建变量并将磁盘信息添加到配置。
   
    ```powershell
    $myVM = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```
8. 最后，创建虚拟机。
   
    ```powershell
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```

## <a name="next-steps"></a>后续步骤
* 如果部署出现问题，后续措施是参阅[排查使用 Azure Resource Manager 时的常见 Azure 部署错误](../azure-resource-manager/resource-manager-common-deployment-errors.md)。
* 查看[使用 Azure Resource Manager 和 PowerShell 管理虚拟机](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，了解如何管理创建的虚拟机。
* 参考 [使用 Resource Manager 模板创建 Windows 虚拟机](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Jan17_HO2-->


