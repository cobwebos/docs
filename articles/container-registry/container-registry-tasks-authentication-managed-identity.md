---
title: ACR 任务中的托管标识
description: 在 Azure 容器注册表任务中启用 Azure 资源的托管标识，使该任务能够访问其他 Azure 资源（包括其他专用容器注册表）。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: f3294698f6973437a23fab798e8daf5642cc9b49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "77111760"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>在 ACR 任务中使用 Azure 托管标识 

在 [ACR 任务](container-registry-tasks-overview.md)中启用 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)，使该任务无需提供或管理凭据即可访问其他 Azure 资源。 例如，使用托管标识可让某个任务步骤将容器映像提取或推送到其他注册表。

本文介绍如何使用 Azure CLI 在 ACR 任务中启用用户分配的或系统分配的托管标识。 您可以使用 Azure CLI 的 Azure Cloud Shell 或本地安装。 若要在本地使用 Azure CLI，需要安装 2.0.68 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

为了进行说明，本文中的示例命令使用 [az acr task create][az-acr-task-create] 创建一个基本映像生成任务，该任务启用了托管标识。 有关使用托管标识从 ACR 任务访问受保护资源的示例方案，请参阅：

* [跨注册表身份验证](container-registry-tasks-cross-registry-authentication.md)
* [使用 Azure Key Vault 中存储的机密访问外部资源](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>为什么使用托管标识？

Azure 资源的托管标识可在 Azure Active Directory 中为选定的 Azure 服务提供一个自动托管标识。 可以在 ACR 任务中配置托管标识，使该任务无需在任务步骤中传递凭据即可访问其他受保护的 Azure 资源。

托管标识有两种类型：

* 用户分配的标识：可将其分配给多个资源，并保留任意长的时间。  用户分配的标识现提供预览版。

* 系统分配的标识：特定资源（例如 ACR 任务）的唯一标识，其保留时间与该资源的生存期相同。 

可以在 ACR 任务中启用上述一种或两种标识。 可为标识授予对其他资源的访问权限，就像为任何安全主体授权一样。 任务运行时，将使用标识来访问任何需要访问权限的任务步骤中的资源。

## <a name="steps-to-use-a-managed-identity"></a>使用托管标识的步骤

请遵循以下概要步骤将托管标识与 ACR 任务配合使用。

### <a name="1-optional-create-a-user-assigned-identity"></a>1.（可选）创建用户分配的标识

如果计划使用用户分配的标识，请使用现有标识，或者使用 Azure CLI 或其他 Azure 工具创建标识。 例如，使用 [az identity create][az-identity-create] 命令。 

如果你打算只使用系统分配的标识，请跳过此步骤。 你将在创建 ACR 任务时创建系统分配的标识。

### <a name="2-enable-identity-on-an-acr-task"></a>2.在 ACR 任务中启用标识

创建 ACR 任务时，可以选择性地启用用户分配的标识和/或系统分配的标识。 例如，在 Azure CLI 中运行 [az acr task create][az-acr-task-create] 命令时传递 `--assign-identity` 参数。

若要启用系统分配的标识，请传递不带任何值或带 `assign-identity [system]` 值的 `--assign-identity`。 以下示例命令基于一个公共 GitHub 存储库创建一个 Linux 任务，该任务生成 `hello-world` 映像并启用系统分配的托管标识：

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

若要启用用户分配的标识，请传递带有标识资源 ID 值的 `--assign-identity`。  以下示例命令基于一个公共 GitHub 存储库创建一个 Linux 任务，该任务生成 `hello-world` 映像并启用用户分配的托管标识：

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

可以运行 [az identity show][az-identity-show] 命令来获取标识的资源 ID。 资源组 *myResourceGroup* 中 ID *myUserAssignedIdentity* 对应的资源 ID 格式如下： 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3.为标识授予对其他 Azure 资源的访问权限

根据任务的要求，为标识授予对其他 Azure 资源的访问权限。 示例包括：

* 为托管标识分配角色，该角色对 Azure 中的目标容器注册表拥有提取、提取/推送或其他权限。 有关完整的注册表角色列表，请参阅 [Azure 容器注册表角色和权限](container-registry-roles.md)。 
* 为托管标识分配一个有权在 Azure Key Vault 中读取机密的角色。

使用 [Azure CLI](../role-based-access-control/role-assignments-cli.md) 或其他 Azure 工具来管理对资源的基于角色的访问。 例如，运行 [az role assignment create][az-role-assignment-create] 命令为标识分配对资源的角色。 

以下示例为托管标识分配从容器注册表中提取内容的权限。 此命令指定了该任务标识的主体 ID 以及目标注册表的资源 ID。  


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4.（可选）将凭据添加到任务

如果你的任务需要使用凭据来拉取映像或将其推送到另一个自定义注册表，或者访问其他资源，请向该任务添加凭据。 运行 [az acr task credential add][az-acr-task-credential-add] 命令来添加凭据，传递 `--use-identity` 参数来指示此标识可以访问凭据。 

例如，若要为系统分配的标识添加凭据以便在 Azure 容器注册表 *targetregistry* 中进行身份验证，请传递 `use-identity [system]`：

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

若要添加用户分配的标识的凭据以便在注册表 *targetregistry* 中进行身份验证，请传递带有标识客户端 ID 值的 `use-identity`。  例如：

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

可以运行 [az identity show][az-identity-show] 命令来获取标识的客户端 ID。 客户端 ID 是采用 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 格式的 GUID。

### <a name="5-run-the-task"></a>5.运行任务

使用托管标识配置任务后，运行该任务。 例如，若要测试本文中创建的任务之一，请使用 [az acr task run][az-acr-task-run] 命令手动触发该任务。 如果配置了其他自动化任务触发器，则该任务会在自动触发时运行。

## <a name="next-steps"></a>后续步骤

本文已介绍如何在 ACR 任务中启用和使用用户分配的或系统分配的托管标识。 有关使用托管标识从 ACR 任务访问受保护资源的方案，请参阅：

* [跨注册表身份验证](container-registry-tasks-cross-registry-authentication.md)
* [使用 Azure Key Vault 中存储的机密访问外部资源](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
