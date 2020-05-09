---
title: 使用 Azure CLI 从托管映像迁移到映像版本
description: 了解如何使用 Azure CLI 从托管映像迁移到共享映像库中的映像版本。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 6f49ece874ea52227e6531193fc53b3bea525702
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796598"
---
# <a name="migrate-from-a-managed-image-to-an-image-version-using-the-azure-cli"></a>使用 Azure CLI 从托管映像迁移到映像版本
如果你有想要迁移到共享映像库中的现有托管映像，可以直接从托管映像创建共享映像库映像。 测试新映像后，可以删除源托管映像。 还可以使用[PowerShell](image-version-managed-image-powershell.md)从托管映像迁移到共享映像库。

图像库中的图像具有两个组件，我们将在此示例中创建此组件：
- **映像定义**携带有关映像的信息以及使用该映像的要求。 这包括映像是 Windows 还是 Linux、专用或通用化、发行说明以及最小和最大内存要求。 它是某种映像类型的定义。 
- 使用共享映像库时，**映像版本**用于创建 VM。 可根据环境的需要创建多个映像版本。 创建 VM 时，映像版本用于为 VM 创建新磁盘。 可以多次使用映像版本。


## <a name="before-you-begin"></a>开始之前

若要完成本文，必须拥有现有的[共享映像库](shared-images-cli.md)。 

若要完成本文中的示例，必须具有通用化 VM 的现有托管映像。 有关详细信息，请参阅[捕获托管映像](./linux/capture-image.md)。 如果托管映像包含数据磁盘，则数据磁盘大小不能超过 1 TB。

通过本文进行操作时，请根据需要替换资源组和 VM 名称。



## <a name="create-an-image-definition"></a>创建映像定义

由于托管映像始终是通用化映像，因此你将使用`--os-state generalized`通用映像创建一个映像定义。

映像定义名称可能包含大写或小写字母、数字、点、短划线和句点。 

若要详细了解可以为映像定义指定的值，请参阅[映像定义](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)。

使用[az sig image definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create)在库中创建映像定义。

在此示例中，映像定义的名称为*myImageDefinition*，适用于[通用](./linux/shared-image-galleries.md#generalized-and-specialized-images)Linux OS 映像。 若要使用 Windows OS 创建映像的定义，请使用`--os-type Windows`。 

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
   --os-state generalized
```


## <a name="create-the-image-version"></a>创建映像版本

使用[az image 画廊 create-version](/cli/azure/sig/image-version#az-sig-image-version-create)创建版本。 你需要传入托管映像的 ID 以作为创建映像版本时要使用的基线。 你可以使用[az image list](/cli/azure/image?view#az-image-list)获取映像的 id。 

```azurecli-interactive
az image list --query "[].[name, id]" -o tsv
```

允许用于映像版本的字符为数字和句点。 数字必须在 32 位整数范围内。 格式： *MajorVersion*。*MinorVersion*。*修补程序*。

在此示例中，我们的映像的版本为*1.0.0* ，我们将在*美国中南部*地区创建1个副本，并使用区域冗余存储在*美国东部 2*区域中创建1个副本。 选择复制的目标区域时，请记住，你还需包括源区域作为复制的目标。**

在`--managed-image`参数中传递托管映像的 ID。


```azurecli-interactive 
imageID="/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --managed-image $imageID
```

> [!NOTE]
> 需等待映像版本彻底生成并复制完毕，然后才能使用同一托管映像来创建另一映像版本。
>
> 你还可以通过在创建映像版本时添加`--storage-account-type standard_zrs`来将所有映像版本副本存储在[区域冗余存储](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)中。
>

## <a name="next-steps"></a>后续步骤

使用[通用化映像版本](vm-generalized-image-version-cli.md)创建 VM。