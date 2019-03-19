---
title: Azure 容器注册表 - 角色和权限
description: 使用 Azure 基于角色的访问控制 (RBAC) 以及标识和访问管理 (IAM)，提供对 Azure 容器注册表中资源的细粒度访问权限。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/20/2019
ms.author: danlep
ms.openlocfilehash: 0148894bb013dc9f8cce595f14919f87d6292df8
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593618"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure 容器注册表角色和权限

Azure 容器注册表服务支持一组 Azure 角色，这些角色提供访问 Azure 容器注册表所需的不同级别的权限。 使用 Azure [基于角色的访问控制](../role-based-access-control/index.yml) (RBAC)，为需要与注册表交互的用户或服务主体分配特定的权限。

| 角色/权限       | [访问资源管理器](#access-resource-manager) | [创建/删除注册表](#create-and-delete-registry) | [推送映像](#push-image) | [拉取映像](#pull-image) | [删除图像数据](#delete-image-data) | [更改策略](#change-policies) |   [对映像签名](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| 所有者 | X | X | X | X | X | X |  |  
| 参与者 | X | X | X |  X | X | X |  |  
| 读取器 | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | X |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>区分用户和服务

应用权限时，最佳做法是为需要完成某个任务的用户或服务提供一组最受限的权限。 以下权限集代表一组可供用户和无外设服务使用的功能。

### <a name="cicd-solutions"></a>CI/CD 解决方案

通过 CI/CD 解决方案自动执行 `docker build` 命令时，需要 `docker push` 功能。 对于这些无外设服务方案，建议分配 **AcrPush** 角色。 该角色不同于权限范围更大的“参与者”角色，可以防止帐户执行其他注册表操作或访问 Azure 资源管理器。

### <a name="container-host-nodes"></a>容器主机节点

同样，运行容器的节点需要 **AcrPull** 角色，但不应该需要“读者”功能。

### <a name="visual-studio-code-docker-extension"></a>Visual Studio Code Docker 扩展

对于 Visual Studio Code [Docker 扩展](https://code.visualstudio.com/docs/azure/docker)这样的工具，需要其他资源提供程序访问权限才能列出可用的 Azure 容器注册表。 在这种情况下，请为用户提供对“读者”或“参与者”角色的访问权限。 这些角色允许 `docker pull`、`docker push`、`az acr list`、`az acr build` 等功能。 

## <a name="access-resource-manager"></a>访问资源管理器

Azure 资源管理器访问权限是 Azure 门户和使用 [Azure CLI](/cli/azure/) 进行注册表管理所需的。 例如，若要通过 `az acr list` 命令获取一系列注册表，需要此权限集。 

## <a name="create-and-delete-registry"></a>创建和删除注册表

创建和删除 Azure 容器注册表的功能。

## <a name="push-image"></a>推送映像

通过 `docker push` 将映像推送到注册表的功能，或者将其他[受支持的项目](container-registry-image-formats.md)（例如 Helm 图表）推送到注册表的功能。 要求使用授权的标识通过注册表进行[身份验证](container-registry-authentication.md)。 

## <a name="pull-image"></a>拉取映像

通过 `docker pull` 从注册表拉取非隔离映像的功能，或者从注册表拉取其他[受支持的项目](container-registry-image-formats.md)（例如 Helm 图表）的功能。 要求使用授权的标识通过注册表进行[身份验证](container-registry-authentication.md)。

## <a name="delete-image-data"></a>删除映像数据

能够[删除容器映像或存储库](container-registry-delete.md)。

## <a name="change-policies"></a>更改策略

在注册表上配置策略的功能。 策略包括映像清除、启用隔离和映像签名。

## <a name="sign-images"></a>对映像签名

对映像签名的功能，通常分配给某个自动化过程，该过程会使用服务主体。 此权限通常与[推送映像](#push-image)功能配合使用，以便将受信任的映像推送到注册表。 有关详细信息，请参阅 [Azure 容器注册表中的内容信任](container-registry-content-trust.md)。

## <a name="next-steps"></a>后续步骤

* 详细了解如何通过 [Azure 门户](../role-based-access-control/role-assignments-portal.md)、[Azure CLI](../role-based-access-control/role-assignments-cli.md) 或其他 Azure 工具将 RBAC 角色分配给 Azure 标识。

* 了解适用于 Azure 容器注册表的[身份验证选项](container-registry-authentication.md)。
