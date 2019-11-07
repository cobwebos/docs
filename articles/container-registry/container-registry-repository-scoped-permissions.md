---
title: Azure 容器注册表中的存储库权限
description: 创建一个令牌，该令牌的权限限定为注册表中特定存储库的请求或推送映像
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 10/31/2019
ms.author: danlep
ms.openlocfilehash: dbfadea9bc05614108333ef5a046e11b5280cc68
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588429"
---
# <a name="repository-scoped-permissions-in-azure-container-registry"></a>Azure 容器注册表中的存储库范围内的权限 

Azure 容器注册表支持多种[身份验证选项](container-registry-authentication.md)，这些选项使用对整个注册表具有[基于角色的访问权限](container-registry-roles.md)的标识。 但是，在某些情况下，你可能只需要为注册表中的特定*存储库*提供访问权限。 

本文介绍如何创建和使用一个访问令牌，该令牌有权在注册表的特定存储库中执行操作。 使用访问令牌，可以为用户或服务提供对存储库的有限限制的访问权限，以便请求或推送或执行其他操作。 

请参阅本文后面的[关于存储库范围的权限](#about-repository-scoped-permissions)，了解有关令牌概念和方案的背景信息。

> [!IMPORTANT]
> 此功能目前以预览版提供，存在一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版推出之前，此功能的某些方面可能会有所更改。

## <a name="preview-limitations"></a>预览版限制

* 此功能仅在**高级**容器注册表中提供。 有关注册表服务层的信息，请参阅[Azure 容器注册表 sku](container-registry-skus.md)。
* 当前无法将存储库范围内的权限分配给某个 Azure Active Directory 对象（例如服务主体或托管标识）。
* 注册表最多允许20000个作用域映射和20000标记。

## <a name="prerequisites"></a>先决条件

* **Azure CLI** -本文要求本地安装 Azure CLI （版本2.0.76 或更高版本）。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。
* **Docker** -若要使用注册表进行身份验证，还需要本地 Docker 安装。 Docker 提供适用于 [macOS](https://docs.docker.com/docker-for-mac/)[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系统的安装说明。
* **包含存储库的容器注册表**-如果没有，请在 Azure 订阅中创建容器注册表。 例如，使用 [Azure 门户](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。 

  出于测试目的，请将一个或多个示例图像[推送](container-registry-get-started-docker-cli.md)或[导入](container-registry-import-images.md)到注册表。 本文中的示例涉及两个存储库中的以下图像： `samples/hello-world:v1` 和 `samples/nginx:v1`。 

## <a name="create-an-access-token"></a>创建访问令牌

使用[az acr token create][az-acr-token-create]命令创建令牌。 创建令牌时，请在每个存储库上指定一个或多个存储库和关联的操作，或使用这些设置指定现有的作用域映射。

### <a name="create-access-token-and-specify-repositories"></a>创建访问令牌并指定存储库

下面的示例创建一个访问令牌，该令牌具有对 `samples/hello-world` 存储库执行 `content/write` 和 `content/read` 操作的权限，以及 `samples/nginx` 存储库上的 `content/read` 操作。 默认情况下，该命令生成两个密码。 

此示例将标记状态设置为 `enabled` （默认设置），但你可以随时更新令牌并将状态设置为 `disabled`。

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read --status enabled
```

输出显示有关令牌的详细信息，包括生成的密码和作用域映射。 建议将密码保存在安全的位置，以便稍后用于 `docker login`。 不能再次检索这些密码，而是可以生成新密码。

输出还显示了自动创建的作用域映射，名为 `MyToken-scope-map`。 可以使用范围映射将相同的存储库操作应用于其他令牌。 或者，稍后更新范围映射以更改令牌权限。

```console
{
  "creationDate": "2019-10-22T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

### <a name="create-a-scope-map-and-associated-token"></a>创建作用域映射和关联的令牌

或者，在创建令牌时，使用存储库和关联的操作指定范围映射。 若要创建作用域映射，请使用[az acr scope map create][az-acr-scope-map-create]命令。

下面的示例命令创建具有上一示例中所用相同权限的范围映射。 它允许 `samples/hello-world` 存储库上的 `content/write` 和 `content/read` 操作以及 `samples/nginx` 存储库上的 `content/read` 操作：

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read \
  --description "Sample scope map"
```

输出与下面类似：

```console
{
  "actions": [
    "repositories/samples/hello-world/content/write",
    "repositories/samples/nginx/content/read"
  ],
  "creationDate": "2019-10-22T05:07:35.194413+00:00",
  "description": "Sample scope map.",
  "id": "/subscriptions/fxxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyScopeMap",
  "name": "MyScopeMap",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapType": "UserDefined",
  "type": "Microsoft.ContainerRegistry/registries/scopeMaps"
```

运行[az acr token create][az-acr-token-create]以创建与*MyScopeMap*范围映射关联的令牌。 默认情况下，该命令生成两个密码。 此示例将标记状态设置为 `enabled` （默认设置），但你可以随时更新令牌并将状态设置为 `disabled`。

```azurecli
az acr token create --name MyToken --registry myregistry --scope-map MyScopeMap --status enabled
```

输出显示有关令牌的详细信息，包括生成的密码和应用的范围映射。 建议将密码保存在安全的位置，以便稍后用于 `docker login`。 不能再次检索这些密码，而是可以生成新密码。

## <a name="generate-passwords-for-token"></a>为令牌生成密码

如果创建令牌时创建了密码，请继续对[注册表进行身份验证](#authenticate-using-token)。

如果没有令牌密码，或想要生成新密码，请运行[az acr token credential 生成][az-acr-token-credential-generate]命令。

下面的示例为你创建的令牌生成新密码，有效期为30天。 它在环境变量 TOKEN_PWD 中存储密码。 此示例的格式适用于 bash shell。

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

## <a name="authenticate-using-token"></a>使用令牌进行身份验证

使用令牌凭据运行 `docker login` 以使用注册表进行身份验证。 输入令牌名称作为用户名，并提供其密码之一。 下面的示例为 bash shell 设置格式，并使用环境变量提供值。

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

输出应显示成功的身份验证：

```console
Login Succeeded
```

## <a name="verify-scoped-access"></a>验证范围的访问权限

可以验证令牌是否为注册表中的存储库提供了作用域内的权限。 在此示例中，以下 `docker pull` 命令会成功完成，以便请求 `samples/hello-world` 和 `samples/nginx` 存储库中可用的映像：

```console
docker pull myregistry.azurecr.io/samples/hello-world:v1
docker pull myregistry.azurecr.io/samples/nginx:v1
```

由于示例标记只允许在 `samples/hello-world` 存储库上进行 `content/write` 操作，因此 `docker push` 成功到该存储库，但对于 `samples/nginx`失败：

```console
# docker push succeeds
docker pull myregistry.azurecr.io/samples/hello-world:v1

# docker push fails
docker pull myregistry.azurecr.io/samples/nginx:v1
```

## <a name="update-scope-map-and-token"></a>更新范围映射和令牌

若要更新令牌权限，请使用[az acr scope map update][az-acr-scope-map-update]更新关联的作用域映射中的权限。 例如，若要更新*MyScopeMap* ，以删除 `samples/hello-world` 存储库中的 `content/write` 操作：

```azurecli
az acr scope-map update --name MyScopeMap --registry myregistry \
  --remove samples/hello-world content/write
```

如果作用域映射与多个令牌关联，则该命令将更新所有关联令牌的权限。

如果要使用不同的作用域映射来更新令牌，请运行[az acr token update][az-acr-token-update]。 例如：

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

更新令牌或与令牌关联的作用域映射后，权限更改将在下一 `docker login` 或使用令牌的其他身份验证后生效。

更新令牌后，你可能想要生成新密码来访问注册表。 运行[az acr token credential 生成][az-acr-token-credential-generate]。 例如：

```azurecli
az acr token credential generate \
  --name MyToken --registry myregistry --days 30
```

## <a name="about-repository-scoped-permissions"></a>关于存储库范围的权限

### <a name="concepts"></a>概念

若要配置存储库范围的权限，请使用 Azure CLI 中的命令创建*访问令牌*和关联的*作用域映射*。

* **访问令牌**是用于在注册表中进行身份验证的密码。 允许对一个或多个存储库的*操作*使用与每个令牌关联的。 可以为每个令牌设置过期时间。 

* 每个指定存储库上的操作包括以下一项或多项**操作**。

  |操作  |说明  |
  |---------|---------|
  |`content/read`     |  读取存储库中的数据。 例如，请求项目。  |
  |`metadata/read`    | 从存储库中读取元数据。 例如，列出标记或显示清单元数据。   |
  |`content/write`     |  将数据写入存储库。 与 `content/read` 一起使用来推送项目。    |
  |`metadata/write`     |  将元数据写入存储库。 例如，更新清单属性。  |
  |`content/delete`    | 从存储库中删除数据。 例如，删除存储库或清单。 |

* **作用域映射**是一个注册表对象，可将应用于令牌的存储库权限组合在一起，也可以重新应用到其他令牌。 如果在创建标记时未应用作用域映射，将自动为你创建一个作用域映射，以保存权限设置。 

  作用域映射有助于配置多个用户对一组存储库具有相同的访问权限。 Azure 容器注册表还提供系统定义的作用域映射，你可以在创建访问令牌时应用此映射。

下图汇总了标记与范围映射之间的关系。 

![注册表范围映射和令牌](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

### <a name="scenarios"></a>方案

使用访问令牌的方案包括：

* 为 IoT 设备提供单独的令牌，以便从存储库中提取映像
* 向外部组织提供对特定存储库的权限 
* 限制存储库访问组织中的特定用户组。 例如，向开发人员提供写入和读取访问权限，这些开发人员构建面向特定存储库的映像，以及对从这些存储库部署的团队的读取访问权限。

### <a name="authentication-using-token"></a>使用令牌进行身份验证

使用令牌名称作为用户名及其关联的密码之一来向目标注册表进行身份验证。 身份验证方法取决于配置的操作。

### <a name="contentread-or-contentwrite"></a>内容/读取或内容/写入

如果令牌仅允许 `content/read` 或 `content/write` 操作，请在以下任一身份验证流中提供令牌凭据：

* 使用 `docker login` 在 Docker 中进行身份验证
* 在 Azure CLI 中使用[az acr login][az-acr-login]命令对注册表进行身份验证

进行身份验证后，该令牌将允许在作用域的存储库或存储库中配置的操作。 例如，如果令牌允许对存储库进行 `content/read` 操作，则允许对该存储库中的映像执行 `docker pull` 操作。

#### <a name="metadataread-metadatawrite-or-contentdelete"></a>元数据/读取、元数据/写入或内容/删除

如果令牌允许对存储库进行 `metadata/read`、`metadata/write`或 `content/delete` 操作，则令牌凭据必须作为参数提供 Azure CLI 中的相关[az acr 存储库][az-acr-repository]命令。

例如，如果在存储库上允许 `metadata/read` 操作，请在运行[az acr repository show-tags][az-acr-repository-show-tags]命令以列出标记时传递令牌凭据。

## <a name="next-steps"></a>后续步骤

* 若要管理作用域映射和访问令牌，请使用[az acr 范围地图][az-acr-scope-map]和[az acr 标记][az-acr-token]命令组中的其他命令。
* 有关使用管理员帐户或 Azure Active Directory 标识对 Azure 容器注册表进行身份验证的方案，请参阅[身份验证概述](container-registry-authentication.md)。


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
