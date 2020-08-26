---
title: 默认用户权限 - Azure Active Directory | Microsoft Docs
description: 了解 Azure Active Directory 中提供的不同用户权限。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18, contperfq1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 042a881cc6021842dec126c92ff13f306f79dad1
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705225"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Azure Active Directory 中的默认用户权限是什么？
在 Azure Active Directory (Azure AD) 中，所有用户都被授予一组默认权限。 用户的访问权限由用户的类型、其[角色分配](active-directory-users-assign-role-azure-portal.md)及其对单个对象的所有权构成。 本文将会介绍这些默认权限，并将成员和来宾用户的默认权限进行比较。 只能在 Azure AD 的用户设置中更改默认用户权限。

## <a name="member-and-guest-users"></a>成员和来宾用户
获得的默认权限集取决于用户是租户的本机成员（成员用户），还是用户作为 B2B 协作来宾（来宾用户）从另一个目录转入。 有关添加来宾用户的详细信息，请参阅[什么是 Azure AD B2B 协作？](../b2b/what-is-b2b.md)。
* 成员用户可以注册应用程序、管理自己的个人资料照片和手机号码、更改自己的密码，以及邀请 B2B 来宾。 此外，用户可以读取所有目录信息（少数用户除外）。 
* 来宾用户的目录权限受到限制。 他们可以管理自己的配置文件、更改自己的密码并检索有关其他用户、组和应用程序的一些信息，但它们不能读取所有目录信息。 例如，来宾用户无法枚举用户、组和其他目录对象。 可将来宾添加到管理员角色，从而向他们授予角色中包含的完全读取和写入权限。 来宾还可以邀请其他来宾。

## <a name="compare-member-and-guest-default-permissions"></a>比较成员和来宾的默认权限

**区域** | **成员用户权限** | **默认来宾用户权限** | **受限来宾用户权限 (预览) **
------------ | --------- | ---------- | ----------
用户和联系人 | <ul><li>读取用户和联系人的所有公共属性</li><li>邀请来宾<li>更改自己的密码<li>管理自己的手机号码<li>管理自己的照片<li>使自己的刷新令牌失效</li></ul> | <ul><li>读取自己的属性<li>读取其他用户和联系人的显示名称、电子邮件、登录名、照片、用户主体名称和用户类型属性<li>更改自己的密码<li>如果允许，按显示名称、用户主体名称或 ObjectId (搜索其他用户) <li>其他用户的读取管理器和直接报表信息</li></ul> | <ul><li>读取自己的属性<li>更改自己的密码</li></ul>
组 | <ul><li>创建安全组<li>创建 Office 365 组<li>读取组的所有属性<li>读取非隐藏的组成员身份<li>读取加入的组的隐藏 Office 365 组成员身份<li>管理用户拥有的组的属性、所有权和成员身份<li>将来宾添加到拥有的组<li>管理动态成员身份设置<li>删除拥有的组<li>还原拥有的 Office 365 组</li></ul> | <ul><li>读取所有非隐藏组的属性，包括成员身份和所有权 (即使是未加入的组) <li>读取加入的组的隐藏 Office 365 组成员身份<li>按显示名称或 ObjectId (搜索组（如果允许）) </li></ul> | 无权限
应用程序 | <ul><li>注册（创建）新应用程序<li>读取已注册的应用程序和企业应用程序的属性<li>管理拥有的应用程序的应用程序属性、分配和凭据<li>创建或删除用户的应用程序密码<li>删除拥有的应用程序<li>还原拥有的应用程序</li></ul> | <ul><li>读取已注册的应用程序和企业应用程序的属性</li></ul> | <ul><li>读取已注册的应用程序和企业应用程序的属性
设备</li></ul> | <ul><li>读取设备的所有属性<li>管理拥有的设备的所有属性</li></ul> | 无权限 | 无权限
Directory | <ul><li>读取所有公司信息<li>读取所有域<li>读取所有合作伙伴协定</li></ul> | <ul><li>读取显示名称和已验证的域</li></ul> | <ul><li>读取显示名称和已验证的域</li></ul>
角色和范围 | <ul><li>读取所有管理角色和成员身份<li>读取管理单元的所有属性和成员身份</li></ul> | 无权限 | 无权限
订阅 | <ul><li>读取所有订阅<li>启用服务计划成员</li></ul> | 无权限 | 无权限
策略 | <ul><li>读取策略的所有属性<li>管理拥有的策略的所有属性</li></ul> | 无权限 | 无权限

## <a name="to-restrict-the-default-permissions-for-member-users"></a>限制成员用户的默认权限

可以通过以下方式限制成员用户的默认权限：

