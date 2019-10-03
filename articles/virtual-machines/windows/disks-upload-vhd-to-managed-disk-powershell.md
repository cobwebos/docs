---
title: 使用 Azure PowerShell 将 vhd 上传到 Azure
description: 了解如何使用 Azure PowerShell 将 vhd 上传到 Azure 托管磁盘并跨区域复制托管磁盘。
author: roygara
ms.author: rogarana
ms.date: 05/06/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: de9975151270ccce8d4a7abd58210c6550d40464
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720339"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>使用 Azure PowerShell 将 vhd 上传到 Azure

本文介绍如何将 vhd 从本地计算机上传到 Azure 托管磁盘。 以前，你必须遵循更多涉及的过程，将数据暂存到存储帐户，并管理该存储帐户。 现在，你不再需要管理存储帐户，也不需要暂存其中的数据来上载 vhd。 而是创建一个空的托管磁盘，并将 vhd 直接上传到该磁盘。 这简化了将本地 Vm 上传到 Azure 的功能，使你能够将 vhd 最多上传到 32 TiB，并将其直接上传到大型托管磁盘。

如果你正在为 Azure 中的 IaaS Vm 提供备份解决方案，我们建议你使用直接上传将客户备份还原到托管磁盘。 如果要从 Azure 中的外部计算机上传 VHD，则的速度取决于你的本地带宽。 如果使用的是 Azure VM，则带宽将与标准 Hdd 相同。

目前，标准 HDD、标准 SSD 和高级 SSD 托管磁盘支持直接上传。 它尚不支持超 Ssd。

## <a name="prerequisites"></a>先决条件

- 下载最新[版本的 AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)。
- [安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)。
- 如果要从-pem 上传 vhd：已[为 Azure 准备](prepare-for-upload-vhd-image.md)的、本地存储的 vhd。
- 如果要执行复制操作，请使用 Azure 中的托管磁盘。

## <a name="create-an-empty-managed-disk"></a>创建一个空托管磁盘

若要将 vhd 上传到 Azure，需要创建一个为此上传过程配置的空托管磁盘。 创建前，还应了解有关这些磁盘的一些其他信息。

这种托管磁盘具有两种独特的状态：

- ReadToUpload，这意味着磁盘已准备好接收上载，但未生成[安全访问签名](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)（SAS）。
- ActiveUpload，这意味着磁盘已准备好接收上传并且已生成 SAS。

无论在哪种状态中，托管磁盘都将按[标准 HDD 定价](https://azure.microsoft.com/pricing/details/managed-disks/)计费，而不考虑实际的磁盘类型。 例如，P10 将按 S10 计费。 在托管磁盘上调用`revoke-access`之前，此值为 true，这是将磁盘附加到虚拟机所必需的。

在创建用于上传的空标准 HDD 之前，需要要上传的 vhd 的文件大小（以字节为单位）。 示例代码将为您提供该代码，但您自己可以使用： `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`。 指定 **-UploadSizeInBytes**参数时，将使用此值。

现在，在本地 shell 上，通过在 **-CreateOption**参数中指定**上传**设置以及[AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) cmdlet 中的 **-UploadSizeInBytes**参数来创建一个空的标准 HDD 进行上传。 然后调用[AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0)创建磁盘：

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

如果要上传高级 SSD 或标准 SSD，请将**Standard_LRS**替换为**Premium_LRS**或**StandardSSD_LRS**。 超级 SSD 尚不受支持。

你现在已创建了一个为上传过程配置的空托管磁盘。 若要将 vhd 上传到磁盘，则需要一个可写 SAS，以便将其作为上传的目标进行引用。

若要生成空托管磁盘的可写 SAS，请使用以下命令：

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>上传 vhd

现在，你有了用于空托管磁盘的 SAS，可以使用它将托管磁盘设置为上传命令的目标。

通过指定生成的 SAS URI，使用 AzCopy v10 将本地 VHD 文件上传到托管磁盘。

此上传与等效的[标准 HDD](disks-types.md#standard-hdd)具有相同的吞吐量。 例如，如果大小等于 S4，则吞吐量最高可达 60 MiB/s。 但是，如果大小等于 S70，则吞吐量最高可达 500 MiB/秒。

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas --blob-type PageBlob
```

如果在上传过程中 SAS 过期，但尚未调用`revoke-access` ，则可以通过再次使用`grant-access`来获取新的 sas 以继续上传。

上传完成后，不再需要将任何数据写入磁盘，请撤销 SAS。 吊销 SAS 将更改托管磁盘的状态，并允许你将磁盘附加到 VM。

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>复制托管磁盘

直接上传还可以简化复制托管磁盘的过程。 可以在同一区域或跨区域（到其他区域）中进行复制。

下面的脚本将为你执行此操作，此过程类似于前面所述的步骤，因为你使用的是现有磁盘。

> [!IMPORTANT]
> 如果要提供 Azure 中托管磁盘的磁盘大小（以字节为单位），则需要添加512的偏移量。 这是因为在返回磁盘大小时，Azure 会省略页脚。 如果不执行此操作，该副本将失败。 以下脚本已为你执行此功能。

使用你的值替换 `<sourceResourceGroupHere>`，`<sourceDiskNameHere>`，`<targetDiskNameHere>`，`<targetResourceGroupHere>`，`<yourOSTypeHere>`，`<yourTargetLocationHere>` （位置值的示例是 uswest2），然后运行以下脚本，以便复制托管磁盘。

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>后续步骤

现在已成功将 vhd 上传到托管磁盘，可以将磁盘附加到 VM 并开始使用它。

若要了解如何将磁盘附加到 VM，请参阅主题的文章：[使用 PowerShell 将数据磁盘附加到 WINDOWS VM](attach-disk-ps.md)。
