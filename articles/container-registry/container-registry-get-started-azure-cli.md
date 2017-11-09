---
title: "快速入门 - 使用 Azure CLI 在 Azure 中创建专用 Docker 注册表"
description: "快速了解如何使用 Azure CLI 创建专用 Docker 容器注册表。"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: tysonn
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6b3fb9a3ea090f0083e8f113ddf13312fe42b59a
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2017
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>使用 Azure CLI 创建容器注册表

Azure 容器注册表是托管的 Docker 容器注册表服务，用于存储专用的 Docker 容器映像。 本指南详述了如何使用 Azure CLI 创建 Azure 容器注册表实例。

本快速入门需要运行 Azure CLI 2.0.20 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。

还必须在本地安装 Docker。 Docker 提供的包可在任何 [Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 或 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系统上轻松配置 Docker。

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>创建容器注册表

在本快速入门中，我们将创建*基本*注册表。 Azure 容器注册表以多个不同 SKU 提供，下表对此进行了简要说明。 有关每个 SKU 的扩展详细信息，请参阅[容器注册表 SKU](container-registry-skus.md)。

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

使用 [az acr create](/cli/azure/acr#create) 命令创建 ACR 实例。

注册表的名称必须是唯一的。 以下示例使用 myContainerRegistry007。 将其更新为唯一值。

```azurecli
az acr create --name myContainerRegistry007 --resource-group myResourceGroup --sku Basic
```

创建注册表时，输出与下面类似：

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr223140"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

本快速入门的剩余部分使用 `<acrname>` 作为容器注册表名称的占位符。

## <a name="log-in-to-acr"></a>登录到 ACR

在推送和拉取容器映像之前，必须登录到 ACR 实例。 为此，请使用 [az acr login](/cli/azure/acr#login) 命令。

```azurecli
az acr login --name <acrname>
```

完成后，该命令会返回“登录成功”消息。

## <a name="push-image-to-acr"></a>将映像推送到 ACR

要将映像推送到 Azure 容器注册表，首先必须具有一个映像。 如果需要，请运行以下命令，从 Docker 中心拉取预创建映像。

```bash
docker pull microsoft/aci-helloworld
```

需要使用 ACR 登录服务器名称标记此映像。 运行以下命令，返回 ACR 实例的登录服务器名称。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

使用 [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) 命令标记映像。 使用 ACR 实例的登录服务器名称替换 <acrLoginServer>。

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

最后，使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 将映像推送到 ACR 实例。 使用 ACR 实例的登录服务器名称替换 <acrLoginServer>。

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>列出容器映像

以下示例列出了注册表中的存储库：

```azurecli
az acr repository list -n <acrname> -o table
```

输出：

```bash
Result
----------------
aci-helloworld
```

以下示例列出了 aci-helloworld 存储库中的标记。

```azurecli
az acr repository show-tags -n <acrname> --repository aci-helloworld -o table
```

输出：

```bash
Result
--------
v1
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、ACR 实例和所有容器映像，可以使用 [az group delete](/cli/azure/group#delete) 命令将其删除。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门教程中，你已使用 Azure CLI 创建 Azure 容器注册表。 如果要对 Azure 容器实例使用 Azure 容器注册表，请继续学习 Azure 容器实例教程。

> [!div class="nextstepaction"]
> [Azure 容器实例教程](../container-instances/container-instances-tutorial-prepare-app.md)