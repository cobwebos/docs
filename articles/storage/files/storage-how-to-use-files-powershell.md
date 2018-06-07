---
title: 使用 Azure PowerShell 管理 Azure 文件共享
description: 了解如何使用 Azure PowerShell 管理 Azure 文件共享。
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 4a7d39910fac6096ef17873a9f81c5e1d1508857
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34757133"
---
# <a name="managing-azure-file-shares-with-azure-powershell"></a>使用 Azure PowerShell 管理 Azure 文件共享 
[Azure 文件](storage-files-introduction.md)是 Microsoft 推出的易用云文件系统。 可以在 Windows、Linux 和 macOS 中装载 Azure 文件共享。 本指南介绍通过 PowerShell 来使用 Azure 文件共享的基本知识。 本文介绍如何执行以下操作：

> [!div class="checklist"]
> * 创建资源组和存储帐户
> * 创建 Azure 文件共享 
> * 创建目录
> * 上传文件 
> * 下载文件
> * 创建和使用共享快照

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

若要在本地安装并使用 PowerShell，则本指南需要 Azure PowerShell 模块 5.1.1 或更高版本。 若要找出正在运行的 Azure PowerShell 模块的版本，请执行 `Get-Module -ListAvailable AzureRM`。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzureRmAccount` 以创建与 Azure 的连接。

## <a name="create-a-resource-group"></a>创建资源组
资源组是在其中部署和管理 Azure 资源的逻辑容器。 如果还没有 Azure 资源组，可以使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) cmdlet 新建一个。 

以下示例在“美国东部”区域创建名为“myResourceGroup”的资源组：

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>创建存储帐户
存储帐户是一个存储共享池，可以用来部署 Azure 文件共享或其他存储资源，例如 Blob 或队列。 一个存储帐户可以包含无限数量的共享，一个共享可以存储无限数量的文件，直到达到存储帐户的容量限制为止。

此示例创建名为 `mystorageacct<random number>` 的存储帐户，然后将该存储帐户的引用置于变量 **$storageAcct** 中。 存储帐户名称必须唯一，因此请使用 `Get-Random` 将一个数字追加到名称末尾，使之变得唯一。 

```azurepowershell-interactive 
$storageAcct = New-AzureRmStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>创建 Azure 文件共享
现在可以创建第一个 Azure 文件共享。 可以使用 [New-AzureStorageShare](/powershell/module/azurerm.storage/new-azurestorageshare) cmdlet 创建文件共享。 此示例创建名为 `myshare` 的共享。

```azurepowershell-interactive
New-AzureStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

> [!Important]  
> 共享名必须全部采用小写字母、数字和单个连字符，但不能以连字符开头。 有关命名文件共享和文件的完整详细信息，请参阅 [命名和引用共享、目录、文件和元数据](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)。

## <a name="work-with-the-contents-of-the-azure-file-share"></a>使用 Azure 文件共享的内容
创建 Azure 文件共享以后，即可使用 SMB 在 [Windows](storage-how-to-use-files-windows.md)、[Linux](storage-how-to-use-files-linux.md) 或 [macOS](storage-how-to-use-files-mac.md) 上装载该文件共享。 也可通过 Azure PowerShell 模块使用 Azure 文件共享。 这相对于通过 SMB 来装载文件共享更有优势，因为所有通过 PowerShell 发出的请求都是通过文件 REST API 发出的，因此可以通过以下方式创建、修改和删除文件共享中的文件和目录：

- PowerShell Cloud Shell（不能通过 SMB 来装载文件共享）。
- 无法装载 SMB 共享的客户端，例如未将端口 445 解除阻止的本地客户端。
- 某个解决方案（例如 [Azure Functions](../../azure-functions/functions-overview.md)）中的无服务器方案。 


### <a name="create-directory"></a>创建目录
若要在 Azure 文件共享的根目录中创建名为 *myDirectory* 的新目录，请使用 [New-AzureStorageDirectory](/powershell/module/azurerm.storage/new-azurestoragedirectory) cmdlet。


```azurepowershell-interactive
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

### <a name="upload-a-file"></a>上传文件
若要演示如何使用 [Set-AzureStorageFileContent](/powershell/module/azure.storage/set-azurestoragefilecontent) cmdlet 来上传文件，首先需要在 PowerShell Cloud Shell 的暂存驱动器中创建要上传的文件。 

此示例将当前的日期和时间置于暂存驱动器的新文件中，然后将文件上传到文件共享。

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzureStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

