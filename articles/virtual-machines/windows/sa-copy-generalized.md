---
title: 在 Azure 中创建通用化 VM 的非托管映像 | Microsoft Docs
description: 创建通用化 Windows VM 的非托管映像，从而在 Azure 中创建多个 VM 的副本。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 3737ea08e593ae1018489633e23e80e1099296ae
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>如何从 Azure VM 创建非托管 VM 映像

本文介绍如何使用存储帐户。 建议使用托管磁盘和托管映像而不是使用存储帐户。 有关详细信息，请参阅[在 Azure 中捕获通用 VM 的托管映像](capture-image-resource.md)。

本文介绍如何通过 Azure PowerShell 使用存储帐户创建通用化 Azure VM 的映像。 然后可以使用该映像创建另一个 VM。 该映像包含 OS 磁盘和附加到虚拟机的数据磁盘。 该映像不包含虚拟网络资源，因此，创建新 VM 时需要设置这些资源。 

## <a name="prerequisites"></a>先决条件
需要安装 Azure PowerShell 1.0.x 版或更新版本。 如果尚未安装 PowerShell，请参阅 [How to install and configure Azure PowerShell](/powershell/azure/overview)（如何安装和配置 Azure PowerShell）了解安装步骤。

## <a name="generalize-the-vm"></a>一般化 VM 
本部分说明如何通用化可用作映像的 Windows 虚拟机。 通用化 VM 将删除所有个人帐户信息及其他某些数据，并准备好要用作映像的计算机。 有关 Sysprep 的详细信息，请参阅[如何使用 Sysprep：简介](http://technet.microsoft.com/library/bb457073.aspx)。

确保 Sysprep 支持计算机上运行的服务器角色。 有关详细信息，请参阅 [Sysprep 对服务器角色的支持](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> 如果是首次将 VHD 上传到 Azure，请确保先[准备好 VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，然后再运行 Sysprep。 
> 
> 

还可以使用 `sudo waagent -deprovision+user` 通用化 Linux VM，并使用 PowerShell 捕获该 VM。 有关使用 CLI 捕获 VM 的信息，请参阅[如何使用 Azure CLI 对 Linux 虚拟机进行通用化和捕获](../linux/capture-image.md)。


1. 登录到 Windows 虚拟机。
2. 以管理员身份打开“命令提示符”窗口。 将目录切换到 **%windir%\system32\sysprep**，然后运行 `sysprep.exe`。
3. 在“系统准备工具”对话框中，选择“进入系统全新体验(OOBE)”，确保已选中“通用化”复选框。
4. 在“关机选项”中选择“关机”。
5. 单击“确定”。
   
    ![启动 Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep 在完成运行后会关闭虚拟机。 

> [!IMPORTANT]
> 将 VHD 上传到 Azure 或从 VM 创建映像完成之前不要重启 VM。 如果 VM 意外重启，请运行 Sysprep 将其再次通用化。
> 
> 

## <a name="log-in-to-azure-powershell"></a>登录到 Azure PowerShell
1. 打开 Azure PowerShell 并登录到 Azure 帐户。
   
    ```powershell
    Connect-AzureRmAccount
    ```
   
    此时会打开一个弹出窗口让输入 Azure 帐户凭据。
2. 获取可用订阅的订阅 ID。
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. 使用订阅 ID 设置正确的订阅。
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>解除分配 VM 并将状态设置为通用化
1. 解除分配 VM 资源。
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    Azure 门户中该 VM 的“状态”将从“已停止”更改为“已停止(已解除分配)”。
2. 将虚拟机的状态设置为“通用化”。 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. 检查 VM 的状态。 VM 的“OSState/通用化”部分中的“DisplayStatus”应设置为“VM 已通用化”。  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>创建映像

使用此命令在目标存储容器中创建非托管虚拟机映像。 该映像在创建时所在的存储帐户与原始虚拟机的相同。 `-Path` 参数将源 VM 的 JSON 模板的副本保存到本地计算机。 `-DestinationContainerName` 参数是要在其中保存映像的容器的名称。 如果该容器不存在，系统会自动创建。
   
```powershell
Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
可以从 JSON 文件模板获取映像的 URL。 转到“资源” > “storageProfile” > “osDisk” > “映像” > “URI”部分即可查找映像的完整路径。 映像的 URL 如下所示：`https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`。
   
也可以在门户中验证 URI。 映像将复制到存储帐户中名为 **system** 的容器中。 

## <a name="create-a-vm-from-the-image"></a>从映像创建 VM

现在可从非托管映像创建一个或多个 VM。

### <a name="set-the-uri-of-the-vhd"></a>设置 VHD 的 URI

VHD 使用的 URI 采用以下格式：https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd。 在此示例中，名为 **myVHD** 的 VHD 位于存储帐户 **mystorageaccount** 的 **mycontainer** 容器中。

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>创建虚拟网络
创建[虚拟网络](../../virtual-network/virtual-networks-overview.md)的 vNet 和子网。

1. 创建子网。 以下示例在资源组 **myResourceGroup** 中创建具有 **10.0.0.0/24** 地址前缀的、名为 **mySubnet** 的子网。  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. 创建虚拟网络。 以下示例在**美国西部**位置创建具有 **10.0.0.0/16** 地址前缀的、名为 **myVnet** 的虚拟网络。  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>创建公共 IP 地址和网络接口
若要与虚拟网络中的虚拟机通信，需要一个 [公共 IP 地址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) 和网络接口。

1. 创建公共 IP 地址。 此示例创建名为 **myPip** 的公共 IP 地址。 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. 创建 NIC。 此示例创建名为 **myNic** 的 NIC。 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>创建网络安全组和 RDP 规则
若要使用 RDP 登录到 VM，需要创建一个允许在端口 3389 上进行 RDP 访问的安全规则。 

此示例创建名为 **myNsg** 的 NSG，其中包含一个允许通过端口 3389 传输 RDP 流量的、名为 **myRdpRule** 的规则。 有关 NSG 的详细信息，请参阅 [Opening ports to a VM in Azure using PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)（使用 PowerShell 在 Azure 中打开 VM 端口）。

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>为虚拟网络创建变量
为完成的虚拟网络创建变量。 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>创建 VM
以下 PowerShell 可完成虚拟机配置并使用非托管映像作为新安装的源。

</br>

```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

### <a name="verify-that-the-vm-was-created"></a>验证是否已创建 VM
完成后，应会在 [Azure 门户](https://portal.azure.com)的“浏览” > “虚拟机”下看到新建的 VM，也可以使用以下 PowerShell 命令查看该 VM：

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>后续步骤
若要使用 Azure PowerShell 管理新虚拟机，请参阅[使用 Azure 资源管理器与 PowerShell 来管理虚拟机](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。


