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
ms.openlocfilehash: 0e2acb346fad87e0c1c7fd7de1389d8fc86206d0
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642106"
---
### <a name="create-a-user-assigned-identity"></a>创建用户分配的标识

在订阅中使用[az identity create][az-identity-create]命令创建名为*myACRTasksId*的标识。 您可以使用以前用过的资源组创建容器注册表, 也可以使用其他资源组。

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

若要在以下步骤中配置用户分配的标识, 请使用[az identity show][az-identity-show]命令将标识的资源 id、服务主体 id 和客户端 id 存储在变量中。

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show