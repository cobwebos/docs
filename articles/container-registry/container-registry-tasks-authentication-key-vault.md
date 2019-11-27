---
title: ACR 任务中的外部身份验证
description: 在 Azure 容器注册表 (ACR) 任务中启用 Azure 资源的托管标识，使该任务能够读取存储在 Azure Key Vault 中的 Docker Hub 凭据。
ms.topic: article
ms.date: 07/12/2019
ms.openlocfilehash: a7086050a4aef380f11298c819817692396216b2
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456219"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>ACR 任务中使用 Azure 托管标识的外部身份验证 

在 [ACR 任务](container-registry-tasks-overview.md)中，可以[启用 Azure 资源的托管标识](container-registry-tasks-authentication-managed-identity.md)。 该任务可以使用该标识来访问其他 Azure 资源，而无需提供或管理凭据。 

本文介绍如何在要访问 Azure Key Vault 中存储的机密的任务中启用托管标识。 

为了创建 Azure 资源，本文要求运行 Azure CLI 版本 2.0.68 或更高版本。 可以运行 `az --version` 来查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

## <a name="scenario-overview"></a>方案概述

示例任务将读取存储在 Azure Key Vault 中的 Docker Hub 凭据。 这些凭据适用于对 Docker Hub 中的专用存储库拥有写入（推送）权限的 Docker Hub 帐户。 若要读取凭据，请使用托管标识配置任务，并为其分配适当的权限。 与标识关联的任务将生成一个映像，并登录到 Docker Hub，以将映像推送到专用存储库。 

此示例演示了使用用户分配的或系统分配的托管标识的步骤。 选择哪种标识取决于组织的需求。

在实际方案中，公司可能会在生成过程中将映像发布到 Docker Hub 中的专用存储库。 

## <a name="prerequisites"></a>先决条件

需要一个 Azure 容器注册表，将在其中运行任务。 在本文中，此注册表名为 *myregistry*。 在后续步骤中，请将其替换为自己的注册表名称。

如果还没有 Azure 容器注册表，请参阅[快速入门：使用 Azure CLI 创建专用容器注册表](container-registry-get-started-azure-cli.md)。 暂时不需要将映像推送到注册表。

在 Docker Hub 中需有一个专用存储库，并且有一个有权写入存储库的 Docker Hub 帐户。 在此示例中，此存储库名为 *hubuser/hubrepo*。 

## <a name="create-a-key-vault-and-store-secrets"></a>创建 Key Vault 并存储机密

首先，如果需要，请在*eastus*位置创建一个名为*myResourceGroup*的资源组，并在其中包含以下[az group create][az-group-create]命令：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az keyvault create][az-keyvault-create] 命令创建 Key Vault。 请务必指定唯一的 Key Vault 名称。 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

使用 [az keyvault secret set][az-keyvault-secret-set] 命令将所需的 Docker Hub 凭据存储到该 Key Vault 中。 在这些命令中，值将传入到环境变量中：

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

在实际方案中，可能会通过单独的过程设置和维护机密。

## <a name="define-task-steps-in-yaml-file"></a>在 YAML 文件中定义任务步骤

此示例任务的步骤在一个 [YAML 文件](container-registry-tasks-reference-yaml.md)中定义。 在本地工作目录中创建名为 `dockerhubtask.yaml` 的文件，并粘贴以下内容。 请务必将该文件中的 Key Vault 名称替换为自己的 Key Vault 名称。

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

任务步骤将执行以下操作：

* 管理用于在 Docker Hub 中进行身份验证的机密凭据。
* 通过向 `docker login` 命令传递机密，在 Docker Hub 中进行身份验证。
* 使用 [Azure-Samples/acr-tasks](https://github.com/Azure-Samples/acr-tasks.git) 存储库中的示例 Dockerfile 生成映像。
* 将映像推送到专用 Docker Hub 存储库。

## <a name="option-1-create-task-with-user-assigned-identity"></a>选项1：创建具有用户分配的标识的任务

本部分中的步骤将创建一个任务并启用用户分配的标识。 如果要改为启用系统分配的标识，请参阅[选项2：使用系统分配的标识创建任务](#option-2-create-task-with-system-assigned-identity)。 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>创建任务

执行以下 *az acr task create* 命令创建 [dockerhubtask][az-acr-task-create] 任务。 该任务无需源代码上下文即可运行，该命令将引用工作目录中的 `dockerhubtask.yaml` 文件。 `--assign-identity` 参数传递用户分配的标识的资源 ID。 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>选项2：创建包含系统分配的标识的任务

本部分中的步骤将创建一个任务并启用系统分配的标识。 如果要改为启用用户分配的标识，请参阅[选项1：使用用户分配的标识创建任务](#option-1-create-task-with-user-assigned-identity)。 

### <a name="create-task"></a>创建任务

执行以下 *az acr task create* 命令创建 [dockerhubtask][az-acr-task-create] 任务。 该任务无需源代码上下文即可运行，该命令将引用工作目录中的 `dockerhubtask.yaml` 文件。 不带任何值的 `--assign-identity` 参数将在任务中启用系统分配的标识。  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="grant-identity-access-to-key-vault"></a>为标识授予对 Key Vault 的访问权限

运行以下 [az keyvault set-policy][az-keyvault-set-policy] 命令来设置对 Key Vault 的访问策略。 以下示例允许标识读取 Key Vault 中的机密。 

```azurecli
az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

## <a name="manually-run-the-task"></a>手动运行任务

若要验证启用了托管标识的任务是否成功运行，请使用 [az acr task run][az-acr-task-run] 命令手动触发该任务。 `--set` 参数用于将专用存储库名称传递给该任务。 在此示例中，占位符存储库名称为 *hubuser/hubrepo*。

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo 
```

如果该任务成功运行，则输出将显示对 Docker Hub 的身份验证成功，并且映像已成功生成并推送到专用存储库：

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

若要确认是否已推送映像，请检查专用 Docker Hub 存储库中的标记（在本示例中为 `cf24`）。

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
