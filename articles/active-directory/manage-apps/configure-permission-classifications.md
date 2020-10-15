---
title: Azure AD 配置权限分类
description: 了解如何管理委托的权限分类。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: phsignor
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: d5016dbff30eb054e413acc66b1b2dfee62a5873
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070667"
---
# <a name="configure-permission-classifications"></a>配置权限分类

通过权限分类，可根据组织的策略和风险评估确定不同权限的影响。 例如，可使用同意策略中的权限分类来确定允许用户同意哪一组权限。

## <a name="manage-permission-classifications"></a>管理权限分类

目前仅支持“影响较低”这一权限分类。 只有无需管理员同意的委托权限可被分类为“影响较低”。

> [!TIP]
> 执行基本登录所需的最小权限是 `openid` 、 `profile` 、 `email` `User.Read` 和 `offline_access` ，它们都是对 Microsoft Graph 的所有委派的权限。 使用这些权限时，应用程序可以读取已登录用户的完整配置文件详细信息，即使用户不再使用该应用程序，也可以保留此访问。

# <a name="portal"></a>[门户](#tab/azure-portal)

按照以下步骤使用 Azure 门户对权限进行分类：

1. 以 "[全局管理员](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)"、"[应用程序管理员](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)" 或 "[云应用程序管理员](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)" 身份登录到[Azure 门户](https://portal.azure.com)
1. 选择“Azure Active Directory” > “企业应用程序” > “同意和权限” > “权限分类”   。
1. 选择“添加权限”，再将一个权限分类为“影响较小”。
1. 选择 API，然后选择委托的权限。

在下例中，我们对单一登录所需的最小一组权限进行了分类：

:::image type="content" source="media/configure-permission-classifications/permission-classifications.png" alt-text="权限分类":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

可使用最新的 Azure AD PowerShell 预览版模块 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) 对权限进行分类。 权限分类是在发布权限的 API 的 ServicePrincipal 对象上配置的。

#### <a name="list-the-current-permission-classifications-for-an-api"></a>列出 API 的当前权限分类

1. 检索 API 的 ServicePrincipal 对象。 在这里，我们将检索 Microsoft Graph API 的 ServicePrincipal 对象：

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. 读取 API 的委托权限分类：

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="classify-a-permission-as-low-impact"></a>将权限分类为 "影响较小"

1. 检索 API 的 ServicePrincipal 对象。 在这里，我们将检索 Microsoft Graph API 的 ServicePrincipal 对象：

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. 查找要对其分类的委托的权限：

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. 使用权限名称和 ID 设置权限分类：

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="remove-a-delegated-permission-classification"></a>删除委托的权限分类

1. 检索 API 的 ServicePrincipal 对象。 在这里，我们将检索 Microsoft Graph API 的 ServicePrincipal 对象：

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. 若要查找要删除的委托权限分类：

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. 请删除权限分类：

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

---

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请访问以下链接：

* [配置用户同意设置](configure-user-consent.md)
* [配置管理员同意工作流](configure-admin-consent-workflow.md)
* [了解如何管理对应用程序的同意并评估同意请求](manage-consent-requests.md)
* [向应用程序授予租户范围的管理许可](grant-admin-consent.md)
* [Microsoft 标识平台中的权限和许可](../develop/active-directory-v2-scopes.md)

获取帮助或查找问题的答案：
* [StackOverflow 上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
