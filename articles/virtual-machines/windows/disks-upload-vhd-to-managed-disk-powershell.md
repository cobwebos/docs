---
title: 将 VHD 上载到 Azure 或跨区域复制磁盘 - Azure PowerShell
description: 了解如何通过直接上载将 VHD 上载到 Azure 托管磁盘，并使用 Azure PowerShell 跨区域复制托管磁盘。
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 7c66507989357569828d4ef933cfdca735f71570
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085405"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>将 VHD 上载到 Azure 或将托管磁盘复制到其他区域 - Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>先决条件

- 下载最新版本 [AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)。
- [安装 Azure 电源外壳模块](/powershell/azure/install-Az-ps)。
- 如果要从本地上载 VHD：[已为 Azure 准备的](prepare-for-upload-vhd-image.md)固定大小 VHD，存储在本地。
- 若要执行复制操作，请使用 Azure 中的托管磁盘。

## <a name="getting-started"></a>入门

如果希望通过 GUI 上载磁盘，可以使用 Azure 存储资源管理器执行此操作。 有关详细信息，请参阅：[使用 Azure 存储资源管理器管理 Azure 托管磁盘](disks-use-storage-explorer-managed-disks.md)

要将 VHD 上载到 Azure，您需要创建为此上载过程配置的空托管磁盘。 在创建托管磁盘之前，应了解有关这些磁盘的一些附加信息。

这种托管磁盘有两种独特的状态：

- ReadToUpload，表示磁盘已做好上传准备，但尚未生成[安全访问签名](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS)。
- ActiveUpload，表示磁盘已做好上传准备，并且已生成 SAS。

> [!NOTE]
> 在任一状态下，无论实际磁盘类型是什么，都会按[标准 HDD 定价](https://azure.microsoft.com/pricing/details/managed-disks/)对托管磁盘计费。 例如，P10 将按 S10 计费。 在对托管磁盘调用 `revoke-access` 之前（将磁盘附加到 VM 需要执行此调用），都是如此。

## <a name="create-an-empty-managed-disk"></a>创建空托管磁盘

在创建用于上载的空标准 HDD 之前，您需要要上载的 VHD 的文件大小（以字节为单位）。 可以使用示例代码来这样做，但若要自己操作，可以使用 `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`。 指定 **-UploadSizeInBytes** 参数时将使用此值。

现在，请在本地 shell 上创建一个要上传的空的标准 HDD，方法是：在 **-CreateOption** 参数中指定 **Upload** 设置，并在 [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) cmdlet 中指定 **-UploadSizeInBytes** 参数。 然后调用[New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0)创建磁盘。

替换`<yourdiskname>` `<yourresourcegroupname>`，`<yourregion>`然后运行以下命令：

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname' -DiskName '<yourdiskname>' -Disk $diskconfig
```

若要上传高级 SSD 或标准 SSD，请将 **Standard_LRS** 替换为 **Premium_LRS** 或 **StandardSSD_LRS**。 不支持超磁盘。

现在，您已经创建了为上载过程配置的空托管磁盘，您可以将 VHD 上载到该磁盘。 要将 VHD 上载到磁盘，您需要一个可写入的 SAS，以便您可以将其作为上载的目标。

要生成空托管磁盘的可写 SAS，请替换`<yourdiskname>`和`<yourresourcegroupname>`，然后使用以下命令：

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>上传 VHD

生成空托管磁盘的 SAS 后，可以使用该 SAS 将托管磁盘设置为上传命令的目标。

使用 AzCopy v10 并指定生成的 SAS URI，将本地 VHD 文件上传到托管磁盘。

此上传过程的吞吐量与相应[标准 HDD](disks-types.md#standard-hdd) 的吞吐量相同。 例如，如果大小相当于 S4，则最高吞吐量为 60 MiB/秒。 但是，如果大小相当于 S70，则最高吞吐量为 500 MiB/秒。

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

上传完成后，如果你不再需要将更多数据写入磁盘，请吊销 SAS。 吊销 SAS 会更改托管磁盘的状态，使你可以将磁盘附加到 VM。

替换`<yourdiskname>``<yourresourcegroupname>`和 ，然后运行以下命令：

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>复制托管磁盘

直接上传还能简化复制托管磁盘的过程。 您可以在同一区域内复制或将托管磁盘复制到其他区域。

遵循的脚本将为您执行此操作，该过程与前面描述的步骤类似，由于您正在使用现有磁盘，因此存在一些差异。

> [!IMPORTANT]
> 提供 Azure 中托管磁盘的磁盘大小（以字节为单位）时，需要添加 512 偏移量。 这是因为，Azure 在返回磁盘大小时会省略脚注。 如果不添加此偏移量，复制将会失败。 以下脚本中已添加此偏移量。

将`<sourceResourceGroupHere>`、 `<sourceDiskNameHere>` `<targetDiskNameHere>`、 `<targetResourceGroupHere>` `<yourOSTypeHere>` 、`<yourTargetLocationHere>`和 （位置值的示例将为 uswest2） 替换为值，然后运行以下脚本以复制托管磁盘。

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

现在，您已成功将 VHD 上载到托管磁盘，您可以将磁盘附加到 VM 并开始使用它。

要了解如何将数据磁盘附加到 VM，请参阅我们有关主题的文章：[使用 PowerShell 将数据磁盘附加到 Windows VM。](attach-disk-ps.md) 若要将磁盘用作 OS 磁盘，请参阅[从专用磁盘创建 Windows VM](create-vm-specialized.md#create-the-new-vm)。
