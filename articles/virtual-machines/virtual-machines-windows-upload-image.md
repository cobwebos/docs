---
title: "上载 Windows VHD 用于 Resource Manager | Microsoft Docs"
description: "了解如何使用 Resource Manager 部署模型将 Windows 虚拟机 VHD 从本地上载到 Azure。 可以从通用或专用 VM 上载 VHD。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 192c8e5a-3411-48fe-9fc3-526e0296cf4c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: 5aa6b2149170ef04af0ebde957feda5630c5d5eb


---
# <a name="upload-a-windows-vhd-from-an-on-premises-vm-to-azure"></a>将 Windows VHD 从本地 VM 上载到 Azure
本文说明如何创建和上载 Windows 虚拟硬盘 (VHD)，以便在创建 Azure VM 时使用。 可以从通用 VM 或专用 VM 上载 VHD。 

有关 Azure 中的磁盘和 VHD 的更多详细信息，请参阅 [About disks and VHDs for virtual machines](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（关于虚拟机的磁盘和 VHD）。

## <a name="prepare-the-vm"></a>准备 VM
可将通用和专用 VHD 上载到 Azure。 每种类型都需要事先准备 VM。

* **通用 VHD** - 通用 VHD 包含使用 Sysprep 删除的所有个人帐户信息。 如果打算使用 VHD 作为映像来创建新 VM，应该：
  
  * [准备好要上载到 Azure 的 Windows VHD](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 
  * [使用 Sysprep 通用化虚拟机](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 
* **专用 VHD** - 专用 VHD 保留原始 VM 中的用户帐户、应用程序和其他状态数据。 如果打算按原样使用 VHD 来创建新 VM，请确保完成以下步骤。 
  
  * [准备好要上载到 Azure 的 Windows VHD](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 **不要**使用 Sysprep 通用化 VM。
  * 删除 VM 上安装的所有来宾虚拟化工具和代理（例如 VMware 工具）。
  * 确保 VM 配置为通过 DHCP 提取 IP 地址和 DNS 设置。 这可以确保服务器在启动时获得 VNet 中的 IP 地址。 

## <a name="log-in-to-azure"></a>登录 Azure
如果尚未安装 Azure PowerShell 1.4 或更高版本，请阅读 [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs)（如何安装和配置 Azure PowerShell）。

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
使用 [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) cmdlet 将映像上载到存储帐户中的容器： 本示例将文件 **myVHD.vhd** 从 `"C:\Users\Public\Documents\Virtual hard disks\"` 上载到 **myResourceGroup** 资源组中名为 **mystorageaccount** 的存储帐户。 该文件将放入名为 **mycontainer** 的容器，新文件名为 **myUploadedVHD.vhd**。

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

## <a name="next-steps"></a>后续步骤
* [基于通用 VHD 在 Azure 中创建 VM](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* 创建新 VM 时将专用 VHD 附加为 OS 磁盘，[基于专用 VHD 在 Azure 中创建 VM](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。




<!--HONumber=Dec16_HO2-->


