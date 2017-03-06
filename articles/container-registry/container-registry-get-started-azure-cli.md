---
title: "创建 Azure 容器注册表 - CLI | Microsoft 文档"
description: "开始使用 Azure CLI 2.0 创建和管理 Azure 容器注册表"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: 2a381431acb6436ddd8e13c69b05423a33cd4fa6
ms.openlocfilehash: 1d5e16952cbc56a381ead23843515cf6ed1d74a9
ms.lasthandoff: 02/22/2017

---
# <a name="create-a-container-registry-using-the-azure-cli"></a>使用 Azure CLI 创建容器注册表
使用 [Azure CLI 2.0](https://github.com/Azure/azure-cli) 中的命令从 Linux、Mac 或 Windows 计算机创建容器注册表并管理其设置。 也可以使用 [Azure 门户](container-registry-get-started-portal.md)或者使用容器注册表 [REST API](https://go.microsoft.com/fwlink/p/?linkid=834376) 以编程方式创建和管理容器注册表。


* 有关背景信息和概念，请参阅[什么是 Azure 容器注册表？](container-registry-intro.md)
* 如需容器注册表 CLI 命令（`az acr` 命令）的帮助，请在任一命令中传递 `-h` 参数。

> [!NOTE]
> 容器注册表目前以预览版提供。
> 
> 

## <a name="prerequisites"></a>先决条件
* **Azure CLI 2.0** - 若要安装并开始使用 CLI 2.0，请参阅[安装说明](https://github.com/Azure/azure-cli/blob/master/README.rst)。 运行 `az login` 登录到你的 Azure 订阅。
* **资源组** - 在创建容器注册表之前创建[资源组](../azure-resource-manager/resource-group-overview.md#resource-groups)，或使用现有资源组。 请确保该资源组位于[提供](https://azure.microsoft.com/regions/services/)容器注册表服务的位置。 若要使用 CLI 2.0 创建资源组，请参阅 [CLI 2.0 示例](https://github.com/Azure/azure-cli-samples/tree/master/arm)。 
* **存储帐户**（可选）- 创建一个标准的 Azure [存储帐户](../storage/storage-introduction.md)用于在同一位置备份容器注册表。 如果使用 `az acr create` 创建注册表时未指定存储帐户，该命令将自动创建一个存储帐户。 若要使用 CLI 2.0 创建存储帐户，请参阅 [CLI 2.0 示例](https://github.com/Azure/azure-cli-samples/tree/master/storage)。
* **服务主体**（可选）- 使用 CLI 创建注册表时，默认情况下不会为该注册表设置访问权限。 可以根据需要将现有 Azure Active Directory 服务主体分配到注册表（或创建并分配新的服务主体），或者启用注册表的管理员用户帐户。 请参阅本文稍后的部分。 有关注册表访问权限的详细信息，请参阅 [Authenticate with the container registry](container-registry-authentication.md)（使用容器注册表进行身份验证）。 

## <a name="create-a-container-registry"></a>创建容器注册表
运行 `az acr create` 命令可以创建容器注册表。 

> [!TIP]
> 创建注册表时，请指定仅包含字母和数字的全局唯一顶级域名。 示例中的注册表名称为 `myRegistry`，但需要将它替换为你自己的唯一名称。 
> 
> 

以下命令使用少量的参数在美国中南部位置的资源组 `myResourceGroup` 中创建容器注册表 `myRegistry`：

```azurecli
az acr create -n myRegistry -g myResourceGroup -l southcentralus
```

* `--storage-account-name` 或 `-s` 是可选的。 如果未指定这些参数，将在指定的资源组中使用随机名称创建存储帐户。

输出与下面类似：

![az acr create output](./media/container-registry-get-started-azure-cli/acr_create.png)


请特别注意：

* `id` - 注册表在订阅中的标识符，分配服务主体时需要使用它。 
* `loginServer` - 为[登录到注册表](container-registry-authentication.md)而指定的完全限定名称。 在本示例中，名称为 `myregistry-contoso.exp.azurecr.io`（全部小写）。

## <a name="assign-a-service-principal"></a>分配服务主体
使用 CLI 2.0 命令可将 Azure Active Directory 服务主体分配到注册表。 为这些示例中的服务主体分配了“所有者”角色，但你可以根据需要分配[其他角色](../active-directory/role-based-access-control-configure.md)。

### <a name="create-a-service-principal-and-assign-access-to-the-registry"></a>创建服务主体并分配注册表访问权限
在以下命令中，新服务主体有权以“所有者”角色身份访问使用 `--scopes` 参数传递的注册表标识符。 使用 `--password` 参数指定一个强密码。

```azurecli
az ad sp create-for-rbac --scopes /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry --role Owner --password myPassword
```



### <a name="assign-an-existing-service-principal"></a>分配现有的服务主体
如果你已有一个服务主体并想要为它分配对注册表的“所有者”角色访问权限，请运行类似于以下示例的命令。 使用 `--assignee` 参数传递服务主体应用 ID：

```azurecli
az role assignment create --scope /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry --role Owner --assignee myAppId
```



## <a name="manage-admin-credentials"></a>管理管理员凭据
管理员帐户是为每个容器注册表自动创建的，默认情况下已禁用。 以下示例演示 `az acr` CLI 命令如何管理容器注册表的管理员凭据。

### <a name="obtain-admin-user-credentials"></a>获取管理员用户凭据
```azurecli
az acr credential show -n myRegistry
```

### <a name="enable-admin-user-for-an-existing-registry"></a>为现有注册表启用管理员用户
```azurecli
az acr update -n myRegistry --admin-enabled true
```

### <a name="disable-admin-user-for-an-existing-registry"></a>为现有注册表禁用管理员用户
```azurecli
az acr update -n myRegistry --admin-enabled false
```

## <a name="list-images-and-tags"></a>列出映像和标记
使用 `az acr` CLI 命令查询存储库中的映像和标记。 

> [!NOTE]
> 目前，容器注册表不支持使用 `docker search` 命令查询映像和标记。


### <a name="list-repositories"></a>列出存储库
以下示例使用 JSON（JavaScript 对象表示法）格式列出注册表中的存储库：

```azurecli
az acr repository list -n myRegistry -o json
```

### <a name="list-tags"></a>列出标记
以下示例使用 JSON 格式列出 **samples/nginx** 存储库中的标记：

```azurecli
az acr repository show-tags -n myRegistry --repository samples/nginx -o json
```

## <a name="next-steps"></a>后续步骤
* [使用 Docker CLI 推送第一个映像](container-registry-get-started-docker-cli.md)


