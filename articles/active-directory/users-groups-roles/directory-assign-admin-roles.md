---
title: 在 Azure Active Directory 中分配管理员角色 | Microsoft Docs
description: 管理员角色可以添加用户、分配管理角色、重置用户密码、管理用户许可证，或者管理域。 分配为管理员角色的用户在组织已订阅的所有云服务中具有相同的权限。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/25/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 5d6254efbb6051bf4fcd01abd4fbf858b0211319
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399934"
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>在 Azure Active Directory 中分配管理员角色

使用 Azure Active Directory (Azure AD) 时，可以指定不同的管理员来执行不同的功能。 可以在 Azure AD 门户中指定管理员用于执行各种任务，例如，添加或更改用户、分配管理角色、重置用户密码、管理用户许可证，以及管理域名。

## <a name="details-about-the-global-administrator-role"></a>有关全局管理员角色的详细信息
全局管理员有权访问所有管理功能。 默认情况下，系统会将注册 Azure 订阅的人员指派为目录的全局管理员角色。 只有全局管理员才能分配其他管理员角色。

## <a name="assign-or-remove-administrator-roles"></a>分配或删除管理员角色
若要了解如何在 Azure Active Directory 中向用户分配管理角色，请参阅[在 Azure Active Directory 中向用户分配管理角色](../fundamentals/active-directory-users-assign-role-azure-portal.md)。

## <a name="available-roles"></a>可用的角色
提供以下管理员角色：

