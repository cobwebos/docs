---
title: Azure 角色和权限
description: 使用 azure RBAC) 和标识和访问管理 (IAM) 中的 Azure 基于角色的访问 (控制，为 Azure 容器注册表中的资源提供精细的权限。
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: b8562d3e33cd49082d4ba4d8567d5f0c816070b0
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661378"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure 容器注册表角色和权限

Azure 容器注册表服务支持一组内置的 [azure 角色](../role-based-access-control/built-in-roles.md) ，这些角色提供对 Azure 容器注册表的不同级别的权限。 使用 azure [RBAC)  (azure 基于角色的访问控制 ](../role-based-access-control/index.yml) 向用户、服务主体或其他需要与注册表进行交互的标识分配特定权限。 你还可以为不同操作的注册表定义具有严格权限的 [自定义角色](#custom-roles) 。

| 角色/权限       | [访问资源管理器](#access-resource-manager) | [创建/删除注册表](#create-and-delete-registry) | [推送映像](#push-image) | [拉取映像](#pull-image) | [删除映像数据](#delete-image-data) | [更改策略](#change-policies) |   [对映像签名](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| 所有者 | X | X | X | X | X | X |  |  
| 参与者 | X | X | X |  X | X | X |  |  
| 读取器 | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>区分用户和服务

应用权限时，最佳做法是为需要完成某个任务的用户或服务提供一组最受限的权限。 以下权限集代表一组可供用户和无外设服务使用的功能。

### <a name="cicd-solutions"></a>CI/CD 解决方案

通过 CI/CD 解决方案自动执行 `docker build` 命令时，需要 `docker push` 功能。 对于这些无外设服务方案，建议分配 **AcrPush** 角色。 该角色不同于权限范围更大的“参与者”角色，**** 可以防止帐户执行其他注册表操作或访问 Azure 资源管理器。

### <a name="container-host-nodes"></a>容器主机节点

同样，运行容器的节点需要 **AcrPull** 角色，但不应该需要“读者”功能。****

### <a name="visual-studio-code-docker-extension"></a>Visual Studio Code Docker 扩展

对于 Visual Studio Code [Docker 扩展](https://code.visualstudio.com/docs/azure/docker)这样的工具，需要其他资源提供程序访问权限才能列出可用的 Azure 容器注册表。 在这种情况下，请为用户提供对“读者”或“参与者”角色的访问权限。******** 这些角色允许 `docker pull`、`docker push`、`az acr list`、`az acr build` 等功能。 

## <a name="access-resource-manager"></a>访问资源管理器

Azure 资源管理器访问权限是 Azure 门户和使用 [Azure CLI](/cli/azure/) 进行注册表管理所需的。 例如，若要通过 `az acr list` 命令获取一系列注册表，需要此权限集。 

## <a name="create-and-delete-registry"></a>创建和删除注册表

创建和删除 Azure 容器注册表的功能。

## <a name="push-image"></a>推送映像

通过 `docker push` 将映像推送到注册表的功能，或者将其他[受支持的项目](container-registry-image-formats.md)（例如 Helm 图表）推送到注册表的功能。 要求使用授权的标识通过注册表进行[身份验证](container-registry-authentication.md)。 

## <a name="pull-image"></a>拉取映像

通过 `docker pull` 从注册表拉取非隔离映像的功能，或者从注册表拉取其他[受支持的项目](container-registry-image-formats.md)（例如 Helm 图表）的功能。 要求使用授权的标识通过注册表进行[身份验证](container-registry-authentication.md)。

## <a name="delete-image-data"></a>删除映像数据

能够从注册表中[删除容器映像](container-registry-delete.md)或者删除其他[受支持的项目](container-registry-image-formats.md)，例如 Helm 图表。

## <a name="change-policies"></a>更改策略

在注册表上配置策略的功能。 策略包括映像清除、启用隔离和映像签名。

## <a name="sign-images"></a>对映像签名

对映像签名的功能，通常分配给某个自动化过程，该过程会使用服务主体。 此权限通常与[推送映像](#push-image)功能配合使用，以便将受信任的映像推送到注册表。 有关详细信息，请参阅 [Azure 容器注册表中的内容信任](container-registry-content-trust.md)。

## <a name="custom-roles"></a>自定义角色

与其他 Azure 资源一样，可以创建具有对 Azure 容器注册表的精细权限的 [自定义角色](../role-based-access-control/custom-roles.md) 。 然后，将自定义角色分配给用户、服务主体或其他需要与注册表进行交互的标识。 

若要确定要将哪些权限应用于自定义角色，请参阅 Microsoft.containerregistry [操作](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry)的列表，查看 [内置 ACR 角色](../role-based-access-control/built-in-roles.md)允许的操作，或运行以下命令：

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

若要定义自定义角色，请参阅 [创建自定义角色的步骤](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)。

> [!IMPORTANT]
> 在自定义角色中，Azure 容器注册表当前不支持使用通配符（如 `Microsoft.ContainerRegistry/*` 或） `Microsoft.ContainerRegistry/registries/*` 来授予对所有匹配操作的访问权限。 在角色中单独指定任何所需的操作。

### <a name="example-custom-role-to-import-images"></a>示例：导入映像的自定义角色

例如，以下 JSON 定义允许将 [图像导入](container-registry-import-images.md) 注册表的自定义角色的最小操作。

```json
{
   "assignableScopes": [
     "/subscriptions/<optional, but you can limit the visibility to one or more subscriptions>"
   ],
   "description": "Can import images to registry",
   "Name": "AcrImport",
   "permissions": [
     {
       "actions": [
         "Microsoft.ContainerRegistry/registries/push/write",
         "Microsoft.ContainerRegistry/registries/pull/read",
         "Microsoft.ContainerRegistry/registries/read",
         "Microsoft.ContainerRegistry/registries/importImage/action"
       ],
       "dataActions": [],
       "notActions": [],
       "notDataActions": []
     }
   ],
   "roleType": "CustomRole"
 }
```

若要使用 JSON 说明创建或更新自定义角色，请使用 [Azure CLI](../role-based-access-control/custom-roles-cli.md)、 [Azure 资源管理器模板](../role-based-access-control/custom-roles-template.md)、 [Azure PowerShell](../role-based-access-control/custom-roles-powershell.md)或其他 azure 工具。 添加或删除自定义角色的角色分配，与管理内置 Azure 角色的角色分配的方式相同。

## <a name="next-steps"></a>后续步骤

* 了解有关使用 [Azure 门户](../role-based-access-control/role-assignments-portal.md)、 [Azure CLI](../role-based-access-control/role-assignments-cli.md)或其他 Azure 工具将 Azure 角色分配到 azure 标识的详细信息。

* 了解适用于 Azure 容器注册表的[身份验证选项](container-registry-authentication.md)。

* 了解如何在容器注册表中启用 [存储库范围内的权限](container-registry-repository-scoped-permissions.md) (预览) 。