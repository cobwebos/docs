---
title: Azure 容器注册表中存储库的权限
description: 创建一个令牌，其权限范围限定于注册表中的特定存储库，用于拉取或推送映像或执行其他操作
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: eeb2155e035dd4a3a7aa09f634c229676cd87db3
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683470"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>创建具有存储库范围权限的令牌

本文介绍了如何创建令牌和范围映射，以便管理容器注册表中存储库范围内的权限。 通过创建令牌，注册表所有者可以为用户或服务提供范围限定于存储库且有时间限制的访问权限，用来拉取或推送映像或执行其他操作。 令牌提供的权限比其他注册表[身份验证选项](container-registry-authentication.md)更精细，后者指定的权限范围是整个注册表。 

适合创建令牌的场景包括：

* 允许具有单独令牌的 IoT 设备从存储库中拉取映像
* 向外部组织提供对特定存储库的权限 
* 针对组织中的不同用户组限制对存储库的访问权限。 例如，为面向特定存储库构建映像的开发人员提供写入和读取访问权限，为从这些存储库进行部署的团队提供读取访问权限。

> [!IMPORTANT]
> 此功能目前以预览版提供，存在一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="preview-limitations"></a>预览版限制

* 此功能仅在“高级”容器注册表中可用。 有关注册表服务层级和限制的信息，请参阅 [Azure 容器注册表服务层级](container-registry-skus.md)。
* 当前无法将存储库范围的权限分配给 Azure Active Directory 标识，例如服务主体或托管标识。
* 无法在启用了[匿名拉取访问](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)的注册表中创建范围映射。

## <a name="concepts"></a>概念

若要配置存储库范围内的权限，请创建具有关联的“范围映射”的“令牌” 。 

* 用户使用令牌与生成的密码对注册表进行身份验证。 你可以为令牌密码设置到期日期，或者随时禁用令牌。  

  使用令牌进行身份验证后，用户或服务可以执行范围限定于一个或多个存储库的一个或多个操作。

  |操作  |说明  | 示例 |
  |---------|---------|--------|
  |`content/delete`    | 从存储库中删除数据  | 删除存储库或清单 |
  |`content/read`     |  从存储库中读取数据 |  拉取项目 |
  |`content/write`     |  将数据写入存储库     | 与 `content/read` 一起使用以推送项目 |
  |`metadata/read`    | 从存储库中读取元数据   | 列出标记或清单 |
  |`metadata/write`     |  将元数据写入存储库  | 启用或禁用读取、写入或删除操作 |

* 范围映射会对你应用于令牌的存储库权限进行分组，并且可以重新应用于其他令牌。 每个令牌都与单个范围映射相关联。 

   使用范围映射可以执行以下操作：

    * 配置对一组存储库具有相同权限的多个令牌
    * 在范围映射中添加或删除存储库操作时更新令牌权限，或者在应用另一个范围映射时这样做 

  Azure 容器注册表还提供了多个系统定义的范围映射，你可以应用这些映射，它们在所有存储库中具有固定的权限。

下图显示了令牌与范围映射之间的关系。 

