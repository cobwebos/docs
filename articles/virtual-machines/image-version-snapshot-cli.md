---
title: CLI-从共享映像库中的快照或 VHD 创建映像
description: 了解如何使用 Azure CLI 从共享映像库中的快照或 VHD 创建映像。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 585d3729a886f3a01dff6dcd9afdab63669c05b5
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225067"
---
# <a name="create-an-image-from-a-vhd-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>使用 Azure CLI 从共享映像库中的 VHD 或快照创建映像

如果你有想要迁移到共享映像库中的现有快照或 VHD，则可以直接从 VHD 或快照创建共享映像库映像。 测试新映像后，可以删除源 VHD 或快照。 你还可以使用[Azure PowerShell](image-version-snapshot-powershell.md)从共享映像库中的 VHD 或快照创建映像。

映像库中的映像具有两个组件，我们将在此示例中创建这两个组件：
- “映像定义”包含有关映像及其使用要求的信息。 这包括该映像是 Windows 映像还是 Linux 映像、是专用映像还是通用映像，此外还包括发行说明以及最低和最高内存要求。 它是某种映像类型的定义。 
- 使用共享映像库时，将使用映像版本来创建 VM。 可根据环境的需要创建多个映像版本。 创建 VM 时，将使用该映像版本为 VM 创建新磁盘。 可以多次使用映像版本。


## <a name="before-you-begin"></a>准备阶段

若要完成本文，必须拥有快照或 VHD。 

如果要包含数据磁盘，数据磁盘大小不能超过 1 TB。

通过本文进行操作时，请根据需要替换资源名称。

## <a name="find-the-snapshot-or-vhd"></a>查找快照或 VHD 

可以在资源组中使用[az snapshot list](/cli/azure/snapshot#az-snapshot-list)查看可用快照的列表。 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

你还可以使用 VHD 而不是快照。 若要获取 VHD，请使用[az disk list](/cli/azure/disk#az-disk-list)。 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

获得快照或 VHD 的 ID 后，将其分配给名为的变量， `$source` 以备稍后使用。

可以使用相同的过程获取要包含在映像中的任何数据磁盘。 将其分配给变量，稍后在创建映像版本时使用这些变量。


## <a name="find-the-gallery"></a>查找库

若要创建映像定义，你将需要有关映像库的信息。

使用[az sig list](/cli/azure/sig#az-sig-list)列出有关可用图像库的信息。 请注意，库的名称将在以后使用库的资源组。

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>创建映像定义

映像定义为映像创建一个逻辑分组。 它们用于管理有关映像的信息。 映像定义名称可能包含大写或小写字母、数字、点、短划线和句点。 

制作映像定义时，请确保它具有所有正确信息。 在此示例中，我们假设快照或 VHD 来自正在使用的 VM，并且尚未通用化。 如果在运行适用于 Windows 的 Sysprep 或[waagent](https://github.com/Azure/WALinuxAgent)或 Linux 之后，在运行 Windows Sysprep 或 Linux) 的情况后，为通用 (OS 创建了 VHD 或快照， `-deprovision` `-deprovision+user` 请将更改 `-OsState` 为 `generalized` 。 

若要详细了解可为映像定义指定的值，请参阅[映像定义](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)。

使用 [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create) 在库中创建一个映像定义。

在此示例中，映像定义名为 myImageDefinition，适用于[专用化](./linux/shared-image-galleries.md#generalized-and-specialized-images) Linux OS 映像。 若要使用 Windows OS 创建映像的定义，请使用 `--os-type Windows`。 

在此示例中，库名为*myGallery*，位于*myGalleryRG*资源组中，映像定义名称将为*mImageDefinition*。

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>创建映像版本

使用[az image 画廊 create-image 版本](/cli/azure/sig/image-version#az-sig-image-version-create)创建映像版本。 

允许用于映像版本的字符为数字和句点。 数字必须在 32 位整数范围内。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

在此示例中，我们的映像的版本为*1.0.0* ，我们将在*美国中南部*地区创建1个副本，并使用区域冗余存储在*美国东部 2*区域中创建1个副本。 为复制选择目标区域时，请记住，还必须将 VHD 或快照的*源*区域包含为复制目标。

在参数中传递快照或 VHD 的 ID `--os-snapshot` 。


```azurecli-interactive 
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --os-snapshot $source
```

如果要在映像中包含数据磁盘，则需要将 `--data-snapshot-luns` 参数设置为 LUN 编号，将 `--data-snapshots` 设置为数据磁盘或快照的 ID。

> [!NOTE]
> 需等待映像版本彻底生成并复制完毕，然后才能使用同一托管映像来创建另一映像版本。
>
> 你还可以通过在创建映像版本时添加来将所有映像版本副本存储在[区域冗余存储](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)中 `--storage-account-type standard_zrs` 。
>

## <a name="next-steps"></a>后续步骤

从[专用映像版本](vm-specialized-image-version-cli.md)创建 VM。

有关如何提供购买计划信息的信息，请参阅[创建映像时提供 Azure Marketplace 购买计划信息](marketplace-images.md)。