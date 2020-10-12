---
title: 使用 Azure PowerShell 创建、列出和删除用户分配的托管标识 - Azure AD
description: 分步说明如何使用 Azure PowerShell 创建、列出和删除用户分配托管标识。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8649c9faf3905e69232cdc15bbba6607abe3e9c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90969505"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>使用 Azure PowerShell 创建、列出和删除用户分配托管标识

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个托管标识。 此标识可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中输入凭据了。 

本文将介绍如何使用 Azure PowerShell 创建、列出和删除用户分配托管标识。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#managed-identity-types)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 若要运行示例脚本，有两个选项：
    - 使用 [Azure Cloud Shell](../../cloud-shell/overview.md)，你可使用代码块右上角的“试用”按钮打开它。
    - 如下一节所述，使用 Azure PowerShell 在本地运行脚本。

### <a name="configure-azure-powershell-locally"></a>在本地配置 Azure PowerShell

若要在本文情景中本地使用 Azure PowerShell（而不是使用 Cloud Shell），请完成以下步骤：

1. 安装[最新版本的 Azure PowerShell](/powershell/azure/install-az-ps)（如果尚未安装）。

1. 登录 Azure：

    ```azurepowershell
    Connect-AzAccount
    ```

1. 安装[最新版本的 PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget)。

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    在下一步运行此命令后，可能需要 `Exit` 退出当前 PowerShell 会话。

1. 安装 `Az.ManagedServiceIdentity` 模块的预发布版本，以执行本文中用户分配托管标识操作：

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识

若要创建用户分配的托管标识，你的帐户需要[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

若要创建用户分配的托管标识，请使用 `New-AzUserAssignedIdentity` 命令。 `ResourceGroupName` 参数指定要从中创建用户分配的托管标识的资源组，`-Name` 参数指定其名称。 将 `<RESOURCE GROUP>` 和 `<USER ASSIGNED IDENTITY NAME>` 参数值替换为自己的值：

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>列出用户分配的托管标识

若要列出/读取用户分配的托管标识，你的帐户需要[托管标识操作员](../../role-based-access-control/built-in-roles.md#managed-identity-operator)或[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

要列出用户分配的托管标识，请使用 [Get-AzUserAssigned] 命令。  `-ResourceGroupName` 参数指定创建了用户分配托管标识的资源组。 将 `<RESOURCE GROUP>` 替换为自己的值：

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
在响应中，用户分配托管标识为 `Type` 键返回 `"Microsoft.ManagedIdentity/userAssignedIdentities"` 值。

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>删除用户分配的托管标识

若要删除用户分配的托管标识，你的帐户需要[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

若要删除用户分配的托管标识，请使用 `Remove-AzUserAssignedIdentity` 命令。  `-ResourceGroupName` 参数指定从中已创建用户分配标识的资源组，`-Name` 参数指定其名称。 将 `<RESOURCE GROUP>` 和 `<USER ASSIGNED IDENTITY NAME>` 参数值替换为自己的值：

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> 删除用户分配的托管标识不会从将其分配到的任何资源中删除引用。 标识分配需要单独移除。

## <a name="next-steps"></a>后续步骤

有关 Azure 资源的 Azure PowerShell 托管标识命令的完整列表和详细信息，请参阅 [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity)。
