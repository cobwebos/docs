---
title: "将 AWS VM 迁移到 Azure | Microsoft Docs"
description: "将 Amazon Web Services (AWS) EC2 实例迁移到 Azure 虚拟机。 此方案使用托管磁盘来简化云存储。"
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
ms.date: 02/08/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 2fc44190970cd2fe827e3547dda80bcd0706e3f7
ms.lasthandoff: 03/31/2017


---

# <a name="migrate-from-amazon-web-services-aws-to-azure-managed-disks"></a>从 Amazon Web Services (AWS) 迁移到 Azure 托管磁盘

可通过上传 VHD 将 Amazon Web Services (AWS) EC2 实例迁移到 Azure。 如果要从同一映像在 Azure 中创建多个 VM，则必须首先通用化 VM，然后将通用 VHD 导出到本地目录。 上传 VHD 后，可以创建使用[托管磁盘](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)进行存储的新 Azure VM。 Azure 托管磁盘不需要为 Azure IaaS VM 管理存储帐户。 仅需指定类型（高级或标准）以及所需的磁盘大小，Azure 将为你创建和管理磁盘。 

开始此过程之前，请确保已查看[规划迁移到托管磁盘](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)。

将任何 VHD 上传到 Azure 之前，应按照[准备要上传到 Azure 的 Windows VHD 或 VHDX](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 进行操作。

## <a name="before-you-begin"></a>开始之前
如果使用 PowerShell，请确保使用最新版本的 AzureRM.Compute PowerShell 模块。 运行以下命令进行安装。

```powershell
Install-Module AzureRM.Compute -MinimumVersion 2.6.0
```
有关详细信息，请参阅 [Azure PowerShell 版本控制](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning)。


## <a name="generalize-the-windows-vm-using-sysprep"></a>使用 Sysprep 通用化 Windows VM

使用 Sysprep 通用化 VM 可从 VHD 中删除任何特定于计算机的信息和个人帐户信息，并准备将计算机用作映像。 有关 Sysprep 的详细信息，请参阅[如何使用 Sysprep：简介](http://technet.microsoft.com/library/bb457073.aspx)。

确保 Sysprep 支持计算机上运行的服务器角色。 有关详细信息，请参阅 [Sysprep 对服务器角色的支持](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> 如果在首次将 VHD 上载到 Azure 之前运行 Sysprep，请确保先[准备好 VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，然后再运行 Sysprep。 
> 
> 

1. 登录到 Windows 虚拟机。
2. 以管理员身份打开“命令提示符”窗口。 将目录切换到 **%windir%\system32\sysprep**，然后运行 `sysprep.exe`。
3. 在“系统准备工具”对话框中，选择“进入系统全新体验(OOBE)”，确保已选中“通用化”复选框。
4. 在“关机选项”中选择“关机”。
5. 单击 **“确定”**。
   
    ![启动 Sysprep](./media/aws-to-azure/sysprepgeneral.png)
6. Sysprep 在完成运行后会关闭虚拟机。 请勿重启 VM。



## <a name="export-the-vhd-from-an-ec2-instance"></a>从 EC2 实例导出 VHD

1.    如果使用 Amazon Web Services (AWS)，请将 EC2 实例导出到 Amazon S3 存储桶中的 VHD。 按照 Amazon 文档导出 Amazon EC2 实例中所述的步骤安装 Amazon EC2 命令行接口 (CLI) 工具，然后运行 create-instance-export-task 命令将 EC2 实例导出到 VHD 文件。 运行 create-instance-export-task 命令时，请务必对 DISK_IMAGE_FORMAT 变量使用 VHD。 导出的 VHD 文件将保存在该过程中指定的 Amazon S3 存储桶中。

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT '
    --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2.    从 S3 存储桶中下载 VHD 文件。 选择 VHD 文件，然后选择“操作” > “下载”。



## <a name="log-in-to-azure"></a>登录 Azure
如果尚未安装 PowerShell 版本，请阅读[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

1. 打开 Azure PowerShell 并登录到 Azure 帐户。 此时将打开一个弹出窗口让输入 Azure 帐户凭据。
   
    ```powershell
    Login-AzureRmAccount
    ```
2. 获取可用订阅的订阅 ID。
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. 使用订阅 ID 设置正确的订阅。 将 `<subscriptionID>` 替换为适当订阅的 ID。
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>获取存储帐户
需要在 Azure 中创建一个存储帐户用于存储上载的 VM 映像。 你可以使用现有存储帐户，也可以创建新的存储帐户。 

如果将使用 VHD 为 VM 创建托管磁盘，存储帐户位置必须与要创建 VM 的位置相同。

若要显示可用的存储帐户，请键入：

```powershell
Get-AzureRmStorageAccount
```

如果要使用现有存储帐户，请转到[上载 VM 映像](#upload-the-vm-vhd-to-your-storage-account)部分。

如果需要创建存储帐户，请执行以下步骤：

1. 需要使用要在其中创建存储帐户的资源组的名称。 若要找出订阅中的所有资源组，请键入：
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    若要在**美国西部**区域创建一个名为 **myResourceGroup** 的资源组，请键入：

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. 使用 [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) cmdlet 在此资源组中创建名为 **mystorageaccount** 的存储帐户：
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    -SkuName 的有效值为：
   
   * **Standard_LRS** - 本地冗余存储。 
   * **Standard_ZRS** - 区域冗余存储。
   * **Standard_GRS** - 异地冗余存储。 
   * **Standard_RAGRS** - 读取访问权限异地冗余存储。 
   * **Premium_LRS** - 高级本地冗余存储。 

## <a name="upload-the-vhd-to-your-storage-account"></a>将 VHD 上载到存储帐户

使用 [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) cmdlet 将 VHD 上传到存储帐户中的容器。 本示例将文件 **myVHD.vhd** 从 `"C:\Users\Public\Documents\Virtual hard disks\"` 上载到 **myResourceGroup** 资源组中名为 **mystorageaccount** 的存储帐户。 该文件将放入名为 **mycontainer** 的容器，新文件名为 **myUploadedVHD.vhd**。

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


如果成功，将显示类似于下面的响应：

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

如果要使用上传的 VHD 创建托管磁盘或新 VM，请保存**目标 URI** 路径供之后使用。

### <a name="other-options-for-uploading-a-vhd"></a>用于上传 VHD 的其他选项

也可以使用以下方法之一将 VHD 上传到存储帐户：

-   [Azure 存储复制 Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)

-   [Azure 存储资源管理器上传 Blob](https://azurestorageexplorer.codeplex.com/)

-   [Storage Import/Export Service REST API Reference](https://msdn.microsoft.com/library/dn529096.aspx)（存储导入/导出服务 REST API 参考）

    如果估计上传时间大于 7 天，则建议使用“导入/导出服务”。 可使用 [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) 根据数据大小和传输单位估计所需时间。 

    导入/导出可用于复制到标准存储帐户。 需要使用 AzCopy 等工具从标准存储复制到高级存储帐户。

## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>从上传的 VHD 中创建托管映像 

使用通用化的 OS VHD 创建托管映像。


1.  首先，设置公共参数：

    ```powershell
    $rgName = "myResourceGroupName"
    $vmName = "myVM"
    $location = "West Central US" 
    $imageName = "yourImageName"
    $osVhdUri = "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```

4.  使用通用化的 OS VHD 创建映像。

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

## <a name="setup-some-variables-for-the-image"></a>设置映像的某些变量

首先需要收集有关映像的基本信息并创建映像的变量。 此示例使用**美国中西部**位置中 **myResourceGroup** 资源组中的托管 VM 映像，名为 **myImage**。 

```powershell
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

## <a name="create-a-virtual-network"></a>创建虚拟网络
创建[虚拟网络](../../virtual-network/virtual-networks-overview.md)的 vNet 和子网。

1. 创建子网。 此示例创建名为 **mySubnet** 的子网，其地址前缀为 **10.0.0.0/24**。  
   
    ```powershell
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. 创建虚拟网络。 此示例创建名为 **myVnet** 的虚拟网络，其地址前缀为 **10.0.0.0/16**。  
   
    ```powershell
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>创建公共 IP 地址和网络接口

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

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>创建网络安全组和 RDP 规则

若要使用 RDP 登录到 VM，需要创建一个允许在端口 3389 上进行 RDP 访问的网络安全规则 (NSG)。 

此示例创建名为 **myNsg** 的 NSG，其中包含一个允许通过端口 3389 传输 RDP 流量的、名为 **myRdpRule** 的规则。 有关 NSG 的详细信息，请参阅 [Opening ports to a VM in Azure using PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)（使用 PowerShell 在 Azure 中打开 VM 端口）。

```powershell
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

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

## <a name="get-the-credentials-for-the-vm"></a>获取 VM 的凭据

以下 cmdlet 将打开一个窗口，需在其中输入远程访问 VM 所用的本地管理员帐户的新用户名和密码。 

```powershell
$cred = Get-Credential
```

## <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>设置 VM 名称和计算机名称的变量以及 VM 的大小

1. 创建 VM 名称与计算机名称的变量。 此示例将 VM 名称设置为 **myVM**，将计算机名称设置为 **myComputer**。

    ```powershell
    $vmName = "myVM"
    $computerName = "myComputer"
    ```
2. 设置虚拟机的大小。 此示例创建 **Standard_DS1_v2** 大小的 VM。 有关详细信息，请参阅 [VM 大小](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/)文档。

    ```powershell
    $vmSize = "Standard_DS1_v2"
    ```

3. 向 VM 配置中添加 VM 的名称和大小。

```powershell
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

## <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>将 VM 映像设置为新 VM 的源映像

使用托管 VM 映像的 ID 设置源映像。

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

## <a name="set-the-os-configuration-and-add-the-nic"></a>设置 OS 配置并添加 NIC。

输入 OS 磁盘的存储类型（PremiumLRS 或 StandardLRS）和大小。 此示例将帐户类型设置为 **PremiumLRS**，将磁盘大小设置为 **128 GB**，将磁盘缓存设置为 **ReadWrite**。

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm  -ManagedDiskStorageAccountType PremiumLRS -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

## <a name="create-the-vm"></a>创建 VM

使用在 **$vm** 变量中生成和存储的配置创建新 VM。

```powershell
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## <a name="verify-that-the-vm-was-created"></a>验证是否已创建 VM
完成后，应会在 [Azure 门户](https://portal.azure.com)的“浏览” > “虚拟机”下看到新建的 VM，也可以使用以下 PowerShell 命令查看该 VM：

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>后续步骤

若要登录到新虚拟机，请在[门户](https://portal.azure.com)中浏览到该 VM，单击“连接”，然后打开远程桌面 RDP 文件。 使用原始虚拟机的帐户凭据登录到新虚拟机。 有关详细信息，请参阅 [How to connect and log on to an Azure virtual machine running Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)（如何连接并登录到运行 Windows 的 Azure 虚拟机）。 
 

