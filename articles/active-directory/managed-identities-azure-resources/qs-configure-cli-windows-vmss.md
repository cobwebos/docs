---
title: 如何在 Azure VMSS 上使用 Azure CLI 配置系统分配托管标识和用户分配托管标识
description: 分步说明如何在 Azure VMSS 上使用 Azure CLI 配置系统分配托管标识和用户分配托管标识。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: priyamo
ms.openlocfilehash: 42b0ab15f43d301629b9fbb3208ba24eae8c227e
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2019
ms.locfileid: "55694595"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>使用 Azure CLI 在虚拟机规模集上配置 Azure 资源托管标识

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文介绍如何使用 Azure CLI 在 Azure 虚拟机规模集 (VMSS) 上执行以下 Azure 资源托管标识操作：
- 在 Azure VMSS 上启用和禁用系统分配托管标识
- 在 Azure VMSS 上添加和删除用户分配托管标识


## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#how-does-it-work)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 若要执行本文中的管理操作，帐户需要以下基于 Azure 角色的访问控制分配：

    > [!NOTE]
    > 无需其他 Azure AD 目录角色分配。

    - [虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)，可创建虚拟机规模集，并从虚拟机规模集启用和删除系统和/或用户分配托管标识。
    - [托管标识参与者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色，可以创建用户分配的托管标识。
    - [托管标识操作员](/azure/role-based-access-control/built-in-roles#managed-identity-operator)角色，可在虚拟机规模集中分配和删除用户分配的托管标识。
- 若要运行 CLI 脚本示例，可以使用下列三种方法：
    - 在 Azure 门户中使用 [Azure Cloud Shell](../../cloud-shell/overview.md)（见下一部分）。
    - 单击各代码块右上角的“试运行”按钮，使用嵌入的 Azure Cloud Shell。
    - 如果喜欢使用本地 CLI 控制台，请[安装最新版 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)（2.0.13 或更高版本）。 
      
      > [!NOTE]
      > 命令已更新，以反映最新版本的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>系统分配的托管标识

本部分介绍如何使用 Azure CLI 为 Azure VMSS 启用和禁用系统分配托管标识。

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>在创建 Azure 虚拟机规模集的过程中启用系统分配托管标识

要创建启用了系统分配托管标识的虚拟机规模集，请执行以下操作：

1. 如果在本地控制台中使用 Azure CLI，首先请使用 [az login](/cli/azure/reference-index#az-login) 登录到 Azure。 使用与要在其下部署虚拟机规模集的 Azure 订阅关联的帐户：

   ```azurecli-interactive
   az login
   ```

2. 使用 [az group create](/cli/azure/group/#az-group-create)，创建用于容纳和部署虚拟机规模集及其相关资源的[资源组](../../azure-resource-manager/resource-group-overview.md#terminology)。 如果已有要改用的资源组，则可以跳过此步骤：

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. 使用 [az vmss create](/cli/azure/vmss/#az-vmss-create) 创建虚拟机规模集。 以下示例创建名为 myVMSS 且已启用系统分配托管标识的虚拟机规模集（应 `--assign-identity` 参数的要求）。 `--admin-username` 和 `--admin-password` 参数指定用于登录虚拟机的管理用户名和密码帐户。 针对自己的环境相应地更新这些值： 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>在现有 Azure 虚拟机规模集上启用系统分配的托管标识

如果需要在现有 Azure 虚拟机规模集上启用系统分配托管标识，请执行以下操作：

1. 如果在本地控制台中使用 Azure CLI，首先请使用 [az login](/cli/azure/reference-index#az-login) 登录到 Azure。 使用与包含虚拟机规模集的 Azure 订阅关联的帐户。

   ```azurecli-interactive
   az login
   ```

2. 使用 [az vmss identity assign](/cli/azure/vmss/identity/#az-vmss-identity-assign) 命令为现有 VM 启用系统分配托管标识：

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>从 Azure 虚拟机规模集中禁用系统分配托管标识

如果某个虚拟机规模集不再需要系统分配托管标识，但仍需要用户分配托管标识，请使用以下命令：

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

如果某个虚拟机不再需要系统分配托管标识，且没有用户分配托管标识，请使用以下命令：

> [!NOTE]
> 值 `none` 区分大小写。 它必须为小写。 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

若要删除 Azure 资源 VM 扩展的托管标识（计划于 2019 年 1 月弃用），请使用 [az vmss identity remove](/cli/azure/vmss/identity/) 命令从 VMSS 中删除系统分配的托管标识：

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

## <a name="user-assigned-managed-identity"></a>用户分配的托管标识

本部分介绍如何使用 Azure CLI 启用和删除用户分配托管标识。

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>在创建虚拟机规模集的过程中分配用户分配托管标识

本部分介绍如何创建 VMSS 以及向 VMSS 分配用户分配托管标识。 如果已有要使用的 VMSS，请跳过此部分，转到下一部分。

1. 如果已有要使用的资源组，可跳过此步骤。 使用 [az group create](/cli/azure/group/#az-group-create) 创建用于包含和部署用户分配托管标识的[资源组](~/articles/azure-resource-manager/resource-group-overview.md#terminology)。 请务必将 `<RESOURCE GROUP>` 和 `<LOCATION>` 参数值替换为自己的值。 :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. 使用 [az identity create](/cli/azure/identity#az-identity-create) 创建用户分配托管标识。  `-g` 参数指定要创建用户分配托管标识的资源组，`-n` 参数指定其名称。 请务必将 `<RESOURCE GROUP>` 和 `<USER ASSIGNED IDENTITY NAME>` 参数值替换为自己的值：

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   响应包含所创建的用户分配托管标识的详细信息，与以下示例类似。 下一步会用到分配给用户分配托管标识的资源 `id` 值。

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. 使用 [az vmss create](/cli/azure/vmss/#az-vmss-create) 创建 VMSS。 以下示例创建与新用户分配托管标识关联的 VMSS，用 `--assign-identity` 参数指定。 请务必将 `<RESOURCE GROUP>`、`<VMSS NAME>`、`<USER NAME>`、`<PASSWORD>` 和 `<USER ASSIGNED IDENTITY>` 参数值替换为你自己的值。 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>将用户分配托管标识分配到现有虚拟机规模集

1. 使用 [az identity create](/cli/azure/identity#az-identity-create) 创建用户分配托管标识。  `-g` 参数指定要创建用户分配托管标识的资源组，`-n` 参数指定其名称。 请务必将 `<RESOURCE GROUP>` 和 `<USER ASSIGNED IDENTITY NAME>` 参数值替换为自己的值：

    > [!IMPORTANT]
    > 目前不支持创建名称中具有特殊字符（即下划线）的用户分配托管标识。 请使用字母数字字符。 稍后返回查看更新。  有关详细信息，请参阅 [FAQ 和已知问题](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
响应包含所创建的用户分配托管标识的详细信息，与以下示例类似。

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. 使用 [az vmss identity assign](/cli/azure/vmss/identity) 将用户分配托管标识分配给 VMSS。 请务必将 `<RESOURCE GROUP>` 和 `<VMSS NAME>` 参数值替换为自己的值。 `<USER ASSIGNED IDENTITY>` 为上一步创建的用户分配标识的资源 `name` 属性：

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>从 Azure 虚拟机规模集删除用户分配的托管标识

要从虚拟机规模集中删除用户分配托管标识，请使用 [az vmss identity remove](/cli/azure/vmss/identity#az-vmss-identity-remove)。 如果这是用户分配给虚拟机规模集的唯一托管标识，则 `UserAssigned` 将从标识类型值中删除。  请务必将 `<RESOURCE GROUP>` 和 `<VMSS NAME>` 参数值替换为自己的值。 `<USER ASSIGNED IDENTITY>` 将为用户分配托管标识的 `name` 属性，可通过 `az vmss identity show` 在虚拟机规模集的标识部分中找到：

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY>
```

如果虚拟机规模集没有系统分配的托管标识，并且你想要从中删除所有用户分配的托管标识，请使用以下命令：

> [!NOTE]
> 值 `none` 区分大小写。 它必须为小写。

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

如果虚拟机规模集同时具有系统分配托管标识和用户分配托管标识，则可通过切换为仅使用系统分配托管标识，删除所有用户分配标识。 请使用以下命令：

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>后续步骤

- [Azure 资源的托管标识概述](overview.md)
- 有关完整的 Azure 虚拟机规模集创建快速入门，请参阅： 

  - [使用 CLI 创建虚拟机规模集](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