* **[应用程序管理员](#application-administrator)**：此角色中的用户可以创建和管理企业应用程序、应用程序注册和应用程序代理设置的所有方面。 此角色还可以同意委派权限，以及除 Microsoft Graph 和 Azure AD Graph 之外的应用程序权限。 在创建新应用程序注册或企业应用程序时，不会将此角色的成员添加为所有者。

* **[应用程序开发人员](#application-developer)**：在将设置“用户可以注册应用程序”设置为“否”时，此角色中的用户可以创建应用程序注册。 在设置“用户可以同意应用代表他们访问公司数据”设置为“否”时，此角色还允许成员代表自己授予同意。 在创建新应用程序注册或企业应用程序时，会将此角色的成员添加为所有者。

* **[计费管理员](#billing-administrator)**：进行采购、管理订阅、管理支持票证并监视服务运行状况。

* **[云应用程序管理员](#cloud-application-administrator)**：此角色中的用户具有与应用程序管理员角色相同的权限，但不包括管理应用程序代理的权限。 此角色授予创建和管理企业应用程序和应用程序注册的所有方面的权限。 此角色还可以同意委派权限，以及除 Microsoft Graph 和 Azure AD Graph 之外的应用程序权限。 在创建新应用程序注册或企业应用程序时，不会将此角色的成员添加为所有者。

* **[合规性管理员](#compliance-administrator)**：拥有此角色的用户具有 Office 365 安全与合规中心和 Exchange 管理中心中的管理权限。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)（关于 Office 365 管理员角色）。

* **[条件访问管理员](#conditional-access-administrator)**：具有此角色的用户可以管理 Azure Active Directory 条件访问设置。
  > [!NOTE]
  > 若要在 Azure 中部署 Exchange ActiveSync 条件访问策略，用户还必须是全局管理员。
  
* **[设备管理员](#device-administrators)**：具有此角色的用户将成为已加入 Azure Active Directory 的所有 Windows 10 设备上的本地计算机管理员。 他们无权管理 Azure Active Directory 中的设备对象。

* **[目录读取者](#directory-readers)**：这是一个遗留的角色，分配给不支持[许可框架](../develop/active-directory-integrating-applications.md)的应用程序。 不应将它分配给任何用户。

* **[目录同步帐户](#directory-synchronization-accounts)**：请勿使用。 此角色自动分配给 Azure AD Connect 服务，不可用于其他任何用途。

* **[目录写入者](#directory-writers)**：这是一个遗留的角色，分配给不支持[许可框架](../develop/active-directory-integrating-applications.md)的应用程序。 不应将它分配给任何用户。

* **[Dynamics 365 服务管理员/CRM 服务管理员](#dynamics-365-service-administrator)**：具有此角色的用户在 Microsoft Dynamics 365 Online（如果存在此服务）中拥有全局权限，并可以管理支持票证和监视服务运行状况。 有关详细信息，请参阅[使用服务管理员角色管理租户](https://docs.microsoft.com/en-us/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)。

* **[Exchange 服务管理员](#exchange-service-administrator)**：具有此角色的用户在 Microsoft Exchange Online（如果存在此服务）中拥有全局权限。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)（关于 Office 365 管理员角色）。

* **[全局管理员/公司管理员](#company-administrator)**：具有此角色的用户有权访问 Azure Active Directory 以及使用 Azure Active Directory 标识的服务（例如 Exchange Online、SharePoint Online 和 Skype for Business Online）中的所有管理功能。 注册 Azure Active Directory 租户的人员将成为全局管理员。 只有全局管理员才能分配其他管理员角色。 公司中可以有多个全局管理员。 全局管理员可以为任何用户和所有其他管理员重置密码。

  > [!NOTE]
  > 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“公司管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“全局管理员”。
  >
  >

* **[来宾邀请者](#guest-inviter)**：此角色中的用户可以管理 Azure Active Directory B2B 来宾用户邀请当“成员可以邀请”用户设置设置为“否”。 [关于 Azure AD B2B 协作](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)中提供了有关 B2B 协作的详细信息。 它不包括任何其他权限。

* **[信息保护管理员](#information-protection-administrator)**：具有此角色的用户拥有 Azure 信息保护服务中的所有权限。 此角色可以配置 Azure 信息保护策略的标签、管理保护模板，以及激活保护。 此角色不会授予 Identity Protection Center、Privileged Identity Management、监视 Office 365 服务运行状况或 Office 365 安全与合规中心的权限。

* **[Intune 服务管理员](#intune-service-administrator)**：具有此角色的用户在 Microsoft Intune Online（如果存在此服务）中拥有全局权限。 此外，此角色包含管理以关联策略，以及创建和管理组的用户和设备的能力。 有关详细信息，请参阅[使用 Microsoft Intune 进行基于角色的管理控制 (RBAC)](https://docs.microsoft.com/en-us/intune/role-based-access-control)

* **[消息中心读取者](#message-center-reader)**：具有此角色的用户可以在其组织的 [Office 365 消息中心](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)内，监视 Exchange、Intune 和 Microsoft Teams 等已配置服务的通知和公告运行状况更新。 消息中心读者会收到包含帖子和最新动态的每周电子邮件摘要，并能在 Office 365 内共享消息中心帖子。 在 Azure AD 中，分配到此角色的用户对 Azure AD 服务只拥有只读访问权限，如用户和组。 

* **[合作伙伴层 1 支持](#partner-tier1-support)**：请勿使用。 此角色已弃用，并将从 Azure AD 中删除。 此角色仅供少数 Microsoft 转售合作伙伴使用，不适用于一般用途。

* **[合作伙伴层 2 支持](#partner-tier2-support)**：请勿使用。 此角色已弃用，并将从 Azure AD 中删除。 此角色仅供少数 Microsoft 转售合作伙伴使用，不适用于一般用途。

* **[密码管理员/支持管理员](#helpdesk-administrator)**：具有此角色的用户可以更改密码、管理服务请求和监视服务运行状况。 支持管理员只能为用户和其他支持管理员更改密码。 

  > [!NOTE]
  > 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“支持管理员”。 它是 [Azure 门户](https://portal.azure.com/)中的“密码管理员”。
  >
  >
  
* **[Power BI 服务管理员](#power-bi-service-administrator)**：具有此角色的用户在 Microsoft Power BI（如果存在此服务）中拥有全局权限，并可以管理支持票证和监视服务运行状况。 有关详细信息，请参阅[了解 Power BI 管理员角色](https://docs.microsoft.com/en-us/power-bi/service-admin-role)。

* **[特权角色管理员](#privileged-role-administrator)**：具有此角色的用户可以管理角色分配以及 Azure AD Privileged Identity Management 内的 Azure Active Directory。 此外，此角色允许 Privileged Identity Management 的所有方面。

* **[报告读取者](#reports-reader)**：具有此角色的用户可以在 Office 365 管理中心查看使用情况报告数据和报告仪表板，在 PowerBI 中查看采用上下文包。 此外，此角色还提供对 Azure AD 中的登录报告和活动以及 Microsoft Graph 报告 API 返回的数据的访问权限。 分配到“报告读者”角色的用户只能访问相关使用情况和采用指标。 它们没有任何管理员权限，无法配置设置或访问产品特定的管理中心（如 Exchange）。 

* **[安全管理员](#security-administrator)**：具有此角色的用户具有“安全读取者”角色的所有只读权限，以及能够管理与安全相关的服务配置的能力：Azure Active Directory Identity Protection、Azure 信息保护、Privileged Identity Management 和 Office 365 安全与合规中心。 [Office 365 安全与合规中心](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)提供了有关 Office 365 权限的详细信息。
  
  | In | 有权执行的操作 |
  | --- | --- |
  | Identity Protection Center |<ul><li>安全读取者角色的所有权限。<li>此外，还能够执行除了重置密码以外的所有 IPC 操作。 |
  | Privileged Identity Management |<ul><li>安全读取者角色的所有权限。<li>**不能**管理 Azure AD 角色成员身份或设置。 |
  | <p>监视 Office 365 服务运行状况</p><p>Office 365 安全与合规中心 |<ul><li>安全读取者角色的所有权限。<li>可以配置高级威胁防护功能中的所有设置（恶意软件和病毒保护、恶意 URL 配置、URL 跟踪等）。 |
  
* **[安全读取者](#security-reader)**：具有此角色的用户具有全局只读访问权限，包括 Azure Active Directory、Identity Protection、Privileged Identity Management，以及能够读取 Azure Active Directory 登录报告和审核日志中的所有信息。 角色还授予 Office 365 安全与合规中心的只读权限。 [Office 365 安全与合规中心](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)提供了有关 Office 365 权限的详细信息。

  | In | 有权执行的操作 |
  | --- | --- |
  | Identity Protection Center |读取安全功能的所有安全报告和设置信息<ul><li>反垃圾邮件<li>加密<li>数据丢失预防<li>反恶意软件<li>高级威胁防护<li>防网络钓鱼<li>邮件流规则 |
  | Privileged Identity Management |<p>以只读方式访问 Azure AD PIM 中所显示的一切信息：Azure AD 角色分配的策略和报告、安全审阅，以及在未来还可通过读取来访问 Azure AD 角色分配以外的方案的策略数据和报告。<p>**不能**注册 Azure AD PIM 或对其进行任何更改。 担任此角色的人员可以在 PIM 的门户中或通过 PowerShell，为其他角色（例如，全局管理员或特权角色管理员）的候选用户激活角色。 |
  | <p>监视 Office 365 服务运行状况</p><p>Office 365 安全与合规中心</p> |<ul><li>读取和管理警报<li>读取安全策略<li>读取威胁情报、云应用发现以及搜索和调查中的隔离区<li>读取所有报告 |

* **[服务支持管理员](#service-support-administrator)**：具有此角色的用户可以打开支持请求与 Microsoft Azure 和 Office 365 服务和服务仪表板和消息中心在 Azure 门户和 Office 365 管理门户中的视图。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)（关于 Office 365 管理员角色）。

* **[SharePoint 服务管理员](#sharepoint-service-administrator)**：具有此角色的用户在 Microsoft SharePoint Online（如果存在此服务）中拥有全局权限，并可以管理支持票证和监视服务运行状况。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)（关于 Office 365 管理员角色）。

* **[Skype for Business/Lync 服务管理员](#lync-service-administrator)**：具有此角色的用户具有 Microsoft Skype for Business 中的全局权限，以及管理 Azure Active Directory 中的特定于 Skype 的用户属性。 此外，此角色授予管理支持票证并监视服务运行状况的功能。 有关详细信息，请参阅[关于 Skype for Business 管理员角色](https://support.office.com/en-us/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5)。

  > [!NOTE]
  > 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“Lync 服务管理员”。 它是 [Azure 门户](https://portal.azure.com/)中的“Skype for Business 服务管理员”。
  >
  >

* **[用户帐户管理员](#user-account-administrator)**：具有此角色的用户可以创建和管理用户和组的所有方面。 此外，此角色包括管理支持票证的功能，并监视服务运行状况。 适用某些限制。 例如，此角色不允许删除全局管理员。 用户帐户管理员只能为用户、支持管理员和其他用户帐户管理员更改密码。

| 有权执行的操作 | 无权执行的操作 |
| --- | --- |
| <p>查看公司信息和用户信息</p><p>管理 Office 支持票证</p><p>只能为用户、支持管理员和其他用户帐户管理员更改密码</p><p>创建和管理用户视图</p><p>创建、编辑和删除用户与组，以及管理用户许可证，但有限制。 他/她不能删除全局管理员或创建其他管理员。</p> |<p>为 Office 产品执行计费和采购操作</p><p>管理域</p><p>管理公司信息</p><p>向其他人委派管理角色</p><p>使用目录同步</p><p>启用或禁用多重身份验证</p><p>查看审核日志</p> |

## <a name="deprecated-roles"></a>已弃用的角色

不应使用以下角色。 这些角色已弃用，并将从 Azure AD 中删除。

* 即席许可证管理员
* 设备联接
* 设备管理器
* 设备用户
* 经电子邮件验证的用户创建者
* 邮箱管理员
* 工作区设备联接


### <a name="to-add-a-colleague-as-a-global-administrator"></a>将某个同事添加为全局管理员

1. 使用租户目录的全局管理员或特权角色管理员的帐户登录到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。

   ![打开 Azure AD 管理中心](./media/directory-assign-admin-roles/active-directory-admin-center.png)

2. 选择“用户”。

3. 找到要指定为全局管理员的用户，并打开该用户的边栏选项卡。

4. 在“用户”边栏选项卡中，选择“目录角色”。
 
5. 在“目录角色”边栏选项卡中，选择“全局管理员”角色，并保存。

## <a name="detailed-azure-active-directory-permissions"></a>详细的 Azure Active Directory 权限
下表描述 Azure Active Directory 中授予每个角色的特定权限。 某些角色（例如全局管理员）可能在 Azure Active Directory 外部的 Microsoft 服务中拥有其他权限。


### <a name="application-administrator"></a>应用程序管理员
可以创建和管理应用注册和企业应用的所有方面。

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/Application/Create | 在 Azure Active Directory 中创建应用程序。 |
| microsoft.aad.directory/Application/Delete | 删除 Azure Active Directory 中的应用程序。 |
| microsoft.aad.directory/Application/Update | 更新 Azure Active Directory 中应用程序的标准属性。 |
| microsoft.aad.directory/Application/Update/DefaultPolicy | 更新 Azure Active Directory 中的 Applications.DefaultPolicy 属性。 |
| microsoft.aad.directory/Application/Update/Owners | 更新 Azure Active Directory 中的 Applications.Owners 属性。 |
| microsoft.aad.directory/AppRoleAssignment/Create | 在 Azure Active Directory 中创建 AppRoleAssignments。 |
| microsoft.aad.directory/AppRoleAssignment/Delete | 删除 Azure Active Directory 中的 AppRoleAssignments。 |
| microsoft.aad.directory/AppRoleAssignment/Update | 更新 Azure Active Directory 中 AppRoleAssignments 的标准属性。 |
| microsoft.aad.directory/Policy/Create | 在 Azure Active Directory 中创建策略。 |
| microsoft.aad.directory/Policy/Delete | 删除 Azure Active Directory 中的策略。 |
| microsoft.aad.directory/Policy/Update | 更新 Azure Active Directory 中策略的标准属性。 |
| microsoft.aad.directory/Policy/Update/Owners | 更新 Azure Active Directory 中的 Policies.Owners 属性。 |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | 可以代表所有用户同意除 Azure Active Directory (Azure AD Graph 和 Microsoft Graph)以外的所有资源。 |
| microsoft.aad.directory/ServicePrincipal/Create | 在 Azure Active Directory 中创建 ServicePrincipals。 |
| microsoft.aad.directory/ServicePrincipal/Delete | 删除 Azure Active Directory 中的 ServicePrincipals。 |
| microsoft.aad.directory/ServicePrincipal/Update | 更新 Azure Active Directory 中 ServicePrincipals 的标准属性。 |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | 更新 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignedTo 属性。 |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignments 属性。 |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | 更新 Azure Active Directory 中的 ServicePrincipals.DefaultPolicy 属性。 |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | 更新 Azure Active Directory 中的 ServicePrincipals.Owners 属性。 |
| microsoft.aad.directory/User/AssignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft.aad.reports/AllEntities/Read | 读取 Azure AD 报告。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 读取和配置 Office 365 服务运行状况。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 创建和管理 Office 365 支持票证。 |

### <a name="application-developer"></a>应用程序开发人员
可以创建独立于“用户可注册应用程序”设置的应用程序注册。

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/Application/CreateAsOwner | 在 Azure Active Directory 中创建应用程序。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.aad.directory/AppRoleAssignment/CreateAsOwner | 在 Azure Active Directory 中创建 AppRoleAssignments。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.aad.directory/OAuth2PermissionGrant/CreateAsOwner | 在 Azure Active Directory 中创建 OAuth2PermissionGrants。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.aad.directory/ServicePrincipal/CreateAsOwner | 在 Azure Active Directory 中创建 ServicePrincipals。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |

### <a name="billing-administrator"></a>计费管理员
可以执行与常见计费相关的任务，例如更新付款信息。

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

  > 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 读取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 属性。 |
| microsoft.aad.directory/Organization/Update | 更新 Azure Active Directory 中组织的标准属性。 |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | 更新 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 属性。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 创建和删除所有资源，然后读取和更新 Azure 访问控制中的标准属性。 |
| microsoft.aad.billing/AllEntities/AllActions | 管理 Office 365 计费的各个方面。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 读取和配置 Office 365 服务运行状况。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 创建和管理 Office 365 支持票证。 |

### <a name="cloud-application-administrator"></a>云应用管理员
可以创建和管理应用注册和企业应用的所有方面，应用代理除外。

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/Application/Create | 在 Azure Active Directory 中创建应用程序。 |
| microsoft.aad.directory/Application/Delete | 删除 Azure Active Directory 中的应用程序。 |
| microsoft.aad.directory/Application/Update | 更新 Azure Active Directory 中应用程序的标准属性。 |
| microsoft.aad.directory/Application/Update/DefaultPolicy | 更新 Azure Active Directory 中的 Applications.DefaultPolicy 属性。 |
| microsoft.aad.directory/Application/Update/Owners | 更新 Azure Active Directory 中的 Applications.Owners 属性。 |
| microsoft.aad.directory/AppRoleAssignment/Create | 在 Azure Active Directory 中创建 AppRoleAssignments。 |
| microsoft.aad.directory/AppRoleAssignment/Delete | 删除 Azure Active Directory 中的 AppRoleAssignments。 |
| microsoft.aad.directory/AppRoleAssignment/Update | 更新 Azure Active Directory 中 AppRoleAssignments 的标准属性。 |
| microsoft.aad.directory/Policy/Create | 在 Azure Active Directory 中创建策略。 |
| microsoft.aad.directory/Policy/Delete | 删除 Azure Active Directory 中的策略。 |
| microsoft.aad.directory/Policy/Update | 更新 Azure Active Directory 中策略的标准属性。 |
| microsoft.aad.directory/Policy/Update/Owners | 更新 Azure Active Directory 中的 Policies.Owners 属性。 |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | 可以代表所有用户同意除 Azure Active Directory (Azure AD Graph 和 Microsoft Graph)以外的所有资源。 |
| microsoft.aad.directory/ServicePrincipal/Create | 在 Azure Active Directory 中创建 ServicePrincipals。 |
| microsoft.aad.directory/ServicePrincipal/Delete | 删除 Azure Active Directory 中的 ServicePrincipals。 |
| microsoft.aad.directory/ServicePrincipal/Update | 更新 Azure Active Directory 中 ServicePrincipals 的标准属性。 |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | 更新 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignedTo 属性。 |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignments 属性。 |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | 更新 Azure Active Directory 中的 ServicePrincipals.DefaultPolicy 属性。 |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | 更新 Azure Active Directory 中的 ServicePrincipals.Owners 属性。 |
| microsoft.aad.directory/User/AssignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft.aad.reports/AllEntities/Read | 读取 Azure AD 报告。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 读取和配置 Office 365 服务运行状况。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 创建和管理 Office 365 支持票证。 |

### <a name="company-administrator"></a>公司管理员
可以管理 Azure AD 和使用 Azure AD 标识的 Microsoft 服务的所有方面。 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“公司管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“全局管理员”。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/AllActions/AllProperties | 创建和删除 AdministrativeUnits，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/Application/AllActions/AllProperties | 创建和删除应用程序，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/AppRoleAssignment/AllActions/AllProperties | 创建和删除 AppRoleAssignments，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/CollaborationSpace/AllActions/AllProperties | 创建和删除 CollaborationSpaces，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/Contact/AllActions/AllProperties | 创建和删除联系人，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/Device/AllActions/AllProperties | 创建和删除设备，然后读取和更新 Azure Active Directory 的所有属性。 |
| microsoft.aad.directory/DirectoryRole/AllActions/AllProperties | 创建和删除 DirectoryRoles，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/DirectoryRoleTemplate/AllActions/AllProperties | 创建和删除 DirectoryRoleTemplates，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/DirectorySetting/AllActions/AllProperties | 创建和删除 DirectorySettings，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/DirectorySettingTemplate/AllActions/AllProperties | 创建和删除 DirectorySettingTemplates，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/Domain/AllActions/AllProperties | 创建和删除域，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/Group/AllActions/AllProperties | 创建和删除组，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/LoginTenantBranding/AllActions/AllProperties | 创建和删除 LoginTenantBrandings，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/OAuth2PermissionGrant/AllActions/AllProperties | 创建和删除 OAuth2PermissionGrants，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/Policy/AllActions/AllProperties | 创建和删除策略，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllWithDirectory | 可以代表所有用户同意包括 Azure Active Directory (Azure AD Graph 和 Microsoft Graph)在内的所有资源。 |
| microsoft.aad.directory/ServicePrincipal/AllActions/AllProperties | 创建和删除 ServicePrincipals，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/Organization/AllActions/AllProperties | 创建和删除组织，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/User/AllActions/AllProperties | 创建和删除用户，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.aadconnect/AllEntities/AllActions | 创建和删除所有资源，然后读取并更新 microsoft.aad.aadconnect 中的标准属性。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 创建和删除所有资源，然后读取和更新 Azure 访问控制中的标准属性。 |
| microsoft.aad.billing/AllEntities/AllActions | 管理 Office 365 计费的各个方面。 |
| microsoft.aad.compliance/AllEntities/AllActions | 创建和删除所有资源，然后读取和更新合规中心中的标准属性。 |
| microsoft.aad.directorysync/AllEntities/AllActions | 在 Azure AD Connect 中执行所有操作。 |
| microsoft.aad.lockbox/AllEntities/AllActions | 管理 Lockbox 服务的各个方面。 |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | 管理特权角色管理服务的各个方面。 |
| microsoft.aad.reports/AllEntities/AllActions | 读取和配置 Azure AD 报告。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 读取和配置 Office 365 服务运行状况。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 创建和管理 Office 365 支持票证。 |
| microsoft.crm/AllEntities/AllActions | 管理 Dynamics 365 的各个方面。 |
| microsoft.exchange/AllEntities/AllActions | 管理 Exchange Online 的各个方面。 |
| microsoft.aad.informationprotection/AllEntities/AllActions | 管理信息保护的各个方面。 |
| microsoft.intune/AllEntities/AllActions | 管理 Intune 的各个方面。 |
| microsoft.powerbi/AllEntities/AllActions | 管理 Power BI 的各个方面。 |
| microsoft.protectioncenter/AllEntities/AllActions | 管理 Office 365 防护中心。 |
| microsoft.sharepoint/AllEntities/AllActions | 联机管理 SharePoint。 |
| microsoft.skypeforbusiness/AllEntities/AllActions | 管理 Skype for Business Online。 |

### <a name="compliance-administrator"></a>符合性管理员
可以读取和管理 Azure AD 和 Office 365 中的符合性配置和报表。

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | 创建和删除所有资源，然后读取和更新 Azure 访问控制中的标准属性。 |
| microsoft.aad.compliance/AllEntities/AllActions | 创建和删除所有资源，然后读取和更新合规中心中的标准属性。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 读取和配置 Office 365 服务运行状况。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 创建和管理 Office 365 支持票证。 |
| microsoft.exchange/Compliance/AllActions | 管理 Exchange Online 中的符合性。 |
| microsoft.sharepoint/Compliance/AllActions | 管理 SharePoint Online 中的符合性。 |
| microsoft.skypeforbusiness/Compliance/AllActions | 管理 Skype for Business Online 中的符合性。 |

### <a name="conditional-access-administrator"></a>条件访问管理员
可以管理条件访问功能。

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/ConditionalAccessPolicy/Create | 在 Azure Active Directory 中创建 ConditionalAccessPolicys。 |
| microsoft.aad.directory/ConditionalAccessPolicy/Delete | 删除 Azure Active Directory 中的 ConditionalAccessPolicys。 |
| microsoft.aad.directory/ConditionalAccessPolicy/Read | 读取 Azure Active Directory 中 ConditionalAccessPolicys 的标准属性。 |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/Owners | 读取 Azure Active Directory 中的 ConditionalAccessPolicys.Owners 属性。 |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/PolicyAppliedTo | 读取 Azure Active Directory 中的 ConditionalAccessPolicys.PolicyAppliedTo 属性。 |
| microsoft.aad.directory/ConditionalAccessPolicy/Update | 更新 Azure Active Directory 中 ConditionalAccessPolicys 的标准属性。 |
| microsoft.aad.directory/ConditionalAccessPolicy/Update/Owners | 更新 Azure Active Directory 中的 ConditionalAccessPolicys.Owners 属性。 |

### <a name="device-administrators"></a>设备管理员
此角色的成员将添加到已加入 Azure AD 的设备上的本地管理员组。

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

| **操作** | **说明** |
| --- | --- |

### <a name="directory-readers"></a>目录读者
可以读取基本目录信息。 用于授予对应用程序的访问权限。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/Read | 读取 Azure Active Directory 中 AdministrativeUnits 的标准属性。 |
| microsoft.aad.directory/AdministrativeUnit/Read/Members | 读取 Azure Active Directory 中的 AdministrativeUnits.Members 属性。 |
| microsoft.aad.directory/Application/Read | 读取 Azure Active Directory 中应用程序的标准属性。 |
| microsoft.aad.directory/Application/Read/Owners | 读取 Azure Active Directory 中的 Applications.Owners 属性。 |
| microsoft.aad.directory/CollaborationSpace/Read | 读取 Azure Active Directory 中 CollaborationSpaces 的标准属性。 |
| microsoft.aad.directory/CollaborationSpace/Read/Owners | 读取 Azure Active Directory 中的 CollaborationSpaces.Owners 属性。 |
| microsoft.aad.directory/Contact/Read | 读取 Azure Active Directory 中联系人的标准属性。 |
| microsoft.aad.directory/Contact/Read/MemberOf | 读取 Azure Active Directory 中的 Contacts.MemberOf 属性。 |
| microsoft.aad.directory/Device/Read | 读取 Azure Active Directory 中设备的标准属性。 |
| microsoft.aad.directory/Device/Read/MemberOf | 读取 Azure Active Directory 中的 Devices.MemberOf 属性。 |
| microsoft.aad.directory/Device/Read/RegisteredOwners | 读取 Azure Active Directory 中的 Devices.RegisteredOwners 属性。 |
| microsoft.aad.directory/Device/Read/RegisteredUsers | 读取 Azure Active Directory 中的 Devices.RegisteredUsers 属性。 |
| microsoft.aad.directory/DirectoryRole/Read | 读取 Azure Active Directory 中 DirectoryRoles 的标准属性。 |
| microsoft.aad.directory/DirectoryRole/Read/EligibleMembers | 读取 Azure Active Directory 中的 DirectoryRoles.EligibleMembers 属性。 |
| microsoft.aad.directory/DirectoryRole/Read/Members | 读取 Azure Active Directory 中的 DirectoryRoles.Members 属性。 |
| microsoft.aad.directory/DirectorySetting/Read | 读取 Azure Active Directory 中 DirectorySettings 的标准属性。 |
| microsoft.aad.directory/DirectorySettingTemplate/Read | 读取 Azure Active Directory 中 DirectorySettingTemplates 的标准属性。 |
| microsoft.aad.directory/Domain/Read | 读取 Azure Active Directory 中域的标准属性。 |
| microsoft.aad.directory/Group/Read | 读取 Azure Active Directory 中组的标准属性。 |
| microsoft.aad.directory/Group/Read/AppRoleAssignments | 读取 Azure Active Directory 中的 Groups.AppRoleAssignments 属性。 |
| microsoft.aad.directory/Group/Read/MemberOf | 读取 Azure Active Directory 中的 Groups.MemberOf 属性。 |
| microsoft.aad.directory/Group/Read/Members | 读取 Azure Active Directory 中的 Groups.Members 属性。 |
| microsoft.aad.directory/Group/Read/Owners | 读取 Azure Active Directory 中的 Groups.Owners 属性。 |
| microsoft.aad.directory/Group/Read/Settings | 读取 Azure Active Directory 中的 Groups.Settings 属性。 |
| microsoft.aad.directory/OAuth2PermissionGrant/Read | 读取 Azure Active Directory 中 OAuth2PermissionGrants 的标准属性。 |
| microsoft.aad.directory/ServicePrincipal/Read | 读取 Azure Active Directory 中 ServicePrincipals 的标准属性。 |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | 读取 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignedTo 属性。 |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | 读取 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignments 属性。 |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | 读取 Azure Active Directory 中的 ServicePrincipals.DefaultPolicy 属性。 |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | 读取 Azure Active Directory 中的 ServicePrincipals.MemberOf 属性。 |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | 读取 Azure Active Directory 中的 ServicePrincipals.OAuth2PermissionGrants 属性。 |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | 读取 Azure Active Directory 中的 ServicePrincipals.Owners 属性。 |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | 读取 Azure Active Directory 中的 ServicePrincipals.OwnedObjects 属性。 |
| microsoft.aad.directory/Organization/Read | 读取 Azure Active Directory 中组织的标准属性。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 读取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 属性。 |
| microsoft.aad.directory/User/Read | 读取 Azure Active Directory 中用户的标准属性。 |
| microsoft.aad.directory/User/Read/AppRoleAssignments | 读取 Azure Active Directory 中的 Users.AppRoleAssignments 属性。 |
| microsoft.aad.directory/User/Read/DirectReports | 读取 Azure Active Directory 中的 Users.DirectReports 属性。 |
| microsoft.aad.directory/User/Read/InvitedBy | 读取 Azure Active Directory 中的 Users.InvitedBy 属性。 |
| microsoft.aad.directory/User/Read/InvitedUsers | 读取 Azure Active Directory 中的 Users.InvitedUsers 属性。 |
| microsoft.aad.directory/User/Read/Manager | 读取 Azure Active Directory 中的 Users.Manager 属性。 |
| microsoft.aad.directory/User/Read/MemberOf | 读取 Azure Active Directory 中的 Users.MemberOf 属性。 |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | 读取 Azure Active Directory 中的 Users.OAuth2PermissionGrants 属性。 |
| microsoft.aad.directory/User/Read/OwnedDevices | 读取 Azure Active Directory 中的 Users.OwnedDevices 属性。 |
| microsoft.aad.directory/User/Read/OwnedObjects | 读取 Azure Active Directory 中的 Users.OwnedObjects 属性。 |
| microsoft.aad.directory/User/Read/RegisteredDevices | 读取 Azure Active Directory 中的 Users.RegisteredDevices 属性。 |

### <a name="directory-synchronization-accounts"></a>目录同步帐户
仅供 Azure AD Connect 服务使用。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/Policy/Create | 在 Azure Active Directory 中创建策略。 |
| microsoft.aad.directory/Policy/Delete | 删除 Azure Active Directory 中的策略。 |
| microsoft.aad.directory/Policy/Read | 读取 Azure Active Directory 中策略的标准属性。 |
| microsoft.aad.directory/Policy/Read/Owners | 读取 Azure Active Directory 中的 Policies.Owners 属性。 |
| microsoft.aad.directory/Policy/Read/PolicyAppliedTo | 读取 Azure Active Directory 中的 Policies.PolicyAppliedTo 属性。 |
| microsoft.aad.directory/Policy/Update | 更新 Azure Active Directory 中策略的标准属性。 |
| microsoft.aad.directory/Policy/Update/Owners | 更新 Azure Active Directory 中的 Policies.Owners 属性。 |
| microsoft.aad.directory/ServicePrincipal/Create | 在 Azure Active Directory 中创建 ServicePrincipals。 |
| microsoft.aad.directory/ServicePrincipal/Read | 读取 Azure Active Directory 中 ServicePrincipals 的标准属性。 |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | 读取 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignedTo 属性。 |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | 读取 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignments 属性。 |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | 读取 Azure Active Directory 中的 ServicePrincipals.DefaultPolicy 属性。 |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | 读取 Azure Active Directory 中的 ServicePrincipals.MemberOf 属性。 |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | 读取 Azure Active Directory 中的 ServicePrincipals.OAuth2PermissionGrants 属性。 |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | 读取 Azure Active Directory 中的 ServicePrincipals.Owners 属性。 |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | 读取 Azure Active Directory 中的 ServicePrincipals.OwnedObjects 属性。 |
| microsoft.aad.directory/ServicePrincipal/Update | 更新 Azure Active Directory 中 ServicePrincipals 的标准属性。 |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | 更新 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignedTo 属性。 |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignments 属性。 |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | 更新 Azure Active Directory 中的 ServicePrincipals.DefaultPolicy 属性。 |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | 更新 Azure Active Directory 中的 ServicePrincipals.Owners 属性。 |
| microsoft.aad.directory/Organization/Update/DirSync | 更新 Azure Active Directory 中的 Organizations.DirSync 属性。 |
| microsoft.aad.directorysync/AllEntities/AllActions | 在 Azure AD Connect 中执行所有操作。 |

### <a name="directory-writer"></a>目录写入者
可以读取和写入基本目录信息。 授予应用程序访问权限

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/DirectorySetting/Create | 在 Azure Active Directory 中创建 DirectorySettings。 |
| microsoft.aad.directory/DirectorySetting/Delete | 删除 Azure Active Directory 中的 DirectorySettings。 |
| microsoft.aad.directory/DirectorySetting/Update | 更新 Azure Active Directory 中 DirectorySettings 的标准属性。 |
| microsoft.aad.directory/Group/Create | 在 Azure Active Directory 中创建组。 |
| microsoft.aad.directory/Group/CreateAsOwner | 在 Azure Active Directory 中创建组。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.aad.directory/Group/Read | 读取 Azure Active Directory 中组的标准属性。 |
| microsoft.aad.directory/Group/Update | 更新 Azure Active Directory 中组的标准属性。 |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 Groups.AppRoleAssignments 属性。 |
| microsoft.aad.directory/Group/Update/Members | 更新 Azure Active Directory 中的 Groups.Members 属性。 |
| microsoft.aad.directory/Group/Update/Owners | 更新 Azure Active Directory 中的 Groups.Owners 属性。 |
| microsoft.aad.directory/Group/Update/Settings | 更新 Azure Active Directory 中的 Groups.Settings 属性。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 读取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 属性。 |
| microsoft.aad.directory/User/AssignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft.aad.directory/User/Update | 更新 Azure Active Directory 中用户的标准属性。 |
| microsoft.aad.directory/User/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 Users.AppRoleAssignments 属性。 |
| microsoft.aad.directory/User/Update/Manager | 更新 Azure Active Directory 中的 Users.Manager 属性。 |

### <a name="dynamics-365-service-administrator"></a>Dynamics 365 服务管理员
可以管理 Dynamics 365 产品的所有方面。

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 读取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 属性。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 创建和删除所有资源，然后读取和更新 Azure 访问控制中的标准属性。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 读取和配置 Office 365 服务运行状况。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 创建和管理 Office 365 支持票证。 |
| microsoft.crm/AllEntities/AllActions | 管理 Dynamics 365 的各个方面。 |

### <a name="exchange-service-administrator"></a>Exchange 服务管理员
可以管理 Exchange 产品的所有方面。

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | 创建和删除所有资源，然后读取和更新 Azure 访问控制中的标准属性。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 创建和管理 Office 365 支持票证。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 读取和配置 Office 365 服务运行状况。 |
| microsoft.exchange/AllEntities/AllActions | 管理 Exchange Online 的各个方面。 |

### <a name="guest-inviter"></a>来宾邀请者
可以邀请与“成员可邀请来宾”设置无关的来宾用户。

  > [!NOTE]
  > 此角色从来宾角色继承其他权限。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/User/InviteGuest | 邀请 Azure Active Directory 中的来宾用户。 |
| microsoft.aad.directory/User/Read | 读取 Azure Active Directory 中用户的标准属性。 |
| microsoft.aad.directory/User/Read/AppRoleAssignments | 读取 Azure Active Directory 中的 Users.AppRoleAssignments 属性。 |
| microsoft.aad.directory/User/Read/DirectReports | 读取 Azure Active Directory 中的 Users.DirectReports 属性。 |
| microsoft.aad.directory/User/Read/InvitedBy | 读取 Azure Active Directory 中的 Users.InvitedBy 属性。 |
| microsoft.aad.directory/User/Read/InvitedUsers | 读取 Azure Active Directory 中的 Users.InvitedUsers 属性。 |
| microsoft.aad.directory/User/Read/Manager | 读取 Azure Active Directory 中的 Users.Manager 属性。 |
| microsoft.aad.directory/User/Read/MemberOf | 读取 Azure Active Directory 中的 Users.MemberOf 属性。 |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | 读取 Azure Active Directory 中的 Users.OAuth2PermissionGrants 属性。 |
| microsoft.aad.directory/User/Read/OwnedDevices | 读取 Azure Active Directory 中的 Users.OwnedDevices 属性。 |
| microsoft.aad.directory/User/Read/OwnedObjects | 读取 Azure Active Directory 中的 Users.OwnedObjects 属性。 |
| microsoft.aad.directory/User/Read/RegisteredDevices | 读取 Azure Active Directory 中的 Users.RegisteredDevices 属性。 |

### <a name="helpdesk-administrator"></a>支持管理员
可以重置非管理员和支持理员的密码。

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 读取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 属性。 |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft.aad.directory/User/Update/PasswordHelpdeskScope | 更新 Azure Active Directory 中有限管理员和其他支持人员管理员的密码。 有关详细信息，请参阅联机文档。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 创建和删除所有资源，然后读取和更新 Azure 访问控制中的标准属性。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 创建和管理 Office 365 支持票证。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 读取和配置 Office 365 服务运行状况。 |

### <a name="information-protection-administrator"></a>信息保护管理员
可以管理 Azure 信息保护产品的所有方面。

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/Group/Read | 读取 Azure Active Directory 中组的标准属性。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 读取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 属性。 |
| microsoft.aad.informationprotection/AllEntities/AllActions | 管理信息保护的各个方面。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 读取和配置 Office 365 服务运行状况。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 创建和管理 Office 365 支持票证。 |

### <a name="intune-service-administrator"></a>Intune 服务管理员
可以管理 Intune 产品的所有方面。

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/Contact/Create | 在 Azure Active Directory 中创建联系人。 |
| microsoft.aad.directory/Contact/Delete | 删除 Azure Active Directory 中的联系人。 |
| microsoft.aad.directory/Contact/Update | 更新 Azure Active Directory 中联系人的标准属性。 |
| microsoft.aad.directory/Device/Create | 在 Azure Active Directory 中创建设备。 |
| microsoft.aad.directory/Device/Delete | 删除 Azure Active Directory 中的设备。 |
| microsoft.aad.directory/Device/Update | 更新 Azure Active Directory 中设备的标准属性。 |
| microsoft.aad.directory/Device/Update/RegisteredOwners | 更新 Azure Active Directory 中的 Devices.RegisteredOwners 属性。 |
| microsoft.aad.directory/Device/Update/RegisteredUsers | 更新 Azure Active Directory 中的 Devices.RegisteredUsers 属性。 |
| microsoft.aad.directory/Group/Create | 在 Azure Active Directory 中创建组。 |
| microsoft.aad.directory/Group/CreateAsOwner | 在 Azure Active Directory 中创建组。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.aad.directory/Group/Delete | 删除 Azure Active Directory 中的组。 |
| microsoft.aad.directory/Group/Read | 读取 Azure Active Directory 中组的标准属性。 |
| microsoft.aad.directory/Group/Read/HiddenMembers | 读取 Azure Active Directory 中的 Groups.HiddenMembers 属性。 |
| microsoft.aad.directory/Group/Restore | 还原 Azure Active Directory 中的组。 |
| microsoft.aad.directory/Group/Update | 更新 Azure Active Directory 中组的标准属性。 |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 Groups.AppRoleAssignments 属性。 |
| microsoft.aad.directory/Group/Update/Members | 更新 Azure Active Directory 中的 Groups.Members 属性。 |
| microsoft.aad.directory/Group/Update/Owners | 更新 Azure Active Directory 中的 Groups.Owners 属性。 |
| microsoft.aad.directory/Group/Update/Settings | 更新 Azure Active Directory 中的 Groups.Settings 属性。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 读取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 属性。 |
| microsoft.aad.directory/User/Update | 更新 Azure Active Directory 中用户的标准属性。 |
| microsoft.aad.directory/User/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 Users.AppRoleAssignments 属性。 |
| microsoft.aad.directory/User/Update/Manager | 更新 Azure Active Directory 中的 Users.Manager 属性。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 创建和管理 Office 365 支持票证。 |
| microsoft.intune/AllEntities/AllActions | 管理 Intune 的各个方面。 |

### <a name="lync-service-administrator"></a>Lync 服务管理员
可以管理 Skype for Business 产品的所有方面。

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | 创建和删除所有资源，然后读取和更新 Azure 访问控制中的标准属性。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 读取和配置 Office 365 服务运行状况。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 创建和管理 Office 365 支持票证。 |
| microsoft.skypeforbusiness/AllEntities/AllActions | 管理 Skype for Business Online。 |

### <a name="message-center-reader"></a>消息中心读取者
只能在 Office 365 消息中心查看其组织的消息和更新。 

  > [!NOTE]
  > 此角色从“目录读取者”角色继承其他权限。
  >
  >

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/Group/Read | 读取 Azure Active Directory 中组的标准属性。 |
| microsoft.aad.accessmessagecenter/AllEntities/AllActions | 创建和删除所有资源，然后读取和更新消息中心中的标准属性。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 创建和删除所有资源，然后读取和更新 Azure 访问控制中的标准属性。 |

### <a name="partner-tier1-support"></a>合作伙伴一线支持人员
不要使用 - 不适用于常规用途。

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | 创建和删除所有资源，然后读取和更新 Azure 访问控制中的标准属性。 |
| microsoft.aad.directory/Contact/Create | 在 Azure Active Directory 中创建联系人。 |
| microsoft.aad.directory/Contact/Delete | 删除 Azure Active Directory 中的联系人。 |
| microsoft.aad.directory/Contact/Update | 更新 Azure Active Directory 中联系人的标准属性。 |
| microsoft.aad.directory/Group/Create | 在 Azure Active Directory 中创建组。 |
| microsoft.aad.directory/Group/CreateAsOwner | 在 Azure Active Directory 中创建组。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.aad.directory/Group/Read | 读取 Azure Active Directory 中组的标准属性。 |
| microsoft.aad.directory/Group/Update/Members | 更新 Azure Active Directory 中的 Groups.Members 属性。 |
| microsoft.aad.directory/Group/Update/Owners | 更新 Azure Active Directory 中的 Groups.Owners 属性。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 读取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 属性。 |
| microsoft.aad.directory/User/AssignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft.aad.directory/User/Delete | 删除 Azure Active Directory 中的用户。 |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft.aad.directory/User/Restore | 还原 Azure Active Directory 中已删除的用户。 |
| microsoft.aad.directory/User/Update | 更新 Azure Active Directory 中用户的标准属性。 |
| microsoft.aad.directory/User/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 Users.AppRoleAssignments 属性。 |
| microsoft.aad.directory/User/Update/Manager | 更新 Azure Active Directory 中的 Users.Manager 属性。 |
| microsoft.aad.directory/User/Update/PasswordUserScope | 更新 Azure Active Directory 中的非管理员密码。 有关详细信息，请参阅联机文档。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 读取和配置 Office 365 服务运行状况。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 创建和管理 Office 365 支持票证。 |

### <a name="partner-tier2-support"></a>合作伙伴二线支持人员
不要使用 - 不适用于常规用途。

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | 创建和删除所有资源，然后读取和更新 Azure 访问控制中的标准属性。 |
| microsoft.aad.directory/Contact/Create | 在 Azure Active Directory 中创建联系人。 |
| microsoft.aad.directory/Contact/Delete | 删除 Azure Active Directory 中的联系人。 |
| microsoft.aad.directory/Contact/Update | 更新 Azure Active Directory 中联系人的标准属性。 |
| microsoft.aad.directory/Domain/AllActions | 创建和删除域，然后读取和更新 Azure Active Directory 中的标准属性。 |
| microsoft.aad.directory/Group/Create | 在 Azure Active Directory 中创建组。 |
| microsoft.aad.directory/Group/Delete | 删除 Azure Active Directory 中的组。 |
| microsoft.aad.directory/Group/Read | 读取 Azure Active Directory 中组的标准属性。 |
| microsoft.aad.directory/Group/Restore | 还原 Azure Active Directory 中的组。 |
| microsoft.aad.directory/Group/Update/Members | 更新 Azure Active Directory 中的 Groups.Members 属性。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 读取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 属性。 |
| microsoft.aad.directory/Organization/Update | 更新 Azure Active Directory 中组织的标准属性。 |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | 更新 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 属性。 |
| microsoft.aad.directory/User/AssignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft.aad.directory/User/Delete | 删除 Azure Active Directory 中的用户。 |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft.aad.directory/User/Restore | 还原 Azure Active Directory 中已删除的用户。 |
| microsoft.aad.directory/User/Update | 更新 Azure Active Directory 中用户的标准属性。 |
| microsoft.aad.directory/User/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 Users.AppRoleAssignments 属性。 |
| microsoft.aad.directory/User/Update/Manager | 更新 Azure Active Directory 中的 Users.Manager 属性。 |
| microsoft.aad.directory/User/Update/Password | 更新 Azure Active Directory 中所有用户的密码。 有关详细信息，请参阅联机文档。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 读取和配置 Office 365 服务运行状况。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 创建和管理 Office 365 支持票证。 |

### <a name="power-bi-service-administrator"></a>Power BI 服务管理员
可以管理 Power BI 产品的所有方面。

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 读取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 属性。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 创建和删除所有资源，然后读取和更新 Azure 访问控制中的标准属性。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 读取和配置 Office 365 服务运行状况。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 创建和管理 Office 365 支持票证。 |
| microsoft.powerbi/AllEntities/AllActions | 管理 Power BI 的各个方面。 |

### <a name="privileged-role-administrator"></a>特权角色管理员
可以管理 Azure AD 中的角色分配

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/DirectoryRole/Update | 更新 Azure Active Directory 中 DirectoryRoles 的标准属性。 |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | 管理特权角色管理服务的各个方面。 |

### <a name="reports-reader"></a>报告读者
可以读取登录和审核报告。

  > [!NOTE]
  > 此角色从“目录读取者”角色继承其他权限。
  >
  >

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.reports/AllEntities/Read | 读取 Azure AD 报告。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.usagereports/AllEntities/Read | 阅读 Office 365 使用情况报告。 |

### <a name="security-administrator"></a>安全管理员
可以读取安全信息和报告

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/Application/Update/DefaultPolicy | 更新 Azure Active Directory 中的 Applications.DefaultPolicy 属性。 |
| microsoft.aad.directory/Policy/Create | 在 Azure Active Directory 中创建策略。 |
| microsoft.aad.directory/Policy/Delete | 删除 Azure Active Directory 中的策略。 |
| microsoft.aad.directory/Policy/Update | 更新 Azure Active Directory 中策略的标准属性。 |
| microsoft.aad.directory/Policy/Update/Owners | 更新 Azure Active Directory 中的 Policies.Owners 属性。 |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | 更新 Azure Active Directory 中的 ServicePrincipals.DefaultPolicy 属性。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 读取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 属性。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 创建和删除所有资源，然后读取和更新 Azure 访问控制中的标准属性。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 读取和配置 Office 365 服务运行状况。 |
| microsoft.aad.privilegedrolemanagement/AllEntities/Read | 读取 Privileged Identity Management 各个方面。 |
| microsoft.protectioncenter/AllEntities/Read | 读取 Office 365 防护中心的各个方面。 |
| microsoft.protectioncenter/AllEntities/Update | 管理 Office 365 防护中心。 |

### <a name="security-reader"></a>安全读取者
可以读取 Azure AD 和 Office 365 中的安全信息和报表。

  > [!NOTE]
  > 此角色从“目录读取者”角色继承其他权限。
  >
  >

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 读取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 属性。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 创建和删除所有资源，然后读取和更新 Azure 访问控制中的标准属性。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 读取和配置 Office 365 服务运行状况。 |
| microsoft.privilegedidentitymanagement/AllEntities/Read | 读取 Privileged Identity Management 各个方面。 |
| microsoft.protectioncenter/AllEntities/Read | 读取 Office 365 防护中心的各个方面。 |

### <a name="service-support-administrator"></a>服务支持管理员
可以读取服务运行状况信息和管理支持票证。

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 读取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 属性。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 创建和删除所有资源，然后读取和更新 Azure 访问控制中的标准属性。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 创建和管理 Office 365 支持票证。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 读取和配置 Office 365 服务运行状况。 |

### <a name="sharepoint-service-administrator"></a>SharePoint 服务管理员
可以管理 SharePoint 服务的所有方面。

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | 创建和删除所有资源，然后读取和更新 Azure 访问控制中的标准属性。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 读取和配置 Office 365 服务运行状况。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 创建和管理 Office 365 支持票证。 |
| microsoft.sharepoint/AllEntities/AllActions | 联机管理 SharePoint。 |

### <a name="user-account-administrator"></a>用户帐户管理员
可以管理用户与组的所有方面

  > [!NOTE]
  > 此角色从[用户角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)继承其他权限。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/AppRoleAssignment/Create | 在 Azure Active Directory 中创建 AppRoleAssignments。 |
| microsoft.aad.directory/AppRoleAssignment/Delete | 删除 Azure Active Directory 中的 AppRoleAssignments。 |
| microsoft.aad.directory/AppRoleAssignment/Update | 更新 Azure Active Directory 中 AppRoleAssignments 的标准属性。 |
| microsoft.aad.directory/Contact/Create | 在 Azure Active Directory 中创建联系人。 |
| microsoft.aad.directory/Contact/Delete | 删除 Azure Active Directory 中的联系人。 |
| microsoft.aad.directory/Contact/Update | 更新 Azure Active Directory 中联系人的标准属性。 |
| microsoft.aad.directory/Group/Create | 在 Azure Active Directory 中创建组。 |
| microsoft.aad.directory/Group/CreateAsOwner | 在 Azure Active Directory 中创建组。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.aad.directory/Group/Delete | 删除 Azure Active Directory 中的组。 |
| microsoft.aad.directory/Group/Read | 读取 Azure Active Directory 中组的标准属性。 |
| microsoft.aad.directory/Group/Read/HiddenMembers | 读取 Azure Active Directory 中的 Groups.HiddenMembers 属性。 |
| microsoft.aad.directory/Group/Restore | 还原 Azure Active Directory 中的组。 |
| microsoft.aad.directory/Group/Update | 更新 Azure Active Directory 中组的标准属性。 |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 Groups.AppRoleAssignments 属性。 |
| microsoft.aad.directory/Group/Update/Members | 更新 Azure Active Directory 中的 Groups.Members 属性。 |
| microsoft.aad.directory/Group/Update/Owners | 更新 Azure Active Directory 中的 Groups.Owners 属性。 |
| microsoft.aad.directory/Group/Update/Settings | 更新 Azure Active Directory 中的 Groups.Settings 属性。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 读取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 属性。 |
| microsoft.aad.directory/User/AssignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft.aad.directory/User/Create | 在 Azure Active Directory 中创建用户。 |
| microsoft.aad.directory/User/Delete | 删除 Azure Active Directory 中的用户。 |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft.aad.directory/User/Restore | 还原 Azure Active Directory 中已删除的用户。 |
| microsoft.aad.directory/User/Update | 更新 Azure Active Directory 中用户的标准属性。 |
| microsoft.aad.directory/User/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 Users.AppRoleAssignments 属性。 |
| microsoft.aad.directory/User/Update/Manager | 更新 Azure Active Directory 中的 Users.Manager 属性。 |
| microsoft.aad.directory/User/Update/PasswordUserAcctAdminScope | 更新受限管理员、支持管理员和 Azure Active Directory 中其他用户帐户管理员的密码。 有关详细信息，请参阅联机文档。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 创建和删除所有资源，然后读取和更新 Azure 访问控制中的标准属性。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 创建和管理 Office 365 支持票证。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 读取和配置 Office 365 服务运行状况。 |

## <a name="next-steps"></a>后续步骤

* 若要了解有关如何更改 Azure 订阅管理员的详细信息，请参阅 [如何添加或更改 Azure 管理员角色](../../billing/billing-add-change-azure-subscription-administrator.md)
* 若要了解有关如何在 Microsoft Azure 中控制资源访问的详细信息，请参阅 [了解 Azure 中的资源访问权限](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* 有关 Azure Active Directory 如何与 Azure 订阅相关联的详细信息，请参阅 [How Azure subscriptions are associated with Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)（Azure 订阅与 Azure Active Directory 的关联方式）
