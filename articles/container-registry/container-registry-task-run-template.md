---
title: 通过模板实现快速任务运行
description: 将 ACR 任务运行排入队列，以使用 Azure 资源管理器模板生成映像
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: 7ad40d2e925d5e1443af9bce4115d45b0e8c06e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "82927762"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>使用资源管理器模板运行 ACR 任务

[ACR 任务](container-registry-tasks-overview.md)是 Azure 容器注册表中的一套功能，可在整个容器生命周期内帮助管理和修改容器映像。 

本文演示了如何使用 Azure 资源管理器模板来将快速任务运行排入队列，这与使用 [az acr build][az-acr-build] 命令来手动创建任务运行类似。

用于将任务运行排入队列的资源管理器模板在自动化方案中很有用，它扩展了 `az acr build` 的功能。 例如：

* 使用模板创建容器注册表，并立即将任务运行排入队列，以生成并推送容器映像
* 创建或启用可在快速任务运行中使用的其他资源，例如 Azure 资源的托管标识

## <a name="limitations"></a>限制

* 必须为任务运行指定一个远程上下文（例如 GitHub 存储库）作为[源位置](container-registry-tasks-overview.md#context-locations)。 不能使用本地源上下文。
* 对于使用托管标识的任务运行，只允许使用用户分配的托管标识。

## <a name="prerequisites"></a>先决条件

* **GitHub 帐户** - 如果没有帐户，请在 https://github.com 上创建一个帐户。 
* **创建示例存储库分支** - 对于此处显示的任务示例，请使用 GitHub UI 在 GitHub 帐户中创建以下示例存储库分支： https://github.com/Azure-Samples/acr-build-helloworld-node 。 此存储库包含用于生成小型容器映像的示例 Dockerfile 和源代码。

## <a name="example-create-registry-and-queue-task-run"></a>示例：创建注册表并将任务运行排入队列

本示例使用[示例模板](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild)创建容器注册表，并将生成和推送映像的任务运行排入队列。 

### <a name="template-parameters"></a>模板参数

对于本示例，请提供以下模板参数的值：

|参数  |Value  |
|---------|---------|
|registryName     |所创建注册表的唯一名称         |
|repository     |用于生成任务的目标存储库        |
|taskRunName     |用于指定映像标记的任务运行的名称 |
|sourceLocation     |生成任务的远程上下文，例如 https://github.com/Azure-Samples/acr-build-helloworld-node 。 存储库根路径中的 Dockerfile 为小型 Node.js Web 应用生成容器映像。 如有需要，可以使用存储库的分支作为生成上下文。         |

### <a name="deploy-the-template"></a>部署模板

使用 [az deployment group create][az-deployment-group-create] 命令部署模板。 本示例将生成 helloworld-node:testrun 映像，并将该映像推送到名为 mycontainerregistry 的注册表中 。

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuild/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=testrun \
    sourceLocation=https://github.com/Azure-Samples/acr-build-helloworld-node.git
 ```

上一条命令在命令行上传递了参数。 如有需要，请将这些参数传递到[参数文件](../azure-resource-manager/templates/parameter-files.md)中。

### <a name="verify-deployment"></a>验证部署

部署成功完成后，请运行 [az acr repository show-tags][az-acr-repository-show-tags]，以验证映像是否已生成：

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

输出：

```console
Result
--------
testrun
```

### <a name="view-run-log"></a>查看运行日志

要查看有关任务运行的详细信息，请查看运行日志。

首先，使用 [az acr task list-runs][az-acr-task-list-runs] 获取运行 ID
```azurecli
az acr task list-runs \
  --registry mycontainerregistry --output table
```

输出类似于：

```console
RUN ID    TASK    PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ------  ----------  ---------  ---------  --------------------  ----------
ca1               linux       Succeeded  Manual     2020-03-23T17:54:28Z  00:00:48
```

运行 [az acr task logs][az-acr-task-logs] 以查看运行 ID 的任务运行日志，在本例中为 ca1：

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

输出显示任务运行日志。

还可以在 Azure 门户中查看任务运行日志。 

1. 导航到容器注册表
2. 在“服务”下，选择“任务” > “运行”  。
3. 选择运行 ID，在本例中为 ca1。 

门户将显示任务运行日志。

## <a name="example-task-run-with-managed-identity"></a>示例：具有托管标识的任务运行

使用[示例模板](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity)将启用用户分配托管标识的任务运行排入队列。 在任务运行期间，会对标识进行身份验证，以从其他 Azure 容器注册表中拉取映像。 

此方案与[在 ACR 任务中使用 Azure 托管标识进行跨注册表身份验证](container-registry-tasks-cross-registry-authentication.md)类似。 例如，一个组织可能维护一个集中注册表，其中包含许多基础映像，多个开发团队都会访问这些映像。

### <a name="prepare-base-registry"></a>准备基础注册表

出于演示目的，创建一个单独的容器注册表作为基础注册表，然后推送从 Docker Hub 拉取的 Node.js 基础映像。

1. 创建第二个容器注册表（例如 mybaseregistry），用于存储基础映像。
1. 从 Docker Hub 中拉取 `node:9-alpine` 映像，将其标记为基础映像，然后推送到基础注册表：

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>创建新的 Dockerfile

创建从基础注册表中拉取基础映像的 Dockerfile。 在 GitHub 存储库的本地分支中执行以下步骤，例如，`https://github.com/myGitHubID/acr-build-helloworld-node.git`。

1. 在 GitHub UI 中，选择“创建新文件”。
1. 将文件命名为 Dockerfile-test，并粘贴以下内容。 将注册表名称替换为 mybaseregistry。
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. 选择“提交新文件”。

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>为标识授予对基础注册表的提取权限

授予托管标识从基础注册表 mybaseregistry 中拉取映像的权限。

使用 [az acr show][az-acr-show] 命令获取基础注册表的资源 ID，并将其存储在变量中：

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

使用 [az role assignment create][az-role-assignment-create] 命令向基础注册表分配 Acrpull 角色标识。 此角色仅有权从该注册表提取映像。

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>模板参数

对于本示例，请提供以下模板参数的值：

|参数  |Value  |
|---------|---------|
|registryName     |在其中生成映像的注册表名称  |
|repository     |用于生成任务的目标存储库        |
|taskRunName     |用于指定映像标记的任务运行的名称 |
|userAssignedIdentity |在任务中启用的用户分配标识的资源 ID|
|customRegistryIdentity | 在任务中启用的用户分配标识的客户端 ID，用于向自定义注册表进行身份验证 |
|customRegistry |在任务中访问的自定义注册表的登录服务器名称，例如， *mybaseregistry.azurecr.io*|
|sourceLocation     |生成任务的远程上下文，例如 https://github.com/\<your-GitHub-ID\> /acr-build-helloworld-node。 |
|dockerFilePath | 远程上下文中 Dockerfile 的路径，用于生成映像。 |

### <a name="deploy-the-template"></a>部署模板

使用 [az deployment group create][az-deployment-group-create] 命令部署模板。 本示例将生成 helloworld-node:testrun 映像，并将该映像推送到名为 mycontainerregistry 的注册表中 。 基本映像是从 *mybaseregistry.azurecr.io*中提取的。

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=basetask \
    userAssignedIdentity=$resourceID \
    customRegistryIdentity=$clientID \
    sourceLocation=https://github.com/<your-GitHub-ID>/acr-build-helloworld-node.git \
    dockerFilePath=Dockerfile-test \
    customRegistry=mybaseregistry.azurecr.io
```

上一条命令在命令行上传递了参数。 如有需要，请将这些参数传递到[参数文件](../azure-resource-manager/templates/parameter-files.md)中。

### <a name="verify-deployment"></a>验证部署

部署成功完成后，请运行 [az acr repository show-tags][az-acr-repository-show-tags]，以验证映像是否已生成：

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

输出：

```console
Result
--------
basetask
```

### <a name="view-run-log"></a>查看运行日志

要查看运行日志，请参阅[前面部分](#view-run-log)中的步骤。

## <a name="next-steps"></a>后续步骤

 * 在 [ACR GitHub 存储库](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment)中查看更多模板示例。
 * 有关模板属性的详细信息，请参阅 [任务运行](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns) 和 [任务](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks)的模板参考。


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-logs]: /cli/azure/acr/task#az-acr-task-logs
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
