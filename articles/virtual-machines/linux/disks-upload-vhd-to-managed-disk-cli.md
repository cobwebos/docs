---
title: 使用 Azure CLI 将 vhd 上传到 Azure
description: 了解如何使用 Azure CLI 将 vhd 上传到 Azure 托管磁盘并跨区域复制托管磁盘。
services: virtual-machines-linux,storage
author: roygara
ms.author: rogarana
ms.date: 09/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: d16e37849ce8ba043fdb1fddb13df2abe8732cda
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71717173"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>使用 Azure CLI 将 vhd 上传到 Azure

本文介绍如何将 vhd 从本地计算机上传到 Azure 托管磁盘。 以前，你必须遵循更多涉及的过程，将数据暂存到存储帐户，并管理该存储帐户。 现在，你不再需要管理存储帐户，也不需要暂存其中的数据来上载 vhd。 而是创建一个空的托管磁盘，并将 vhd 直接上传到该磁盘。 这简化了将本地 Vm 上传到 Azure 的功能，使你能够将 vhd 最多上传到 32 TiB，并将其直接上传到大型托管磁盘。

如果你正在为 Azure 中的 IaaS Vm 提供备份解决方案，我们建议你使用直接上传将客户备份还原到托管磁盘。 如果要从 Azure 中的外部计算机上传 VHD，则的速度取决于你的本地带宽。 如果使用的是 Azure VM，则带宽将与标准 Hdd 相同。

目前，标准 HDD、标准 SSD 和高级 SSD 托管磁盘支持直接上传。 它尚不支持超 Ssd。

## <a name="prerequisites"></a>先决条件

- 下载最新[版本的 AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)。
- [安装 Azure CLI](/cli/azure/install-azure-cli)。
- Vhd 文件，本地存储
- 如果要从-pem 上传 vhd：已[为 Azure 准备](../windows/prepare-for-upload-vhd-image.md)的、本地存储的 vhd。
- 如果要执行复制操作，请使用 Azure 中的托管磁盘。

## <a name="create-an-empty-managed-disk"></a>创建一个空托管磁盘

若要将 vhd 上传到 Azure，需要创建一个为此上传过程配置的空托管磁盘。 创建前，还应了解有关这些磁盘的一些其他信息。

这种托管磁盘具有两种独特的状态：

- ReadToUpload，这意味着磁盘已准备好接收上载，但未生成[安全访问签名](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)（SAS）。
- ActiveUpload，这意味着磁盘已准备好接收上传并且已生成 SAS。

无论在哪种状态中，托管磁盘都将按[标准 HDD 定价](https://azure.microsoft.com/pricing/details/managed-disks/)计费，而不考虑实际的磁盘类型。 例如，P10 将按 S10 计费。 在托管磁盘上调用`revoke-access`之前，此值为 true，这是将磁盘附加到虚拟机所必需的。

在创建用于上传的空标准 HDD 之前，需要上载要上传的 vhd 的文件大小（以字节为单位）。 为此，可以使用 `wc -c <yourFileName>.vhd` 或 `ls -al <yourFileName>.vhd`。 此值在指定 **--上传大小-字节**参数时使用。

通过在[disk create](/cli/azure/disk#az-disk-create) cmdlet 中指定 **--for 上传**参数和 **--上传大小-字节**参数，创建一个空的标准 HDD 以进行上传：

```bash
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

如果要上传高级 SSD 或标准 SSD，请将**standard_lrs**替换为**premium_LRS**或**standardssd_lrs**。 超级 SSD 尚不受支持。

你现在已创建了一个为上传过程配置的空托管磁盘。 若要将 vhd 上传到磁盘，则需要一个可写 SAS，以便将其作为上传的目标进行引用。

若要生成空托管磁盘的可写 SAS，请使用以下命令：

```bash
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

示例返回值：

```
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>上传 vhd

现在，你有了用于空托管磁盘的 SAS，可以使用它将托管磁盘设置为上传命令的目标。

通过指定生成的 SAS URI，使用 AzCopy v10 将本地 VHD 文件上传到托管磁盘。

此上传与等效的[标准 HDD](disks-types.md#standard-hdd)具有相同的吞吐量。 例如，如果大小等于 S4，则吞吐量最高可达 60 MiB/s。 但是，如果大小等于 S70，则吞吐量最高可达 500 MiB/秒。

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

如果你的 sas 在上传过程中过期， `revoke-access`但你尚未调用，则可以通过再次使用`grant-access`来获取新的 sas 以继续上传。

上传完成后，不再需要将任何数据写入磁盘，请撤销 SAS。 吊销 SAS 将更改托管磁盘的状态，并允许你将磁盘附加到 VM。

```bash
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="copy-a-managed-disk"></a>复制托管磁盘

直接上传还可以简化复制托管磁盘的过程。 可以在同一区域或跨区域（到其他区域）中进行复制。

下面的脚本将为你执行此操作，此过程类似于前面所述的步骤，因为你使用的是现有磁盘。

> [!IMPORTANT]
> 如果要提供 Azure 中托管磁盘的磁盘大小（以字节为单位），则需要添加512的偏移量。 这是因为在返回磁盘大小时，Azure 会省略页脚。 如果不执行此操作，该副本将失败。 以下脚本已为你执行此功能。

使用你的值替换 `<sourceResourceGroupHere>`，`<sourceDiskNameHere>`，`<targetDiskNameHere>`，`<targetResourceGroupHere>`，@no__t 并使用 uswest2 （位置值的示例为 ""），然后运行以下脚本，以便复制托管磁盘。

```bash
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[uniqueId]' -o tsv)

az disk create -n $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n <sourceDiskNameHere> -g $sourceRG --duration-in-seconds 86400 --query [acessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>后续步骤

现在已成功将 vhd 上传到托管磁盘，可以将磁盘附加到 VM 并开始使用它。

若要了解如何将磁盘附加到 VM，请参阅主题的文章：[将磁盘添加到 LINUX VM](add-disk.md)。
