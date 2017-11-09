---
title: "使用 PowerShell 对 Azure Blob 存储（对象存储）执行操作 | Microsoft Docs"
description: "教程 - 使用 PowerShell 对 Azure Blob 存储（对象存储）执行操作"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: robinsh
ms.openlocfilehash: 565bcba848de1c518b25ff4c55a9a47aaa45bfb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="perform-azure-blob-storage-operations-with-azure-powershell"></a>使用 Azure PowerShell 执行 Azure Blob 存储操作

Azure Blob 存储是用于存储大量非结构化对象数据（例如文本或二进制数据）的服务，这些数据可通过 HTTP 或 HTTPS 从世界各地进行访问。 本文介绍 Azure Blob 存储的基本操作，如上传、下载和删除 Blob。 学习如何：

> [!div class="checklist"]
> * 创建容器 
> * 上传 Blob
> * 列出容器中的 Blob 
> * 下载 Blob
> * 复制 blob
> * 删除 Blob
> * 查看和设置 Blob 的元数据和属性
> * 使用共享访问签名管理安全性

本教程需要 Azure PowerShell 模块 3.6 或更高版本。 可以运行 `Get-Module -ListAvailable AzureRM` 来查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>创建容器

始终将 blob 上传到容器中。 容器类似于计算机上的目录，允许在容器中组织成组的 Blob，就好比在计算机上的文件夹中组织文件。 存储帐户可以有任意数量的容器；它仅受到存储帐户中所占用空间的限制（最大 500TB）。 

创建容器时，可以设置访问级别，这有助于定义谁可以访问该容器中的 Blob。 例如，它们可以是专用的（访问级别 = `Off`），这意味着如果没有共享访问签名或存储帐户的访问密钥，就无法访问它们。 如果你在创建容器时未指定访问级别，则默认为专用。

你可能希望容器中的图像可以公开访问。 例如，如果存储要在网站上显示的图像，则希望将其公开。 在这种情况下，将容器访问级别设置为 `blob`，这样，具有指向该容器中的 Blob 的 URL 的任何人都可以访问该 Blob。