权限 | 设置说明
---------- | ------------
用户可以注册应用程序 | 将此选项设置为“否”可阻止用户创建应用程序注册。 然后，通过将特定的个人添加到“应用程序开发人员”角色，可以将该能力重新授予这些个人。
允许用户使用 LinkedIn 连接工作或学校帐户 | 将此选项设置为“否”可阻止用户使用其 LinkedIn 帐户连接其工作或学校帐户。 有关详细信息，请参阅 [LinkedIn 帐户连接数据共享和同意](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent)。
能够创建安全组 | 将此选项设置为“否”可阻止用户创建安全组。 全局管理员和用户管理员仍可创建安全组。 有关操作方法，请参阅[用于配置组设置的 Azure Active Directory cmdlet](../users-groups-roles/groups-settings-cmdlets.md)。
能够创建 Office 365 组 | 将此选项设置为“否”可阻止用户创建 Office 365 组。 将此选项设置为“某些”可让选定的一组用户创建 Office 365 组。 全局管理员和用户管理员仍可创建 Office 365 组。 有关操作方法，请参阅[用于配置组设置的 Azure Active Directory cmdlet](../users-groups-roles/groups-settings-cmdlets.md)。
限制访问 Azure AD 管理门户 | 如果将此选项设为“否”，则允许非管理员使用 Azure AD 管理门户读取和管理 Azure AD 资源。 如果设为“是”，则限制所有非管理员在管理门户中访问任何 Azure AD 数据。<p>**注意**：此设置不会限制使用 PowerShell 或其他客户端（如 Visual Studio）对 Azure AD 数据的访问。当设置为 "是" 时，若要向特定的非管理员用户授予使用 Azure AD 管理门户的能力，请分配任何管理角色，如目录读取者角色。<p>此角色允许读取基本目录信息，默认情况下成员用户有这些信息（来宾和服务主体没有）。
能够读取其他用户 | 此设置仅可在 PowerShell 中使用。 将此标记设置为 $false 可阻止所有非管理员用户从目录读取用户信息。 此标记不会阻止读取其他 Microsoft 服务（如 Exchange Online）中的用户信息。 此设置适用于特殊情况，因此不建议将此标记设置为 $false。


## <a name="to-restrict-the-default-permissions-for-guest-users"></a>限制来宾用户的默认权限

可以通过以下方式限制来宾用户的默认权限：

>[!NOTE] 
>"来宾用户访问限制" 设置已替换 " **来宾用户权限** 限制" 设置。 有关使用此功能的指南，请参阅 [Azure Active Directory 中的限制来宾访问权限 (预览) ](../users-groups-roles/users-restrict-guest-permissions.md)。

