---
title: 使用 Azure CLI 从专用映像版本创建 VM
description: 使用 Azure CLI 的共享映像库中的专用映像版本创建 VM。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 1ccf03deee2a2f72c1eb2008e1acc5bf67d16447
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796767"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>使用具有 Azure CLI 的专用映像版本创建 VM

使用共享映像库中存储的[专用映像版本](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images)创建 VM。 如果要使用通用化映像版本创建 VM，请参阅[从一般化映像版本创建 vm](vm-generalized-image-version-cli.md)。

在此示例中，请根据需要替换资源名称。 

使用[az sig image definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list)列出库中的图像定义，查看定义的名称和 ID。

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

使用[az vm create](/cli/azure/vm#az-vm-create)创建 vm，使用--专门化参数来指示映像是专用映像。 

使用的映像定义 ID `--image`从最新版本的映像创建 VM。 还可以通过为`--image`提供映像版本 ID，从特定版本创建 VM。 

在此示例中，我们从最新版本的*myImageDefinition*映像创建 VM。

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>后续步骤
[Azure 映像生成器（预览版）](./linux/image-builder-overview.md)可帮助自动创建映像版本，甚至还可以使用它来更新[现有映像版本并创建新的映像版本](./linux/image-builder-gallery-update-image-version.md)。 

此外可以使用模板创建共享映像库资源。 提供多个 Azure 快速入门模板： 

- [创建共享映像库](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共享映像库中创建映像定义](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共享映像库中创建映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [从映像版本创建 VM](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


