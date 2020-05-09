---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: de1a22ed6e9707767c0d097a9250f0bdd31414d5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788954"
---
## <a name="create-an-image-gallery"></a>创建映像库 

映像库是用于启用映像共享的主要资源。 

允许用于库名称的字符为大写或小写字母、数字、点和句点。 库名称不能包含短划线。   库名称在你的订阅中必须唯一。 

使用 [az sig create](/cli/azure/sig#az-sig-create) 创建一个映像库。 以下示例创建名为*myGalleryRG*的名为 "myGallery" 的资源组，以及一个名为 " *myGallery* *" 的*库。

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>共享库

可以使用基于角色的访问控制 (RBAC) 在订阅之间共享映像。 可在库、图像定义或图像版本上面上共享图像。 对映像版本具有读取权限的任何用户甚至跨订阅，都将能够使用映像版本来部署 VM。

我们建议你在库级别与其他用户共享。 若要获取库的对象 ID，请使用 [az sig show](/cli/azure/sig#az-sig-show)。

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

使用对象 ID 作为作用域以及电子邮件地址，并使用 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) 授予用户对共享映像库的访问权限。 请将 `<email-address>` 和 `<gallery iD>` 替换为自己的信息。

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

有关如何使用 RBAC 共享资源的详细信息，请参阅[使用 RBAC 和 Azure CLI 管理访问权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)。
