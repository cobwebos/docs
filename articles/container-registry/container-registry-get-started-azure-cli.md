---
title: "创建专用 Docker 容器注册表 - Azure CLI | Microsoft 文档"
description: "开始使用 Azure CLI 2.0 创建和管理专用 Docker 容器注册表"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 2875f4089231ed12a0312b2c2e077938440365c6
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="create-a-private-docker-container-registry-using-the-azure-cli-20"></a>使用 Azure CLI 2.0 创建专用 Docker 容器注册表
使用 [Azure CLI 2.0](https://github.com/Azure/azure-cli) 中的命令从 Linux、Mac 或 Windows 计算机创建容器注册表并管理其设置。 也可以使用 [Azure 门户](container-registry-get-started-portal.md)或者使用容器注册表 [REST API](https://go.microsoft.com/fwlink/p/?linkid=834376) 以编程方式创建和管理容器注册表。


* 有关背景和概念，请参阅[概述](container-registry-intro.md)
* 如需容器注册表 CLI 命令（`az acr` 命令）的帮助，请在任一命令中传递 `-h` 参数。


## <a name="prerequisites"></a>先决条件
* **Azure CLI 2.0**：若要安装并开始使用 CLI 2.0，请参阅[安装说明](/cli/azure/install-azure-cli)。 运行 `az login` 登录到 Azure 订阅。 有关详细信息，请参阅 [CLI 2.0 入门](/cli/azure/get-started-with-azure-cli)。
* **资源组**：在创建容器注册表之前创建[资源组](../azure-resource-manager/resource-group-overview.md#resource-groups)，或使用现有资源组。 请确保该资源组位于[提供](https://azure.microsoft.com/regions/services/)容器注册表服务的位置。 若要使用 CLI 2.0 创建资源组，请查看 [CLI 2.0 参考](/cli/azure/group)。
* **存储帐户**（可选）：创建一个标准的 Azure [存储帐户](../storage/common/storage-introduction.md)，用于在同一位置备份容器注册表。 如果使用 `az acr create` 创建注册表时未指定存储帐户，该命令会自动创建一个存储帐户。 若要使用 CLI 2.0 创建存储帐户，请查看 [CLI 2.0 参考](/cli/azure/storage/account)。 当前不支持高级存储。
* **服务主体**（可选）：使用 CLI 创建注册表时，默认情况下不会为该注册表设置访问权限。 可以根据需要将现有 Azure Active Directory 服务主体分配到注册表（或创建并分配新的服务主体），或者启用注册表的管理员用户帐户。 请参阅本文稍后的部分。 有关注册表访问权限的详细信息，请参阅 [Authenticate with the container registry](container-registry-authentication.md)（使用容器注册表进行身份验证）。

## <a name="create-a-container-registry"></a>创建容器注册表
运行 `az acr create` 命令可以创建容器注册表。

> [!TIP]
> 创建注册表时，请指定仅包含字母和数字的全局唯一顶级域名。 示例中的注册表名称为 `myRegistry1`，但需要将它替换成自己的唯一名称。
>
>

以下命令使用少量参数在资源组 `myResourceGroup` 中创建容器注册表 `myRegistry1`，并且使用基本 sku：

```azurecli
az acr create --name myRegistry1 --resource-group myResourceGroup --sku Basic
```

* `--storage-account-name` 是可选项。 如果未指定，则会创建一个存储帐户，其名称由注册表名称以及指定资源组中的一个时间戳组成。

创建注册表时，输出与下面类似：

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T18:36:29.124842+00:00",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry
/registries/myRegistry1",
  "location": "southcentralus",
  "loginServer": "myregistry1.azurecr.io",
  "name": "myRegistry1",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myregistry123456789"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}

```


请特别注意：

* `id` - 注册表在订阅中的标识符，分配服务主体时需要使用它。
* `loginServer` - 为[登录到注册表](container-registry-authentication.md)而指定的完全限定名称。 在本示例中，名称为 `myregistry1.exp.azurecr.io`（全部小写）。

## <a name="assign-a-service-principal"></a>分配服务主体
使用 CLI 2.0 命令可将 Azure Active Directory 服务主体分配到注册表。 为这些示例中的服务主体分配了“所有者”角色，但可以根据需要分配[其他角色](../active-directory/role-based-access-control-configure.md)。

### <a name="create-a-service-principal-and-assign-access-to-the-registry"></a>创建服务主体并分配注册表访问权限
在以下命令中，新服务主体有权以“所有者”角色身份访问使用 `--scopes` 参数传递的注册表标识符。 使用 `--password` 参数指定一个强密码。

```azurecli
az ad sp create-for-rbac --scopes /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --password myPassword
```



### <a name="assign-an-existing-service-principal"></a>分配现有的服务主体
如果已有一个服务主体并想要为它分配对注册表的“所有者”角色访问权限，请运行类似于以下示例的命令。 使用 `--assignee` 参数传递服务主体应用 ID：

```azurecli
az role assignment create --scope /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --assignee myAppId
```



## <a name="manage-admin-credentials"></a>管理管理员凭据
管理员帐户是为每个容器注册表自动创建的，默认情况下已禁用。 以下示例演示 `az acr` CLI 命令如何管理容器注册表的管理员凭据。

### <a name="obtain-admin-user-credentials"></a>获取管理员用户凭据
```azurecli
az acr credential show -n myRegistry1
```

### <a name="enable-admin-user-for-an-existing-registry"></a>为现有注册表启用管理员用户
```azurecli
az acr update -n myRegistry1 --admin-enabled true
```

### <a name="disable-admin-user-for-an-existing-registry"></a>为现有注册表禁用管理员用户
```azurecli
az acr update -n myRegistry1 --admin-enabled false
```

## <a name="list-images-and-tags"></a>列出映像和标记
使用 `az acr` CLI 命令查询存储库中的映像和标记。

> [!NOTE]
> 目前，容器注册表不支持使用 `docker search` 命令查询映像和标记。


### <a name="list-repositories"></a>列出存储库
以下示例使用 JSON（JavaScript 对象表示法）格式列出注册表中的存储库：

```azurecli
az acr repository list -n myRegistry1 -o json
```

### <a name="list-tags"></a>列出标记
以下示例使用 JSON 格式列出 **samples/nginx** 存储库中的标记：

```azurecli
az acr repository show-tags -n myRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>后续步骤
* [使用 Docker CLI 推送第一个映像](container-registry-get-started-docker-cli.md)

