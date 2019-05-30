---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1ec3ecdafb8e475f5f13372789528612ccd7b8b9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226013"
---
## <a name="using-rbac-to-share-images"></a>使用 RBAC 共享映像

可以使用基于角色的访问控制 (RBAC) 在订阅之间共享映像。 任何对映像版本具有读取权限的用户，即使跨订阅，也能够使用映像版本部署虚拟机。

有关如何使用 RBAC 共享资源的详细信息，请参阅[使用 RBAC 和 Azure CLI 管理访问权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)。


## <a name="list-information"></a>列出信息

使用 [az sig list](/cli/azure/sig#az-sig-list) 获取有关可用映像库的位置、状态和其他信息。

```azurecli-interactive 
az sig list -o table
```

使用 [az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list) 列出库中的映像定义，包括有关 OS 类型和状态的信息。

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

使用 [az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list) 列出库中的共享映像版本。

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

使用 [az sig image-version show](/cli/azure/sig/image-version#az-sig-image-version-show) 获取映像版本的 ID。

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```