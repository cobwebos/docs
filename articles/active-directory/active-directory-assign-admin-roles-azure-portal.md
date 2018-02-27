---
title: "在 Azure Active Directory 中分配管理员角色 | Microsoft Docs"
description: "管理员角色可以添加用户、分配管理角色、重置用户密码、管理用户许可证，或者管理域。 分配为管理员角色的用户在组织已订阅的所有云服务中具有相同的权限。"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 02/07/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 1fed14fc117c7652e677a245625502c1810068ff
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2018
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>在 Azure Active Directory 中分配管理员角色

使用 Azure Active Directory (Azure AD) 时，可以指定不同的管理员来执行不同的功能。 管理员可以按角色访问 Azure 门户中的各种功能：创建或编辑用户、将管理角色分配给他人、重置用户密码、管理用户许可证以及管理域等。 分配为管理员角色的用户在组织所订阅的所有云服务中拥有相同的权限，不管该角色是通过 Office 365 门户、Azure 门户还是用于 Windows PowerShell 的 Azure AD 模块分配的。

## <a name="details-about-the-global-administrator-role"></a>有关全局管理员角色的详细信息
全局管理员有权访问所有管理功能。 默认情况下，系统会将注册 Azure 订阅的人员指派为目录的全局管理员角色。 只有全局管理员才能分配其他管理员角色。

## <a name="assign-or-remove-administrator-roles"></a>分配或删除管理员角色
若要了解如何在 Azure Active Directory 中向用户分配管理角色，请参阅[在 Azure Active Directory 中向用户分配管理角色](active-directory-users-assign-role-azure-portal.md)。

## <a name="available-roles"></a>可用的角色
提供以下管理员角色：

* **计费管理员**：进行采购、管理订阅、管理支持票证并监视服务运行状况。

* **合规性管理员**：拥有此角色的用户具有 Office 365 安全与合规中心和 Exchange 管理中心中的管理权限。 有关详细信息，请参阅“[关于 Office 365 管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)”。

* **条件访问管理员**具有此角色的用户可以管理 Azure Active Directory 条件访问设置。
  > [!NOTE]
  > 若要在 Azure 中部署 Exchange ActiveSync 条件访问策略，用户还必须是全局管理员。
  
* **CRM 服务管理员**：具有此角色的用户在 Microsoft CRM Online（如果存在此服务）中拥有全局权限，并可以管理支持票证和监视服务运行状况。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)（关于 Office 365 管理员角色）。

* **设备管理员**：具有此角色的用户将成为已加入 Azure Active Directory 的所有 Windows 10 设备上的本地计算机管理员。 他们无权管理 Azure Active Directory 中的设备对象。

* **目录读取者**：这是一个遗留的角色，分配给不支持[同意框架](active-directory-integrating-applications.md)的应用程序。 不应将它分配给任何用户。

* **目录同步帐户**：请勿使用。 此角色自动分配给 Azure AD Connect 服务，不可用于其他任何用途。

* **目录写入者**：这是一个遗留的角色，分配给不支持[同意框架](active-directory-integrating-applications.md)的应用程序。 不应将它分配给任何用户。

* **Exchange 服务管理员**：具有此角色的用户在 Microsoft Exchange Online（如果存在此服务）中拥有全局权限。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)（关于 Office 365 管理员角色）。

* **全局管理员/公司管理员**：与此角色的用户有权访问 Azure Active Directory，以及对 Exchange Online、SharePoint Online 和 Skype for Business Online 等 Azure Active Directory 联合的服务中的所有管理功能。 注册 Azure Active Directory 租户的人员将成为全局管理员。 只有全局管理员才能分配其他管理员角色。 公司中可以有多个全局管理员。 全局管理员可以为任何用户和所有其他管理员重置密码。

  > [!NOTE]
  > 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“公司管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“全局管理员”。
  >
  >

* **来宾邀请者**：此角色中的用户可以管理 Azure Active Directory B2B 来宾用户邀请当“邀请成员”用户设置设置为“否”。 [关于 Azure AD B2B 协作预览版](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)中提供了有关 B2B 协作的详细信息。 它不包括任何其他权限。

