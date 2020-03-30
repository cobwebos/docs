---
title: Azure 容器注册表中存储库的权限
description: 创建具有限定到注册表中特定存储库的权限的令牌，以拉取或推送图像，或执行其他操作
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 7d390bf4d97561e374c70f184534ac4f98a40611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444265"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>使用存储库范围权限创建令牌

本文介绍如何创建令牌和范围映射来管理容器注册表中的存储库范围权限。 通过创建令牌，注册表所有者可以为用户或服务提供具有作用域、有时间限制的存储库访问权限，以提取或推送图像或执行其他操作。 令牌提供的权限比其他注册表[身份验证选项](container-registry-authentication.md)更多，这些选项将权限范围限定在整个注册表中。 

创建令牌的方案包括：

* 允许具有单个令牌的 IoT 设备从存储库中提取映像
* 向外部组织提供对特定存储库的权限 
* 限制对组织中不同用户组的存储库访问。 例如，为构建面向特定存储库的图像的开发人员提供写入和读取访问权限，以及对这些存储库部署的团队的读取访问权限。

> [!IMPORTANT]
> 此功能目前以预览版提供，存在一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="preview-limitations"></a>预览版限制

* 此功能仅在**高级**容器注册表中可用。 有关注册表服务层和限制的信息，请参阅[Azure 容器注册表 SKU](container-registry-skus.md)。
* 当前无法将存储库范围的权限分配给 Azure 活动目录标识，例如服务主体或托管标识。

## <a name="concepts"></a>概念

要配置存储库范围的权限，请使用关联的*作用域映射*创建*令牌*。 

* **令牌**以及生成的密码允许用户使用注册表进行身份验证。 您可以为令牌密码设置到期日期，或随时禁用令牌。  

  使用令牌进行身份验证后，用户或服务可以执行一个或多个限定到一个或多个存储库*的操作*。

  |操作  |说明  | 示例 |
  |---------|---------|--------|
  |`content/delete`    | 从存储库中删除数据  | 删除存储库或清单 |
  |`content/read`     |  从存储库读取数据 |  拉取项目 |
  |`content/write`     |  将数据写入存储库     | 用于`content/read`推送项目 |
  |`metadata/read`    | 从存储库读取元数据   | 列出标记或清单 |
  |`metadata/write`     |  将元数据写入存储库  | 启用或禁用读取、写入或删除操作 |

* **范围映射**将应用于令牌的存储库权限分组，并可以重新应用于其他令牌。 每个令牌都与单个作用域映射相关联。 

   使用范围映射：

    * 配置多个具有与一组存储库具有相同权限的令牌
    * 在范围映射中添加或删除存储库操作或应用其他范围映射时更新令牌权限 

  Azure 容器注册表还提供多个系统定义的作用域映射，您可以应用，并对所有存储库具有固定权限。

下图显示了令牌和范围映射之间的关系。 