如果在本地运行 PowerShell，则应将 `C:\Users\ContainerAdministrator\CloudDrive\` 替换为计算机上的现有路径。

上传文件以后，可以使用 [Get-AzureStorageFile](/powershell/module/Azure.Storage/Get-AzureStorageFile) cmdlet 进行检查，确保文件已上传到 Azure 文件共享。 

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

### <a name="download-a-file"></a>下载文件
可以使用 [Get-AzureStorageFileContent](/powershell/module/azure.storage/get-azurestoragefilecontent) cmdlet 下载刚上传到 Cloud Shell 的暂存驱动器的文件的副本。

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item 
    `-Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzureStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

下载文件以后，可以使用 `Get-ChildItem` 来查看该文件是否已下载到 PowerShell Cloud Shell 的暂存驱动器。

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

### <a name="copy-files"></a>复制文件
一项常见的任务是将文件从一个文件共享复制到另一个文件共享，或者将文件在文件共享和 Azure Blob 存储容器之间来回复制。 若要演示此功能，可以创建一个新的共享，然后使用 [Start-AzureStorageFileCopy](/powershell/module/azure.storage/start-azurestoragefilecopy) cmdlet 将刚上传的文件复制到该新共享。 

```azurepowershell-interactive
New-AzureStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzureStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

现在，如果列出新共享中的文件，则应看到已复制的文件。

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

虽然 `Start-AzureStorageFileCopy` cmdlet 可以方便地用于 Azure 文件共享和 Azure Blob 存储容器之间的临时文件移动，但我们仍建议你使用 AzCopy 进行较大型的移动（就要移动的文件的数量或大小而言）。 详细了解 [Windows 版 AzCopy](../common/storage-use-azcopy.md) 和 [Linux 版 AzCopy](../common/storage-use-azcopy-linux.md)。 AzCopy 必须安装在本地，在 Cloud Shell 中不可用。 

## <a name="create-and-modify-share-snapshots"></a>创建和修改共享快照
可以通过 Azure 文件共享执行的另一项有用的任务是创建共享快照。 快照保存的是 Azure 文件共享的某个时间点。 共享快照类似于你可能已经熟悉的操作系统技术，例如：
- 适用于 Windows 文件系统（例如 NTFS 和 ReFS）的[卷影复制服务 (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636)
- 适用于 Linux 系统的[逻辑卷管理器 (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) 快照。
- 适用于 macOS 的 [Apple 文件系统 (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) 快照。 

可以在通过 [Get-AzureStorageShare](/powershell/module/azure.storage/get-azurestorageshare) cmdlet 检索的文件共享的 PowerShell 对象上使用 `Snapshot` 方法来创建某个共享的共享快照。 

```azurepowershell-interactive
$share = Get-AzureStorageShare -Context $storageAcct.Context -Name "myshare"
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>浏览共享快照
可以浏览共享快照的内容，只需将快照引用 (`$snapshot`) 传递给 `Get-AzureStorageFile` cmdlet 的 `-Share` 参数即可。

```azurepowershell-interactive
Get-AzureStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>列出共享快照
可以使用以下命令查看为共享生成的快照的列表。

```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>从共享快照还原
可以使用以前用过的 `Start-AzureStorageFileCopy` 命令还原某个文件。 就本快速入门来说，首先请删除以前上传的 `SampleUpload.txt` 文件，这样才能将其从快照还原。

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzureStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzureStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>删除共享快照
可以使用 [Remove-AzureStorageShare](/powershell/module/azure.storage/remove-azurestorageshare) cmdlet 删除共享快照，其中的变量包含对 `-Share` 参数的 `$snapshot` 引用。

```azurepowershell-interactive
Remove-AzureStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>清理资源
完成后，可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) cmdlet 删除资源组和所有相关的资源。 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

也可逐一删除资源：

- 删除为本快速入门创建的 Azure 文件共享。
```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
    Remove-AzureStorageShare -Context $storageAcct.Context -Name $_.Name
}
```

- 删除存储帐户本身（这会隐式删除已创建的 Azure 文件共享，以及任何其他可能已创建的存储资源，例如 Azure Blob 存储容器）。
```azurepowershell-interactive
Remove-AzureRmStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
```

## <a name="next-steps"></a>后续步骤
- [使用 Azure 门户管理文件共享](storage-how-to-use-files-portal.md)
- [使用 Azure CLI 管理文件共享](storage-how-to-use-files-cli.md)
- [使用存储资源管理器管理文件共享](storage-how-to-use-files-storage-explorer.md)
- [规划 Azure 文件部署](storage-files-planning.md)