* **信息保护管理员(预览)**：具有此角色的用户仅对 Azure 信息保护服务具有用户权限。 他们未被授予对 Identity Protection Center、Privileged Identity Management、监视 Office 365 服务运行状况或 Office 365 安全与合规中心的用户权限。 他们可以配置 Azure 信息保护策略的标签、管理保护模板，以及激活保护。

* **Intune 服务管理员**：具有此角色的用户在 Microsoft Intune Online（如果存在此服务）中拥有全局权限。 此外，此角色包含管理以关联策略，以及创建和管理组的用户和设备的能力。

* **邮箱管理员**：此角色仅用作 RIM Blackberry 设备的 Exchange Online 电子邮件支持的一部分。 如果组织不在 RIM Blackberry 设备上使用 Exchange Online 电子邮件，请勿使用此角色。

* **合作伙伴层 1 支持**：请勿使用。 此角色已弃用，并将从 Azure AD 中删除。 此角色仅供少数 Microsoft 转售合作伙伴使用，不适用于一般用途。

* **合作伙伴层 2 支持**：请勿使用。 此角色已弃用，并将从 Azure AD 中删除。 此角色仅供少数 Microsoft 转售合作伙伴使用，不适用于一般用途。

* **密码管理员/支持管理员**：具有此角色的用户可以更改密码、管理服务请求和监视服务运行状况。 支持管理员只能为用户和其他支持管理员更改密码。 

  > [!NOTE]
  > 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“支持管理员”。 它是 [Azure 门户](https://portal.azure.com/)中的“密码管理员”。
  >
  >
  
* **Power BI 服务管理员**：具有此角色的用户在 Microsoft Power BI（如果存在此服务）中拥有全局权限，并可以管理支持票证和监视服务运行状况。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-001&ad=US)（关于 Office 365 管理员角色）。

* **特权角色管理员**：具有此角色的用户可以管理角色分配以及 Azure AD Privileged Identity Management 内的 Azure Active Directory。 此外，此角色允许 Privileged Identity Management 的所有方面。

* **报告读者**：具有此角色的用户可以在 Office 365 管理中心查看使用情况报告数据和报告仪表板，在 PowerBI 中查看采用上下文包。 此外，此角色还提供对 Azure AD 中的登录报告和活动以及 Microsoft Graph 报告 API 返回的数据的访问权限。 分配到“报告读者”角色的用户只能访问相关使用情况和采用指标。 它们没有任何管理员权限，无法配置设置或访问产品特定的管理中心（如 Exchange）。 

* **安全管理员**：具有此角色的用户具有“安全读取者”角色的所有只读权限，以及能够管理与安全相关的服务配置的能力：Azure Active Directory Identity Protection、Azure 信息保护、Privileged Identity Management 和 Office 365 安全与合规中心。 [Office 365 安全与合规中心](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)提供了有关 Office 365 权限的详细信息。

* **安全读取者**：具有此角色的用户具有全局只读访问权限，包括 Azure Active Directory、Identity Protection、Privileged Identity Management，以及能够读取 Azure Active Directory 登录报告和审核日志中的所有信息。 角色还授予 Office 365 安全与合规中心的只读权限。 [Office 365 安全与合规中心](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)提供了有关 Office 365 权限的详细信息。

* **服务支持管理员**：具有此角色的用户可以打开支持请求与 Microsoft Azure 和 Office 365 服务和服务仪表板和消息中心在 Azure 门户和 Office 365 管理门户中的视图。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)（关于 Office 365 管理员角色）。

* **SharePoint 服务管理员**：具有此角色的用户在 Microsoft SharePoint Online（如果存在此服务）中拥有全局权限，并可以管理支持票证和监视服务运行状况。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)（关于 Office 365 管理员角色）。

