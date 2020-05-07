---
title: 从 VM 创建映像（预览）
description: 了解如何使用 Azure PowerShell 在 Azure 中的现有 VM 上创建共享映像库中的映像。
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 454ed810f950924d3dd790a2442fe29816bf940d
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838461"
---
# <a name="preview-create-an-image-from-a-vm"></a>预览：从 VM 创建映像

如果你有一个要用于生成多个相同 Vm 的现有 VM，则可以使用该 VM 在使用 Azure PowerShell 的共享映像库中创建映像。 还可以使用[Azure CLI](image-version-vm-cli.md)从 VM 创建映像。

你可以使用 Azure PowerShell 从[专用和一般化](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#generalized-and-specialized-images)vm 捕获映像。 

图像库中的图像具有两个组件，我们将在此示例中创建此组件：
- **映像定义**携带有关映像的信息以及使用该映像的要求。 这包括映像是 Windows 还是 Linux、专用或通用化、发行说明以及最小和最大内存要求。 它是某种映像类型的定义。 
- 使用共享映像库时，**映像版本**用于创建 VM。 可根据环境的需要创建多个映像版本。 创建 VM 时，映像版本用于为 VM 创建新磁盘。 可以多次使用映像版本。


## <a name="before-you-begin"></a>准备阶段

若要完成本文，必须拥有现有的共享映像库，以及 Azure 中的现有 VM 以用作源。 

如果 VM 附加了数据磁盘，则数据磁盘大小不能超过 1 TB。

完成本文后，请在需要时替换资源名称。


## <a name="get-the-gallery"></a>获取库

可以按名称列出所有库和图像定义。 结果采用格式`gallery\image definition\image version`。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

找到正确的库和图像定义后，创建变量供以后使用。 此示例在*myResourceGroup*资源组中获取名为*myGallery*的库。

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```

## <a name="get-the-vm"></a>获取 VM

可以使用[new-azvm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm)在资源组中查看可用 vm 的列表。 知道 VM 名称及其所在的资源组后，可以再次使用`Get-AzVM`来获取 vm 对象，并将其存储在变量中以供以后使用。 此示例从 "myResourceGroup" 资源组中获取名为*sourceVM*的 VM，并将其分配给变量 *$sourceVm*。 

```azurepowershell-interactive
$sourceVm = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

最佳做法是在使用[new-azvm](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm)创建映像之前 stop\deallocate VM。

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName $sourceVm.ResourceGroupName `
   -Name $sourceVm.Name `
   -Force
```

## <a name="create-an-image-definition"></a>创建映像定义 

映像定义为映像创建一个逻辑分组。 它们用于管理有关映像的信息。 映像定义名称可能包含大写或小写字母、数字、点、短划线和句点。 

在制作映像定义时，请确保具有所有正确的信息。 如果已通用化 VM （使用适用于 Windows 的 Sysprep 或适用于 Linux 的 waagent 预配），则应使用`-OsState generalized`创建映像定义。 如果未通用化 VM，请使用`-OsState specialized`创建映像定义。

若要详细了解可以为映像定义指定的值，请参阅[映像定义](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)。

使用 [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) 创建映像定义。 

在此示例中，映像定义的名称为*myImageDefinition*，适用于运行 Windows 的专用 VM。 若要使用 Linux 为映像创建定义，请`-OsType Linux`使用。 

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


## <a name="create-an-image-version"></a>创建映像版本

使用[AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)创建映像版本。 

允许用于映像版本的字符为数字和句点。 数字必须在 32 位整数范围内。 格式： *MajorVersion*。*MinorVersion*。*修补程序*。

在此示例中，映像版本为 1.0.0，该版本被复制到美国中西部和美国中南部数据中心******。 选择复制的目标区域时，请记住，你还需包括源区域作为复制的目标。**

若要从 VM 创建映像版本，请将`$vm.Id.ToString()`用于`-Source`。

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -Source $sourceVm.Id.ToString() `
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
> 你还可以通过添加`-StorageAccountType Premium_LRS`或[区域冗余存储](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)在 Premiun 存储中存储映像，方法是在`-StorageAccountType Standard_ZRS`创建映像版本时添加。
>

## <a name="next-steps"></a>后续步骤

验证新映像版本是否正常工作后，可以创建 VM。 从[专用映像版本](vm-specialized-image-version-powershell.md)或[通用化映像版本](vm-generalized-image-version-powershell.md)创建 VM。
