---
title: "在 Azure 中创建托管映像 | Microsoft Docs"
description: "在 Azure 中创建通用 VM 或 VHD 的托管映像。 映像可用于创建多个使用托管磁盘的 VM。"
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
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: d6409ac490f530d49f82c93b07b0fd22adbec4de
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>在 Azure 中创建通用 VM 的托管映像

可通过在存储帐户中存储为托管/非托管磁盘的通用 VM 创建托管的映像资源。 随后，该映像可用于创建多个 VM。 


## <a name="generalize-the-windows-vm-using-sysprep"></a>使用 Sysprep 通用化 Windows VM

Sysprep 将删除所有个人帐户信息及其他某些数据，并准备好要用作映像的计算机。 有关 Sysprep 的详细信息，请参阅[如何使用 Sysprep：简介](http://technet.microsoft.com/library/bb457073.aspx)。

确保 Sysprep 支持计算机上运行的服务器角色。 有关详细信息，请参阅 [Sysprep 对服务器角色的支持](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> 如果在首次将 VHD 上传到 Azure 之前运行 Sysprep，请确保先[准备好 VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，然后再运行 Sysprep。 
> 
> 

1. 登录到 Windows 虚拟机。
2. 以管理员身份打开“命令提示符”窗口。 将目录切换到 **%windir%\system32\sysprep**，然后运行 `sysprep.exe`。
3. 在“系统准备工具”对话框中，选择“进入系统全新体验(OOBE)”，确保已选中“通用化”复选框。
4. 在“关机选项”中选择“关机”。
5. 单击 **“确定”**。
   
    ![启动 Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep 在完成运行后会关闭虚拟机。 请勿重启 VM。


## <a name="create-a-managed-image-in-the-portal"></a>在门户中创建托管映像 

1. 打开[门户](https://portal.azure.com)。
2. 在左侧菜单中，单击“虚拟机”，然后从列表中选择 VM。
3. 在 VM 页的上部菜单中，单击“捕获”。
3. 在“名称”中，键入想要用于映像的名称。
4. 在“资源组”中，选择“新建”或键入名称，或选择“使用现有”并从下拉列表中选择要使用的资源组。
5. 如果想要在创建映像后删除源 VM，选择“创建映像后自动删除此虚拟机”。
6. 完成后，单击“创建”。
16. 创建映像后，在资源组的资源列表中，你会看到它作为“映像”资源而出现。



## <a name="create-an-image-of-a-vm-using-powershell"></a>使用 PowerShell 创建 VM 映像

直接从 VM 创建映像，可确保映像包含所有与该 VM 关联的磁盘，包括 OS 磁盘和任何数据磁盘。 本示例演示如何从使用托管磁盘的 VM 创建托管映像。


开始前，请确保具有最新版本的 AzureRM.Compute PowerShell 模块。 运行以下命令进行安装。

```azurepowershell-interactive
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
有关详细信息，请参阅 [Azure PowerShell 版本控制](/powershell/azure/overview)。


1. 创建若干变量。

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. 确保 VM 已解除分配。

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 将虚拟机的状态设置为“通用化”。 
   
    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. 获取虚拟机。 

    ```azurepowershell-interactive
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. 创建映像配置。

    ```azurepowershell-interactive
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. 创建映像。

    ```azurepowershell-interactive
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 
## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>使用 PowerShell 从托管磁盘创建映像

如果你只想创建 OS 磁盘的映像，还可通过将托管磁盘 ID 指定为 OS 磁盘来创建映像。

    
1. 创建若干变量。 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. 获取 VM。

   ```azurepowershell-interactive
   $vm = Get-AzureRmVm -Name myVM -ResourceGroupName $rgName
   ```

3. 获取托管磁盘的 ID。

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. 创建映像配置。

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. 创建映像。

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>使用 PowerShell 从快照创建映像

可以从通用 VM 的快照创建托管映像。

    
1. 创建若干变量。 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. 获取快照。

   ```azurepowershell-interactive
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. 创建映像配置。

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. 创建映像。

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-image-from-a-vhd-in-a-storage-account"></a>从存储帐户中的 VHD 创建映像

从存储帐户中的通用 OS VHD 创建托管映像。 需要存储帐户中 VHD 的 URI，其格式为 https://mystorageaccount.blob.core.windows.net/container/vhd_filename.vhd。 在本示例中，使用的 VHD 位于名为 vhdcontainer 的容器中的 mystorageaccount 中，且 VHD 文件名为 osdisk.vhd。


1.  首先，设置公共参数：

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. 停止\解除分配 VM。

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 将 VM 标记为通用化。

    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized 
    ```
4.  使用通用化的 OS VHD 创建映像。

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>后续步骤
- 现在，可以[从通用托管映像创建 VM](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。  

