---
title: ACR 任务中的托管标识
description: 启用 Azure 容器注册表任务中 Azure 资源的托管标识，以允许任务访问其他 Azure 资源，包括其他专用容器注册表。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: b2f5a9bacf96eb098e307a6a8df3e13cb9d04bd0
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513410"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>在 ACR 任务中使用 Azure 托管标识 

在[ACR 任务](container-registry-tasks-overview.md)中启用[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)，以便任务可以访问其他 Azure 资源，而无需提供或管理凭据。 例如，使用托管标识启用任务步骤，将容器映像拉取或推送到其他注册表。

本文介绍如何使用 Azure CLI 在 ACR 任务上启用用户分配的托管标识或系统分配的托管标识。 您可以使用 Azure CLI 的 Azure Cloud Shell 或本地安装。 如果要在本地使用，则需要版本2.0.68 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

出于说明目的，本文中的示例命令使用[az acr task create][az-acr-task-create]来创建启用托管标识的基本映像生成任务。 有关使用托管标识从 ACR 任务访问受保护资源的示例方案，请参阅：

* [跨注册表身份验证](container-registry-tasks-cross-registry-authentication.md)
* [使用 Azure Key Vault 中存储的机密访问外部资源](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>为什么使用托管标识？

Azure 资源的托管标识通过 Azure Active Directory 中的自动托管标识提供所选 Azure 服务。 您可以使用托管标识配置 ACR 任务，以便该任务可以访问其他受保护的 Azure 资源，而无需在任务步骤中传递凭据。

托管标识有两种类型：

* *用户分配的标识*，你可以将其分配给多个资源并在需要时保留。 用户分配的标识现提供预览版。

* *系统分配的标识*，它对特定资源（如 ACR 任务）唯一，并持续到该资源的生存期。

可以在 ACR 任务中启用这两种类型的标识。 向其他资源授予标识访问权限，就像任何安全主体一样。 当任务运行时，它使用标识来访问任何需要访问的任务步骤中的资源。

## <a name="steps-to-use-a-managed-identity"></a>使用托管标识的步骤

按照这些高级步骤使用具有 ACR 任务的托管标识。

### <a name="1-optional-create-a-user-assigned-identity"></a>1. （可选）创建用户分配的标识

如果计划使用用户分配的标识，请使用现有标识，或者使用 Azure CLI 或其他 Azure 工具创建标识。 例如，使用[az identity create][az-identity-create]命令。 

如果计划只使用系统分配的标识，请跳过此步骤。 在创建 ACR 任务时创建系统分配的标识。

### <a name="2-enable-identity-on-an-acr-task"></a>2. 在 ACR 任务上启用标识

在创建 ACR 任务时，可以选择启用用户分配的标识、系统分配的标识或同时启用这两者。 例如，在 Azure CLI 中运行[az acr task create][az-acr-task-create]命令时传递 `--assign-identity` 参数。

若要启用系统分配的标识，请将 `--assign-identity` 传递到无值或 `assign-identity [system]`。 下面的示例命令从公共 GitHub 存储库创建 Linux 任务，该存储库生成 `hello-world` 映像并启用系统分配的托管标识：

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

若要启用用户分配的标识，请将 `--assign-identity` 传递到标识的*资源 ID*的值。 下面的示例命令从公共 GitHub 存储库创建 Linux 任务，该存储库生成 `hello-world` 映像并启用用户分配的托管标识：

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

可以通过运行[az identity show][az-identity-show]命令获取标识的资源 ID。 资源组*myResourceGroup*中 id *MYUSERASSIGNEDIDENTITY*的资源 id 的格式为： 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. 授予标识访问其他 Azure 资源的权限

根据任务的要求，授予标识访问其他 Azure 资源的权限。 示例包括：

* 向 Azure 中的目标容器注册表分配包含请求、推送和拉取或其他权限的托管标识。 有关注册表角色的完整列表，请参阅[Azure 容器注册表角色和权限](container-registry-roles.md)。 
* 将托管标识指定为读取 Azure 密钥保管库中的机密。

使用[Azure CLI](../role-based-access-control/role-assignments-cli.md)或其他 Azure 工具来管理对资源的基于角色的访问。 例如，运行[az role assign create][az-role-assignment-create]命令，将角色分配给资源。 

下面的示例为托管标识分配从容器注册表中请求的权限。 该命令指定标识的*服务主体 id*和目标注册表的*资源 id* 。


```azurecli
az role assignment create \
  --assignee <servicePrincipalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. （可选）向任务添加凭据

如果任务需要凭据来请求或将映像推送到其他自定义注册表，或者要访问其他资源，请向任务添加凭据。 运行[az acr task credential add][az-acr-task-credential-add]命令以添加凭据，并传递 `--use-identity` 参数以指示标识可以访问凭据。 

例如，若要为系统分配的标识添加凭据以使用 Azure 容器注册表*targetregistry*进行身份验证，请通过 `use-identity [system]`：

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

若要为用户分配的标识添加凭据以便通过注册表*targetregistry*进行身份验证，请将 `use-identity` 传递给标识的*客户端 ID*的值。 例如：

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

可以通过运行[az identity show][az-identity-show]命令获取标识的客户端 ID。 客户端 ID 是 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`格式的 GUID。

### <a name="5-run-the-task"></a>5. 运行任务

使用托管标识配置任务后，运行该任务。 例如，若要测试本文中创建的任务之一，请使用[az acr task run][az-acr-task-run]命令手动触发该任务。 如果配置了其他自动任务触发器，则该任务会在自动触发时运行。

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 ACR 任务上启用和使用用户分配的托管标识或系统分配的托管标识。 有关使用托管标识从 ACR 任务中访问受保护资源的方案，请参阅：

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
