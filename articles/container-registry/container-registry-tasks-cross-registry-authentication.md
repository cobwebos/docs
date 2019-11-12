---
title: Azure 容器注册表任务的跨注册表身份验证
description: 使用 Azure 资源的托管标识配置 Azure 容器注册表任务（ACR 任务）以访问其他专用 Azure 容器注册表
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/12/2019
ms.author: danlep
ms.openlocfilehash: f2ffb42ce109f5e6f7186461f931b7f8da57ff32
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931517"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>ACR 任务中使用 Azure 托管标识的跨注册表身份验证 

在 [ACR 任务](container-registry-tasks-overview.md)中，可以[启用 Azure 资源的托管标识](container-registry-tasks-authentication-managed-identity.md)。 该任务可以使用该标识来访问其他 Azure 资源，而无需提供或管理凭据。 

本文介绍如何在任务中启用托管标识。该任务不是从运行它的注册表提取映像，而是从其他注册表提取。

为了创建 Azure 资源，本文要求运行 Azure CLI 版本 2.0.68 或更高版本。 可以运行 `az --version` 来查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

## <a name="scenario-overview"></a>方案概述

该示例任务从另一 Azure 容器注册表提取基础映像，以生成和推送应用程序映像。 若要提取基础映像，请使用托管标识配置任务，并为其分配适当的权限。 

此示例演示了使用用户分配的或系统分配的托管标识的步骤。 选择哪种标识取决于组织的需求。

在实际方案中，组织可能会维护一组基础映像，供所有开发团队用来生成应用程序。 这些基础映像存储在企业注册表中，每个开发团队对该注册表仅拥有提取权限。 

## <a name="prerequisites"></a>先决条件

在本文中，需要准备好两个 Azure 容器注册表：

* 第一个注册表用于创建和执行 ACR 任务。 在本文中，此注册表名为 *myregistry*。 
* 第二个注册表托管由任务用来生成映像的基础映像。 在本文中，第二个注册表名为 *mybaseregistry*。 

在后续步骤中，请将其替换为自己的注册表名称。

如果还没有所需的 Azure 容器注册表，请参阅[快速入门：使用 Azure CLI 创建专用容器注册表](container-registry-get-started-azure-cli.md)。 暂时不需要将映像推送到注册表。

## <a name="prepare-base-registry"></a>准备基础注册表

首先创建一个工作目录，然后创建包含以下内容的名为 Dockerfile 的文件。 此简单示例基于 Docker Hub 中的公共映像生成一个 Node.js 基础映像。
    
```bash
echo FROM node:9-alpine > Dockerfile
```
在当前目录中运行 [az acr build][az-acr-build] 命令，生成基础映像并将其推送到基础注册表。 在实践中，组织中的另一个团队或流程可能会维护基础注册表。
    
```azurecli
az acr build --image baseimages/node:9-alpine --registry mybaseregistry --file Dockerfile .
```

## <a name="define-task-steps-in-yaml-file"></a>在 YAML 文件中定义任务步骤

此示例[多步骤任务](container-registry-tasks-multi-step.md)的步骤在一个 [YAML 文件](container-registry-tasks-reference-yaml.md)中定义。 在本地工作目录中创建名为 `helloworldtask.yaml` 的文件，并粘贴以下内容。 使用基础注册表的服务器名称更新生成步骤中的 `REGISTRY_NAME` 值。

```yml
version: v1.0.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}  https://github.com/Azure-Samples/acr-build-helloworld-node.git -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

生成步骤使用 `Dockerfile-app`Azure-Samples/acr-build-helloworld-node[ 存储库中的 ](https://github.com/Azure-Samples/acr-build-helloworld-node.git) 文件生成映像。 `--build-arg` 引用基础注册表以提取基础映像。 成功生成后，该映像将推送到用于运行该任务的注册表。

## <a name="option-1-create-task-with-user-assigned-identity"></a>选项1：创建具有用户分配的标识的任务

本部分中的步骤将创建一个任务并启用用户分配的标识。 如果要改为启用系统分配的标识，请参阅[选项2：使用系统分配的标识创建任务](#option-2-create-task-with-system-assigned-identity)。 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>创建任务

执行以下 *az acr task create* 命令创建 [helloworldtask][az-acr-task-create] 任务。 该任务无需源代码上下文即可运行，该命令将引用工作目录中的 `helloworldtask.yaml` 文件。 `--assign-identity` 参数传递用户分配的标识的资源 ID。 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>选项2：创建包含系统分配的标识的任务

本部分中的步骤将创建一个任务并启用系统分配的标识。 如果要改为启用用户分配的标识，请参阅[选项1：使用用户分配的标识创建任务](#option-1-create-task-with-user-assigned-identity)。 

### <a name="create-task"></a>创建任务

执行以下 *az acr task create* 命令创建 [helloworldtask][az-acr-task-create] 任务。 该任务无需源代码上下文即可运行，该命令将引用工作目录中的 `helloworldtask.yaml` 文件。 不带任何值的 `--assign-identity` 参数将在任务中启用系统分配的标识。 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="give-identity-pull-permissions-to-the-base-registry"></a>为标识授予对基础注册表的提取权限

在本部分，为托管标识授予从基础注册表 *mybaseregistry* 提取映像的权限。

使用 [az acr show][az-acr-show] 命令获取基础注册表的资源 ID，并将其存储在变量中：

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

使用 [az role assignment create][az-role-assignment-create] 命令为标识分配对基础注册表的 `acrpull` 角色。 此角色仅有权从该注册表提取映像。

```azurecli
az role assignment create --assignee $principalID --scope $baseregID --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>将目标注册表凭据添加到任务

现在，请使用 [az acr task credential add][az-acr-task-credential-add] 命令将标识的凭据添加到任务，使该标识可在基础注册表中进行身份验证。 根据在任务中启用的托管标识类型运行相应的命令。 如果启用了用户分配的标识，请传递包含标识客户端 ID 的 `--use-identity`。 如果启用了系统分配的标识，请传递 `--use-identity [system]`。

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>手动运行任务

若要验证启用了托管标识的任务是否成功运行，请使用 [az acr task run][az-acr-task-run] 命令手动触发该任务。 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

如果任务成功运行，则输出将类似于：

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
9-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 9-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

运行 [az acr repository show-tags][az-acr-repository-show-tags] 命令，验证映像是否已生成并成功推送到 *myregistry*：

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

示例输出：

```console
cf10
```

## <a name="next-steps"></a>后续步骤

* 详细了解如何[在 ACR 任务中启用托管标识](container-registry-tasks-authentication-managed-identity.md)。
* 参阅 [ACR 任务 YAML 参考](container-registry-tasks-reference-yaml.md)

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
