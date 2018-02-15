---
title: "如何使用 Azure CLI 向用户分配的 MSI 分配对 Azure 资源的访问权限"
description: "分步说明了如何使用 Azure CLI 向用户在一个资源上分配的 MSI 分配对另一资源的访问权限。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5bea41999f59fe8be7ae0a0bd5b726527beeddd5
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="assign-a-user-assigned-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>使用 Azure CLI 向用户分配的托管服务标识 (MSI) 分配对资源的访问权限

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

创建用户分配的 MSI 后，便可以向该 MSI 授予对其他资源的访问权限，这与任何安全主体一样。 此示例展示了如何使用 Azure CLI 向用户分配的 MSI 授予对 Azure 存储帐户的访问权限。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

若要运行本教程中的 CLI 脚本示例，你有两种选择：

- 从 Azure 门户中或者通过每个代码块右上角的“试用”按钮使用 [Azure Cloud Shell](~/articles/cloud-shell/overview.md)。
- 如果喜欢使用本地 CLI 控制台，请[安装最新版 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)（2.0.23 或更高版本）。 然后使用 [az login](/cli/azure/#az_login) 登录到 Azure。 使用与要在其下部署用户分配的 MSI 和 VM 的 Azure 订阅关联的帐户：

   ```azurecli
   az login
   ```

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>使用 RBAC 授予 MSI 对另一个资源的访问权限

为了将 MSI 用于针对主机资源（例如 VM）的登录或资源访问，必须首先通过角色管理向 MSI 授予资源访问权限。 可以在将 MSI 与主机进行关联之前或之后执行此操作。 有关创建和关联 VM 的完整步骤，请参阅[使用 Azure CLI 为 VM 配置用户分配的 MSI](msi-qs-configure-cli-windows-vm.md)。

下面的示例向用户分配的 MSI 授予对存储帐户的访问权限。  

1. 要想向 MSI 授予访问权限，你需要具有 MSI 的服务主体的客户端 ID（也称为应用 ID）。 客户端 ID 是在预配 MSI 及其服务主体时通过 [az identity create](/cli/azure/identity#az_identity_create) 提供的（此处进行了展示供参考）。

   ```azurecli-interactive
   az identity create -g rgPrivate -n msiServiceApp
   ```

   成功的响应在 `clientId` 属性中包含了用户分配的 MSI 的服务主体的客户端 ID：

   ```json
   {
        "clientId": "9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp/credentials?tid=733a8f0p-ec41-4e69-8adz-971fc4b533bl&oid=z4baa4fc-fce4-4202-8250-5fb359abblll&aid=9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp",
        "location": "westcentralus",
        "name": "msiServiceApp",
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "resourceGroup": "rgPrivate",
        "tags": {},
        "tenantId": "733a8f0p-ec41-4e69-8adz-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
   }
   ```

2. 在知道客户端 ID 后，使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 向 MSI 分配对另一资源的访问权限。 请确保为 `--assignee` 参数使用客户端 ID，并使用在运行 `az identity create` 时返回的匹配的订阅 ID 和资源组名称。 这里，为 MSI 分配了对名为“myStorageAcct”的存储帐户的“Reader”访问权限：

   ```azurecli-interactive
   az role assignment create --assignee 9391e5b1-dada-4a8z-834a-43ad44f296bl --role Reader --scope /subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

   成功的响应类似于以下输出：

   ```json
   {
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct/providers/Microsoft.Authorization/roleAssignments/f4766864-9493-43c6-91d7-abd131c3c017",
        "name": "f4766864-9493-43c6-91d7-abd131c3c017",
        "properties": {
            "additionalProperties": {
            "createdBy": null,
            "createdOn": "2017-12-21T20:49:37.5590544Z",
            "updatedBy": "pd78b21f-17a4-41az-b7db-9aadec3376bl",
            "updatedOn": "2017-12-21T20:49:37.5590544Z"
            },
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "roleDefinitionId": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "scope": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct"
        },
        "resourceGroup": "rgPrivate",
        "type": "Microsoft.Authorization/roleAssignments"
   }
   ```

## <a name="next-steps"></a>后续步骤

- 有关 MSI 的概述，请参阅[托管服务标识概述](msi-overview.md)。
- 若要在 Azure VM 上启用用户分配的 MSI，请参阅[使用 Azure CLI 为 VM 配置用户分配的托管服务标识 (MSI)](msi-qs-configure-cli-windows-vm.md)。

使用以下评论部分提供反馈，帮助我们改进内容。

