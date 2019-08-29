---
title: 在 Azure 中创建托管映像 | Microsoft Docs
description: 在 Azure 中创建通用 VM 或 VHD 的托管映像。 映像可用于创建多个使用托管磁盘的 VM。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 33f3b03ba76a0c3fd33e057d0f15b2ab7a0f44e4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089501"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>在 Azure 中创建通用 VM 的托管映像

可通过在存储帐户中存储为托管/非托管磁盘的通用虚拟机 (VM) 创建托管的映像资源。 随后，该映像可用于创建多个 VM。 有关托管映像如何计费的信息，请参阅[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)。 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="generalize-the-windows-vm-using-sysprep"></a>使用 Sysprep 通用化 Windows VM

Sysprep 将删除所有个人帐户和安全信息，并准备好要用作映像的计算机。 有关 Sysprep 的信息，请参阅 [Sysprep 概述](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)。

确保 Sysprep 支持计算机上运行的服务器角色。 有关详细信息, 请参阅[Sysprep 对服务器角色的支持](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)和[不支持的方案](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios)。

> [!IMPORTANT]
> 在 VM 上运行 Sysprep 后，该 VM 将被视为已通用化而无法重启。 通用化 VM 的过程是不可逆的。 如果需要保持原始 VM 正常运行，请创建 [VM 的副本](create-vm-specialized.md#option-3-copy-an-existing-azure-vm)并将其副本通用化。 
>
> 如果计划在首次将虚拟硬盘 (VHD) 上传到 Azure 之前运行 Sysprep，请确保先[准备好 VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。  
> 
> 

若要将 Windows VM 通用化，请执行以下步骤：

1. 登录到 Windows VM。
   
2. 以管理员身份打开“命令提示符”窗口。 将目录切换到 %windir%\system32\sysprep，然后运行 `sysprep.exe`。
   
3. 在“系统准备工具”对话框中，选择“进入系统全新体验(OOBE)”，并选中“通用”复选框。
   
4. 在“关机选项”中选择“关机”。
   
5. 选择“确定”。
   
    ![启动 Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. Sysprep 在完成运行后会关闭 VM。 请勿重启 VM。


## <a name="create-a-managed-image-in-the-portal"></a>在门户中创建托管映像 

1. 打开 [Azure 门户](https://portal.azure.com)。

2. 在左侧菜单中，选择“虚拟机”，然后从列表中选择 VM。

3. 在 VM 的“虚拟机”页面的上方菜单中，选择“捕获”。

   将显示“创建映像”页面。

4. 对于“名称”，可以使用预填充的名称或输入想要为映像使用的名称。

5. 对于“资源组”，选择“新建”并输入名称，或选择“使用现有”并从下拉列表中选择要使用的资源组。

6. 如果想要在创建映像后删除源 VM，选择“创建映像后自动删除此虚拟机”。

7. 如果希望能够在任何[可用性区域](../../availability-zones/az-overview.md)中使用映像，请为“区域复原”选择“打开”。

8. 选择“创建”以创建映像。

9. 创建映像后，在资源组的资源列表中，你会看到它作为“映像”资源而出现。



## <a name="create-an-image-of-a-vm-using-powershell"></a>使用 PowerShell 创建 VM 映像

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

直接从 VM 创建映像，可确保映像包含所有与该 VM 关联的磁盘，包括 OS 磁盘和任何数据磁盘。 本示例演示如何从使用托管磁盘的 VM 创建托管映像。

在开始之前，请确保有最新版本的 Azure PowerShell 模块。 若要查找版本，请在 PowerShell 中运行 `Get-Module -ListAvailable Az`。 如需升级，请参阅[使用 PowerShellGet 在 Windows 上安装 Azure PowerShell](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则运行 `Connect-AzAccount` 以创建与 Azure 的连接。


> [!NOTE]
> 如果希望将映像存储在具有区域冗余能力的存储中，需要在支持[可用性区域](../../availability-zones/az-overview.md)的区域中创建该映像并在映像配置中包括 `-ZoneResilient` 参数（`New-AzImageConfig` 命令）。

若要创建 VM 映像，请遵循下列步骤：

1. 创建若干变量。

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. 确保 VM 已解除分配。

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 将虚拟机的状态设置为“通用化”。 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. 获取虚拟机。 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. 创建映像配置。

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. 创建映像。

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>使用 PowerShell 从托管磁盘创建映像

如果仅想创建 OS 磁盘的映像，则将托管磁盘 ID 指定为 OS 磁盘：

    
1. 创建若干变量。 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. 获取 VM。

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. 获取托管磁盘的 ID。

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. 创建映像配置。

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. 创建映像。

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>使用 PowerShell 从快照创建映像

通过执行以下步骤，可以从通用 VM 的快照创建托管映像：

    
1. 创建若干变量。 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. 获取快照。

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. 创建映像配置。

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. 创建映像。

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vhd-in-a-storage-account"></a>从存储帐户中的 VHD 创建映像

从存储帐户中的通用 OS VHD 创建托管映像。 需要存储帐户中 VHD 的 URI，其格式如下： https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer*/*vhdfilename.vhd*。 在本示例中，VHD 位于名为 vhdcontainer 的容器中的 mystorageaccount 中，且 VHD 文件名为 vhdfilename.vhd。


1.  创建若干变量。

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. 停止/解除分配 VM。

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 将 VM 标记为通用化。

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  使用通用化的 OS VHD 创建映像。

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>后续步骤
- [从托管的映像创建 VM](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。    

