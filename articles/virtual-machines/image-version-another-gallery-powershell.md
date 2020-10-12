---
title: 使用 PowerShell 复制另一个库中的映像
description: 使用 Azure PowerShell 复制另一个库中的映像。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 2f26716a4d04b574bf393f502758a725948419da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89299943"
---
# <a name="copy-an-image-from-another-gallery-using-powershell"></a>使用 PowerShell 复制另一个库中的映像

如果你的组织有多个库，可以从其他库中存储的映像创建映像。 例如，你可能会使用一个开发和测试库用于创建和测试新映像。 准备好在生产环境中使用这些映像时，可以遵循本示例将它们复制到生产库中。 也可以使用 [Azure CLI](image-version-another-gallery-cli.md) 从另一个库中的映像创建映像。


## <a name="before-you-begin"></a>准备阶段

若要完成本文，必须有一个现有的源库、映像定义和映像版本。 此外，还应有一个目标库。 

必须将源映像版本复制到目标库所在的区域。 

我们将在目标库中创建新的映像定义和映像版本。


通过本文进行操作时，请根据需要替换资源名称。


## <a name="get-the-source-image"></a>获取源映像 

需要源映像定义中的信息，以便可以在目标库中创建该映像的副本。

使用 [Get-AzResource](/powershell/module/az.resources/get-azresource) cmdlet 列出有关现有库、映像定义和映像版本的信息。

结果采用 `gallery\image definition\image version` 格式。

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceGroupName
```

获取全部所需信息后，可以使用 [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) 获取源映像版本的 ID。 在本示例中，我们将在 `myResourceGroup` 资源组的 `myGallery` 源库中，获取 `myImageDefinition` 定义的 `1.0.0` 映像版本。

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```


## <a name="create-the-image-definition"></a>创建映像定义 

需要创建一个与源的映像定义相匹配的新映像定义。 可以使用 [Get-AzGalleryImageDefinition](/powershell/module/az.compute/get-azgalleryimagedefinition) 查看重新创建映像定义所需的全部信息。

```azurepowershell-interactive
Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition
```


输出将如下所示：

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Windows",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

使用 [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) cmdlet 以及上述输出中的信息，在目标库中创建新的映像定义。


在本示例中，映像定义在名为 *myDestinationGallery* 的库中命名为 *myDestinationImgDef*。


```azurepowershell-interactive
$destinationImgDef  = New-AzGalleryImageDefinition `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -Name 'myDestinationImgDef' `
   -OsState specialized `
   -OsType Windows `
   -HyperVGeneration v1 `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-the-image-version"></a>创建映像版本

使用 [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) 创建映像版本。 需要在 `--managed-image` 参数中传递源映像的 ID，用于在目标库中创建映像版本。 

允许用于映像版本的字符为数字和句点。 数字必须在 32 位整数范围内。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

在此示例中，目标库名为*myDestinationGallery*，位于*美国西部*位置的*myDestinationRG*资源组中。 我们的映像版本为 *1.0.0* ，我们将在 *美国中南部* 地区创建1个副本，在 *美国西部* 区域创建2个副本。 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $destinationImgDef.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -TargetRegion $targetRegions  `
   -Source $sourceImgVer.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -asJob 
```

可能需要一段时间才能将该映像复制到所有目标区域，因此我们创建了作业，以便可以跟踪进度。 要查看作业的进度，请键入 `$job.State`。

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> 需等待映像版本彻底生成并复制完毕，然后才能使用同一托管映像来创建另一映像版本。
>
> 创建映像版本时，还可以通过添加 `-StorageAccountType Premium_LRS` 在高级存储中存储映像，或者通过添加 `-StorageAccountType Standard_ZRS` 在[区域冗余存储](../storage/common/storage-redundancy.md)中存储映像。
>


## <a name="next-steps"></a>后续步骤

从[通用化](vm-generalized-image-version-powershell.md)或[专用化](vm-specialized-image-version-powershell.md)映像版本创建 VM。

[Azure 映像生成器 (预览版) ](./linux/image-builder-overview.md) 可帮助自动创建映像版本，甚至还可以使用它来更新 [现有映像版本并创建新的映像版本](./linux/image-builder-gallery-update-image-version.md)。 

若要了解如何提供购买计划信息，请参阅[创建映像时提供 Azure 市场购买计划信息](marketplace-images.md)。