![注册表令牌和范围映射](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>先决条件

* **Azure CLI** - Azure CLI 2.0.76 或更高版本中提供了用于创建和管理令牌的 Azure CLI 命令。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。
* **Docker** - 若要对注册表进行身份验证以拉取或推送映像，你需要安装一个本地 Docker。 Docker 提供适用于 [macOS](https://docs.docker.com/docker-for-mac/)[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系统的安装说明。
* **容器注册表** - 如果没有，请在你的 Azure 订阅中创建一个高级容器注册表，或升级现有注册表。 例如，使用 [Azure 门户](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。 

## <a name="create-token---cli"></a>创建令牌 - CLI

### <a name="create-token-and-specify-repositories"></a>创建令牌并指定存储库

可使用 [az acr token create][az-acr-token-create] 命令创建令牌。 创建令牌时，可以指定一个或多个存储库，并在每个存储库上指定关联的操作。 存储库暂时不需要在注册表中。 若要指定现有范围映射来创建令牌，请参阅下一部分。

下面的示例在注册表 myregistry 中创建一个令牌，该令牌对 `samples/hello-world` 存储库具有以下权限：`content/write` 和 `content/read`。 默认情况下，该命令会将默认令牌状态设置为 `enabled`，但你随时可以将状态更新为 `disabled`。

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

输出会显示有关令牌的详细信息，包括生成的两个密码。 建议将密码保存在安全的位置，以便以后将其用于身份验证。 无法再次检索这些密码，但可以生成新密码。

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

输出包含该命令创建的范围映射的详细信息。 可以使用范围映射（在此处名为 `MyToken-scope-map`）将相同的存储库操作应用于其他令牌。 或者，稍后更新范围映射以更改关联的令牌的权限。

### <a name="create-token-and-specify-scope-map"></a>创建令牌并指定范围映射

创建令牌的另一种方法是指定现有的范围映射。 如果还没有范围映射，请先指定存储库和关联的操作来创建一个。 然后，在创建令牌时指定该范围映射。 

若要创建范围映射，请使用 [az acr scope-map create][az-acr-scope-map-create] 命令。 以下命令创建的范围映射对前面使用的 `samples/hello-world` 存储库具有相同权限。 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

运行 [az acr token create][az-acr-token-create] 来创建令牌，同时指定 MyScopeMap 范围映射。 与前面的示例一样，该命令会将默认令牌状态设置为 `enabled`。

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

输出会显示有关令牌的详细信息，包括生成的两个密码。 建议将密码保存在安全的位置，以便以后将其用于身份验证。 无法再次检索这些密码，但可以生成新密码。

## <a name="create-token---portal"></a>创建令牌 - 门户

你可以使用 Azure 门户来创建令牌和范围映射。 与 `az acr token create` CLI 命令一样，你可以应用现有的作范围映射，也可以在创建令牌时通过指定一个或多个存储库和关联的操作创建范围映射。 存储库暂时不需要在注册表中。 

下面的示例创建了一个令牌和一个范围映射，后者具有对 `samples/hello-world` 存储库的以下权限：`content/write` 和 `content/read`。

1. 在门户中，导航到容器注册表。
1. 在“服务”下，选择“令牌(预览)”>“+添加” 。
  ![在门户中创建令牌](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. 输入令牌名称。
1. 在“范围映射”下，选择“新建” 。
1. 配置范围映射：
    1. 为范围映射输入名称和说明。 
    1. 在“存储库”下，输入 `samples/hello-world`，并在“权限”下，选择 `content/read` 和 `content/write` 。 然后选择“+添加”。  
    ![在门户中创建范围映射](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. 添加存储库和权限后，选择“添加”，以添加范围映射。
1. 接受默认令牌状态（“启用”），然后选择“创建”  。

验证并创建令牌后，令牌详细信息将显示在“令牌”屏幕中。

### <a name="add-token-password"></a>添加令牌密码

创建令牌后生成密码。 要对注册表进行身份验证，必须启用该令牌并使用有效密码。

你可以生成一个或两个密码，并为每个密码设置到期日期。 

1. 在门户中，导航到容器注册表。
1. 在“服务”下，选择“令牌(预览)”，然后选择一个令牌 。
1. 在“令牌详细信息”中，选择“password1”或“password2”，然后选择“生成”图标 。
1. 在“密码”屏幕上，根据需要为密码设置过期日期，并选择“生成”。
1. 生成密码后，将其复制到一个安全的位置并保存。 关闭屏幕后无法检索生成的密码，但可以生成新的密码。

    ![在门户中创建令牌密码](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>使用令牌进行身份验证

当用户或服务使用令牌对目标注册表进行身份验证时，它会提供令牌名称作为用户名，以及生成的其中一个密码。 身份验证方法取决于已配置的操作或与令牌关联的操作。

|操作  |如何进行身份验证  |
  |---------|---------|
  |`content/delete`    | Azure CLI 中的 `az acr repository delete` |
  |`content/read`     |  `docker login`<br/><br/>Azure CLI 中的 `az acr login`  |
  |`content/write`     |  `docker login`<br/><br/>Azure CLI 中的 `az acr login`     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>Azure CLI 中的 `az acr repository show-manifests`   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>Azure CLI 中的 `az acr repository update` |

## <a name="examples-use-token"></a>示例：使用令牌

以下示例使用之前在本文中创建的令牌对存储库执行常见操作：推送和拉取映像、删除映像以及列出存储库标记。 该令牌最初设置有针对 `samples/hello-world` 存储库的推送权限（`content/write` 和 `content/read` 操作）。

### <a name="pull-and-tag-test-images"></a>请求和标记测试映像

对于以下示例，请从 Docker Hub 拉取 `hello-world` 和 `alpine` 映像，并针对注册表和存储库对其进行标记。

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>使用令牌进行身份验证

运行 `docker login` 以对注册表进行身份验证，提供令牌名称作为用户名，并提供它的其中一个密码。 令牌必须具有 `Enabled` 状态。

下面的示例针对 bash shell 进行了格式设置，并使用环境变量提供值。

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

输出应显示身份验证成功：

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>将映像推送到注册表

成功登录后，尝试将标记的映像推送到注册表。 由于该令牌有权将映像推送到 `samples/hello-world` 存储库，因此以下推送成功：

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

该令牌无权访问 `samples/alpine` 存储库，因此，以下推送尝试将失败，并显示类似于 `requested access to the resource is denied` 的错误：

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>更改推送/请求权限

若要更新令牌的权限，请更新相关联的范围映射中的权限。 更新的范围映射会立即应用于所有关联的令牌。 

例如，使用对 `samples/alpine` 存储库的 `content/write` 和 `content/read` 操作更新 `MyToken-scope-map`，并删除对 `samples/hello-world` 存储库的 `content/write` 操作。  

若要使用 Azure CLI，请运行 [az acr scope map update][az-acr-scope-map-update] 来更新范围映射：

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

在 Azure 门户中：

1. 导航到容器注册表。
1. 在“服务”下，选择“范围映射(预览)”，然后选择要更新的范围映射 。
1. 在“存储库”下，输入 `samples/alpine`，并在“权限”下，选择 `content/read` 和 `content/write` 。 然后选择“+添加”。
1. 在“存储库”下，选择 `samples/hello-world`，并在“权限”下，取消选择 `content/write` 。 再选择“保存”。

范围映射更新后，以下推送成功：

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

由于范围映射仅具有对 `samples/hello-world` 存储库的 `content/read` 权限，因此，对 `samples/hello-world` 存储库的推送尝试现在会失败：
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

从这两个存储库中提取映像将成功，因为范围映射提供对两个存储库的 `content/read` 权限：

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>删除映像

通过将 `content/delete` 操作添加到 `alpine` 存储库可以更新范围映射。 此操作允许删除存储库中的映像或删除整个存储库。

为简洁起见，我们只显示用于更新范围映射的 [az acr scope map update][az-acr-scope-map-update] 命令：

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

若要使用门户更新范围映射，请参阅上一部分。

使用以下 [az acr repository delete][az-acr-repository-delete] 命令删除 `samples/alpine` 存储库。 若要删除映像或存储库，令牌不能通过 `docker login` 进行身份验证。 而是需要将令牌的名称和密码传递给命令。 下面的示例使用之前在本文中创建的环境变量：

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>显示存储库标记 

将 `metadata/read` 操作添加到 `hello-world` 存储库可以更新范围映射。 此操作允许读取存储库中的清单和标记数据。

为简洁起见，我们只显示用于更新范围映射的 [az acr scope map update][az-acr-scope-map-update] 命令：

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

若要使用门户更新范围映射，请参阅上一部分。

若要读取 `samples/hello-world` 存储库中的元数据，请运行 [az acr repository show-manifests][az-acr-repository-show-manifests] 或 [az acr repository show-tags][az-acr-repository-show-tags] 命令。 

若要读取元数据，令牌不能通过 `docker login` 进行身份验证。 而是需要将令牌的名称和密码传给任一命令。 下面的示例使用之前在本文中创建的环境变量：

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
## <a name="manage-tokens-and-scope-maps"></a>管理令牌和范围映射

### <a name="list-scope-maps"></a>列出范围映射

使用 [az acr scope-map list][az-acr-scope-map-list] 命令或门户中的“范围映射(预览)”屏幕，列出在注册表中配置的所有范围映射。 例如：

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

输出会显示你定义的范围映射，此外还会显示多个系统定义的供你用来配置令牌的范围映射：

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>显示令牌详细信息

若要查看某个令牌的详细信息（例如其状态和密码到期日期），请运行 [az acr token show][az-acr-token-show] 命令，或在门户中的“令牌(预览)”屏幕中选择该令牌。 例如：

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

使用门户中的 [az acr token list][az-acr-token-list] 命令或“令牌(预览)”屏幕列出注册表中配置的所有令牌。 例如：

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>为令牌生成密码

如果没有令牌密码，或者要生成新密码，请运行 [az acr token credential generate][az-acr-token-credential-generate] 命令。 

下面的示例为 MyToken 令牌的 password1 生成新值，有效期为 30 天。 它将密码存储在环境变量 `TOKEN_PWD` 中。 此示例的格式是针对 bash shell 设置的。

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

要使用 Azure 门户生成令牌密码，请参阅本文前面的[创建令牌 - 门户](#create-token---portal)中的步骤。

### <a name="update-token-with-new-scope-map"></a>使用新的范围映射更新令牌

如果要使用不同的范围映射来更新令牌，请运行 [az acr token update][az-acr-token-update] 并指定新的范围映射。 例如：

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

在门户中的“令牌(预览)”屏幕上，选择相应令牌，然后在“范围映射”下，选择一个不同的范围映射 。

> [!TIP]
> 使用新的范围映射更新令牌后，建议生成新的令牌密码。 请使用 [az acr token credential 生成][az-acr-token-credential-generate] 命令或在 Azure 门户中重新生成令牌密码。

## <a name="disable-or-delete-token"></a>禁用或删除令牌

建议临时对用户或服务禁用令牌凭据。 

使用 Azure CLI 运行 [az acr token update][az-acr-token-update] 命令，将 `status` 设置为 `disabled`：

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

在门户中的“令牌(预览)”屏幕中选择令牌，然后在“状态”下选择“禁用”  。

若要删除令牌以使利用其凭据的任何人的访问权限永久失效，请运行 [az acr token delete][az-acr-token-delete] 命令。 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

在门户中的“令牌(预览)”屏幕中选择令牌，然后选择“禁用” 。

## <a name="next-steps"></a>后续步骤

* 若要管理范围映射和令牌，请使用 [az acr scope map][az-acr-scope-map] 和 [az acr token][az-acr-token] 命令组中的其他命令。
* 请参阅[身份验证概述](container-registry-authentication.md)，了解对 Azure 容器注册表进行身份验证的其他选项，包括使用 Azure Active Directory 标识、服务主体或管理员帐户。


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
