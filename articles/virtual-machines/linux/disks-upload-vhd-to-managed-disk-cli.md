---
title: 将 VHD 上载到 Azure 或跨区域复制磁盘 - Azure CLI
description: 了解如何通过直接上载将 VHD 上载到 Azure 托管磁盘，并使用 Azure CLI 跨区域复制托管磁盘。
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 6813206aebe67e4e6d2afd0d9c78d03f0c20c952
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420970"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>将 VHD 上载到 Azure 或将托管磁盘复制到其他区域 - Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>先决条件

- 下载最新版本 [AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)。
- [安装 Azure CLI](/cli/azure/install-azure-cli)。
- 如果要从本地上载 VHD：[已为 Azure 准备的](../windows/prepare-for-upload-vhd-image.md)固定大小 VHD，存储在本地。
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

在创建用于上载的空标准 HDD 之前，您需要要上载的 VHD 的文件大小（以字节为单位）。 为此，可以使用 `wc -c <yourFileName>.vhd` 或 `ls -al <yourFileName>.vhd`。 指定 **--upload-size-bytes** 参数时将使用此值。

通过在磁盘中指定 **--for 上传**参数和 **--upload 大小字节**参数创建用于上载的空标准 HDD[创建](/cli/azure/disk#az-disk-create)cmdlet：

用`<yourdiskname>`您选择的`<yourresourcegroupname>`值`<yourregion>`替换 。。 参数`--upload-size-bytes`包含`34359738880`的示例值 ， 将其替换为适合您的值。

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

如果要上传高级 SSD 或标准 SSD，请将**standard_lrs**替换为**premium_LRS**或**standardssd_lrs**。 目前不支持超磁盘。

现在，您已经创建了为上载过程配置的空托管磁盘，您可以将 VHD 上载到该磁盘。 要将 VHD 上载到磁盘，您需要一个可写入的 SAS，以便您可以将其作为上载的目标。

要生成空托管磁盘的可写 SAS，请替换`<yourdiskname>`和`<yourresourcegroupname>`，然后使用以下命令：

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

替换`<yourdiskname>``<yourresourcegroupname>`和 ，然后使用以下命令使磁盘可用：

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>复制托管磁盘

直接上传还能简化复制托管磁盘的过程。 可以在同一区域中进行复制，或者跨区域复制（复制到另一区域）。

以下脚本可自动完成此操作，此过程类似于前面所述的步骤，但由于处理的是现有磁盘，因此存在一些差异。

> [!IMPORTANT]
> 提供 Azure 中托管磁盘的磁盘大小（以字节为单位）时，需要添加 512 偏移量。 这是因为，Azure 在返回磁盘大小时会省略脚注。 如果不添加此偏移量，复制将会失败。 以下脚本中已添加此偏移量。

将`<sourceResourceGroupHere>`、 `<sourceDiskNameHere>` `<targetDiskNameHere>`、 `<targetResourceGroupHere>`、`<yourTargetLocationHere>`和 （位置值的示例将为 uswest2） 替换为值，然后运行以下脚本以复制托管磁盘。

```azurecli
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[uniqueId]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>后续步骤

现在，您已成功将 VHD 上载到托管磁盘，您可以将磁盘作为[数据磁盘附加到现有 VM，](add-disk.md)或[将磁盘作为 OS 磁盘附加到 VM，](upload-vhd.md#create-the-vm)以创建新的 VM。 