权限 | 设置说明
---------- | ------------
来宾用户访问限制 (预览)  | 将此选项设置为 Guest 用户具有与成员相同的访问权限。默认情况下，会向来宾用户授予所有成员用户权限。<p>默认情况下，将此选项设置为 Guest 用户访问仅限于其自己的目录对象的属性和成员身份限制其自己的用户配置文件的来宾访问权限。 即使按用户主体名称或 objectId 进行搜索，也不再允许访问其他用户。 还不再允许访问包含组成员身份的组。 此设置不会阻止对其他 Microsoft 服务（如 Microsoft 团队）中的组进行访问。 有关详细信息，请参阅 [Microsoft 团队来宾访问权限]() 。<p>不管此权限设置如何，来宾用户仍可添加到管理员角色。
来宾可发出邀请 | 如果将此选项设置为 "是"，则允许来宾邀请其他来宾。 若要了解详细信息，请参阅 [为 B2B 协作委派邀请](https://docs.microsoft.com/azure/active-directory/external-identities/delegate-invitations#configure-b2b-external-collaboration-settings) 。
成员可发出邀请 | 成员可以邀请将此选项设置为 "是"，以允许目录的非管理员成员邀请来宾。 若要了解详细信息，请参阅 [为 B2B 协作委派邀请](https://docs.microsoft.com/azure/active-directory/external-identities/delegate-invitations#configure-b2b-external-collaboration-settings) 。
拥有来宾邀请者角色的管理员和用户可发出邀请 | 如果将此选项设置为 "是"，则允许 "来宾邀请者" 角色中的管理员和用户邀请来宾。 如果设置为 "是"，则来宾邀请者角色中的用户仍可以邀请来宾，无论成员是否可以邀请设置。 若要了解详细信息，请参阅 [为 B2B 协作委派邀请](https://docs.microsoft.com/azure/active-directory/external-identities/delegate-invitations#assign-the-guest-inviter-role-to-a-user) 。

## <a name="object-ownership"></a>对象所有权

### <a name="application-registration-owner-permissions"></a>应用程序注册所有者权限
当某个用户注册某个应用程序时，该用户将自动添加为该应用程序的所有者。 所有者可以管理应用程序的元数据，例如应用请求的名称和权限。 他们还可以管理应用程序的特定于租户的配置，例如 SSO 配置和用户分配。 所有者还可以添加或删除其他所有者。 与全局管理员不同，所有者只能管理他们拥有的应用程序。

### <a name="enterprise-application-owner-permissions"></a>企业应用程序所有者权限
当某个用户添加新的企业应用程序时，系统会将该用户自动添加为所有者。 作为所有者，他们可以管理应用程序的特定于租户的配置，例如 SSO 配置、设置和用户分配。 所有者还可以添加或删除其他所有者。 与全局管理员不同，所有者只能管理他们拥有的应用程序。

### <a name="group-owner-permissions"></a>组所有者权限
当某个用户创建某个组时，该用户将自动添加为该组的所有者。 所有者可以管理组的属性（例如名称），以及管理组成员身份。 所有者还可以添加或删除其他所有者。 与全局管理员和用户管理员不同，所有者只能管理他们拥有的组。 若要分配组所有者，请参阅[管理组的所有者](active-directory-accessmanagement-managing-group-owners.md)。

### <a name="ownership-permissions"></a>所有权权限
下表描述成员用户在 Azure Active Directory 中具有的针对所拥有对象的特定权限。 用户仅在所拥有的对象上具有这些权限。

#### <a name="owned-application-registrations"></a>拥有的应用程序注册
用户可以在拥有的应用程序注册上执行以下操作。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/applications/audience/update | 更新 Azure Active Directory 中的 applications.audience 属性。 |
| microsoft.directory/applications/authentication/update | 更新 Azure Active Directory 中的 applications.authentication 属性。 |
| microsoft.directory/applications/basic/update | 更新 Azure Active Directory 中应用程序的基本属性。 |
| microsoft.directory/applications/credentials/update | 更新 Azure Active Directory 中的 applications.credentials 属性。 |
| microsoft.directory/applications/delete | 删除 Azure Active Directory 中的应用程序。 |
| microsoft.directory/applications/owners/update | 更新 Azure Active Directory 中的 applications.owners 属性。 |
| microsoft.directory/applications/permissions/update | 更新 Azure Active Directory 中的 applications.permissions 属性。 |
| microsoft.directory/applications/policies/update | 更新 Azure Active Directory 中的 applications.policies 属性。 |
| microsoft.directory/applications/restore | 还原 Azure Active Directory 中的应用程序。 |

#### <a name="owned-enterprise-applications"></a>拥有的企业应用程序
用户可以在拥有的企业应用程序上执行以下操作。 企业应用程序包含服务主体、一个或多个应用程序策略，有时还包含应用程序对象，该对象与服务主体位于同一租户中。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| microsoft.directory/policies/basic/update | 更新 Azure Active Directory 中策略的基本属性。 |
| microsoft.directory/policies/delete | 删除 Azure Active Directory 中的策略。 |
| microsoft.directory/policies/owners/update | 更新 Azure Active Directory 中的 policies.owners 属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft.directory/servicePrincipals/audience/update | 更新 Azure Active Directory 中的 servicePrincipals.audience 属性。 |
| microsoft.directory/servicePrincipals/authentication/update | 更新 Azure Active Directory 中的 servicePrincipals.authentication 属性。 |
| microsoft.directory/servicePrincipals/basic/update | 更新 Azure Active Directory 中 servicePrincipals 的基本属性。 |
| microsoft.directory/servicePrincipals/credentials/update | 更新 Azure Active Directory 中的 servicePrincipals.credentials 属性。 |
| microsoft.directory/servicePrincipals/delete | 删除 Azure Active Directory 中的 servicePrincipals。 |
| microsoft.directory/servicePrincipals/owners/update | 更新 Azure Active Directory 中的 servicePrincipals.owners 属性。 |
| microsoft.directory/servicePrincipals/permissions/update | 更新 Azure Active Directory 中的 servicePrincipals.permissions 属性。 |
| microsoft.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 属性。 |
| microsoft.directory/signInReports/allProperties/read | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |

#### <a name="owned-devices"></a>拥有的设备
用户可以在拥有的设备上执行以下操作。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | 读取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 属性。 |
| microsoft.directory/devices/disable | 禁用 Azure Active Directory 中的设备。 |

#### <a name="owned-groups"></a>拥有的组
用户可以在拥有的组上执行以下操作。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.appRoleAssignments 属性。 |
| microsoft.directory/groups/basic/update | 更新 Azure Active Directory 中组的基本属性。 |
| microsoft.directory/groups/delete | 删除 Azure Active Directory 中的组。 |
| microsoft.directory/groups/dynamicMembershipRule/update | 更新 Azure Active Directory 中的 groups.dynamicMembershipRule 属性。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft.directory/groups/restore | 还原 Azure Active Directory 中的组。 |
| microsoft.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 属性。 |

## <a name="next-steps"></a>后续步骤

* 若要了解有关 "来宾用户访问限制" 设置的详细信息，请参阅 [Azure Active Directory 中的 "限制来宾访问权限 (预览") ](../users-groups-roles/users-restrict-guest-permissions.md)。
* 若要详细了解如何分配 Azure AD 管理员角色，请参阅[在 Azure Active Directory 中向用户分配管理员角色](active-directory-users-assign-role-azure-portal.md)
* 若要了解有关如何在 Microsoft Azure 中控制资源访问的详细信息，请参阅 [了解 Azure 中的资源访问权限](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* 有关 Azure Active Directory 如何与 Azure 订阅相关联的详细信息，请参阅 [How Azure subscriptions are associated with Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)（Azure 订阅与 Azure Active Directory 的关联方式）
* [管理用户](add-users-azure-active-directory.md)
