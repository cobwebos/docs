---
title: Azure 容器注册表中的存储库权限
description: 创建一个令牌，该令牌的权限限定为注册表中的特定存储库，用于请求或推送映像，或执行其他操作
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 7d390bf4d97561e374c70f184534ac4f98a40611
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444265"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>使用存储库范围内的权限创建令牌

本文介绍如何创建令牌和作用域映射，以管理容器注册表中存储库范围内的权限。 通过创建令牌，注册表所有者可为用户或服务提供对存储库的有限限制的访问权限，以便提取或推送映像或执行其他操作。 令牌提供比其他注册表[身份验证选项](container-registry-authentication.md)更细化的权限，这些选项将对整个注册表的权限作用域。 

创建令牌的方案包括：

* 允许包含单独令牌的 IoT 设备从存储库中提取映像
* 向外部组织提供对特定存储库的权限 
* 限制存储库对组织中不同用户组的访问。 例如，向开发人员提供写入和读取访问权限，这些开发人员构建面向特定存储库的映像，以及对从这些存储库部署的团队的读取访问权限。

> [!IMPORTANT]
> 此功能目前以预览版提供，存在一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="preview-limitations"></a>预览版限制

* 此功能仅在**高级**容器注册表中提供。 有关注册表服务层和限制的信息，请参阅[Azure 容器注册表 sku](container-registry-skus.md)。
* 当前无法将存储库范围的权限分配给 Azure Active Directory 标识，如服务主体或托管标识。

## <a name="concepts"></a>概念

若要配置存储库范围的权限，请使用关联的*作用域映射*创建*令牌*。 

* **标记**以及生成的密码允许用户使用注册表进行身份验证。 你可以为令牌密码设置过期日期，或者随时禁用令牌。  

  使用令牌进行身份验证后，用户或服务可以执行一个或多个作用域为一个或多个存储库的*操作*。

  |操作  |说明  | 示例 |
  |---------|---------|--------|
  |`content/delete`    | 从存储库中删除数据  | 删除存储库或清单 |
  |`content/read`     |  从存储库中读取数据 |  请求项目 |
  |`content/write`     |  将数据写入存储库     | 与 `content/read` 一起使用来推送项目 |
  |`metadata/read`    | 读取存储库中的元数据   | 列出标记或清单 |
  |`metadata/write`     |  将元数据写入存储库  | 启用或禁用读取、写入或删除操作 |

* **作用域映射**将应用于令牌的存储库权限分组，并可以重新应用到其他令牌。 每个标记都与单个范围映射相关联。 

   使用范围映射：

    * 为一组存储库配置多个具有相同权限的令牌
    * 在作用域映射中添加或删除存储库操作时更新令牌权限，或应用其他作用域映射 

  Azure 容器注册表还提供多个系统定义的作用域映射，你可以应用这些映射，并在所有存储库中具有固定的权限。

下图显示了标记与范围映射之间的关系。 

