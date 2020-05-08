---
title: 通过模板运行快速任务
description: 使用 Azure 资源管理器模板对 ACR 任务运行排队以生成映像
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: 7ad40d2e925d5e1443af9bce4115d45b0e8c06e1
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927762"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>使用资源管理器模板运行 ACR 任务

[ACR 任务](container-registry-tasks-overview.md)是 Azure 容器注册表中的一套功能，可在整个容器生命周期内帮助管理和修改容器映像。 

本文介绍 Azure 资源管理器模板示例，用于对快速任务运行进行排队，这类似于可以使用[az acr build][az-acr-build]命令手动创建的模板。

用于对任务运行进行排队的资源管理器模板在自动化方案中非常有用，它扩展`az acr build`了的功能。 例如：

* 使用模板创建容器注册表并立即对任务运行进行排队以生成并推送容器映像
* 创建或启用可在快速任务运行中使用的其他资源，例如 Azure 资源的托管标识

## <a name="limitations"></a>限制

* 您必须指定远程上下文，例如 GitHub 存储库作为任务运行的[源位置](container-registry-tasks-overview.md#context-locations)。 不能使用本地源上下文。
* 对于使用托管标识运行的任务，只允许使用*用户分配*的托管标识。

## <a name="prerequisites"></a>必备条件

* **GitHub 帐户**- https://github.com如果你还没有帐户，则创建一个。 
* **分叉示例存储库**-对于此处显示的任务示例，请使用 github UI 将以下示例存储库加入 github 帐户： https://github.com/Azure-Samples/acr-build-helloworld-node。 此存储库包含用于构建小型容器映像的示例 Dockerfile 和源代码。

## <a name="example-create-registry-and-queue-task-run"></a>示例：创建注册表和队列任务运行

此示例使用[示例模板](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild)创建容器注册表，并对生成并推送图像的任务运行进行排队。 

### <a name="template-parameters"></a>模板参数

对于此示例，请为以下模板参数提供值：

|参数  |值  |
|---------|---------|
|registryName     |创建的注册表的唯一名称         |
|repository     |生成任务的目标存储库        |
|taskRunName     |任务运行的名称，指定图像标记 |
|sourceLocation     |生成任务的远程上下文，例如https://github.com/Azure-Samples/acr-build-helloworld-node。 存储库根中的 Dockerfile 为小型 node.js web 应用生成容器映像。 如果需要，请使用存储库的分叉作为生成上下文。         |

### <a name="deploy-the-template"></a>部署模板

用[az deployment group create][az-deployment-group-create]命令部署模板。 此示例生成*helloworld： testrun*映像并将其推送到名为*mycontainerregistry*的注册表。

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

前面的命令将参数传递到命令行。 如果需要，请在[参数文件](../azure-resource-manager/templates/parameter-files.md)中传递它们。

### <a name="verify-deployment"></a>验证部署

部署成功完成后，请运行[az acr 存储库显示标记][az-acr-repository-show-tags]来验证映像是否已生成：

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

若要查看有关任务运行的详细信息，请查看运行日志。

首先，通过[az acr 任务列表][az-acr-task-list-runs]获取运行 ID-运行
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

运行[az acr task logs][az-acr-task-logs]查看运行 ID 的任务运行日志，在本例中为*ca1*：

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

输出显示任务运行日志。

你还可以在 Azure 门户中查看任务运行日志。 

1. 导航到容器注册表
2. 在 "**服务**" 下，选择 "**任务** > " "**运行**"。
3. 选择运行 ID，在本例中为*ca1*。 

门户会显示 "任务运行日志"。

## <a name="example-task-run-with-managed-identity"></a>示例：任务以托管标识运行

使用[示例模板](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity)对启用用户分配的托管标识的任务运行进行排队。 在任务运行期间，标识将进行身份验证，从另一个 Azure 容器注册表中提取映像。 

此方案类似于[使用 Azure 托管标识的 ACR 任务中的跨注册表身份验证](container-registry-tasks-cross-registry-authentication.md)。 例如，组织可能会保留一个集中式注册表，其中包含由多个开发团队访问的基本映像。

### <a name="prepare-base-registry"></a>准备基础注册表

为了便于演示，请创建一个单独的容器注册表作为基本注册表，并推送从 Docker 中心拉取的 node.js 基映像。

1. 创建另一个容器注册表（例如*mybaseregistry*）来存储基本映像。
1. 从 Docker `node:9-alpine`中心拉取映像，为基本注册表标记该映像，并将其推送到基本注册表：

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>创建新的 Dockerfile

创建从基本注册表提取基本映像的 Dockerfile。 在 GitHub 存储库的本地分支中执行以下步骤，例如`https://github.com/myGitHubID/acr-build-helloworld-node.git`。

1. 在 GitHub UI 中，选择 "**新建文件**"。
1. 将文件命名*为 "Dockerfile* "，并粘贴以下内容。 将*mybaseregistry*的注册表名称替换为。
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. 选择 "**提交新文件**"。

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>为标识授予对基础注册表的提取权限

向托管标识授予从基本注册表*mybaseregistry*请求的权限。

使用 [az acr show][az-acr-show] 命令获取基础注册表的资源 ID，并将其存储在变量中：

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

使用[az role assign create][az-role-assignment-create]命令将 Acrpull 角色的标识分配给基本注册表。 此角色仅有权从该注册表提取映像。

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>模板参数

对于此示例，请为以下模板参数提供值：

|参数  |值  |
|---------|---------|
|registryName     |生成映像的注册表的名称  |
|repository     |生成任务的目标存储库        |
|taskRunName     |任务运行的名称，指定图像标记 |
|userAssignedIdentity |任务中已启用用户分配的标识的资源 ID|
|customRegistryIdentity | 任务中已启用用户分配的标识的客户端 ID，用于通过自定义注册表进行身份验证 |
|customRegistry |在任务中访问的自定义注册表的登录服务器名称，例如， *mybaseregistry.azurecr.io*|
|sourceLocation     |生成任务的远程上下文，例如* https://github.com/\<your-GitHub-ID\>/acr-build-helloworld-node。* |
|dockerFilePath | 用于生成映像的远程上下文中的 Dockerfile 的路径。 |

### <a name="deploy-the-template"></a>部署模板

用[az deployment group create][az-deployment-group-create]命令部署模板。 此示例生成*helloworld： testrun*映像并将其推送到名为*mycontainerregistry*的注册表。 基本映像是从*mybaseregistry.azurecr.io*中提取的。

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

前面的命令将参数传递到命令行。 如果需要，请在[参数文件](../azure-resource-manager/templates/parameter-files.md)中传递它们。

### <a name="verify-deployment"></a>验证部署

部署成功完成后，请运行[az acr 存储库显示标记][az-acr-repository-show-tags]来验证映像是否已生成：

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

若要查看运行日志，请参阅[上一节](#view-run-log)中的步骤。

## <a name="next-steps"></a>后续步骤

 * 请参阅[ACR GitHub](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment)存储库中的更多模板示例。
 * 有关模板属性的详细信息，请参阅[任务运行](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns)和[任务](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks)的模板参考。


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
