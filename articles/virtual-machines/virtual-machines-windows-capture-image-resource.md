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
ms.date: 02/27/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 0f6bd85668533ee79700caead2edc6bc6c7d70de
ms.openlocfilehash: 45383cb3b8238d72bf9ba398a22fe0a005941c3f
ms.lasthandoff: 03/01/2017


---
# <a name="capture-a-managed-image-of-a-generalized-vm-in-azure"></a>在 Azure 中捕获通用 VM 的托管映像

可以从在存储帐户中存储为托管磁盘或非托管磁盘的通用 VM 创建托管映像资源。 映像随后可用于创建多个使用托管磁盘进行存储的 VM。 


## <a name="prerequisites"></a>先决条件
必须已将[ VM 通用化](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，且停止\解除分配 VM。 通用化 VM 将删除所有个人帐户信息及其他某些数据，并准备好要用作映像的计算机。



## <a name="create-a-managed-image-in-the-portal"></a>在门户中创建托管映像 

1. 打开[门户](https://portal.azure.com)。
2. 单击加号以创建新资源。
3. 在筛选器搜索中，键入“映像”。
4. 从结果中选择“映像”。
5. 在“映像”边栏选项卡中，单击“创建”。
6. 在“名称”中，键入映像的名称。
7. 如果有多个订阅，请从“订阅”下拉列表中选择正确的订阅。
7. 在“资源组”中，选择“新建”并键入名称，或选择“来自现有”并从下拉列表中选择要使用的资源组。
8. 在“位置”中，选择资源组的位置。
9. 在“OS 类型”中，选择操作系统类型，可以为 Windows 或 Linux。
11. 在“存储 blob”中，单击“浏览”以在 Azure 存储中查找 VHD。
12. 在“帐户类型”中，选择 Standard_LRS 或 Premium_LRS。 标准版使用硬盘驱动器，高级版使用固态硬盘。 它们都使用本地冗余存储。
13. 在“磁盘缓存”中，选择适当的磁盘缓存选项。 选项包括“无”、“只读”和“读\写”。
14. 可选：也可以将现有的数据磁盘添加到映像，只需单击“+ 添加数据磁盘”即可。  
15. 完成选择后，请单击“创建”。
16. 创建映像后，在所选资源组的资源列表中，你将看到它作为**映像**资源而出现。



## <a name="create-a-managed-image-of-a-vm-using-powershell"></a>使用 PowerShell 创建 VM 的托管映像

直接从 VM 创建映像，可确保映像包含所有与该 VM 关联的磁盘，包括 OS 磁盘和任何数据磁盘。


开始前，请确保具有最新版本的 AzureRM.Compute PowerShell 模块。 运行以下命令进行安装。

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
有关详细信息，请参阅 [Azure PowerShell 版本控制](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning)。


1. 创建若干变量。 
    ```powershell
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. 确保 VM 已解除分配。

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 将虚拟机的状态设置为“通用化”。 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. 获取虚拟机。 

    ```powershell
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. 创建映像配置。

    ```powershell
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. 创建映像。

    ```powershell
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ```    



## <a name="create-a-managed-image-of-a-vhd-in-powershell"></a>在 PowerShell 中创建 VHD 的托管映像

使用通用化的 OS VHD 创建托管映像。


1.  首先，设置公共参数：

    ```powershell
    $rgName = "myResourceGroupName"
    $vmName = "myVM"
    $location = "West Central US" 
    $imageName = "yourImageName"
    $osVhdUri = "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. 停止\解除分配 VM。

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 将 VM 标记为通用化。

    ```powershell
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized    
    ```
4.  使用通用化的 OS VHD 创建映像。

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```


## <a name="create-a-managed-image-from-a-snapshot-using-powershell"></a>使用 PowerShell 从快照创建托管映像

还可以从通用 VM 的 VHD 快照创建托管映像。

    
1. 创建若干变量。 

    ```powershell
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. 获取快照。

   ```powershell
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. 创建映像配置。

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. 创建映像。

    ```powershell
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```    
    

## <a name="next-steps"></a>后续步骤
- 现在，你可以[从通用托管映像创建 VM](virtual-machines-windows-create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。    


