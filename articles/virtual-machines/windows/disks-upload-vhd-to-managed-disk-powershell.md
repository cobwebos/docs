---
title: 将 VHD 上传到 Azure 或跨区域复制磁盘 - Azure PowerShell
description: 了解如何通过直接上传将 VHD 上传到 Azure 托管磁盘，以及如何使用 Azure PowerShell 跨区域复制托管磁盘。
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 25d8c36cc42c3f1d1cc2a8477a7361ba45bec706
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977912"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>将 VHD 上传到 Azure，或将托管磁盘复制到其他区域 - Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>先决条件

- 下载最新版本 [AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)。
- [安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)。
- 若要从本地上传 VHD：[已为 Azure 准备了](prepare-for-upload-vhd-image.md)一个固定大小的 VHD，存储在本地。
- 若要执行复制操作，请使用 Azure 中的托管磁盘。

## <a name="getting-started"></a>入门

如果希望通过 GUI 上传磁盘，可以使用 Azure 存储资源管理器。 有关详细信息，请参阅：[使用 Azure 存储资源管理器管理 Azure 托管磁盘](../disks-use-storage-explorer-managed-disks.md)

若要将 VHD 上传到 Azure，需要创建一个针对此上传过程配置的空托管磁盘。 在创建托管磁盘之前，应了解有关这些磁盘的一些附加信息。

这种托管磁盘有两种独特的状态：

- ReadToUpload，表示磁盘已做好上传准备，但尚未生成[安全访问签名](../../storage/common/storage-sas-overview.md) (SAS)。
- ActiveUpload，表示磁盘已做好上传准备，并且已生成 SAS。

> [!NOTE]
> 在任一状态下，无论实际磁盘类型是什么，都会按[标准 HDD 定价](https://azure.microsoft.com/pricing/details/managed-disks/)对托管磁盘计费。 例如，P10 将按 S10 计费。 在对托管磁盘调用 `revoke-access` 之前（将磁盘附加到 VM 需要执行此调用），都是如此。

## <a name="create-an-empty-managed-disk"></a>创建空托管磁盘

在创建要上传的空标准 HDD 之前，需要获取要上传的 VHD 的文件大小（以字节为单位）。 可以使用示例代码来这样做，但若要自己操作，可以使用 `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`。 指定 **-UploadSizeInBytes** 参数时将使用此值。

现在，请在本地 shell 上创建一个要上传的空的标准 HDD，方法是：在 **-CreateOption** 参数中指定 **Upload** 设置，并在 [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) cmdlet 中指定 **-UploadSizeInBytes** 参数。 然后调用 [New-AzDisk](/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) 来创建磁盘。

替换 `<yourdiskname>`、`<yourresourcegroupname>` 和 `<yourregion>`，然后运行以下命令：

> [!TIP]
> 如果要创建 OS 磁盘，请将 -HyperVGeneration <yourGeneration> 添加到 `New-AzDiskConfig`。

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname' -DiskName '<yourdiskname>' -Disk $diskconfig
```

若要上传高级 SSD 或标准 SSD，请将 **Standard_LRS** 替换为 **Premium_LRS** 或 **StandardSSD_LRS**。 目前尚不支持 Ultra 磁盘。

现在，你已创建了一个针对上传过程配置的空托管磁盘，可以将 VHD 上传到其中了。 若要将 VHD 上传到磁盘，需要一个可写的 SAS，以便将此磁盘作为上传目标引用。

若要生成空托管磁盘的可写 SAS，请替换 `<yourdiskname>` 和 `<yourresourcegroupname>`，然后使用以下命令：

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>上传 VHD

生成空托管磁盘的 SAS 后，可以使用该 SAS 将托管磁盘设置为上传命令的目标。

使用 AzCopy v10 并指定生成的 SAS URI，将本地 VHD 文件上传到托管磁盘。

此上传过程的吞吐量与相应[标准 HDD](../disks-types.md#standard-hdd) 的吞吐量相同。 例如，如果大小相当于 S4，则最高吞吐量为 60 MiB/秒。 但是，如果大小相当于 S70，则最高吞吐量为 500 MiB/秒。

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

上传完成后，如果你不再需要将更多数据写入磁盘，请吊销 SAS。 吊销 SAS 会更改托管磁盘的状态，使你可以将磁盘附加到 VM。

替换 `<yourdiskname>` 和 `<yourresourcegroupname>`，然后运行以下命令：

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>复制托管磁盘

直接上传还能简化复制托管磁盘的过程。 可以在同一区域中进行复制，或者将托管磁盘复制到其他区域。

以下脚本可自动完成此操作，此过程类似于前面所述的步骤，但由于处理的是现有磁盘，因此存在一些差异。

> [!IMPORTANT]
> 提供 Azure 中托管磁盘的磁盘大小（以字节为单位）时，需要添加 512 偏移量。 这是因为，Azure 在返回磁盘大小时会省略脚注。 如果不添加此偏移量，复制将会失败。 以下脚本中已添加此偏移量。

替换 `<sourceResourceGroupHere>` 、 `<sourceDiskNameHere>` 、 `<targetDiskNameHere>` 、 `<targetResourceGroupHere>` `<yourOSTypeHere>` 和 `<yourTargetLocationHere>` (位置值的示例将与值 uswest2) ，然后运行以下脚本，以便复制托管磁盘。

> [!TIP]
> 如果要创建 OS 磁盘，请将 -HyperVGeneration <yourGeneration> 添加到 `New-AzDiskConfig`。

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

成功将 VHD 上传到托管磁盘后，即可将磁盘附加到 VM 并开始使用它。

若要了解如何将数据磁盘附加到 VM，请参阅有关此主题的文章：[使用 PowerShell 将数据磁盘附加到 Windows VM](attach-disk-ps.md)。 若要将磁盘用作 OS 磁盘，请参阅[从专用磁盘创建 Windows VM](create-vm-specialized.md#create-the-new-vm)。