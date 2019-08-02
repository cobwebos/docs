---
title: Azure 容器注册表任务中的外部身份验证
description: 为 azure 容器注册表 (ACR) 任务中的 Azure 资源启用托管标识, 以允许任务读取存储在 Azure 密钥保管库中的 Docker 中心凭据。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/12/2019
ms.author: danlep
ms.openlocfilehash: 6aa729e4f32769ec50632bea582c8b69c7c0ce91
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642132"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>使用 Azure 托管标识的 ACR 任务中的外部身份验证 

在[ACR 任务](container-registry-tasks-overview.md)中, 可以[为 Azure 资源启用托管标识](container-registry-tasks-authentication-managed-identity.md)。 该任务可以使用该标识来访问其他 Azure 资源, 而无需提供或管理凭据。 

本文介绍如何在访问 Azure 密钥保管库中存储的密钥的任务中启用托管标识。 

若要创建 Azure 资源, 本文要求运行 Azure CLI 版本2.0.68 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

## <a name="scenario-overview"></a>方案概述

示例任务读取存储在 Azure 密钥保管库中的 Docker 中心凭据。 凭据适用于对 Docker 中心中的专用存储库具有写入 (推送) 权限的 Docker 中心帐户。 若要读取凭据, 请使用托管标识配置该任务, 并为其分配适当的权限。 与标识关联的任务将生成一个映像, 并登录到 Docker Hub, 将映像推送到专用存储库。 

此示例演示使用用户分配的托管标识或系统分配的托管标识的步骤。 你选择的标识取决于你的组织的需求。

在实际方案中, 公司可以将图像作为生成过程的一部分发布到 Docker 中心内的专用存储库。 

## <a name="prerequisites"></a>先决条件

需要一个 Azure 容器注册表, 用于运行任务。 在本文中, 此注册表名为*myregistry*。 在后面的步骤中将替换为自己的注册表名称。

如果还没有 Azure 容器注册表, 请参阅[快速入门:使用 Azure CLI 创建专用容器注册表](container-registry-get-started-azure-cli.md)。 你还不需要将映像推送到注册表。

还需要 Docker Hub 中的专用存储库, 以及有权写入存储库的 Docker 中心帐户。 在此示例中, 此存储库名为*hubuser/hubrepo*。 

## <a name="create-a-key-vault-and-store-secrets"></a>创建密钥保管库并存储机密

首先, 如果需要, 请在*eastus*位置创建一个名为*myResourceGroup*的资源组, 并在其中包含以下[az group create][az-group-create]命令:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用[az keyvault create][az-keyvault-create]命令创建密钥保管库。 请确保指定唯一的密钥保管库名称。 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

使用[az keyvault secret set][az-keyvault-secret-set]命令将所需的 Docker 中心凭据存储在密钥保管库中。 在这些命令中, 在环境变量中传递值:

```azurecli
# Store Docker Hub user name
az keyvault secret set \
  --name UserName \
  --value $USERNAME \
  --vault-name mykeyvault

# Store Docker Hub password
az keyvault secret set \
  --name Password \
  --value $PASSWORD \
  --vault-name mykeyvault
```

在实际方案中, 可能会在单独的进程中设置和维护密码。

## <a name="define-task-steps-in-yaml-file"></a>在 YAML 文件中定义任务步骤

此示例任务的步骤在[YAML 文件](container-registry-tasks-reference-yaml.md)中定义。 在本地工作目录`dockerhubtask.yaml`中创建一个名为的文件并粘贴以下内容。 请确保将该文件中的密钥保管库名称替换为你的密钥保管库的名称。

```yml
version: v1.0.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: username
    keyvault: https://mykeyvault.vault.azure.net/secrets/UserName
  - id: password
    keyvault: https://mykeyvault.vault.azure.net/secrets/Password
steps:
# Log in to Docker Hub
  - cmd: docker login --username '{{.Secrets.username}}' --password '{{.Secrets.password}}'
# Build image
  - build: -t {{.Values.PrivateRepo}}:{{.Run.ID}} https://github.com/Azure-Samples/acr-tasks.git -f hello-world.dockerfile
# Push image to private repo in Docker Hub
  - push:
    - {{.Values.PrivateRepo}}:{{.Run.ID}}
```

任务步骤执行以下操作:

