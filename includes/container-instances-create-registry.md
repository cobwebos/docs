---
title: include 文件
description: include 文件
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 2b1d9b7f9ff07a3e0c7745191decc3e82181553e
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708013"
---
## <a name="create-azure-container-registry"></a>创建 Azure 容器注册表

在创建容器注册表之前，需要创建一个资源组，以便将容器注册表部署到其中。** 资源组是在其中部署和管理所有 Azure 资源的逻辑集合。

使用“[az group create][az-group-create]”命令创建资源组。 以下示例在 *eastus* 区域创建名为 *myResourceGroup* 的资源组：

```azurecli
az group create --name myResourceGroup --location eastus
```

创建资源组后，使用 [az acr create][az-acr-create] 命令创建 Azure 容器注册表。 容器注册表名称在 Azure 中必须唯一，并且必须包含 5-50 个字母数字字符。 将 `<acrName>` 替换为注册表的唯一名称：

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

下面是名为 mycontainerregistry082 的新 Azure 容器注册表的部分输出：

```output
{
  "creationDate": "2020-07-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

本教程的余下部分使用 `<acrName>` 作为在此步骤中选择的容器注册表名称的占位符。

## <a name="log-in-to-container-registry"></a>登录到容器注册表

必须先登录到 Azure 容器注册表实例，才能向其推动映像。 使用 [az acr login][az-acr-login] 命令完成此操作。 必须提供创建容器注册表时选择的唯一名称。

```azurecli
az acr login --name <acrName>
```

例如：

```azurecli
az acr login --name mycontainerregistry082
```

该命令在完成后返回 `Login Succeeded`：

```output
Login Succeeded
```

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create