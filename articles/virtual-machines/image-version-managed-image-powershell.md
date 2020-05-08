---
title: 将托管映像迁移到共享映像库
description: 了解如何使用 Azure PowerShell 将托管映像迁移到共享映像库中的映像版本。
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: e00538d1112492c5b7f9fc0f91c86df6d3500701
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796585"
---
# <a name="migrate-from-a-managed-image-to-a-shared-image-gallery-image"></a>从托管映像迁移到共享映像库映像

如果你有想要迁移到共享映像库中的现有托管映像，可以直接从托管映像创建共享映像库映像。 测试新映像后，可以删除源托管映像。 你还可以使用[Azure CLI](image-version-managed-image-cli.md)从托管映像迁移到共享映像库。

图像库中的图像具有两个组件，我们将在此示例中创建此组件：
- **映像定义**携带有关映像的信息以及使用该映像的要求。 这包括映像是 Windows 还是 Linux、专用或通用化、发行说明以及最小和最大内存要求。 它是某种映像类型的定义。 
- 使用共享映像库时，**映像版本**用于创建 VM。 可根据环境的需要创建多个映像版本。 创建 VM 时，映像版本用于为 VM 创建新磁盘。 可以多次使用映像版本。


## <a name="before-you-begin"></a>开始之前

若要完成本文，必须有一个现有的托管映像。 如果托管映像包含数据磁盘，则数据磁盘大小不能超过 1 TB。

通过本文进行操作时，请根据需要替换资源组和 VM 名称。

## <a name="get-the-gallery"></a>获取库

可以按名称列出所有库和图像定义。 结果采用格式`gallery\image definition\image version`。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

找到正确的库后，创建一个以后要使用的变量。 此示例在*myResourceGroup*资源组中获取名为*myGallery*的库。

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>创建映像定义 

映像定义为映像创建一个逻辑分组。 它们用于管理有关映像的信息。 映像定义名称可能包含大写或小写字母、数字、点、短划线和句点。 

在制作映像定义时，请确保具有所有正确的信息。 由于托管映像总是通用化，因此应该设置`-OsState generalized`。 

若要详细了解可以为映像定义指定的值，请参阅[映像定义](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)。

使用 [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) 创建映像定义。 在此示例中，映像定义的名称为*myImageDefinition*，适用于通用 Windows OS。 若要使用 Linux 操作系统创建映像的定义，请使用`-OsType Linux`。 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

## <a name="get-the-managed-image"></a>获取托管映像

可使用 [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage) 查看资源组中可用的映像列表。 了解映像名称及其所在的资源组后，就可以再次使用 `Get-AzImage` 来获取映像对象并将其存储在变量中以便以后使用。 此示例将从“myResourceGroup”资源组获取名为“myImage”的映像，并将其分配给变量“$managedImage”****。 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-version"></a>创建映像版本

使用[AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)从托管映像创建映像版本。 

允许用于映像版本的字符为数字和句点。 数字必须在 32 位整数范围内。 格式： *MajorVersion*。*MinorVersion*。*修补程序*。

在此示例中，映像版本为 1.0.0，该版本被复制到美国中西部和美国中南部数据中心******。 选择复制的目标区域时，请记住，你还需包括源区域作为复制的目标。** 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-31' `
   -asJob 
```

可能需要一段时间才能将该映像复制到所有目标区域，因此我们创建了作业，以便可以跟踪进度。 若要查看进度，请`$job.State`键入。

```azurepowershell-interactive
$job.State
```


> [!NOTE]
> 需等待映像版本彻底生成并复制完毕，然后才能使用同一托管映像来创建另一映像版本。 
>
> 你还可以通过添加`-StorageAccountType Premium_LRS`或[区域冗余存储](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)在 Premiun 存储中存储映像，方法是在`-StorageAccountType Standard_ZRS`创建映像版本时添加。
>

## <a name="delete-the-managed-image"></a>删除托管映像

验证新映像版本是否正常工作后，可以删除托管映像。

```azurepowershell-interactive
Remove-AzImage `
   -ImageName $managedImage.Name `
   -ResourceGroupName $managedImage.ResourceGroupName
```

## <a name="next-steps"></a>后续步骤

验证复制完成后，可以从[一般化映像](vm-generalized-image-version-powershell.md)创建 VM。

