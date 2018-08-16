---
title: include 文件
description: include 文件
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 08/03/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 2174ae44f8e78763c1939aee5e6b86c95a0924ce
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39514086"
---
## <a name="create-a-service-principal"></a>创建服务主体

若要创建可以访问容器注册表的服务主体，请在 [Azure Cloud Shell](../articles/cloud-shell/overview.md) 或本地安装的 [Azure CLI](/cli/azure/install-azure-cli) 中运行以下脚本。 此脚本已针对 Bash Shell 格式化。

运行脚本之前，请将 `ACR_NAME` 变量更新为容器注册表的名称。 `SERVICE_PRINCIPAL_NAME` 值必须在 Azure Active Directory 租户中唯一。 如果收到“`'http://acr-service-principal' already exists.`”错误，请为服务主体指定另一名称。

如果需要授予其他权限，可以选择修改 [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] 命令中的 `--role` 值。

运行脚本后，请记下服务主体的 **ID** 和**密码**。 获得其凭据后，可以配置应用程序和服务使其作为服务主体对容器注册表进行身份验证。

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh --> [!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>使用现有的服务主体

若要向现有服务主体授予注册表访问权限，必须为服务主体分配新角色。 与创建新的服务主体一样，可以授予“拉取”、“推送和拉取”以及“所有者”访问权限。

以下脚本使用 [az role assignment create][az-role-assignment-create] 命令向 `SERVICE_PRINCIPAL_ID` 变量中指定的服务主体授予“拉取”权限。 如果要授予不同的访问级别，请调整 `--role` 值。

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh --> [!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
