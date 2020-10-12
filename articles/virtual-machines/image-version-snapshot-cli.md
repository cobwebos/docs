---
title: CLI-从共享映像库中的快照或托管磁盘创建映像
description: 了解如何使用 Azure CLI 从共享映像库中的快照或托管磁盘创建映像。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: e694630d8bcd7879d9405152c4141fb6e5bad4e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89297087"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>使用 Azure CLI 在共享映像库中从托管磁盘或快照创建映像

如果你有想要迁移到共享映像库中的现有快照或托管磁盘，则可以直接从托管磁盘或快照创建共享映像库映像。 测试新映像后，可以删除源托管磁盘或快照。 你还可以使用 [Azure PowerShell](image-version-snapshot-powershell.md)在共享映像库中从托管磁盘或快照创建映像。

映像库中的映像具有两个组件，我们将在此示例中创建这两个组件：
- “映像定义”包含有关映像及其使用要求的信息。 这包括该映像是 Windows 映像还是 Linux 映像、是专用映像还是通用映像，此外还包括发行说明以及最低和最高内存要求。 它是某种映像类型的定义。 
- 使用共享映像库时，将使用映像版本来创建 VM。 可根据环境的需要创建多个映像版本。 创建 VM 时，将使用该映像版本为 VM 创建新磁盘。 可以多次使用映像版本。


## <a name="before-you-begin"></a>准备阶段

若要完成本文，必须拥有快照或托管磁盘。 

如果要包含数据磁盘，则数据磁盘大小不能超过 1 TB。

通过本文进行操作时，请根据需要替换资源名称。

## <a name="find-the-snapshot-or-managed-disk"></a>查找快照或托管磁盘 

可以使用 [az snapshot list](/cli/azure/snapshot#az-snapshot-list) 查看资源组中可用的快照列表。 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

你还可以使用托管磁盘而不是快照。 若要获取托管磁盘，请使用 [az disk list](/cli/azure/disk#az-disk-list)。 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

一旦获得快照或托管磁盘的 ID，并将其分配给名为的变量， `$source` 以后再使用。

可以使用相同的过程获取要包含在映像中的任何数据磁盘。 将其分配给变量，稍后在创建映像版本时会使用这些变量。


## <a name="find-the-gallery"></a>查找库

若要创建映像定义，需要有关映像库的信息。

使用 [az sig list](/cli/azure/sig#az-sig-list) 列出有关可用映像库的信息。 请记下库的名称及库所在的资源组，以供以后使用。

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>创建映像定义

映像定义为映像创建一个逻辑分组。 它们用于管理有关映像的信息。 映像定义名称可能包含大写或小写字母、数字、点、短划线和句点。 

创建映像定义时，请确保它包含所有正确信息。 在此示例中，我们假设快照或托管磁盘来自正在使用的 VM，并且尚未通用化。 如果在运行适用于 Windows 或 [waagent](https://github.com/Azure/WALinuxAgent)或) Linux 的 Sysprep 后，托管磁盘或快照已被通用化 OS 占用 (， `-deprovision` `-deprovision+user` 请将更改 `-OsState` 为 `generalized` 。 

若要详细了解可为映像定义指定的值，请参阅[映像定义](./linux/shared-image-galleries.md#image-definitions)。

使用 [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create) 在库中创建一个映像定义。

在此示例中，映像定义名为 myImageDefinition，适用于[专用化](./linux/shared-image-galleries.md#generalized-and-specialized-images) Linux OS 映像。 若要使用 Windows OS 创建映像的定义，请使用 `--os-type Windows`。 

在此示例中，库名为 myGallery，它位于 myGalleryRG 资源组中，映像定义名称将为 mImageDefinition 。

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

使用 [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) 创建映像版本。 

允许用于映像版本的字符为数字和句点。 数字必须在 32 位整数范围内。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

在此示例中，我们的映像的版本为 *1.0.0* ，我们将在 *美国中南部* 地区创建1个副本，并使用区域冗余存储在 *美国东部 2* 区域中创建1个副本。 为复制选择目标区域时，请记住，还必须将托管磁盘或快照的 *源* 区域包含为复制目标。

在参数中传递快照或托管磁盘的 ID `--os-snapshot` 。


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

如果要在映像中包含数据磁盘，则需要同时包含设置为 LUN 编号的 `--data-snapshot-luns` 参数和设置为数据磁盘或快照 ID 的 `--data-snapshots` 参数。

> [!NOTE]
> 需等待映像版本彻底生成并复制完毕，然后才能使用同一托管映像来创建另一映像版本。
>
> 你还可以通过在创建映像版本时添加来将所有映像版本副本存储在 [区域冗余存储](../storage/common/storage-redundancy.md) 中 `--storage-account-type standard_zrs` 。
>

## <a name="next-steps"></a>后续步骤

从[专用映像版本](vm-specialized-image-version-cli.md)创建 VM。

若要了解如何提供购买计划信息，请参阅[创建映像时提供 Azure 市场购买计划信息](marketplace-images.md)。
