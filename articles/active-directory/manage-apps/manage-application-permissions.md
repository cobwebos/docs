---
title: 管理用户和管理员权限-Azure Active Directory |Microsoft Docs
description: 了解如何在 Azure AD 上查看和管理应用程序的权限。 例如，撤消授予应用程序的所有权限。
services: active-directory
author: mimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95e13cedc0cdbaedc8c00b9d855057da7e631c19
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510872"
---
# <a name="take-action-on-overprivileged-or-suspicious-applications-in-azure-active-directory"></a>在 Azure Active Directory 中对 overprivileged 或可疑应用程序执行操作

了解如何查看和管理应用程序权限。 本文提供了不同的操作，你可以采取这些措施来根据方案保护应用程序。 这些操作适用于所有已添加到 Azure Active Directory (通过用户或管理员同意 Azure AD) 租户的应用程序。

有关许可应用程序的详细信息，请参阅 [Azure Active Directory 许可框架](../develop/consent-framework.md)。

## <a name="prerequisites"></a>先决条件

若要执行以下操作，必须以 "全局管理员"、"应用程序管理员" 或 "云应用程序管理员" 身份登录。

若要限制对应用程序的访问，你需要要求用户分配，然后将用户或组分配到应用程序。  有关详细信息，请参阅[分配用户和组的方法](methods-for-assigning-users-and-groups.md)。

可以访问 Azure AD 门户，获取上下文 PowerShell 脚本来执行这些操作。
 
1. 以全局管理员、应用程序管理员或云应用程序管理员的身份登录到 [Azure 门户](https://portal.azure.com)。
2. 选择**Azure Active Directory**  >  **企业应用程序**。
3. 选择要限制其访问权限的应用程序。
4. 选择“权限”。 在命令栏中，选择 " **检查权限**"。

!["检查权限" 窗口的屏幕截图。](./media/manage-application-permissions/review-permissions.png)


## <a name="control-access-to-an-application"></a>控制对应用程序的访问

建议通过打开 **用户分配** 设置来限制对应用程序的访问。

1. 以全局管理员、应用程序管理员或云应用程序管理员的身份登录到 [Azure 门户](https://portal.azure.com)。
2. 选择**Azure Active Directory**  >  **企业应用程序**。
3. 选择要限制其访问权限的应用程序。
4. 选择 " **属性**"，然后将 " **需要用户要求** " 设置为 **"是"**。
5. 选择 " **用户和组**"，然后删除分配给该应用程序的不需要的用户。
6. 将用户或组分配给应用程序。

还可以选择使用 PowerShell 删除分配到该应用程序的所有用户。

## <a name="revoke-all-permissions-for-an-application"></a>撤消应用程序的所有权限

使用 PowerShell 脚本将撤消授予此应用程序的所有权限。

> [!NOTE]
> 撤消当前授予的权限不会阻止用户重新同意该应用程序。 如果要阻止用户同意，请阅读 [配置用户同意应用程序的方式](configure-user-consent.md)。

（可选）可以禁用应用程序，防止用户访问应用，并使应用程序无法访问数据。

1. 以全局管理员、应用程序管理员或云应用程序管理员的身份登录到 [Azure 门户](https://portal.azure.com)。
2. 选择**Azure Active Directory**  >  **企业应用程序**。
3. 选择要限制其访问权限的应用程序。
4. 选择 " **属性**"，然后将 **"允许用户登录"** 设置为 " **否**"。

## <a name="investigate-a-suspicious-application"></a>调查可疑应用程序

建议通过打开 **用户分配** 设置来限制对应用程序的访问。 然后查看用户和管理员向应用程序授予的权限。

1. 以全局管理员、应用程序管理员或云应用程序管理员的身份登录到 [Azure 门户](https://portal.azure.com)。
3. 选择**Azure Active Directory**  >  **企业应用程序**。
5. 选择要限制其访问权限的应用程序。
6. 选择 " **属性**"，然后将 " **需要用户要求** " 设置为 **"是"**。
7. 选择 " **权限**"，并查看管理员和用户许可权限。

还可以通过使用 PowerShell 来执行以下操作：

- 删除所有已分配的用户，使其停止登录到应用程序。
- 使有权访问应用程序的用户的刷新令牌无效。
- 撤消应用程序的所有权限。

也可以禁用应用程序，阻止用户访问和停止应用程序对数据的访问。


## <a name="disable-a-malicious-application"></a>禁用恶意应用程序 

建议你禁用应用程序以阻止用户的访问，并使应用程序不会访问你的数据。 如果改为删除应用程序，用户可以重新同意该应用程序，并授予对数据的访问权限。

1. 以全局管理员、应用程序管理员或云应用程序管理员的身份登录到 [Azure 门户](https://portal.azure.com)。
2. 选择**Azure Active Directory**  >  **企业应用程序**。
3. 选择要限制其访问权限的应用程序。
4. 选择 " **属性**"，并复制 "对象 ID"。

### <a name="powershell-commands"></a>PowerShell 命令


检索服务主体对象 ID。

1. 以全局管理员、应用程序管理员或云应用程序管理员的身份登录到 [Azure 门户](https://portal.azure.com)。
2. 选择**Azure Active Directory**  >  **企业应用程序**。
3. 选择要限制其访问权限的应用程序。
4. 选择 " **属性**"，并复制 "对象 ID"。

```powershell
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $sp.ObjectId
```
删除分配给该应用程序的所有用户。
 ```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get Azure AD App role assignments using objectId of the Service Principal
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

    # Remove all users and groups assigned to the application
    $assignments | ForEach-Object {
        if ($_.PrincipalType -eq "User") {
            Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        } elseif ($_.PrincipalType -eq "Group") {
            Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        }
    }
 ```

撤消授予应用程序的权限。

```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get all delegated permissions for the service principal
    $spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

    # Remove all delegated permissions
    $spOAuth2PermissionsGrants | ForEach-Object {
        Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
    }

    # Get all application permissions for the service principal
    $spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

    # Remove all delegated permissions
    $spApplicationPermissions | ForEach-Object {
        Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
    }
```
使刷新令牌无效。
```powershell
        Connect-AzureAD

        # Get Service Principal using objectId
        $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

        # Get Azure AD App role assignments using objectID of the Service Principal
        $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

        # Revoke refresh token for all users assigned to the application
        $assignments | ForEach-Object {
            Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
        }
```
## <a name="next-steps"></a>后续步骤
- [管理对应用程序的同意并评估同意请求](manage-consent-requests.md)
- [配置用户同意](configure-user-consent.md)
- [配置管理员许可工作流](configure-admin-consent-workflow.md)