要创建容器，请设置容器名称，然后创建容器，同时将权限设置为“blob”。 容器名称必须以字母或数字开头，并且只能包含字母、数字和连字符 (-)。 有关命名 Blob 和容器的详细信息，请参阅[命名和引用容器、Blob 和元数据](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

使用 [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer) 创建新容器。 将访问级别设置为公开。 本示例中的容器名称为 howtoblobs。

```powershell
$containerName = "howtoblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-into-a-container"></a>将 Blob 上传到容器中

Azure Blob 存储支持块 Blob、追加 Blob 和页 Blob。  用于备份 IaaS VM 的 VHD 文件是页 Blob。 追加 blob 用于日志记录，例如有时需要写入到文件，再继续添加更多信息。 Blob 存储中存储的大多数文件都是块 blob。 

要将文件上传到块 blob，请获取容器引用，然后获取对该容器中的块 blob 的引用。 获取 Blob 引用后，可以通过使用 [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent) 将数据上传到其中。 此操作将创建 Blob（如果该 Blob 不存在），或者覆盖它（如果该 Blob 已存在）。

下面介绍如何将 Blob 上传到容器。 首先，设置指向文件所在本地计算机上的目录的变量，并为要上传的文件的名称设置一个变量。 如果你希望重复执行相同的操作，这样做很有用。 上传几个文件，以便在容器中列出 Blob 时可以看到多个条目。

以下示例将 Image001.jpg 和 Image002.png 从本地磁盘上的 D:\\_TestImages 加载到刚刚创建的容器。

```powershell
$localFileDirectory = "D:\_TestImages\"

$blobName = "Image001.jpg"
$localFile = $localFileDirectory + $blobName
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx 
```

上传另一个文件。 

```powershell
$blobName = "Image002.png"
$localFile = $localFileDirectory + $blobName 
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx
```

上传尽可能多的文件，然后继续操作。

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

使用 [ Get-AzureStorageBlob ](/powershell/module/azure.storage/get-azurestorageblob) 获取容器中的 Blob 列表，并选择要显示的 Blob 名称。

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>下载 Blob

将 blob 下载到本地磁盘。 首先，设置一个指向要将 Blob 下载到的本地文件夹的变量。 然后，对于要下载的每个 Blob，请设置名称并调用 [ Get-AzureStorageBlobContent ](/powershell/module/azure.storage/get-azurestorageblobcontent) 以下载 Blob。

本示例将 Blob 复制到本地磁盘上的 D:\\_TestImages\Downloads。 

```powershell
# local directory to which to download the files
# example "D:\_TestImages\Downloads\"
$localTargetDirectory = "D:\_TestImages\Downloads\"

# download the first blob
$blobName = "Image001.jpg"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 

# download another blob
$blobName = "Image002.png"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 
```

## <a name="copy-blobs"></a>复制 blob

复制 Blob 时需要考虑以下几个事项。 只能将 Blob 复制到同一位置的新名称。 可以将 Blob 复制到单独的存储帐户。 可以将大型 Blob（如 VHD 文件）复制到其他存储帐户。 上述每个操作以不同的方式完成。

### <a name="simple-blob-copy"></a>简单的 Blob 副本

可以在容器中将一个 Blob 复制到新的 Blob 中，从而创建副本。 本示例使用不同的名称将 Blob 复制到同一容器中，但是你可以很容易地创建另一个容器并将 Blob 复制到其中。 本示例演示如何使用 [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) 复制 Blob。 必须同时指定源和目标 Blob 名称以及容器名称。

```powershell
$blobName = "Image002.png"
$newBlobName = "CopyOf_" + $blobName 

Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName `
  -DestBlob $newBlobName `
  -Context $ctx 

# get list of blobs to verify the new one has been created
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

### <a name="copy-a-blob-to-a-different-storage-account"></a>将 Blob 复制到不同的存储帐户 

你可能希望将 Blob 复制到单独的存储帐户。 例如，将备份你的一个虚拟机的 VHD 文件复制到另一个存储帐户以便备份它。 

设置第二个存储帐户，检索上下文，在该存储帐户中设置容器并执行复制操作。 这部分的脚本与上述脚本几乎相同，只不过使用的第二个存储帐户而不是第一个。

```powershell
#create new storage acount, get context 
$storageAccount2Name = "blobstutorialtestcopy"
$storageAccount2 = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccount2Name `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx2 = $storageAccount2.Context

#create a container in the second storage account 
$containerName2 = "tutorialblobscopied"
New-AzureStorageContainer -Name $containerName2 `
  -Context $ctx2 `
  -Permission blob

# copy one of the blobs from the first storage account to the second one 
# specify the source and destination container, blob name, and context
Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -SrcContext $ctx `
  -DestContext $ctx2 

# list the blobs in the container in the second storage account
# to verify that the newly copied blob is there
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

### <a name="copying-very-large-blobs-asynchronously"></a>以异步方式复制大型 Blob

如果要复制大型 Blob（大小为几个 GB），可以通过启动来利用异步复制，然后返回并检查状态，直到完成。 这样一来，你在执行复制操作时就不会忙得不可开交。

如果在一个存储帐户内进行复制，复制速度非常快。 如果要在同一区域中的两个存储帐户之间复制，复制速度比较快。 但是，如果你的源和目标位置位于不同的区域，则可能需要几个小时才能完成复制，具体取决于文件的距离和大小。 

此脚本使用上一个复制示例中定义的同一变量。 区别在于此脚本捕获副本的状态，并且每 10 秒重复一次查询，直到完成。 可能需要将一个大型 Blob 上传到存储帐户，这样才会发现完成上述过程需要多个迭代。

使用 [Get-AzureStorageBlobCopyState](/powershell/module/azure.storage/get-azurestorageblobcopystate) 查询副本的状态。 

```powershell
# start the blob copy, and assign the result to $blobResult
$blobResult = Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -Context $ctx `
  -DestContext $ctx2

# get the status of the blob copy
$status = $blobResult | Get-AzureStorageBlobCopyState 
# show the value of the status
$status 

# loop until it finishes copying, pausing for 10 seconds after each iteration
# it is finished when the status is no longer 'Pending'
while ($status.Status -eq "Pending") {
    $status = $blobResult | Get-AzureStorageBlobCopyState 
    $status
    Start-Sleep 10
}

# get the list of blobs to see the new file in the second storage account 
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

## <a name="delete-blobs"></a>删除 Blob

若要从存储帐户中删除 Blob，请使用 [Remove-azurestorageblob](/powershell/module/azure.storage/Remove-AzureStorageBlob)。 

```powershell
$blobName = "Image001.jpg"
Remove-AzureStorageBlob -Blob $blobName -Container $containerName -Context $ctx

# get list of blobs to see the one you deleted is gone
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

## <a name="read-and-write-a-blobs-properties-and-metadata"></a>读取和写入 Blob 的属性和元数据

要访问返回的 IListBlobItem 的属性和方法集，必须将它转换为 CloudBlockBlob、CloudPageBlob 或 CloudBlobDirectory 对象。 如果类型未知，可以使用类型检查来确定要将其转换为哪种类型。 以下代码演示如何使用 [ Get-AzureStorageBlob ](/powershell/module/azure.storage/get-azurestorageblob) 检索和输出之前上传的一个 Blob 的属性，并将结果转换为 CloudBlockBlob 对象。

```powershell
# blob properties
# get a reference to the blob you uploaded -- this is an **IListBlobItem** -- then
# convert it to a CloudBlockBlob, giving you access to the properties 
# and methods of the blob 
$blobName = "Image001.jpg"
$blob = Get-AzureStorageBlob -Context $ctx `
   -Container $containerName `
   -Blob $blobName 

#convert $blob to a CloudBlockBlob object
$cloudBlockBlob = [Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob] $blob.ICloudBlob

# you can view the properties by typing in $cloudBlockBlob 
#   and hitting the period key; this brings up IntelliSense,
#   which shows you all of the available properties
Write-Host "blob type = " $cloudBlockBlob.BlobType
Write-Host "blob name = " $cloudBlockBlob.Name
Write-Host "blob uri = " $cloudBLockBlob.Uri
```

通过调用 FetchAttributes 来填充系统属性，然后查看这些属性。 某些属性是可写的，可以更改它们的值。 此示例演示如何更改内容类型，也称为 MIME 类型。

```powershell
# populate the system properties
$cloudBlockBlob.FetchAttributes()

# view some of the system properties
# contentType is commonly referred to as MIME type
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
Write-Host "size = " $cloudBlockBlob.Properties.Length 

# change the content type to image/png
$contentType = "image/jpg"
$cloudBlockBlob.Properties.ContentType = $contentType 
$cloudBlockBlob.SetProperties() 

# get the system properties again to show that the content type has changed
$cloudBlockBlob.FetchAttributes()
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
```

每个 Blob 都有它自己的元数据，可以设置这些元数据。 例如，可以存储 Blob 上传者的名字或首次上传的日期/时间。 元数据包含键/值对。 如下所示，可以使用 PowerShell 进行修改。

```powershell
# "filename" is the key, "original file name" is the value
$cloudBlockBlob.Metadata["filename"] = "original file name"

# "owner" is the key, "RobinS" is the value
$cloudBlockBlob.Metadata["owner"] = "RobinS"

# save the metadata and then display it
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata

# clear the metadata, save it, and show it
$cloudBlockBlob.Metadata.Clear()
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata
```

## <a name="managing-security-for-blobs"></a>管理 Blob 安全性 

默认情况下，Azure 存储会限制拥有存储帐户访问密钥的帐户所有者的访问权限以保持数据安全性。 当需要共享存储帐户中的 Blob 数据时，请注意不可危及帐户访问密钥的安全性。 为此，可以使用共享访问签名 URL，这是一个指向包含查询参数以及允许在指定时间内使用特定级别的权限的安全令牌的资产的 URL。 例如，你可能希望提供对专用 Blob 5 分钟的读取权限，以便用户可以查看。 

### <a name="set-the-access-level-of-the-container-and-its-blobs-to-private"></a>将容器及其 Blob 的访问级别设置为专用

首先，将容器的访问级别设置为 `Off`，用于指定没有共享访问签名或帐户密钥就不能访问。 使用 [Set-AzureStorageContainerAcl](/powershell/module/azure.storage/Set-AzureStorageContainerAcl)。

```powershell
Set-AzureStorageContainerAcl -Name $containerName -Context $ctx -Permission Off 
```

### <a name="test-private-access"></a>测试专用访问

要验证并确认你无法访问该容器中的 Blob，请将 URL 构造为其中一个没有共享访问签名的 Blob，并尝试查看 Blob。 使用 HTTPS 协议，URL 将为以下格式：

    `https://storageaccountname.blob.core.windows.net/containername/blobname`

这将演示如何创建 Blob URL。

```powershell
$blobUrl = "https://" `
  $storageAccountName ".blob.core.windows.net/" + `
  $containerName +  "/" $blobName

Write-Host "Blob URL = " $blobUrl 
```

复制 Blob URL 并将其粘贴到私密浏览器窗口中，窗口会显示授权错误，因为 Blob 是专用的，并且你没有加入共享访问签名。 

### <a name="create-the-sas-uri"></a>创建 SAS URI

创建 SAS URI -- 这是指向包含构成 SAS 的查询参数和安全令牌的 Blob 的链接。 将此 URI 粘贴到一个专用浏览窗口，窗口将显示图像。 

首先创建访问开始日期/时间和过期日期/时间。 这将使用 2 分钟的时间范围。 

```powershell
# set the start time to the current date/time 
# set the end time to 2 minutes in the future
$StartTime = Get-Date
$EndTime = $StartTime.AddMinutes(2.0)
```

使用 [New-AzureStorageBlobSASToken](/powershell/module/azure.storage/new-azurestorageblobsastoken) 创建 SAS URI。 需要容器名称、Blob 名称、存储帐户上下文、要授予的权限（在本例中为读取、写入和删除）以及访问的开始时间和结束时间。 

```powershell
$SASURI = New-AzureStorageBlobSASToken -Container $containerName `
    -Blob $blobName `
    -Context $ctx `
    -Permission "rwd" `
    -StartTime $StartTime `
    -ExpiryTime $EndTime `
    -FullUri

Write-Host "URL with SAS = " $SASURI
```

复制 SAS URI 并将其置于私密浏览器窗口中；它会显示图像。

等待足够长的时间直至 URL 过期（在本例中为 2 分钟），然后将该 URL 粘贴到另一个私密浏览器窗口中。 此时，由于 SAS URI 已过期，你将收到授权错误，因此不会显示图片。

## <a name="clean-up-resources"></a>清理资源

删除所有已创建的资产。 为此，可以删除资源组，这也会删除组中包含的所有资源。 在这种情况下，会删除所有的存储帐户和资源组本身。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解 Blob 存储管理的基本知识，例如如何：

> [!div class="checklist"]
> * 创建容器 
> * 上传 Blob
> * 列出容器中的 Blob 
> * 下载 Blob
> * 复制 blob
> * 删除 Blob
> * 读取和写入 Blob 的元数据和属性
> * 使用共享访问签名管理安全性

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell 存储 cmdlet
* [存储 PowerShell cmdlet](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure 存储资源管理器
* [Microsoft Azure 存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)是 Microsoft 免费提供的独立应用，适用于在 Windows、macOS 和 Linux 上以可视方式处理 Azure 存储数据。