* 管理用于通过 Docker 中心进行身份验证的机密凭据。
* 通过向`docker login`命令传递机密来向 Docker 中心进行身份验证。
* 使用[Azure 示例/acr](https://github.com/Azure-Samples/acr-tasks.git)存储库中的示例 Dockerfile 生成映像。
* 将映像推送到专用 Docker 中心存储库。

## <a name="option-1-create-task-with-user-assigned-identity"></a>选项 1：创建具有用户分配的标识的任务

本部分中的步骤创建任务并启用用户分配的标识。 如果要改为启用系统分配的标识, 请参阅[选项 2:用系统分配的标识](#option-2-create-task-with-system-assigned-identity)创建任务。 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>创建任务

通过执行以下[az acr task create][az-acr-task-create]命令创建任务*dockerhubtask* 。 任务上下文是本地系统, 命令引用工作目录中的文件`dockerhubtask.yaml` 。 `--assign-identity`参数传递用户分配的标识的资源 ID。 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>选项 2：用系统分配的标识创建任务

本部分中的步骤将创建一个任务, 并启用系统分配的标识。 如果要改为启用用户分配的标识, 请参阅[选项 1:创建具有用户分配的标识](#option-1-create-task-with-user-assigned-identity)的任务。 

### <a name="create-task"></a>创建任务

通过执行以下[az acr task create][az-acr-task-create]命令创建任务*dockerhubtask* 。 任务上下文是本地系统, 命令引用工作目录中的文件`dockerhubtask.yaml` 。  不`--assign-identity`带值的参数可对任务启用系统分配的标识。  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="grant-identity-access-to-key-vault"></a>向密钥保管库授予标识访问权限

运行以下[az keyvault set-policy][az-keyvault-set-policy]命令来设置密钥保管库上的访问策略。 以下示例允许标识读取密钥保管库中的机密。 

```azurecli
az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

## <a name="manually-run-the-task"></a>手动运行任务

若要验证启用了托管标识的任务是否成功运行, 请使用[az acr task run][az-acr-task-run]命令手动触发该任务。 `--set`参数用于将专用存储库名称传递给任务。 在此示例中, 占位符存储库名称为*hubuser/hubrepo*。

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo 
```

如果任务成功运行, 则输出显示对 Docker 中心的身份验证成功, 并且映像已成功生成并推送到专用存储库:

```console
Queued a run with ID: cf24
Waiting for an agent...
2019/06/20 18:05:55 Using acb_vol_b1edae11-30de-4f2b-a9c7-7d743e811101 as the home volume
2019/06/20 18:05:58 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/20 18:05:58 Successfully set up Docker network: acb_default_network
2019/06/20 18:05:58 Setting up Docker configuration...
2019/06/20 18:05:59 Successfully set up Docker configuration
2019/06/20 18:05:59 Logging in to registry: myregistry.azurecr.io
2019/06/20 18:06:00 Successfully logged into myregistry.azurecr.io
2019/06/20 18:06:00 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:00 Launching container with name: acb_step_0
[...]
Login Succeeded
2019/06/20 18:06:02 Successfully executed container: acb_step_0
2019/06/20 18:06:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:02 Scanning for dependencies...
2019/06/20 18:06:04 Successfully scanned dependencies
2019/06/20 18:06:04 Launching container with name: acb_step_1
Sending build context to Docker daemon    129kB
[...]
2019/06/20 18:06:07 Successfully pushed image: hubuser/hubrepo:cf24
2019/06/20 18:06:07 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 2.064353)
2019/06/20 18:06:07 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.594061)
2019/06/20 18:06:07 Populating digests for step ID: acb_step_1...
2019/06/20 18:06:09 Successfully populated digests for step ID: acb_step_1
2019/06/20 18:06:09 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.743923)
2019/06/20 18:06:09 The following dependencies were found:
2019/06/20 18:06:09
- image:
    registry: registry.hub.docker.com
    repository: hubuser/hubrepo
    tag: cf24
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0e11c388b664df8a27a901dce21eb89f11d8292f7fca1b3e3c4321bf7897bffe
  git:
    git-head-revision: b0ffa6043dd893a4c75644c5fed384c82ebb5f9e

Run ID: cf24 was successful after 15s
```

若要确认已推送映像, 请在专用 Docker 中心`cf24`存储库中检查标记 (在此示例中为)。

## <a name="next-steps"></a>后续步骤

* 详细了解如何[在 ACR 任务中启用托管标识](container-registry-tasks-authentication-managed-identity.md)。
* 请参阅[ACR 任务 YAML 参考](container-registry-tasks-reference-yaml.md)


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