* **Skype for Business/Lync 服务管理员**：具有此角色的用户具有 Microsoft Skype for Business 中的全局权限，以及管理 Azure Active Directory 中的特定于 Skype 的用户属性。 此外，此角色授予管理支持票证并监视服务运行状况的功能。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)（关于 Office 365 管理员角色）。

  > [!NOTE]
  > 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“Lync 服务管理员”。 它是 [Azure 门户](https://portal.azure.com/)中的“Skype for Business 服务管理员”。
  >
  >

* **用户帐户管理员**：具有此角色的用户可以创建和管理用户和组的所有方面。 此外，此角色包括管理支持票证的功能，并监视服务运行状况。 适用某些限制。 例如，此角色不允许删除全局管理员。 用户帐户管理员只能为用户、支持管理员和其他用户帐户管理员更改密码。

## <a name="administrator-permissions"></a>管理员权限

### <a name="billing-administrator"></a>计费管理员

| 有权执行的操作 | 无权执行的操作 |
| --- | --- |
|<p>查看公司信息和用户信息</p><p>管理 Office 支持票证</p><p>为 Office 产品执行计费和采购操作</p> |<p>重置用户密码</p><p>创建和管理用户视图</p><p>创建、编辑和删除用户与组，以及管理用户许可证</p><p>管理域</p><p>管理公司信息</p><p>向其他人委派管理角色</p><p>使用目录同步</p><p>查看审核日志</p>|

### <a name="conditional-access-administrator"></a>条件访问管理员

| 有权执行的操作 | 无权执行的操作 |
| --- | --- |
|<p>查看公司信息和用户信息</p><p>管理条件访问设置</p> |<p>重置用户密码</p><p>创建和管理用户视图</p><p>创建、编辑和删除用户与组，以及管理用户许可证</p><p>管理域</p><p>管理公司信息</p><p>向其他人委派管理角色</p><p>使用目录同步</p><p>查看审核日志</p>|

### <a name="global-administrator"></a>全局管理员
| 有权执行的操作 | 无权执行的操作 |
| --- | --- |
|<p>查看公司信息和用户信息</p><p>管理 Office 支持票证</p><p>为 Office 产品执行计费和采购操作</p><p>重置用户密码</p><p>重置其他管理员的密码</p> <p>创建和管理用户视图</p><p>创建、编辑和删除用户与组，以及管理用户许可证</p><p>管理域</p><p>管理公司信息</p><p>向其他人委派管理角色</p><p>使用目录同步</p><p>启用或禁用多重身份验证</p><p>查看审核日志</p> |不适用 |

### <a name="password-administrator--helpdesk-administrator"></a>密码管理员/支持管理员
| 有权执行的操作 | 无权执行的操作 |
| --- | --- |
| <p>查看公司信息和用户信息</p><p>管理 Office 支持票证</p><p>只能为用户和其他支持管理员更改密码</p>|<p>为 Office 产品执行计费和采购操作</p><p>创建和管理用户视图</p><p>创建、编辑和删除用户与组，以及管理用户许可证</p><p>管理域</p><p>管理公司信息</p><p>向其他人委派管理角色</p><p>使用目录同步</p><p>查看报告</p>|

### <a name="information-protection-administrator-preview"></a>信息保护管理员（预览）
In | 有权执行的操作
-------- | ---------
Azure 信息保护 | <li>配置全局策略和作用域内策略中的标签和设置<li>配置和管理保护模板<li>激活或停用保护--
 
### <a name="reports-reader"></a>报告读者 
有权执行的操作 | 无权执行的操作
------ | ----------
查看 Azure AD 登录报告和审核日志<br>查看公司信息和用户信息<br>访问 Office 365 使用情况仪表板 | 创建和管理用户视图<br>创建、编辑和删除用户与组，以及管理用户许可证<br>向其他人委派管理角色<br>管理公司信息

### <a name="security-reader"></a>安全读取者
| In | 有权执行的操作 |
| --- | --- |
| Identity Protection Center |读取安全功能的所有安全报告和设置信息<ul><li>反垃圾邮件<li>加密<li>数据丢失预防<li>反恶意软件<li>高级威胁防护<li>防网络钓鱼<li>邮件流规则 |
| Privileged Identity Management |<p>以只读方式访问 Azure AD PIM 中所显示的一切信息：Azure AD 角色分配的策略和报告、安全审阅，以及在未来还可通过读取来访问 Azure AD 角色分配以外的方案的策略数据和报告。<p>**不能**注册 Azure AD PIM 或对其进行任何更改。 担任此角色的人员可以在 PIM 的门户中或通过 PowerShell，为其他角色（例如，全局管理员或特权角色管理员）的候选用户激活角色。 |
| <p>监视 Office 365 服务运行状况</p><p>Office 365 安全与合规中心</p> |<ul><li>读取和管理警报<li>读取安全策略<li>读取威胁情报、云应用发现以及搜索和调查中的隔离区<li>读取所有报告 |

### <a name="security-administrator"></a>安全管理员
| In | 有权执行的操作 |
| --- | --- |
| Identity Protection Center |<ul><li>安全读取者角色的所有权限。<li>此外，还能够执行除了重置密码以外的所有 IPC 操作。 |
| Privileged Identity Management |<ul><li>安全读取者角色的所有权限。<li>**不能**管理 Azure AD 角色成员身份或设置。 |
| <p>监视 Office 365 服务运行状况</p><p>Office 365 安全与合规中心 |<ul><li>安全读取者角色的所有权限。<li>可以配置高级威胁防护功能中的所有设置（恶意软件和病毒保护、恶意 URL 配置、URL 跟踪等）。 |

### <a name="service-administrator"></a>服务管理员
| 有权执行的操作 | 无权执行的操作 |
| --- | --- |
| <p>查看公司信息和用户信息</p><p>管理 Office 支持票证</p> |<p>重置用户密码</p><p>为 Office 产品执行计费和采购操作</p><p>创建和管理用户视图</p><p>创建、编辑和删除用户与组，以及管理用户许可证</p><p>管理域</p><p>管理公司信息</p><p>向其他人委派管理角色</p><p>使用目录同步</p><p>查看审核日志</p> |

### <a name="user-account-administrator"></a>用户帐户管理员
| 有权执行的操作 | 无权执行的操作 |
| --- | --- |
| <p>查看公司信息和用户信息</p><p>管理 Office 支持票证</p><p>只能为用户、支持管理员和其他用户帐户管理员更改密码</p><p>创建和管理用户视图</p><p>创建、编辑和删除用户与组，以及管理用户许可证，但有限制。 他/她不能删除全局管理员或创建其他管理员。</p> |<p>为 Office 产品执行计费和采购操作</p><p>管理域</p><p>管理公司信息</p><p>向其他人委派管理角色</p><p>使用目录同步</p><p>启用或禁用多重身份验证</p><p>查看审核日志</p> |

### <a name="to-add-a-colleague-as-a-global-administrator"></a>将某个同事添加为全局管理员

1. 使用租户目录的全局管理员的帐户登录到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。

   ![打开 Azure AD 管理中心](./media/active-directory-assign-admin-roles-azure-portal/active-directory-admin-center.png)

2. 选择“用户和组”&gt;“所有用户”

3. 找到要指定为全局管理员的用户，并打开该用户的边栏选项卡。

4. 在“用户”边栏选项卡中，选择“目录角色”。
 
5. 在“目录角色”边栏选项卡中，选择“全局管理员”角色，并保存。

## <a name="deprecated-roles"></a>已弃用的角色

不应使用以下角色。 这些角色已弃用，并将从 Azure AD 中删除。

* 即席许可证管理员
* 经电子邮件验证的用户创建者
* 设备联接
* 设备管理器
* 设备用户
* 工作区设备联接

## <a name="next-steps"></a>后续步骤

* 若要了解有关如何更改 Azure 订阅管理员的详细信息，请参阅 [如何添加或更改 Azure 管理员角色](../billing-add-change-azure-subscription-administrator.md)
* 若要了解有关如何在 Microsoft Azure 中控制资源访问的详细信息，请参阅 [Understanding resource access in Azure](active-directory-understanding-resource-access.md)（了解 Azure 中的资源访问）
* 有关 Azure Active Directory 如何与 Azure 订阅相关联的详细信息，请参阅 [How Azure subscriptions are associated with Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)（Azure 订阅与 Azure Active Directory 的关联方式）
* [管理用户](active-directory-create-users.md)
* [管理密码](active-directory-manage-passwords.md)
* [管理组](active-directory-manage-groups.md)
