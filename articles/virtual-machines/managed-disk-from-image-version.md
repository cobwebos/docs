---
title: 从映像版本创建托管磁盘
description: 使用共享映像库中的映像版本创建托管磁盘。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/06/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: bf4a1feb91a1ac4b0bca0d6afdbac41a8be3aa4f
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92049652"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>从映像版本创建托管磁盘

如果需要，可以从存储在共享映像库中的映像版本创建托管磁盘。


## <a name="cli"></a>CLI

将 `source` 变量设置为映像版本的 ID，然后使用 [az disk create](/cli/azure/disk.md#az_disk_create) 创建托管磁盘。 


你可以使用 [az sig image 版本列表](/cli/azure/sig/image-version.md#az_sig_image_version_list)查看列表图像版本。 在此示例中，我们将查找属于*myGallery*映像库中的*myImageDefinition*映像定义的所有映像版本。

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```


在此示例中，我们将在名为*myResourceGroup*的资源组中创建名为*myManagedDisk**的托管*磁盘。 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDisk --gallery-image-reference $source 
```

如果磁盘是数据磁盘，请添加 `--gallery-image-reference-lun` 以指定 LUN。

## <a name="powershell"></a>PowerShell

可以使用 [AzResource](/powershell/module/az.resources/get-azresource)列出所有映像版本。 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```



获取所需的所有信息后，可以使用 [AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) 获取要使用的源映像版本，并将其分配给变量。 在本示例中，我们将在 `myResourceGroup` 资源组的 `myGallery` 源库中，获取 `myImageDefinition` 定义的 `1.0.0` 映像版本。

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

为磁盘信息设置一些变量。

```azurepowershell-interactive
$location = "East US"
$resourceGroup = "myResourceGroup"
$diskName = "myDisk"
```

使用源映像版本 ID 创建磁盘配置和磁盘。 对于 `-GalleryImageReference` ，仅当源为数据磁盘时，才需要 LUN。

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location $location `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=1}
```

创建该磁盘。

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName $resourceGroup `
   -DiskName $diskName
```

## <a name="next-steps"></a>后续步骤

你还可以使用 [Azure CLI](image-version-managed-image-cli.md) 或 [PowerShell](image-version-managed-image-powershell.md)从托管磁盘创建映像版本。


