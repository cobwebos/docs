---
title: include 文件
description: include 文件
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 04/23/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 6ed114ea6162c9d4888b6f86998cfb422a3944e8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32198220"
---
## <a name="create-a-service-principal"></a>创建服务主体

若要创建可以访问容器注册表的服务主体，可以使用以下脚本。 使用容器注册表的名称更新 `ACR_NAME` 变量，并可选择性地更新 [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] 命令中的 `--role` 值以授予不同权限。 必须已安装 [Azure CLI](/cli/azure/install-azure-cli) 才能使用此脚本。

运行脚本后，请记下服务主体的 **ID** 和**密码**。 获得其凭据后，可以配置应用程序和服务使其作为服务主体对容器注册表进行身份验证。

[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>使用现有的服务主体

若要向现有服务主体授予注册表访问权限，必须为服务主体分配新角色。 与创建新的服务主体一样，可以授予“拉取”、“推送和拉取”以及“所有者”访问权限。

以下脚本使用 [az role assignment create][az-role-assignment-create] 命令向 `SERVICE_PRINCIPAL_ID` 变量中指定的服务主体授予“拉取”权限。 如果要授予不同的访问级别，请调整 `--role` 值。

[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
