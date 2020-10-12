---
title: PowerShell-从共享映像库中的快照或托管磁盘创建映像
description: 了解如何使用 PowerShell 从共享映像库中的快照或托管磁盘创建映像。
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 2ebff0d86c27bcdbc11d23e18116b33b4ea838a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89300249"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-powershell"></a>使用 PowerShell 从托管磁盘或共享映像库中的快照创建映像

如果你有想要迁移到共享映像库中的现有快照或托管磁盘，则可以直接从托管磁盘或快照创建共享映像库映像。 测试新映像后，可以删除源托管磁盘或快照。 你还可以使用 [Azure CLI](image-version-snapshot-cli.md)在共享映像库中从托管磁盘或快照创建映像。

映像库中的映像具有两个组件，我们将在此示例中创建这两个组件：
- “映像定义”包含有关映像及其使用要求的信息。 这包括该映像是 Windows 映像还是 Linux 映像、是专用映像还是通用映像，此外还包括发行说明以及最低和最高内存要求。 它是某种映像类型的定义。 
- 使用共享映像库时，将使用映像版本来创建 VM。 可根据环境的需要创建多个映像版本。 创建 VM 时，将使用该映像版本为 VM 创建新磁盘。 可以多次使用映像版本。


## <a name="before-you-begin"></a>准备阶段

若要完成本文，必须拥有快照或托管磁盘。 

如果要包含数据磁盘，则数据磁盘大小不能超过 1 TB。

通过本文进行操作时，请根据需要替换资源名称。


## <a name="get-the-snapshot-or-managed-disk"></a>获取快照或托管磁盘

可以使用 [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) 查看资源组中可用的快照列表。 

```azurepowershell-interactive
get-azsnapshot | Format-Table -Property Name,ResourceGroupName
```

了解快照的名称及其所在资源组后，可以再次使用 `Get-AzSnapshot` 来获取快照对象并将其存储在变量中，供稍后使用。 此示例从“myResourceGroup”资源组获取名为 mySnapshot 的快照，并将其分配给变量 $source 。 

```azurepowershell-interactive
$source = Get-AzSnapshot `
   -SnapshotName mySnapshot `
   -ResourceGroupName myResourceGroup
```

你还可以使用托管磁盘而不是快照。 若要获取托管磁盘，请使用 [AzDisk](/powershell/module/az.compute/get-azdisk)。 

```azurepowershell-interactive
Get-AzDisk | Format-Table -Property Name,ResourceGroupName
```

然后获取托管磁盘并将其分配给 `$source` 变量。

```azurepowershell-interactive
$source = Get-AzDisk `
   -Name myDisk
   -ResourceGroupName myResourceGroup
```

可以使用相同的 cmdlet 获取要包含在映像中的任何数据磁盘。 将其分配给变量，稍后在创建映像版本时使用这些变量。


## <a name="get-the-gallery"></a>获取库

可以按名称列出所有库和映像定义。 结果采用 `gallery\image definition\image version` 格式。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

找到正确的库后，创建一个变量供稍后使用。 此示例获取 *myResourceGroup* 资源组中名为 *myGallery* 的库。

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>创建映像定义 

映像定义为映像创建一个逻辑分组。 它们用于管理有关映像的信息。 映像定义名称可能包含大写或小写字母、数字、点、短划线和句点。 

制作映像定义时，请确保它具有所有正确信息。 在此示例中，我们假设快照或托管磁盘来自正在使用的 VM，并且尚未通用化。 如果在运行适用于 Windows 或 [waagent](https://github.com/Azure/WALinuxAgent)或) Linux 的 Sysprep 后，托管磁盘或快照已被通用化 OS 占用 (， `-deprovision` `-deprovision+user` 请将更改 `-OsState` 为 `generalized` 。 

有关可为映像定义指定的值的详细信息，请参阅[映像定义](./windows/shared-image-galleries.md#image-definitions)。

使用 [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) 创建映像定义。 在此示例中，映像定义名为 myImageDefinition，适用于通用化的 Windows OS。 若要使用 Linux OS 创建映像的定义，请使用 `-OsType Linux`。 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

### <a name="purchase-plan-information"></a>购买计划信息

在某些情况下，当从基于 Azure 市场映像的映像创建 VM 时，需要传入购买计划信息。 在这些情况下，我们建议你在映像定义中包含购买计划信息。 在这种情况下，请参阅[在创建映像时提供 Azure 市场购买计划信息](marketplace-images.md)。


## <a name="create-an-image-version"></a>创建映像版本

使用 [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) 从快照创建映像版本。 

允许用于映像版本的字符为数字和句点。 数字必须在 32 位整数范围内。 格式：MajorVersion.MinorVersion.Patch  。

如果希望映像包含数据磁盘，则除了操作系统磁盘之外，还需要添加 `-DataDiskImage` 参数，并将其设置为数据磁盘快照或托管磁盘的 ID。

在此示例中，映像版本为 1.0.0，该版本被复制到美国中西部和美国中南部数据中心******。 选择复制的目标区域时，请记住，你还需包括源区域作为复制的目标。


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -OSDiskImage @{Source = @{Id=$source.Id}; HostCaching = "ReadOnly" } `
   -PublishingProfileEndOfLifeDate '2025-01-01' `
   -asJob 
```

可能需要一段时间才能将该映像复制到所有目标区域，因此我们创建了作业，以便可以跟踪进度。 要查看作业的进度，请键入 `$job.State`。

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> 需等待映像版本完全生成并复制完毕，然后才能使用同一快照来创建另一映像版本。 
>
> 也可在[区域冗余存储](../storage/common/storage-redundancy.md)中存储映像版本，只需在创建映像版本时添加 `-StorageAccountType Standard_ZRS` 即可。
>

## <a name="delete-the-source"></a>删除源

验证新的映像版本是否正常工作后，可以使用 [Remove-AzSnapshot](/powershell/module/Az.Compute/Remove-AzSnapshot) 或 [Remove-AzDisk](/powershell/module/az.compute/remove-azdisk) 删除映像的源。


## <a name="next-steps"></a>后续步骤

确认复制完成后，可以从[专用映像](vm-specialized-image-version-powershell.md)创建 VM。

有关如何提供购买计划信息的信息，请参阅[在创建映像时提供 Azure 市场购买计划信息](marketplace-images.md)。
