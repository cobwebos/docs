---
title: include 文件
description: include 文件
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 04/30/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 9647cdd584b53f581f46f728ca2d08f9a113ce92
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156155"
---
## <a name="before-you-begin"></a>开始之前

若要完成本文中的示例，必须具有通用化 VM 的现有托管映像。 有关详细信息，请参阅[教程：使用 Azure CLI 2.0 创建 Azure VM 的自定义映像](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images)。 如果托管映像包含数据磁盘，则数据磁盘大小不能超过 1 TB。

## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。 也可以通过转到 [https://shell.azure.com/bash](https://shell.azure.com/bash) 在单独的浏览器标签页中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。

## <a name="create-an-image-gallery"></a>创建映像库 

映像库是用于启用映像共享的主要资源。 允许用于库名称的字符为大写或小写字母、数字、点和句点。 库名称不能包含短划线。   库名称在你的订阅中必须唯一。 

使用 [az sig create](/cli/azure/sig#az-sig-create) 创建一个映像库。 以下示例在 *myGalleryRG* 中创建一个名为 *myGallery* 的库。

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create -g myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>创建映像定义

映像定义为映像创建一个逻辑分组。 它们用于管理有关映像版本的信息，这些版本是在其中创建的。 映像定义名称可能包含大写或小写字母、数字、点、短划线和句点。 若要详细了解可以为映像定义指定的值，请参阅[映像定义](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)。

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

使用 [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) 根据需要创建映像的版本。 你需要传入托管映像的 ID 以作为创建映像版本时要使用的基线。 可以使用 [az image list](/cli/azure/image?view#az-image-list) 获取资源组中的映像的相关信息。 

允许用于映像版本的字符为数字和句点。 数字必须在 32 位整数范围内。 格式:*MajorVersion*.*MinorVersion*.*Patch*。

在此示例中，我们的映像的版本是*1.0.0*我们将创建 2 个副本中的*美国中西部*区域，在 1 个副本*美国中南部*区域和 1中的副本*美国东部 2*区域。


```azurecli-interactive 
az sig image-version create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1" \
   --replica-count 2 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

> [!NOTE]
> 需等待映像版本彻底生成并复制完毕，然后才能使用同一托管映像来创建另一映像版本。
>
> 也可在[区域冗余存储](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)中存储映像版本，只需在创建映像版本时添加 `--storage-account-type standard_zrs` 即可。
>
