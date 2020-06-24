---
title: include 文件
description: include 文件
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 012800806aeff81939baa2cee88e78191e4fb6c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195258"
---
### <a name="create-a-user-assigned-identity"></a>创建用户分配的标识

使用 [az identity create][az-identity-create] 命令在订阅中创建一个名为 *myACRTasksId* 的标识。 可以使用之前用于创建容器注册表的同一资源组，也可以使用其他资源组。

```azurecli
az identity create \
  --resource-group myResourceGroup \
  --name myACRTasksId
```

为了在以下步骤中配置用户分配的标识，请使用 [az identity show][az-identity-show] 命令将标识的资源 ID、主体 ID 和客户端 ID 存储在变量中。

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show