![注册表标记和作用域映射](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>必备条件

* Azure CLI 2.0.76 或更高版本中提供了用于创建和管理令牌的**Azure CLI** Azure CLI 命令。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。
* **Docker** -若要使用注册表进行请求请求或推送映像，需要安装本地 Docker。 Docker 提供适用于 [macOS](https://docs.docker.com/docker-for-mac/)[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系统的安装说明。
* **容器注册表**-如果没有，请在 Azure 订阅中创建高级容器注册表，或升级现有注册表。 例如，使用 [Azure 门户](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。 

## <a name="create-token---cli"></a>创建令牌-CLI

### <a name="create-token-and-specify-repositories"></a>创建令牌并指定存储库

使用[az acr token create][az-acr-token-create]命令创建令牌。 创建令牌时，可以指定一个或多个存储库，并在每个存储库上指定关联的操作。 存储库尚不需要位于注册表中。 若要通过指定现有的范围映射来创建令牌，请参阅下一节。

下面的示例在注册表*myregistry*中创建一个令牌，该令牌具有对 `samples/hello-world` 存储库的以下权限： `content/write` 和 `content/read`。 默认情况下，该命令会将默认令牌状态设置为 `enabled`，但你可以随时将状态更新为 `disabled`。

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

输出显示有关令牌的详细信息，包括两个生成的密码。 建议将密码保存在安全的位置，以便以后用于身份验证。 不能再次检索这些密码，而是可以生成新密码。

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
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

输出包含有关创建的命令的范围映射的详细信息。 可以使用名为 `MyToken-scope-map`的作用域映射将相同的存储库操作应用于其他令牌。 或者，稍后更新范围映射来更改关联令牌的权限。

### <a name="create-token-and-specify-scope-map"></a>创建令牌并指定作用域映射

创建令牌的另一种方法是指定现有的作用域映射。 如果还没有范围映射，请先通过指定存储库和关联的操作创建一个。 然后，在创建令牌时指定作用域映射。 

若要创建作用域映射，请使用[az acr scope map create][az-acr-scope-map-create]命令。 以下命令在以前使用的 `samples/hello-world` 存储库中创建具有相同权限的作用域映射。 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

若要创建令牌，请运行[az acr token create][az-acr-token-create] ，并指定*MyScopeMap* scope map。 如前面的示例所示，该命令会将默认令牌状态设置为 `enabled`。

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

输出显示有关令牌的详细信息，包括两个生成的密码。 建议将密码保存在安全的位置，以便以后用于身份验证。 不能再次检索这些密码，而是可以生成新密码。

## <a name="create-token---portal"></a>创建令牌-门户

你可以使用 Azure 门户来创建令牌和作用域映射。 与 `az acr token create` CLI 命令一样，你可以应用现有的作用域映射，或者在创建令牌时通过指定一个或多个存储库和关联的操作创建作用域映射。 存储库尚不需要位于注册表中。 

下面的示例创建一个标记，并在 `samples/hello-world` 存储库上创建具有以下权限的范围映射： `content/write` 和 `content/read`。

1. 在门户中，导航到容器注册表。
1. 在 "**服务**" 下，选择 "**令牌（预览） > + 添加**"。
  ![在门户中创建令牌](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. 输入令牌名称。
1. 在 "**作用域映射**" 下，选择 "**新建**"。
1. 配置作用域映射：
    1. 为作用域映射输入名称和描述。 
    1. 在 "**存储库**" 下，输入 `samples/hello-world`，并在 "**权限**" 下选择 "`content/read` 和 `content/write`"。 然后选择 " **+ 添加**"。  
    ![在门户中创建作用域映射](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. 添加存储库和权限后，选择 "**添加**" 以添加范围映射。
1. 接受默认标记**状态**"**已启用**"，然后选择 "**创建**"。

验证并创建令牌后，**令牌屏幕中将显示令牌详细**信息。

### <a name="add-token-password"></a>添加令牌密码

创建令牌后生成密码。 若要使用注册表进行身份验证，必须启用该令牌并使用有效密码。

您可以生成一个或两个密码，并为每个密码设置到期日期。 

1. 在门户中，导航到容器注册表。
1. 在 "**服务**" 下，选择 "**令牌（预览）** "，然后选择令牌。
1. 在 "令牌详细信息" 中，选择 " **password1** " 或 " **password2**"，然后选择 "生成" 图标。
1. 在 "密码" 屏幕上，可以选择为密码设置过期日期，然后选择 "**生成**"。
1. 生成密码后，将其复制并保存到一个安全的位置。 关闭屏幕后无法检索生成的密码，但可以生成新的密码。

    ![在门户中创建令牌密码](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>用令牌进行身份验证

当用户或服务使用令牌向目标注册表进行身份验证时，它会将令牌名称提供为用户名及其生成的密码之一。 身份验证方法取决于已配置的操作或与令牌关联的操作。

|操作  |如何进行身份验证  |
  |---------|---------|
  |`content/delete`    | Azure CLI 中的 `az acr repository delete` |
  |`content/read`     |  `docker login`<br/><br/>Azure CLI 中的 `az acr login`  |
  |`content/write`     |  `docker login`<br/><br/>Azure CLI 中的 `az acr login`     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>Azure CLI 中的 `az acr repository show-manifests`   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>Azure CLI 中的 `az acr repository update` |

## <a name="examples-use-token"></a>示例：使用令牌

以下示例使用在本文前面创建的令牌对存储库执行常见操作：推送和拉取映像、删除映像以及列出存储库标记。 该令牌最初是用 `samples/hello-world` 存储库上的推送权限（`content/write` 和 `content/read` 操作）设置的。

### <a name="pull-and-tag-test-images"></a>请求和标记测试映像

对于以下示例，请从 Docker 中心拉取 `hello-world` 和 `alpine` 映像，并将其标记为注册表和存储库。

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>使用令牌进行身份验证

运行 `docker login` 以通过注册表进行身份验证，提供令牌名称作为用户名，并提供其密码之一。 令牌必须具有 `Enabled` 状态。

下面的示例为 bash shell 设置格式，并使用环境变量提供值。

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

输出应显示成功的身份验证：

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>将映像推送到注册表

成功登录后，尝试将标记的映像推送到注册表。 由于该令牌有权将映像推送到 `samples/hello-world` 存储库，因此以下推送成功：

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

该令牌无权访问 `samples/alpine` 存储库，因此，以下推送尝试将失败，并出现类似于 `requested access to the resource is denied`的错误：

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>更改推送/请求权限

若要更新令牌的权限，请更新相关联的作用域映射中的权限。 更新的范围映射会立即应用于所有关联的令牌。 

例如，在 `samples/alpine` 存储库上更新具有 `content/write` 和 `content/read` 操作的 `MyToken-scope-map`，并删除 `content/write` 存储库上的 `samples/hello-world` 操作。  

若要使用 Azure CLI，请运行[az acr scope map update][az-acr-scope-map-update]来更新作用域映射：

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

在 Azure 门户中：

1. 导航到容器注册表。
1. 在 "**服务**" 下，选择 "**范围映射（预览版）** "，然后选择要更新的范围映射。
1. 在 "**存储库**" 下，输入 `samples/alpine`，并在 "**权限**" 下选择 "`content/read` 和 `content/write`"。 然后选择 " **+ 添加**"。
1. 在 "**存储库**" 下，选择 "`samples/hello-world`" `content/write`，然后在 "**权限**" 再选择“保存”。

更新作用域映射后，以下推送成功：

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

由于作用域映射仅对 `samples/hello-world` 存储库具有 `content/read` 权限，因此，对 `samples/hello-world` 存储库的推送尝试现在会失败：
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

从这两个存储库中提取映像将成功，因为作用域映射提供两个存储库的 `content/read` 权限：

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>删除映像

通过将 `content/delete` 操作添加到 `alpine` 存储库来更新范围映射。 此操作允许删除存储库中的映像或删除整个存储库。

为简洁起见，我们只显示[az acr scope map update][az-acr-scope-map-update]命令来更新作用域映射：

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

若要使用门户更新作用域映射，请参阅上一节。

使用以下[az acr repository delete][az-acr-repository-delete]命令删除 `samples/alpine` 存储库。 若要删除图像或存储库，该令牌不会通过 `docker login`进行身份验证。 而是将令牌的名称和密码传递给命令。 下面的示例使用前面在本文中创建的环境变量：

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>显示存储库标记 

通过将 `metadata/read` 操作添加到 `hello-world` 存储库来更新范围映射。 此操作允许读取存储库中的清单和标记数据。

为简洁起见，我们只显示[az acr scope map update][az-acr-scope-map-update]命令来更新作用域映射：

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

若要使用门户更新作用域映射，请参阅上一节。

若要读取 `samples/hello-world` 存储库中的元数据，请运行[az acr 存储库 show-清单][az-acr-repository-show-manifests]或[az acr repository show-tags][az-acr-repository-show-tags]命令。 

若要读取元数据，令牌不通过 `docker login`进行身份验证。 而是将令牌的名称和密码传递到任一命令。 下面的示例使用前面在本文中创建的环境变量：

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

示例输出：

```console
[
  "v1"
]
```
## <a name="manage-tokens-and-scope-maps"></a>管理令牌和作用域映射

### <a name="list-scope-maps"></a>列出范围映射

使用[az acr scope map list][az-acr-scope-map-list]命令或门户中的 "**作用域地图（预览）** " 屏幕列出在注册表中配置的所有作用域映射。 例如：

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

输出显示你定义的作用域映射和多个可用于配置令牌的系统定义的作用域映射：

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>显示标记详细信息

若要查看令牌的详细信息（例如其状态和密码到期日期），请运行[az acr token show][az-acr-token-show]命令，或在门户的 "**令牌（预览）** " 屏幕中选择令牌。 例如：

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

使用[az acr token list][az-acr-token-list]命令或门户中的 "**令牌（预览）** " 屏幕列出在注册表中配置的所有令牌。 例如：

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>为令牌生成密码

如果没有令牌密码，或想要生成新密码，请运行[az acr token credential 生成][az-acr-token-credential-generate]命令。 

下面的示例为*MyToken*标记生成新的 password1 值，有效期为30天。 它在 `TOKEN_PWD`的环境变量中存储密码。 此示例的格式适用于 bash shell。

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

若要使用 Azure 门户生成令牌密码，请参阅本文前面的[创建令牌门户](#create-token---portal)中的步骤。

### <a name="update-token-with-new-scope-map"></a>用新的范围映射更新令牌

如果要使用不同的作用域映射来更新令牌，请运行[az acr token update][az-acr-token-update]并指定新的作用域映射。 例如：

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

在门户中的 "**令牌（预览）** " 屏幕上，选择令牌，然后在 "**作用域映射**" 下选择不同的作用域映射。

> [!TIP]
> 使用新的作用域映射更新令牌后，你可能需要生成新的令牌密码。 在 Azure 门户中使用[az acr token credential 生成][az-acr-token-credential-generate]命令或重新生成令牌密码。

## <a name="disable-or-delete-token"></a>禁用或删除令牌

可能需要临时禁用用户或服务的令牌凭据。 

使用 Azure CLI 运行[az acr token update][az-acr-token-update]命令，将 `status` 设置为 `disabled`：

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

在门户中的 "**令牌（预览）** " 屏幕中选择令牌，然后在 "**状态**" 下选择 "**禁用**"。

若要删除令牌以永久地使使用其凭据的任何人都无法访问，请运行[az acr token delete][az-acr-token-delete]命令。 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

在门户中的 "**令牌（预览）** " 屏幕中选择令牌，然后选择 "**放弃**"。

## <a name="next-steps"></a>后续步骤

* 若要管理作用域映射和标记，请使用[az acr 范围地图][az-acr-scope-map]和[az acr 标记][az-acr-token]命令组中的其他命令。
* 有关使用 Azure 容器注册表进行身份验证的其他选项，请参阅[身份验证概述](container-registry-authentication.md)，包括使用 Azure Active Directory 标识、服务主体或管理员帐户。


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az-acr-repository-show-manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az-acr-repository-delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-list]: /cli/azure/acr/token/#az-acr-token-list
[az-acr-token-delete]: /cli/azure/acr/token/#az-acr-token-delete
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
