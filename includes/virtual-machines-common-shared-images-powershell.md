---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 12/10/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 91889971e1ab8a9ea8341f6bc57735d973ea0e89
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58114931"
---
## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。 也可以通过转到 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) 在单独的浏览器标签页中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。


## <a name="preview-register-the-feature"></a>预览版：注册功能

共享映像库当前为预览版，但需要先注册此功能，然后才能使用它。 若要注册共享映像库功能，请使用以下命令：

```azurepowershell-interactive
Register-AzProviderFeature `
   -FeatureName GalleryPreview `
   -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="get-the-managed-image"></a>获取托管映像

可使用 [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage) 查看资源组中可用的映像列表。 了解映像名称及其所在的资源组后，就可以再次使用 `Get-AzImage` 来获取映像对象并将其存储在变量中以便以后使用。 此示例将从“myResourceGroup”资源组获取名为“myImage”的映像，并将其分配给变量“$managedImage”。 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>创建映像库 

映像库是用于启用映像共享的主要资源。 库名称在你的订阅中必须唯一。 使用 [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery) 创建映像库。 以下示例在“myGalleryRG”资源组中创建名为“myGallery”的库。

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   
## <a name="create-an-image-definition"></a>创建映像定义 

使用 [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) 创建库映像定义。 在此示例中，库映像名为 myGalleryImage。

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```
### <a name="using-publisher-offer-and-sku"></a>使用发布者、 产品/服务和 SKU 
实现共享的映像，计划的客户**即将推出的版本**，你将能够使用您个人定义 **-发布者**， **-提供**和 **-Sku**值查找和指定图像定义，然后使用最新版本从匹配的映像创建 VM 映像定义。 例如，以下是三个映像定义及其值：

|映像定义|发布者|产品/服务|SKU|
|---|---|---|---|
|myImage1|myPublisher|myOffer|mySku|
|myImage2|myPublisher|standardOffer|mySku|
|myImage3|测试|standardOffer|testSku|

所有这三个映像都有唯一的一组值。 可以拥有共享一个或两个但不是全部三个值的映像版本。 **即将推出的版本**，可以将这些值合并以请求特定映像的最新版本。 **在当前版本中不起作用**，但将在将来提供。 发布时，使用以下语法用于设置为源映像*myImage1*从上表。

```powershell
$vmConfig = Set-AzVMSourceImage `
   -VM $vmConfig `
   -PublisherName myPublisher `
   -Offer myOffer `
   -Skus mySku 
```

它类似于如何目前指定使用发布者、 产品/服务和 SKU [Azure Marketplace 映像](../articles/virtual-machines/windows/cli-ps-findimage.md)若要获取 Marketplace 映像的最新版本。 请记住这一点，每个映像定义都需要具有一组唯一的这些值。  

## <a name="create-an-image-version"></a>创建映像版本

使用 [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) 根据托管映像创建映像版本。 在此示例中，映像版本为 1.0.0，该版本被复制到美国中西部和美国中南部数据中心。


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-01-01' `
   -asJob 
```

可能需要一段时间才能将该映像复制到所有目标区域，因此我们创建了作业，以便可以跟踪进度。 要查看作业的进度，请键入 `$job.State`。

```azurepowershell-interactive
$job.State
```

