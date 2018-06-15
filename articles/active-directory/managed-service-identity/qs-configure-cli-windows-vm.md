---
title: 如何在 Azure VM 上使用 Azure CLI 配置系统分配标识和用户分配标识
description: 分步说明如何在 Azure VM 上使用 Azure CLI 配置系统分配标识和用户分配标识。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 09ee4dfc403bf570631f64b0b13d1592a03eed17
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698962"
---
# <a name="configure-managed-service-identity-msi-on-an-azure-vm-using-azure-cli"></a>在 Azure VM 上使用 Azure CLI 配置托管服务标识 (MSI)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

托管服务标识为 Azure 服务提供了 Azure Active Directory 中的自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

在本文中，你将了解如何在 Azure VM 上使用 Azure CLI 执行以下托管服务标识操作：
- 在 Azure VM 上启用和禁用系统分配标识
- 在 Azure VM 上添加和删除用户分配标识

## <a name="prerequisites"></a>先决条件

- 如果不熟悉托管服务标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配标识与用户分配标识之间的差异](overview.md#how-does-it-work)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 若要运行 CLI 脚本示例，可以使用下列三种方法：

    - 在 Azure 门户中使用 [Azure Cloud Shell](../../cloud-shell/overview.md)（见下一部分）。
    - 单击各代码块右上角的“试运行”按钮，使用嵌入的 Azure Cloud Shell。
    - 如果希望使用本地 CLI 控制台，可以[安装最新版 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)（2.0.13 或更高版本）。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>系统分配标识

在此部分中，你将了解如何在 Azure VM 上使用 Azure CLI 来启用和禁用系统分配标识。

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>在创建 Azure VM 的过程中启用系统分配标识

若要创建启用了系统分配标识的 Azure VM，请执行以下操作：

1. 如果在本地控制台中使用 Azure CLI，首先请使用 [az login](/cli/azure/reference-index#az_login) 登录到 Azure。 使用与要在其下部署 VM 的 Azure 订阅关联的帐户：

   ```azurecli-interactive
   az login
   ```

2. 运行 [az group create](/cli/azure/group/#az_group_create)，创建用于容纳和部署 VM 及其相关资源的[资源组](../../azure-resource-manager/resource-group-overview.md#terminology)。 如果已有要改用的资源组，可以跳过这一步：

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. 运行 [az vm create](/cli/azure/vm/#az_vm_create) 创建 VM。 下面的示例创建名为 *myVM* 且已启用系统分配标识的 VM（应 `--assign-identity` 参数的要求）。 `--admin-username` 和 `--admin-password` 参数指定用于登录虚拟机的管理用户名和密码帐户。 针对自己的环境相应地更新这些值： 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>在现有 Azure VM 上启用系统分配标识

如果需要在现有 VM 上启用系统分配标识，请执行以下操作：

1. 如果在本地控制台中使用 Azure CLI，首先请使用 [az login](/cli/azure/reference-index#az_login) 登录到 Azure。 使用与包含 VM 的 Azure 订阅关联的帐户。 此外，请确保该帐户属于可授予对 VM 的写权限的角色，如“虚拟机参与者”：

   ```azurecli-interactive
   az login
   ```

2. 将 [az vm identity assign](/cli/azure/vm/identity/#az_vm_identity_assign) 与 `identity assign` 命令配合使用，为现有 VM 启用系统分配标识：

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>从 Azure VM 中禁用系统分配标识

> [!NOTE]
> 目前不支持从虚拟机中禁用托管服务标识。 同时，你可以切换使用系统分配标识和用户分配标识。

如果某个虚拟机不再需要系统分配标识，但仍需要用户分配标识，请使用以下命令：

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```
若要删除 MSI VM 扩展，请将 `-n ManagedIdentityExtensionForWindows` 或 `-n ManagedIdentityExtensionForLinux` 开关（具体取决于 VM 的类型）与 [az vm extension delete](https://docs.microsoft.com/cli/azure/vm/#assign-identity) 配合使用：

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

## <a name="user-assigned-identity"></a>用户分配标识

在此部分中，你将了解如何在 Azure VM 中使用 Azure CLI 来添加和删除用户分配标识。

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>在创建 Azure VM 的过程中分配用户分配标识

此部分介绍如何创建 VM 以及向 VM 分配用户分配标识。 如果已有要使用的 VM，请跳过本部分，继续到下一部分。

1. 如果已有要使用的资源组，可跳过此步骤。 使用 [az group create](/cli/azure/group/#az_group_create)，创建用于容纳和部署 VM 的[资源组](~/articles/azure-resource-manager/resource-group-overview.md#terminology)。 请务必将 `<RESOURCE GROUP>` 和 `<LOCATION>` 参数值替换为自己的值。 :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. 使用 [az identity create](/cli/azure/identity#az_identity_create) 创建用户分配标识。  `-g` 参数指定要创建用户分配标识的资源组，`-n` 参数指定其名称。    
    
[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


```azurecli-interactive
az identity create -g myResourceGroup -n myUserAssignedIdentity
```
响应包含所创建的用户分配标识的详细信息，与以下示例类似。 下一步会用到分配给用户分配标识的资源 ID 值。

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

3. 运行 [az vm create](/cli/azure/vm/#az_vm_create) 创建 VM。 下面的示例创建与新用户分配标识关联的 VM，用 `--assign-identity` 参数指定。 请务必将 `<RESOURCE GROUP>`、`<VM NAME>`、`<USER NAME>`、`<PASSWORD>` 和 `<MSI ID>` 参数值替换为你自己的值。 对于 `<MSI ID>`，请使用上一步创建的用户分配标识的资源 `id` 属性： 

```azurecli-interactive 
az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>向现有 Azure VM 分配用户分配标识

1. 使用 [az identity create](/cli/azure/identity#az-identity-create) 创建用户分配标识。  `-g` 参数指定要创建用户分配标识的资源组，`-n` 参数指定其名称。 请务必将 `<RESOURCE GROUP>` 和 `<MSI NAME>` 参数值替换为自己的值：

    > [!IMPORTANT]
    > 目前不支持创建名称中具有特殊字符（即下划线）的用户分配标识。 请使用字母数字字符。 稍后返回查看更新。  有关详细信息，请参阅 [FAQ 和已知问题](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
响应包含所创建的用户分配 MSI 的详细信息，与以下示例类似。 下一步会用到分配给用户分配标识的资源 `id` 值。

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

2. 使用 [az vm identity assign](/cli/azure/vm#az-vm-identity-assign) 将用户分配标识分配给 VM。 请务必将 `<RESOURCE GROUP>` 和 `<VM NAME>` 参数值替换为自己的值。 `<MSI ID>` 将为上一步创建的用户分配标识的资源 `id` 属性：

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>从 Azure VM 中删除用户分配标识

> [!NOTE]
> 目前不支持从虚拟机中删除所有用户分配标识，除非有系统分配标识。

如果 VM 具有多个用户分配标识，则可以使用 [az vm identity remove](/cli/azure/vm#az-vm-identity-remove) 将这些标识删除到只剩一个。 请务必将 `<RESOURCE GROUP>` 和 `<VM NAME>` 参数值替换为自己的值。 `<MSI NAME>` 将为用户分配标识的 `name` 属性，可通过 `az vm show` 在 VM 的标识部分中找到：

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```
如果 VM 同时具有系统分配标识和用户分配标识，则可以切换为仅使用系统分配标识，从而删除所有用户分配标识。 请使用以下命令：

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null 
```

## <a name="related-content"></a>相关内容
- [托管服务标识概述](overview.md)
- 有关完整的 Azure VM 创建快速入门，请参阅： 
  - [使用 CLI 创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-cli.md)  
  - [使用 CLI 创建 Linux 虚拟机](../../virtual-machines/linux/quick-create-cli.md) 

















