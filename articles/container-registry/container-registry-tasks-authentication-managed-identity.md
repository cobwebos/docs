---
title: ACR 任务中的托管标识
description: 在 Azure 容器注册表任务中启用 Azure 资源的托管标识，使该任务能够访问其他 Azure 资源（包括其他专用容器注册表）。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/11/2019
ms.author: danlep
ms.openlocfilehash: c86553d7658e57032393c682628d4b12d6945381
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454730"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>在 ACR 任务中使用 Azure 托管标识 

在 [ACR 任务](../active-directory/managed-identities-azure-resources/overview.md)中启用 [Azure 资源的托管标识](container-registry-tasks-overview.md)，使该任务无需提供或管理凭据即可访问其他 Azure 资源。 例如，使用托管标识可让某个任务步骤将容器映像提取或推送到其他注册表。

本文介绍如何使用 Azure CLI 在 ACR 任务中启用用户分配的或系统分配的托管标识。 您可以使用 Azure CLI 的 Azure Cloud Shell 或本地安装。 若要在本地使用 Azure CLI，需要安装 2.0.68 或更高版本。 可以运行 `az --version` 来查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

有关使用托管标识从 ACR 任务访问受保护资源的方案，请参阅：

* [跨注册表身份验证](container-registry-tasks-cross-registry-authentication.md)
* [使用 Azure Key Vault 中存储的机密访问外部资源](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>为什么使用托管标识？

Azure 资源的托管标识可在 Azure Active Directory (Azure AD) 中为选定的 Azure 服务提供一个自动托管标识。 可以在 ACR 任务中配置托管标识，使该任务无需在任务步骤中传递凭据即可访问其他受保护的 Azure 资源。

托管标识有两种类型：

* 用户分配的标识：可将其分配给多个资源，并保留任意长的时间。 用户分配的标识现提供预览版。

* 系统分配的标识：特定资源（例如 ACR 任务）的唯一标识，其保留时间与该资源的生存期相同。

可以在 ACR 任务中启用上述一种或两种标识。 可为标识授予对其他资源的访问权限，就像为任何安全主体授权一样。 任务运行时，将使用标识来访问任何需要访问权限的任务步骤中的资源。

## <a name="steps-to-use-a-managed-identity"></a>使用托管标识的步骤

请遵循以下概要步骤将托管标识与 ACR 任务配合使用。

### <a name="1-optional-create-a-user-assigned-identity"></a>1. （可选）创建用户分配的标识

如果你打算使用用户分配的标识，可以使用现有的标识。 或者，使用 Azure CLI 或其他 Azure 工具创建标识。 例如，使用 [az identity create][az-identity-create] 命令。 

如果你打算只使用系统分配的标识，请跳过此步骤。 可以在创建 ACR 任务时创建系统分配的标识。

### <a name="2-enable-identity-on-an-acr-task"></a>2. 在 ACR 任务上启用标识

创建 ACR 任务时，可以选择性地启用用户分配的标识和/或系统分配的标识。 例如，在 Azure CLI 中运行 `--assign-identity`az acr task create[ 命令时传递 ][az-acr-task-create] 参数。

若要启用系统分配的标识，请传递不带任何值或带 `--assign-identity` 值的 `assign-identity [system]`。 以下命令从公共 GitHub 存储库创建一个 Linux 任务，该任务生成具有 Git 提交触发器和系统分配的托管标识的 `hello-world` 映像：

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --assign-identity
```

若要启用用户分配的标识，请传递带有标识资源 ID 值的 `--assign-identity`。 以下命令从公共 GitHub 存储库创建一个 Linux 任务，该任务生成具有 Git 提交触发器和用户分配的托管标识的 `hello-world` 映像：

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --assign-identity <resourceID>
```

可以运行 [az identity show][az-identity-show] 命令来获取标识的资源 ID。 资源组 *myResourceGroup* 中 ID *myUserAssignedIdentity* 的资源 ID 格式如下。 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. 授予标识访问其他 Azure 资源的权限

根据任务的要求，为标识授予对其他 Azure 资源的访问权限。 示例包括：

* 为托管标识分配角色，该角色对 Azure 中的目标容器注册表拥有提取、提取/推送或其他权限。 有关完整的注册表角色列表，请参阅 [Azure 容器注册表角色和权限](container-registry-roles.md)。 
* 为托管标识分配一个有权在 Azure Key Vault 中读取机密的角色。

使用 [Azure CLI](../role-based-access-control/role-assignments-cli.md) 或其他 Azure 工具来管理对资源的基于角色的访问。 例如，运行 [az role assignment create][az-role-assignment-create] 命令为标识分配角色。 

以下示例为托管标识分配从容器注册表中提取内容的权限。 该命令指定了该标识的服务主体 ID 以及目标注册表的资源 ID。


```azurecli
az role assignment create --assignee <servicePrincipalID> --scope <registryID> --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. （可选）向任务添加凭据

如果任务要将映像提取或推送到其他 Azure 容器注册表，请将凭据添加到该任务，使标识能够进行身份验证。 运行 [az acr task credential add][az-acr-task-credential-add] 命令，并传递 `--use-identity` 参数以将标识的凭据添加到任务。 

例如，若要添加系统分配的标识的凭据以便在注册表 *targetregistry* 中进行身份验证，请传递 `use-identity [system]`：

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

若要添加用户分配的标识的凭据以便在注册表 *targetregistry* 中进行身份验证，请传递带有标识客户端 ID 值的 `use-identity`。 例如：

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

可以运行 [az identity show][az-identity-show] 命令来获取标识的客户端 ID。 客户端 ID 是采用 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 格式的 GUID。

## <a name="next-steps"></a>后续步骤

本文已介绍如何在 ACR 任务中启用和使用用户分配的或系统分配的托管标识。 有关使用托管标识从 ACR 任务访问受保护资源的方案，请参阅：

* [跨注册表身份验证](container-registry-tasks-cross-registry-authentication.md)
* [使用 Azure Key Vault 中存储的机密访问外部资源](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
