---
title: "如何使用 Azure CLI 为 Azure VM 配置用户分配的 MSI"
description: "逐步介绍了如何使用 Azure CLI 为 Azure VM 配置用户分配的托管服务标识 (MSI)。"
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
ms.openlocfilehash: e8d21aad23782f22b93baf12ce58d1aed4dd5dee
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-azure-cli"></a>使用 Azure CLI 为 VM 配置用户分配的托管服务标识 (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

托管服务标识在 Azure Active Directory 中为 Azure 服务提供托管标识。 此标识可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中输入凭据了。 

本文将介绍如何使用 Azure CLI 为 Azure VM 启用和删除用户分配的 MSI。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

若要运行本教程中的 CLI 脚本示例，你有两种选择：

- 从 Azure 门户中或者通过每个代码块右上角的“试用”按钮使用 [Azure Cloud Shell](~/articles/cloud-shell/overview.md)。
- 如果喜欢使用本地 CLI 控制台，请[安装最新版 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)（2.0.23 或更高版本）。 然后使用 [az login](/cli/azure/reference-index#az_login) 登录到 Azure。 使用与要在其下部署用户分配的 MSI 和 VM 的 Azure 订阅关联的帐户：

   ```azurecli
   az login
   ```

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>在 Azure VM 创建过程中启用 MSI

本部分介绍如何创建 VM 以及向 VM 分配用户分配的 MSI。 如果已有要使用的 VM，请跳过本部分，继续到下一部分。

1. 如果已有要使用的资源组，可跳过此步骤。 使用 [az group create](/cli/azure/group/#az_group_create)，创建用于容纳和部署 VM 的[资源组](~/articles/azure-resource-manager/resource-group-overview.md#terminology)。 请务必将 `<RESOURCE GROUP>` 和 `<LOCATION>` 参数值替换为自己的值。 :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. 使用 [az identity create](/cli/azure/identity#az_identity_create) 创建用户分配的 MSI。  `-g` 参数指定要创建 MSI 的资源组，`-n` 参数指定其名称。 请务必将 `<RESOURCE GROUP>` 和 `<MSI NAME>` 参数值替换为自己的值：

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
响应包含所创建用户分配 MSI 的详细信息，类似以下详细信息。 下一步中将使用分配给该 MSI 的资源 `id` 值。

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. 运行 [az vm create](/cli/azure/vm/#az_vm_create) 创建 VM。 以下示例创建与新的用户分配 MSI 关联的 VM，用 `--assign-identity` 参数指定。 请务必将 `<RESOURCE GROUP>`、`<VM NAME>`、`<USER NAME>`、`<PASSWORD>` 和 `<`MSI ID> 替换为自己的值。对于 <MSI ID>，将使用上一步中所创建用户分配 MSI 的资源 id 属性值：` parameter values with your own values. For `<MSI ID>`, use the user-assigned MSI's resource ` 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>在现有 Azure VM 上启用 MSI

1. 使用 [az identity create](/cli/azure/identity#az_identity_create) 创建用户分配的 MSI。  `-g` 参数指定要创建 MSI 的资源组，`-n` 参数指定其名称。 请务必将 `<RESOURCE GROUP>` 和 `<MSI NAME>` 参数值替换为自己的值：

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
响应包含所创建用户分配 MSI 的详细信息，类似以下详细信息。 下一步中将使用分配给该 MSI 的资源 `id` 值。

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. 使用 [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity) 向 VM 分配用户分配的 MSI。 请务必将 `<RESOURCE GROUP>` 和 `<VM NAME>` 参数值替换为自己的值。 `<MSI ID>` 将为用户分配 MSI 的资源 `id` 属性，在上一步中创建：

    ```azurecli-interactive
    az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

## <a name="remove-msi-from-an-azure-vm"></a>在 Azure VM 上禁用 MSI

1. 使用 [az vm remove-identity](/cli/azure/vm#az_vm_remove_identity) 从 VM 中删除用户分配的 MSI。 请务必将 `<RESOURCE GROUP>` 和 `<VM NAME>` 参数值替换为自己的值。 `<MSI NAME>` 将为用户分配 MSI 的 `name` 属性，在 `az identity create` 命令中给定（请参阅前几部分中的示例）：

   ```azurecli-interactive
   az vm remove-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
   ```

## <a name="next-steps"></a>后续步骤

- [托管服务标识概述](msi-overview.md)
- 有关完整的 Azure VM 创建快速入门，请参阅： 

  - [使用 CLI 创建 Windows 虚拟机](~/articles/virtual-machines/windows/quick-create-cli.md)  
  - [使用 CLI 创建 Linux 虚拟机](~/articles/virtual-machines/linux/quick-create-cli.md) 

使用以下评论部分提供反馈，帮助我们改进内容。
















