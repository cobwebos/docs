---
title: 如何使用 Azure PowerShell 创建、列出和删除用户分配的 MSI
description: 分步说明如何使用 Azure PowerShell 创建、列出和删除用户分配的托管服务标识。
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
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: d413e5821aff8cf26dfc9ba03e6ec9d4134af76e
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698938"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-azure-powershell"></a>使用 Azure PowerShell 创建、列出或删除用户分配的标识

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

托管服务标识在 Azure Active Directory 中为 Azure 服务提供托管标识。 此标识可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中输入凭据了。 

在本文中，你将了解如何使用 Azure PowerShell 创建、列出和删除用户分配标识。

## <a name="prerequisites"></a>先决条件

- 如果不熟悉托管服务标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配标识与用户分配标识之间的差异](overview.md#how-does-it-work)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 安装[最新版本的 Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM)（如果尚未安装）。
- 如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 5.7.0 或更高版本。 运行 ` Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。

## <a name="create-a-user-assigned-identity"></a>创建用户分配的标识

如需创建用户分配标识，请使用 [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) 命令。 `ResourceGroupName` 参数指定要从中创建用户分配标识的资源组，`-Name` 参数指定其名称。 将 `<RESOURCE GROUP>` 和 `<USER ASSIGNED IDENTITY NAME>` 参数值替换为自己的值：

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>列出用户分配标识

若要列出用户分配标识，请使用 [Get AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity) 命令。  `-ResourceGroupName` 参数指定从中已创建用户分配标识的资源组。  将 `<RESOURCE GROUP>` 替换为自己的值：

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
在响应中，用户标识为 `Type` 键返回 `"Microsoft.ManagedIdentity/userAssignedIdentities"` 值。

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-identity"></a>删除用户分配标识

若要删除用户分配标识，请使用 [Remove-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity) 命令。  `-ResourceGroupName` 参数指定从中已创建用户分配标识的资源组，`-Name` 参数指定其名称。  将 `<RESOURCE GROUP>` 和 `<USER ASSIGNED IDENTITY NAME>` 参数值替换为自己的值：

 ```azurecli-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> 删除用户分配标识不会从将其分配到的任何资源中移除引用。 标识分配需要单独移除。

## <a name="related-content"></a>相关内容

有关 Azure PowerShell MSI 命令的完整列表和详细信息，请参阅 [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity)。


 
