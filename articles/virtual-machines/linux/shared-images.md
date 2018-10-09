---
title: 使用 Azure CLI 创建共享 VM 映像 | Microsoft Docs
description: 在本文中，你将了解如何使用 Azure CLI 在 Azure 中创建 VM 的共享映像。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: axayjo
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/19/2018
ms.author: akjosh; cynthn
ms.custom: ''
ms.openlocfilehash: 2f8ddae05b97b3fa6f1d3f65681defdd4e5a38b7
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47045929"
---
# <a name="preview-create-a-shared-image-gallery-with-the-azure-cli"></a>预览：使用 Azure CLI 创建共享映像库

共享映像库大大简化了整个组织中的自定义映像共享。 自定义映像类似于市场映像，不同的是自定义映像的创建者是自己。 自定义映像可用于启动配置，例如预加载应用程序、应用程序配置和其他 OS 配置。 使用共享映像库，你可以在 AAD 租户内在同一区域中或跨区域与组织中的其他人共享自定义 VM 映像。 选择要共享哪些映像，要使它们在哪些区域中可用，以及希望与谁共享它们。 可以创建多个库，以便可以按逻辑方式对共享映像进行分组。 库是顶级资源，它提供完全的基于角色的访问控制 (RBAC)。 可以对映像进行版本控制，并且可以选择将每个映像版本复制到一组不同的 Azure 区域。 库仅适用于托管映像。

在本文中，你将为 Azure 虚拟机创建你自己的库和自定义映像。 学习如何：

> [!div class="checklist"]
> * 创建共享映像库
> * 创建共享映像定义
> * 创建共享映像版本
> * 基于共享映像创建 VM
> * 删除资源


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0.46 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

[!INCLUDE [virtual-machines-common-shared-images-cli](../../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-vm"></a>创建 VM

使用 [az vm create](/cli/azure/vm#az-vm-create) 基于映像版本创建 VM。

```azurecli-interactive 
az vm create\
   -g myGalleryRG \
   -n myVM \
   --image "/subscriptions/<subscription-ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0" \
   --generate-ssh-keys
```

[!INCLUDE [virtual-machines-common-gallery-list-cli](../../../includes/virtual-machines-common-gallery-list-cli.md)]



## <a name="next-steps"></a>后续步骤

有关共享映像库的详细信息，请参阅[概述](shared-image-galleries.md)。
