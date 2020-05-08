---
title: 使用 Azure CLI 从一般化映像创建 VM
description: 使用 Azure CLI 从通用化映像版本创建 VM。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.openlocfilehash: 5e59872a4da0136232652008a2980601428eeab6
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796780"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-cli"></a>使用 CLI 从通用化映像版本创建 VM

使用共享映像库中存储的[通用映像版本](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images)创建 VM。 如果要使用专用映像创建 VM，请参阅[从专用映像创建 vm](vm-specialized-image-version-powershell.md)。 


## <a name="get-the-image-id"></a>获取映像 ID

使用[az sig image definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list)列出库中的图像定义，查看定义的名称和 ID。

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>创建 VM

运行 [az vm create](/cli/azure/vm#az-vm-create) 创建 VM。 若要使用最新版本的映像，请`--image`将设置为映像定义的 ID。 

在此示例中，请根据需要替换资源名称。 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

也可以通过使用 `--image` 参数的映像版本 ID 来使用特定版本。 例如，若要使用映像版本 *1.0.0*，请键入：`--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`。

## <a name="next-steps"></a>后续步骤

[Azure 映像生成器（预览版）](./linux/image-builder-overview.md)可帮助自动创建映像版本，甚至还可以使用它来更新[现有映像版本并创建新的映像版本](./linux/image-builder-gallery-update-image-version.md)。 
