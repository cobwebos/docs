---
title: 限制来宾用户访问权限-Azure Active Directory |Microsoft Docs
description: 使用 Azure 门户、PowerShell 或 Microsoft Graph 在 Azure Active Directory 中限制来宾用户的访问权限
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 09/04/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 418be35cb7996acaa7f11f37627d065451c9c7c6
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2020
ms.locfileid: "90055208"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>限制来宾访问权限 (预览) 在 Azure Active Directory

Azure Active Directory (Azure AD) 允许你在 Azure AD 中限制外部来宾用户可以在其组织中看到的内容。 默认情况下，来宾用户在 Azure AD 中设置为有限的权限级别，而成员用户的默认值是完整的默认用户权限集。 这是你的 Azure AD 组织的外部协作设置中新的来宾用户权限级别的预览，以实现更严格的访问权限，因此你现在的来宾访问权限选项如下：

权限级别         | 访问级别
----------------         | ------------
与成员用户相同     | 来宾与成员用户具有相同的访问权限 Azure AD 资源
受限访问 (默认值)  | 来宾可以查看所有非隐藏组的成员身份
**受限访问 (新的) **  | **来宾看不到任何组的成员身份**

当来宾访问受到限制时，来宾只能查看自己的用户配置文件。 即使来宾正在按用户主体名称或 objectId 搜索，也不允许查看其他用户的权限。 受限访问权限还限制来宾用户看到其所在组的成员身份。 有关总体默认用户权限（包括来宾用户权限）的详细信息，请参阅 [Azure Active Directory 中的默认用户权限是什么？](../fundamentals/users-default-permissions.md)。

## <a name="permissions-and-licenses"></a>权限和许可证

你必须是全局管理员角色才能配置外部协作设置。 无额外的许可要求来限制来宾访问权限。

## <a name="update-in-the-azure-portal"></a>Azure 门户中的更新

已对来宾用户权限的现有 Azure 门户控件进行更改。

1. 用全局管理员权限登录到 [Azure AD 管理中心](https://aad.portal.azure.com) 。
1. 在组织的 **Azure Active Directory** 概述页上，选择 " **用户设置**"。
1. 在“外部用户”下，选择“管理外部协作设置”。  
1. 在 " **外部协作设置** " 页上，选择 " **来宾用户访问限制为其自己的目录对象的属性和成员身份** " 选项。

    ![Azure AD 外部协作设置 "页](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. 选择“保存”。 对于来宾用户，更改可能需要15分钟才能生效。

## <a name="update-with-the-microsoft-graph-api"></a>更新 Microsoft Graph API

我们添加了新的 Microsoft Graph API 来配置 Azure AD 组织中的来宾权限。 可以执行以下 API 调用来分配任何权限级别。 此处使用的 guestUserRoleId 的值是说明最受限制的来宾用户设置。 有关使用 Microsoft Graph 设置来宾权限的详细信息，请参阅 [authorizationPolicy 资源类型](/graph/api/resources/authorizationpolicy)。

### <a name="configuring-for-the-first-time"></a>首次配置

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

响应应为成功204。

### <a name="updating-the-existing-value"></a>更新现有值

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

响应应为成功204。

### <a name="view-the-current-value"></a>查看当前值

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

示例响应:

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>用 PowerShell cmdlet 更新

利用此功能，我们添加了通过 PowerShell v2 cmdlet 配置受限权限的功能。 Get 和 Set PowerShell cmdlet 已在版本2.0.2.85 中发布。

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Get 命令： AzureADMSAuthorizationPolicy

示例：

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Set 命令： AzureADMSAuthorizationPolicy

示例：

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> 请求时，必须输入 authorizationPolicy 作为 ID。

## <a name="supported-microsoft-365-services"></a>支持的 Microsoft 365 服务

### <a name="supported-services"></a>支持的服务

通过支持，我们意味着体验按预期方式进行;具体而言，它与当前来宾体验相同。

- Teams
- Outlook (OWA) 
- SharePoint

### <a name="services-currently-not-supported"></a>当前不支持服务

没有当前支持的服务可能与新的来宾限制设置存在兼容性问题。

- 窗体
- 团队中的 Planner
- Planner 应用
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>常见问题 (FAQ)

问题 | Answer
-------- | ------
这些权限适用于何处？ | 这些目录级别权限在 Azure AD 服务和门户中强制执行，包括 Microsoft Graph、PowerShell v2、Azure 门户和我的应用门户。 利用协作方案 Microsoft 365 组 Microsoft 365 服务也会受到影响，特别是 Outlook、Microsoft 团队和 SharePoint。
此功能在 "我的应用" 门户中的哪些部分会受到影响？ | "我的应用" 门户中的组功能将服从这些新权限。 这包括在我的应用中查看组列表和组成员身份的所有路径。 未对组磁贴可用性进行任何更改。 组磁贴可用性仍由 Azure 管理门户中的现有组设置控制。
这些权限是否替代 SharePoint 或 Microsoft 团队来宾设置？ | 否。 这些现有设置仍然会控制这些应用程序中的体验和访问权限。 例如，如果您在 SharePoint 中看到问题，请仔细检查您的外部共享设置。
Planner 和 Yammer 中已知的兼容性问题是什么？ | <li>当权限设置为 "受限制" 时，登录到 Planner 应用或访问 Microsoft 团队中的 Planner 的来宾将无法访问其计划或任何任务。<li>当权限设置为 "受限制" 时，登录到 Yammer 的来宾将无法离开组。
我的现有来宾权限是否会在我的租户中更改？ | 当前设置未进行任何更改。 我们保持与现有设置的向后兼容性。 确定要进行更改的时间。
这些权限是否默认设置？ | 否。 现有的默认权限保持不变。 您可以选择将权限设置为更严格的权限。
此功能是否有任何许可要求？ | 不能，此功能没有新的许可要求。

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure AD 中的现有来宾权限，请参阅 [Azure Active Directory 中的默认用户权限是什么？](../fundamentals/users-default-permissions.md)。
- 若要查看用于限制来宾访问的 Microsoft Graph API 方法，请参阅 [authorizationPolicy 资源类型](/graph/api/resources/authorizationpolicy)。
- 若要撤消对某用户的所有访问权限，请参阅 [在 Azure AD 撤消用户访问](users-revoke-access.md)权限。