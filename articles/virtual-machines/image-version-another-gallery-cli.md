---
title: 复制另一个库中的映像版本
description: 使用 Azure CLI 复制另一个库中的映像版本。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: add08d7b8ef39322f03e0faf78959b08a6ae2a14
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82797053"
---
# <a name="copy-an-image-from-another-gallery"></a>复制另一个库中的图像

如果组织中有多个库，还可以从存储在其他库中的现有映像版本创建映像版本。 例如，你可能有一个开发和测试库，用于创建和测试新映像。 如果已准备好在生产环境中使用，则可以使用此示例将它们复制到生产库中。 还可以使用[Azure PowerShell](image-version-another-gallery-powershell.md)从另一个库中的映像创建图像。



## <a name="before-you-begin"></a>开始之前

若要完成本文，必须拥有现有的源库、映像定义和映像版本。 还应具有目标库。 

必须将源映像版本复制到目标库所在的区域。 

完成本文后，请在需要时替换资源名称。



## <a name="get-information-from-the-source-gallery"></a>从源库中获取信息

你将需要源映像定义中的信息，以便可以在新库中创建它的副本。

列出有关可用图像库的信息使用[az sig list](/cli/azure/sig#az-sig-list)查找有关源库的信息。

```azurecli-interactive 
az sig list -o table
```

使用[az sig image definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list)列出库中的映像定义。 在此示例中，我们将在*myGalleryRG*资源组中的名为*myGallery*的库中搜索图像定义。

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

使用[az sig 映像文件](/cli/azure/sig/image-version#az-sig-image-version-list)列表查找要复制到新库中的映像版本，列出库中图像的版本。 在此示例中，我们将查找属于*myImageDefinition*映像定义的所有映像版本。

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

获得所需的所有信息后，可以使用[az sig image 版本 show](/cli/azure/sig/image-version#az-sig-image-version-show)获取源映像版本的 ID。

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>创建映像定义 

需要创建与源映像版本的映像定义匹配的映像定义。 你可以使用[az sig image definition show](/cli/azure/sig/image-definition#az-sig-image-definition-show)查看在新库中重新创建映像定义所需的所有信息。

```azurecli-interactive
az sig image-definition show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
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
  "osType": "Linux",
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

使用上面的输出中的信息，在新库中创建新的映像定义。


```azurecli-interactive 
az sig image-definition create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --hyper-v-generation V1 \
   --os-state specialized 
```


## <a name="create-the-image-version"></a>创建映像版本

使用[az image 画廊 create-version](/cli/azure/sig/image-version#az-sig-image-version-create)创建版本。 你需要传入托管映像的 ID 以作为创建映像版本时要使用的基线。 可以使用 [az image list](/cli/azure/image?view#az-image-list) 获取资源组中的映像的相关信息。 

允许用于映像版本的字符为数字和句点。 数字必须在 32 位整数范围内。 格式： *MajorVersion*。*MinorVersion*。*修补程序*。

在此示例中，我们的映像的版本为*1.0.0* ，我们将在*美国中南部*地区创建1个副本，并使用区域冗余存储在*美国东部*区域中创建1个副本。


```azurecli-interactive 
az sig image-version create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"
```

> [!NOTE]
> 需等待映像版本彻底生成并复制完毕，然后才能使用同一托管映像来创建另一映像版本。
>
> 你还可以通过添加`--storage-account-type  premium_lrs`或[区域冗余存储](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)在 Premiun 存储中存储映像，方法是在`--storage-account-type  standard_zrs`创建映像版本时添加。
>

## <a name="next-steps"></a>后续步骤

使用[通用化](vm-generalized-image-version-cli.md)或[专用](vm-specialized-image-version-cli.md)映像版本创建 VM。

此外，试用[Azure 映像生成器（预览版）](./linux/image-builder-overview.md)可帮助自动创建映像版本，甚至还可以使用它来更新[现有映像版本并创建新的映像版本](./linux/image-builder-gallery-update-image-version.md)。 