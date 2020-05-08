---
title: 将 VHD 上传到 Azure 或跨区域复制磁盘-Azure CLI
description: 了解如何使用 Azure CLI 通过直接上传将 VHD 上传到 Azure 托管磁盘，以及如何跨区域复制托管磁盘。
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: c32915617d3149eee42bfdfd03d22f9ce5799ef2
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580226"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>将 VHD 上传到 Azure，或将托管磁盘复制到其他区域 - Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>先决条件

- 下载最新版本 [AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)。
- [安装 Azure CLI](/cli/azure/install-azure-cli)。
- 若要从本地上传 VHD：[已为 Azure 准备了](../windows/prepare-for-upload-vhd-image.md)一个固定大小的 VHD，存储在本地。
- 若要执行复制操作，请使用 Azure 中的托管磁盘。

## <a name="getting-started"></a>入门

如果希望通过 GUI 上传磁盘，可以使用 Azure 存储资源管理器。 有关详细信息，请参阅：[使用 Azure 存储资源管理器管理 Azure 托管磁盘](disks-use-storage-explorer-managed-disks.md)

若要将 VHD 上传到 Azure，需要创建一个针对此上传过程配置的空托管磁盘。 在创建托管磁盘之前，应了解有关这些磁盘的一些附加信息。

这种托管磁盘有两种独特的状态：

- ReadToUpload，表示磁盘已做好上传准备，但尚未生成[安全访问签名](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS)。
- ActiveUpload，表示磁盘已做好上传准备，并且已生成 SAS。

> [!NOTE]
> 在任一状态下，无论实际磁盘类型是什么，都会按[标准 HDD 定价](https://azure.microsoft.com/pricing/details/managed-disks/)对托管磁盘计费。 例如，P10 将按 S10 计费。 在对托管磁盘调用 `revoke-access` 之前（将磁盘附加到 VM 需要执行此调用），都是如此。

## <a name="create-an-empty-managed-disk"></a>创建空托管磁盘

在创建要上传的空标准 HDD 之前，需要获取要上传的 VHD 的文件大小（以字节为单位）。 为此，可以使用 `wc -c <yourFileName>.vhd` 或 `ls -al <yourFileName>.vhd`。 指定 **--upload-size-bytes** 参数时将使用此值。

通过在[disk create](/cli/azure/disk#az-disk-create) cmdlet 中指定 **--for 上传**参数和 **--上传大小-字节**参数，创建一个空的标准 HDD 以进行上传：

将 `<yourdiskname>`、`<yourresourcegroupname>`、`<yourregion>` 替换为所选值。 `--upload-size-bytes` 参数包含示例值 `34359738880`，请将其替换为适合你的值。

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

若要上传高级 SSD 或标准 SSD，请将 **standard_lrs** 替换为 **premium_LRS** 或 **standardssd_lrs**。 目前不支持超级磁盘。

现在，你已创建了一个针对上传过程配置的空托管磁盘，可以将 VHD 上传到其中了。 若要将 VHD 上传到磁盘，需要一个可写的 SAS，以便将此磁盘作为上传目标引用。

若要生成空托管磁盘的可写 SAS，请替换 `<yourdiskname>` 和 `<yourresourcegroupname>`，然后使用以下命令：

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

示例返回值：

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>上传 VHD

生成空托管磁盘的 SAS 后，可以使用该 SAS 将托管磁盘设置为上传命令的目标。

使用 AzCopy v10 并指定生成的 SAS URI，将本地 VHD 文件上传到托管磁盘。

此上传过程的吞吐量与相应[标准 HDD](disks-types.md#standard-hdd) 的吞吐量相同。 例如，如果大小相当于 S4，则最高吞吐量为 60 MiB/秒。 但是，如果大小相当于 S70，则最高吞吐量为 500 MiB/秒。

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

上传完成后，如果你不再需要将更多数据写入磁盘，请吊销 SAS。 吊销 SAS 会更改托管磁盘的状态，使你可以将磁盘附加到 VM。

替换 `<yourdiskname>` 和 `<yourresourcegroupname>`，然后使用以下命令使磁盘可用：

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>复制托管磁盘

直接上传还能简化复制托管磁盘的过程。 可以在同一区域中进行复制，或者跨区域复制（复制到另一区域）。

以下脚本可自动完成此操作，此过程类似于前面所述的步骤，但由于处理的是现有磁盘，因此存在一些差异。

> [!IMPORTANT]
> 提供 Azure 中托管磁盘的磁盘大小（以字节为单位）时，需要添加 512 偏移量。 这是因为，Azure 在返回磁盘大小时会省略脚注。 如果不添加此偏移量，复制将会失败。 以下脚本中已添加此偏移量。

将`<sourceResourceGroupHere>` `<sourceDiskNameHere>`、 `<targetDiskNameHere>` `<yourTargetLocationHere>` 、、和（位置值的示例为 uswest2）替换为你的值，然后运行以下脚本，以便复制托管磁盘。 `<targetResourceGroupHere>`

```azurecli
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[diskSizeBytes]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>后续步骤

成功将 VHD 上传到托管磁盘后，可[将该磁盘作为数据磁盘附加到现有的 VM](add-disk.md)，或者[将该磁盘作为 OS 磁盘附加到 VM](upload-vhd.md#create-the-vm)，以创建新的 VM。 

