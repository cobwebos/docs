---
title: "在 Azure 中从专用磁盘创建 VM | Microsoft Docs"
description: "在 Resource Manager 部署模型中，通过附加专用非托管磁盘来创建新 VM。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 995437f5a4a6fe1bc99bfe7fee555d0ac53101a8
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2018
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>从存储帐户中的专用 VHD 创建 VM

通过使用 Powershell 将专用非托管磁盘附加为 OS 磁盘来创建新 VM。 专用磁盘是保留原始 VM 中的用户帐户、应用程序和其他状态数据的现有 VM 中 VHD 的副本。 

可以使用两个选项：
* [上传 VHD](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [复制现有 Azure VM 的 VHD](sa-create-vm-specialized.md#option-2-copy-an-existing-azure-vm)

## <a name="before-you-begin"></a>开始之前
如果使用 PowerShell，请确保使用最新版本的 AzureRM.Compute PowerShell 模块。 运行以下命令进行安装。

```powershell
Install-Module AzureRM.Compute 
```
有关详细信息，请参阅 [Azure PowerShell 版本控制](/powershell/azure/overview)。


## <a name="option-1-upload-a-specialized-vhd"></a>选项 1：上传专用 VHD

可从使用本地虚拟化工具（如 Hyper-V）创建的专用 VM 或从另一个云导出的 VM 上传 VHD。

### <a name="prepare-the-vm"></a>准备 VM
可上传用本地 VM 创建的专用 VHD或从另一个云导出的 VHD。 用 VHD 保留原始 VM 中的用户帐户、应用程序和其他状态数据。 如果打算按原样使用 VHD 来创建新 VM，请确保完成以下步骤。 
  
  * [准备好要上传到 Azure 的 Windows VHD](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 **不要**使用 Sysprep 通用化 VM。
  * 删除 VM 上安装的所有来宾虚拟化工具和代理（例如 VMware 工具）。
  * 确保 VM 配置为通过 DHCP 提取 IP 地址和 DNS 设置。 这可以确保服务器在启动时获得 VNet 中的 IP 地址。 


### <a name="get-the-storage-account"></a>获取存储帐户
需要在 Azure 中创建一个存储帐户用于存储上传的 VM 映像。 可以使用现有存储帐户，也可以创建新的存储帐户。 

若要显示可用的存储帐户，请键入：

```powershell
Get-AzureRmStorageAccount
```

如果要使用现有存储帐户，请转到[上传 VM 映像](#upload-the-vm-vhd-to-your-storage-account)部分。

如果需要创建存储帐户，请执行以下步骤：

1. 需要使用要在其中创建存储帐户的资源组的名称。 若要找出订阅中的所有资源组，请键入：
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    若要在**美国西部**区域创建一个名为 **myResourceGroup** 的资源组，请键入：

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. 使用 [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet 在此资源组中创建名为 **mystorageaccount** 的存储帐户：
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>将 VHD 上传到存储帐户
使用 [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) cmdlet 将映像上传到存储帐户中的容器： 本示例将文件 **myVHD.vhd** 从 `"C:\Users\Public\Documents\Virtual hard disks\"` 上传到 **myResourceGroup** 资源组中名为 **mystorageaccount** 的存储帐户。 该文件将放入名为 **mycontainer** 的容器，新文件名为 **myUploadedVHD.vhd**。

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


如果成功，会显示类似于下面的响应：

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

根据网络连接速度和 VHD 文件的大小，此命令可能需要一段时间才能完成。


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>选项 2：从现有 Azure VM 复制 VHD

可将 VHD 复制到另一个存储帐户，以便在创建新的重复 VM 时使用。

### <a name="before-you-begin"></a>开始之前
请确保：

* 获取有关**源和目标存储帐户**的信息。 对于源 VM，需要具有存储帐户和容器名称。 通常，容器名称为 **vhds**。 还需要获取目标存储帐户。 如果尚未拥有存储帐户，可以使用门户（“更多服务”>“存储帐户”>“添加”）或使用 [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet 创建一个存储帐户。 
* 已下载并安装 [AzCopy 工具](../../storage/common/storage-use-azcopy.md)。 

### <a name="deallocate-the-vm"></a>解除分配 VM
解除分配 VM，释放要复制的 VHD。 

* **门户**：单击“虚拟机” > “myVM”>“停止”
* Powershell：使用 [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) 可停止（解除分配）资源组“myResourceGroup”中名为“myVM”的 VM。

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

Azure 门户中该 VM 的“状态”将从“已停止”更改为“已停止(已解除分配)”。

### <a name="get-the-storage-account-urls"></a>获取存储帐户 URL
需要源和目标存储帐户的 URL。 URL 类似于：`https://<storageaccount>.blob.core.windows.net/<containerName>/`。 如果已经知道了存储帐户和容器名称，则只需替换括号之间的信息即可创建 URL。 

可以使用 Azure 门户或 Azure PowerShell 获取 URL：

* 门户：单击>了解“更多服务” > “存储帐户” > “存储帐户” > “Blob”，源 VHD 文件可能在“vhds”容器中。 单击容器的“属性”并复制标记为 **URL** 的文本。 需要用到源和目标容器的 URL。 
* Powershell：使用 [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) 可获取资源组“myResourceGroup”中名为“myVM”的 VM 的信息。 在结果中，查看 **Vhd Uri** 的 **Storage profile** 部分。 URI 的第一部分是容器的 URL，最后一部分是 VM 的 OS VHD 名称。

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>获取存储访问密钥
查找源和目标存储帐户的访问密钥。 有关访问密钥的详细信息，请参阅[关于 Azure 存储帐户](../../storage/common/storage-create-storage-account.md)。

* 门户：单击“更多服务” > “存储帐户” > “存储帐户” > “访问密钥”。 复制标记为 **key1** 的密钥。
* Powershell：使用 [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) 获取资源组“myResourceGroup”中存储帐户“mystorageaccount”的存储密钥。 复制标记为“key1”的密钥。

```powershell
Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>复制 VHD
可以使用 AzCopy 在存储帐户之间复制文件。 对于目标容器，如果指定的容器不存在，系统会自动创建该容器。 

要使用 AzCopy，请在本地计算机上打开命令提示符，并导航到安装 AzCopy 的文件夹。 路径类似于 *C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy*。 

若要复制容器中的所有文件，请使用 **/S** 开关。 此开关可用于复制 OS VHD 和所有数据磁盘（如果它们在同一个容器中）。 本示例演示如何将存储帐户 **mysourcestorageaccount** 中容器 **mysourcecontainer** 内的所有文件复制到存储帐户 **mydestinationstorageaccount** 中的容器 **mydestinationcontainer**。 将存储帐户和容器的名称替换为自己的名称。 将 `<sourceStorageAccountKey1>` 和 `<destinationStorageAccountKey1>` 替换为自己的密钥。

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

如果只想要复制包含多个文件的容器中的特定 VHD，则还可以使用 /Pattern 开关指定文件名。 在本示例中，仅复制名为 **myFileName.vhd** 的文件。

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


完成后，将出现如下所示的消息：

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

### <a name="troubleshooting"></a>故障排除
* 使用 AZCopy 时，如果看到错误“服务器无法对请求进行身份验证”，请确保授权标头的值构成正确（包括签名）。 如果使用的是密钥 2 或辅助存储密钥，则请尝试使用主密钥或第一个存储密钥。

## <a name="create-the-new-vm"></a>创建新 VM 

需要创建用于新 VM 的网络和其他 VM 资源。

### <a name="create-the-subnet-and-vnet"></a>创建子网和 vNet

创建[虚拟网络](../../virtual-network/virtual-networks-overview.md)的 vNet 和子网。

1. 创建子网。 本示例在资源组 **myResourceGroup** 中创建名为 **mySubnet** 的子网，并将子网地址前缀设置为 **10.0.0.0/24**。
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. 创建 vNet。 本示例将虚拟网络名称设置为 **myVnetName**，将位置设置为**美国西部**，将虚拟网络的地址前缀设置为 **10.0.0.0/16**。 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-nic"></a>创建公共 IP 地址和 NIC
若要与虚拟网络中的虚拟机通信，需要一个 [公共 IP 地址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) 和网络接口。

1. 创建公共 IP 地址。 在本示例中，公共 IP 地址名称设置为 **myIP**。
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. 创建 NIC。 在本示例中，NIC 名称设置为 **myNicName**。
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>创建网络安全组和 RDP 规则
若要使用 RDP 登录到 VM，需要创建一个允许在端口 3389 上进行 RDP 访问的安全规则。 由于新 VM 的 VHD 是从现有专用 VM 创建的，因此在创建 VM 后，可以使用源虚拟机中有权通过 RDP 登录的现有帐户。
本示例将 NSG 名称设置为 **myNsg**，将 RDP 规则名称设置为 **myRdpRule**。

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

有关终结点和 NSG 规则的详细信息，请参阅 [Opening ports to a VM in Azure using PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)（使用 PowerShell 在 Azure 中打开 VM 端口）。

### <a name="set-the-vm-name-and-size"></a>设置 VM 名称和大小

此示例将 VM 名称设置为“myVM”，将 VM 大小设置为“Standard_A2”。
```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>添加 NIC
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>配置 OS 磁盘

1. 为上传或复制的 VHD 设置 URI。 在此示例中，将名为 **myOsDisk.vhd** 的 VHD 文件保留在容器（名为 **myContainer**）中名为 **myStorageAccount** 的存储帐户中。

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. 添加 OS 磁盘。 本例中，创建 OS 磁盘时，会在 VM 名称后面附加词“osDisk”以创建 OS 磁盘名称。 此示例还指定应将此基于 Windows 的 VHD 作为 OS 磁盘附加到 VM。
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

可选：如果有需要附加到 VM 的数据磁盘，可使用数据 VHD 的 URL 和相应的逻辑单元号 (Lun) 添加数据磁盘。

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

使用存储帐户时，数据和操作系统磁盘 URL 类似于：`https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`。 可通过以下方法在门户上找到此信息：浏览到目标存储容器，单击复制的操作系统或数据 VHD，并复制 URL 的内容。


### <a name="complete-the-vm"></a>完成 VM 

使用刚刚创建的配置创建 VM。

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

如果此命令成功，会看到类似于下面的输出：

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>验证是否已创建 VM
应会在 [Azure 门户](https://portal.azure.com)的“浏览” > “虚拟机”下看到新建的 VM，也可以使用以下 PowerShell 命令查看该 VM：

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>后续步骤
要登录到新虚拟机，请在[门户](https://portal.azure.com)中浏览到该 VM，单击“连接”，并打开远程桌面 RDP 文件。 使用原始虚拟机的帐户凭据登录到新虚拟机。 有关详细信息，请参阅 [How to connect and log on to an Azure virtual machine running Windows](connect-logon.md)（如何连接并登录到运行 Windows 的 Azure 虚拟机）。

