---
title: include 文件
description: include 文件
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 09/13/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 830deb7569772b610b7e6abde649830b7ad67a92
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47045631"
---
## <a name="before-you-begin"></a>开始之前

若要完成本文中的示例，必须具有通用化 VM 的现有托管映像。 有关详细信息，请参阅[教程：使用 Azure CLI 2.0 创建 Azure VM 的自定义映像](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images)。 

## <a name="preview-register-the-feature"></a>预览版：注册此功能

共享映像库当前为预览版，但需要先注册此功能，然后才能使用它。 若要注册共享映像库功能，请使用以下命令：

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register -n Microsoft.Compute
```

注册此功能可能需要花费几分钟时间。 可以使用以下命令检查进度：

```azurecli-interactive
az provider show -n Microsoft.Compute
```

## <a name="create-an-image-gallery"></a>创建映像库 

映像库是用于启用映像共享的主要资源。 库名称在你的订阅中必须唯一。 使用 [az sig create](/cli/azure/sig#az-sig-create) 创建一个映像库。 以下示例在 *myGalleryRG* 中创建一个名为 *myGallery* 的库。

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create -g myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>创建映像定义

使用 [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create) 在该库中创建一个初始映像定义。

```azurecli-interactive 
az sig image-definition create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```

## <a name="create-an-image-version"></a>创建映像版本 
 
使用 [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) 根据需要创建映像的版本。 你需要传入托管映像的 ID 以作为创建映像版本时要使用的基线。 可以使用 [az image list](/cli/azure/image?view#az-image-list) 获取资源组中的映像的相关信息。 在此示例中，映像的版本为 *1.0.0*，并且我们打算在“美国中西部”、“美国中南部”和“美国东部 2”*区域中创建总共 5 个副本。

```azurecli-interactive 
az sig image-version create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1" \
   --replica-count 5 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

