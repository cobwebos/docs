---
title: "创建专用 Docker 容器注册表 - Azure CLI | Microsoft 文档"
description: "开始使用 Azure CLI 2.0 创建和管理专用 Docker 容器注册表"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: c7cdb1b13bf32388d18c2a25af28337a81861c1e
ms.contentlocale: zh-cn
ms.lasthandoff: 07/12/2017

---

# 使用 Azure CLI 创建托管的容器注册表
<a id="create-a-managed-container-registry-using-the-azure-cli" class="xliff"></a>

Azure 容器注册表是托管的 Docker 容器注册表服务，用于存储专用的 Docker 容器映像。 本指南详述了如何使用 Azure CLI 创建托管的 Azure 容器注册表实例。

托管的 Azure 容器注册表为预览版，尚未在所有区域推出。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，此快速入门教程要求运行 Azure CLI 2.0.4 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## 创建资源组
<a id="create-a-resource-group" class="xliff"></a>

使用 [az group create](/cli/azure/group#create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在 westcentralus 位置创建名为 myResourceGroup 的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus
```

## 创建容器注册表
<a id="create-a-container-registry" class="xliff"></a>

使用 [az acr create](/cli/azure/acr#create) 命令创建 ACR 实例。

> [!NOTE]
> 创建注册表时，请指定仅包含字母和数字的全局唯一顶级域名。

 示例中的注册表名称为 *myContainerRegistry1*，请将它替换为你自己的唯一名称。

```azurecli
az acr create --name myContainerRegistry1 --resource-group myResourceGroup --sku Managed_Standard
```

创建注册表时，输出与下面类似：

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-29T04:50:28.607134+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry1",
  "location": "westcentralus",
  "loginServer": "mycontainerregistry1.azurecr.io",
  "name": "myContainerRegistry1",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Managed_Standard",
    "tier": "Managed"
  },
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## 登录到 ACR 实例
<a id="log-in-to-acr-instance" class="xliff"></a>

在推送和拉取容器映像之前，必须登录到 ACR 实例。 为此，请使用 [az acr login](/cli/azure/acr#login) 命令。

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

完成后，该命令会返回“登录成功”消息。

## 使用 Azure 容器注册表
<a id="use-azure-container-registry" class="xliff"></a>

### 列出容器映像
<a id="list-container-images" class="xliff"></a>

使用 `az acr` CLI 命令查询存储库中的映像和标记。

> [!NOTE]
> 目前，容器注册表不支持使用 `docker search` 命令查询映像和标记。

### 列出存储库
<a id="list-repositories" class="xliff"></a>

以下示例使用 JSON（JavaScript 对象表示法）格式列出注册表中的存储库：

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### 列出标记
<a id="list-tags" class="xliff"></a>

以下示例使用 JSON 格式列出 **samples/nginx** 存储库中的标记：

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## 后续步骤
<a id="next-steps" class="xliff"></a>

在本快速入门中，你已使用 Azure CLI 创建托管的 Azure 容器注册表实例。

> [!div class="nextstepaction"]
> [使用 Docker CLI 推送第一个映像](container-registry-get-started-docker-cli.md)