![注册表令牌和范围映射](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>先决条件

* **Azure CLI** - Azure CLI 命令用于创建和管理令牌，可在 Azure CLI 版本 2.0.76 或更高版本中提供。 运行 `az --version` 即可查找版本。 如果需要安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。
* **Docker** - 要使用注册表进行身份验证以拉取或推送映像，您需要本地 Docker 安装。 Docker 提供适用于 [macOS](https://docs.docker.com/docker-for-mac/)[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系统的安装说明。
* **容器注册表**- 如果没有，请在 Azure 订阅中创建高级容器注册表，或升级现有注册表。 例如，使用 [Azure 门户](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。 

## <a name="create-token---cli"></a>创建令牌 - CLI

### <a name="create-token-and-specify-repositories"></a>创建令牌并指定存储库

使用[az acr 令牌创建命令创建][az-acr-token-create]令牌。 创建令牌时，可以在每个存储库上指定一个或多个存储库和相关操作。 存储库不需要在注册表中。 要通过指定现有范围映射来创建令牌，请参阅下一节。

下面的示例在注册表*myregistry*中创建一个令牌，该令牌具有`samples/hello-world`以下对回购`content/write`的权限`content/read`： 和 。 默认情况下，该命令将默认令牌状态设置为`enabled`，但您可以随时将状态更新为`disabled`。

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

输出显示有关令牌的详细信息，包括两个生成的密码。 建议将密码保存在安全的地方，以便以后用于身份验证。 无法再次检索密码，但可以生成新密码。

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

输出包括有关创建命令的范围映射的详细信息。 您可以使用此处命名为`MyToken-scope-map`的范围映射将相同的存储库操作应用于其他令牌。 或者，稍后更新范围映射以更改关联令牌的权限。

### <a name="create-token-and-specify-scope-map"></a>创建令牌并指定范围映射

创建令牌的另一种方法是指定现有范围映射。 如果还没有范围映射，请首先通过指定存储库和相关操作创建一个范围映射。 然后，在创建令牌时指定范围映射。 

要创建范围映射，请使用[az acr 范围映射创建][az-acr-scope-map-create]命令。 以下命令在以前使用的`samples/hello-world`存储库上创建具有相同权限的范围映射。 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

运行[创建 az acr 令牌][az-acr-token-create]以创建令牌，指定*MyScopeMap*范围映射。 与前面的示例中一样，该命令将默认令牌状态设置为`enabled`。

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

输出显示有关令牌的详细信息，包括两个生成的密码。 建议将密码保存在安全的地方，以便以后用于身份验证。 无法再次检索密码，但可以生成新密码。

## <a name="create-token---portal"></a>创建令牌 - 门户

可以使用 Azure 门户创建令牌和范围映射。 与`az acr token create`CLI 命令一样，您可以应用现有范围映射，或者通过指定一个或多个存储库和相关操作来创建令牌时创建范围映射。 存储库不需要在注册表中。 

下面的示例创建一个令牌，并创建一个作用域映射，该映射在`samples/hello-world`存储库上具有`content/write`以下`content/read`权限： 和 。

1. 在门户中，导航到容器注册表。
1. 在 **"服务**"下，选择**标记（预览）> +添加**。
  ![在门户中创建令牌](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. 输入令牌名称。
1. 在 **"范围"映射**下，选择 **"创建新**"。
1. 配置范围映射：
    1. 输入范围映射的名称和说明。 
    1. 在 **"存储库**"下，输入`samples/hello-world`和 **"权限**"`content/read`下`content/write`，选择 和 。 然后选择 **+添加**。  
    ![在门户中创建范围映射](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. 添加存储库和权限后，选择 **"添加"** 以添加范围映射。
1. 接受默认令牌 **"已启用****"状态**，然后选择 **"创建**"。

验证并创建令牌后，令牌详细信息将显示在 **"令牌"** 屏幕中。

### <a name="add-token-password"></a>添加令牌密码

创建令牌后生成密码。 要对注册表进行身份验证，必须启用令牌并具有有效的密码。

您可以生成一个或两个密码，并为每个密码设置到期日期。 

1. 在门户中，导航到容器注册表。
1. 在 **"服务**"下，选择**令牌（预览），** 然后选择令牌。
1. 在令牌详细信息中，选择**密码1**或**密码2，** 然后选择"生成"图标。
1. 在密码屏幕中，可以选择设置密码的到期日期，然后选择 **"生成**"。
1. 生成密码后，将其复制并保存到安全位置。 关闭屏幕后无法检索生成的密码，但可以生成新密码。

    ![在门户中创建令牌密码](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>使用令牌进行身份验证

当用户或服务使用令牌对目标注册表进行身份验证时，它将令牌名称作为用户名及其生成的密码之一提供。 身份验证方法取决于配置的操作或与令牌关联的操作。

|操作  |如何进行身份验证  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete`在 Azure CLI 中 |
  |`content/read`     |  `docker login`<br/><br/>`az acr login`在 Azure CLI 中  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login`在 Azure CLI 中     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests`在 Azure CLI 中   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update`在 Azure CLI 中 |

## <a name="examples-use-token"></a>示例：使用令牌

以下示例使用本文前面创建的令牌对存储库执行常见操作：推送和拉取映像、删除映像和列表存储库标记。 令牌最初在`content/write``samples/hello-world`存储库上使用推送权限 （和`content/read`操作）进行设置。

### <a name="pull-and-tag-test-images"></a>拉取和标记测试图像

对于以下示例，请从`hello-world`Docker `alpine` Hub 中提取 和 映像，并为注册表和存储库标记它们。

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>使用令牌进行身份验证

运行`docker login`以使用注册表进行身份验证，将令牌名称作为用户名提供，并提供其密码之一。 令牌必须具有状态`Enabled`。

以下示例为 bash 外壳设置格式，并使用环境变量提供值。

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

成功登录后，尝试将标记的图像推送到注册表。 由于令牌具有将映像推送到`samples/hello-world`存储库的权限，因此以下推送成功：

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

令牌对`samples/alpine`回购没有权限，因此以下推送尝试失败，错误类似于`requested access to the resource is denied`：

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>更改推送/拉取权限

要更新令牌的权限，请更新关联作用域映射中的权限。 更新的范围映射将立即应用于所有关联的令牌。 

例如`MyToken-scope-map`，在`content/write``content/read``samples/alpine`存储库上更新 和 操作，并删除存储库`content/write`上`samples/hello-world`的操作。  

要使用 Azure CLI，请运行[az acr 范围映射更新][az-acr-scope-map-update]以更新范围映射：

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

在 Azure 门户中：

1. 导航到容器注册表。
1. 在 **"服务**"下，选择 **"范围映射"（预览），** 然后选择要更新的范围映射。
1. 在 **"存储库**"下，输入`samples/alpine`和 **"权限**"`content/read`下`content/write`，选择 和 。 然后选择 **+添加**。
1. 在 **"存储库**"下，选择`samples/hello-world`和 下 **"权限**"取消选择`content/write`。 再选择“保存”****。

更新范围映射后，以下推送将成功：

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

由于范围映射仅在`content/read``samples/hello-world`存储库上具有权限，因此推送到`samples/hello-world`存储库的尝试现在失败：
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

从两个存储库中拉取图像成功，因为范围映射为`content/read`两个存储库提供了权限：

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>删除映像

通过将操作添加到`content/delete``alpine`存储库来更新范围映射。 此操作允许删除存储库中的图像，或删除整个存储库。

为简洁起见，我们仅显示用于更新作用域映射的[az acr 范围映射更新][az-acr-scope-map-update]命令：

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

要使用门户更新范围映射，请参阅上一节。

使用以下[az acr 存储库删除][az-acr-repository-delete]命令删除`samples/alpine`存储库。 要删除图像或存储库，令牌不会通过`docker login`进行身份验证。 相反，将令牌的名称和密码传递给命令。 下面的示例使用本文前面创建的环境变量：

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>显示存储库标记 

通过将操作添加到`metadata/read``hello-world`存储库来更新范围映射。 此操作允许在存储库中读取清单和标记数据。

为简洁起见，我们仅显示用于更新作用域映射的[az acr 范围映射更新][az-acr-scope-map-update]命令：

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

要使用门户更新范围映射，请参阅上一节。

要读取存储库中的`samples/hello-world`元数据，请运行[az acr 存储库显示清单][az-acr-repository-show-manifests]或 az [acr 存储库显示标记][az-acr-repository-show-tags]命令。 

要读取元数据，令牌不会通过`docker login`进行身份验证。 相反，将令牌的名称和密码传递给任一命令。 下面的示例使用本文前面创建的环境变量：

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

使用[az acr 范围映射列表][az-acr-scope-map-list]命令或门户中的 **"范围映射（预览）"** 屏幕列出注册表中配置的所有作用域映射。 例如：

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

输出显示您定义的范围映射以及可用于配置令牌的多个系统定义的范围映射：

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>显示令牌详细信息

要查看令牌的详细信息（如其状态和密码到期日期），请运行[az acr 令牌显示][az-acr-token-show]命令，或在门户中的 **"令牌（预览）"** 屏幕中选择令牌。 例如：

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

使用[az acr 令牌列表][az-acr-token-list]命令或门户中的**令牌（预览）** 屏幕列出注册表中配置的所有令牌。 例如：

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>生成令牌密码

如果您没有令牌密码，或者想要生成新密码，请运行[az acr 令牌凭据生成][az-acr-token-credential-generate]命令。 

下面的示例为*MyToken 令牌*生成密码 1 的新值，过期期为 30 天。 它将密码存储在环境变量`TOKEN_PWD`中。 此示例为 bash 外壳格式化。

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

要使用 Azure 门户生成令牌密码，请参阅本文前面创建[令牌门户](#create-token---portal)中的步骤。

### <a name="update-token-with-new-scope-map"></a>使用新范围映射更新令牌

如果要使用不同的作用域映射更新令牌，请运行[az acr 令牌更新][az-acr-token-update]并指定新的作用域映射。 例如：

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

在门户中，在 **"令牌（预览）"** 屏幕上，选择令牌，并在 **"范围"映射**下选择其他范围映射。

> [!TIP]
> 使用新的作用域映射更新令牌后，可能需要生成新的令牌密码。 使用[az acr 令牌凭据生成][az-acr-token-credential-generate]命令或在 Azure 门户中重新生成令牌密码。

## <a name="disable-or-delete-token"></a>禁用或删除令牌

您可能需要暂时禁用用户或服务的令牌凭据的使用。 

使用 Azure CLI，运行[az acr 令牌更新][az-acr-token-update]命令`status`以`disabled`设置为 ：

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

在门户中，在 **"令牌（预览）"** 屏幕中选择令牌，并在 **"状态**"下选择 **"禁用**"。

要删除令牌以永久使使用其凭据的任何人的访问无效，请运行[az acr 令牌删除][az-acr-token-delete]命令。 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

在门户中，在 **"令牌（预览）"** 屏幕中选择令牌，然后选择 **"丢弃**"。

## <a name="next-steps"></a>后续步骤

* 要管理作用域映射和令牌，请使用[az acr 作用域映射][az-acr-scope-map]和 az [acr 令牌][az-acr-token]命令组中的其他命令。
* 有关使用 Azure 容器注册表进行身份验证的其他选项（包括使用 Azure 活动目录标识、服务主体或管理员帐户），请参阅[身份验证概述](container-registry-authentication.md)。


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
