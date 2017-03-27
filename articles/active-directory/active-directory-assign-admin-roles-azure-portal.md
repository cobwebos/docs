---
title: "在 Azure Active Directory 中分配管理员角色 | Microsoft 文档"
description: "介绍 Azure Active Directory 提供的管理员角色，以及如何分配这些角色。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7fc27e8e-b55f-4194-9b8f-2e95705fb731
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: faffd356191b7cca124f0a2cd147837078e4150f
ms.lasthandoff: 03/07/2017


---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>在 Azure Active Directory 中分配管理员角色
> [!div class="op_single_selector"]
> * [Azure 门户](active-directory-assign-admin-roles-azure-portal.md)
> * [Azure 经典门户](active-directory-assign-admin-roles.md)
>
>

使用 Azure Active Directory (Azure AD) 时，可以指定不同的管理员来执行不同的功能。 这些管理员可以按角色访问 Azure 门户或 Azure 经典门户中的各种功能：创建或编辑用户、将管理角色分配给他人、重置用户密码、管理用户许可证以及管理域等。 分配为管理员角色的用户在你的组织所订阅的所有云服务中拥有相同的权限，不管该角色是通过 Office 365 门户、Azure 经典门户还是用于 Windows PowerShell 的 Azure AD 模块分配的。

提供以下管理员角色：

* **计费管理员**：进行采购、管理订阅、管理支持票证并监视服务运行状况。

* **全局管理员/公司管理员**：有权访问所有管理功能。 注册 Azure 帐户的人员将成为全局管理员。 只有全局管理员才能分配其他管理员角色。 你的公司中可以有多个全局管理员。 全局管理员可以为任何用户和所有其他管理员重置密码。 若要重置他们自己的密码，他们必须使用密码重置服务 (passwordreset.microsoftonline.com) 或者让另一个全局管理员来为他们重置密码。

  > [!NOTE]
  > 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“公司管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“全局管理员”。
  >
  >

* **合规性管理员**：拥有此角色的用户具有 Office 365 安全与合规中心和 Exchange 管理中心的管理权限，以及读取 Office 365 管理中心审核记录的权限。 有关详细信息，请参阅“[关于 Office 365 管理员角色](https://microsoft.sharepoint.com/teams/adiamteam/_layouts/15/WopiFrame.aspx?sourcedoc={dae8d6f3-5990-46a2-b12b-4c0e561bc7cc}&action=view&wdAccPdf=1)”。

* **CRM 服务管理员**：具有此角色的用户在 Microsoft CRM Online（如果存在此服务）中拥有全局权限。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)（关于 Office 365 管理员角色）。

* **客户密码箱访问审批人**：如果启用了密码箱服务，具有此角色的用户可以审批 Microsoft 工程师访问公司信息的请求。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)（关于 Office 365 管理员角色）。

* **设备管理员**：具有此角色的用户将成为已加入 Azure Active Directory 的所有 Windows 10 设备上的管理员。

* **目录读取者**：这是一个遗留的角色，分配给不支持[同意框架](active-directory-integrating-applications.md)的应用程序。 不应将它分配给任何用户。

* **目录同步帐户**：请勿使用。 此角色自动分配给 Azure AD Connect 服务，不可用于其他任何用途。
* **目录写入者**：这是一个遗留的角色，分配给不支持[同意框架](active-directory-integrating-applications.md)的应用程序。 不应将它分配给任何用户。

* **Exchange 服务管理员**：具有此角色的用户在 Microsoft Exchange Online（如果存在此服务）中拥有全局权限。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)（关于 Office 365 管理员角色）。

* **Intune 服务管理员**：具有此角色的用户在 Microsoft Intune Online（如果存在此服务）中拥有全局权限。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)（关于 Office 365 管理员角色）。

* **邮箱管理员**：此角色仅用作 RIM Blackberry 设备的 Exchange Online 电子邮件支持的一部分。 如果你的组织不在 RIM Blackberry 设备上使用 Exchange Online 电子邮件，请勿使用此角色。

* **合作伙伴层 1 支持**：请勿使用。 此角色已弃用，并将从 Azure AD 中删除。 此角色仅供少数 Microsoft 转售合作伙伴使用，不适用于一般用途。

* **合作伙伴层 2 支持**：请勿使用。 此角色已弃用，并将从 Azure AD 中删除。 此角色仅供少数 Microsoft 转售合作伙伴使用，不适用于一般用途。

* **来宾邀请者**：此角色中的用户可以管理来宾邀请。 它不包括任何其他权限。

* **密码管理员/支持管理员**：重置密码、管理服务请求并监视服务运行状况。 密码管理员只能为用户和其他密码管理员重置密码。

  > [!NOTE]
  > 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“支持管理员”。
  >
  >
* **SharePoint 服务管理员**：具有此角色的用户在 Microsoft SharePoint Online（如果存在此服务）中拥有全局权限。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)（关于 Office 365 管理员角色）。

- **Skype for Business 服务管理员**：具有此角色的用户在 Microsoft Skype for Business（如果存在此服务）中拥有全局权限。 有关详细信息，请参阅“About Office 365 admin roles（关于 Office 365 管理员角色）”。 此角色以前称为 Lync 服务管理员角色。

- **安全管理员**：拥有安全读取者角色的所有只读权限，再加上下列相同服务的一些附加管理权限：Identity Protection Center、Privileged Identity Management、监视 Office 365 服务运行状况和 Office 365 安全与合规中心。

- **安全读取者**：能够以只读方式访问 Identity Protection Center、Privileged Identity Management、审核日志、监视 Office 365 服务运行状况和 Office 365 安全与合规中心的一些安全功能。

* **服务管理员**：管理服务请求并监视服务运行状况。 具有安全读取者角色的所有只读权限（例如，还可以读取审核日志）。

  > [!NOTE]
  > 若要为用户分配服务管理员角色，全局管理员必须先在服务（例如 Exchange Online）中将管理权限分配给用户，然后再在 Azure 经典门户中将服务管理员角色分配给用户。
  >
  >
* **用户帐户管理员**：重置密码、监视服务运行状况，并管理用户帐户、用户组和服务请求。 用户管理管理员权限存在一些限制。 例如，他们不能删除全局管理员或创建其他管理员。 他们可以为用户、其他密码管理员和其他用户管理员重置密码，但不能为计费管理员、全局管理员和服务管理员重置密码。

## <a name="administrator-permissions"></a>管理员权限

### <a name="application-administrator"></a>应用程序管理员
| 有权执行的操作 | 无权执行的操作 |
| --- | --- |
| <p>创建应用程序 </p><p>管理拥有的应用程序 </p>|<p>重置用户密码 </p><p>创建和管理用户视图</p><p>创建、编辑和删除用户与组，以及管理用户许可证</p><p>管理域</p><p>管理公司信息 </p><p>向其他人委派管理角色</p><p>使用目录同步</p><p>查看报告审核日志</p>|

###<a name="application-developer"></a>应用程序开发人员

| 有权执行的操作 | 无权执行的操作 |
| --- | --- |
|<p>查看目录信息 </p><p>创建和管理所有应用程序 </p><p>分配许可证和对应用程序的访问权限 </p><p>管理 Office 支持票证并监视服务运行状况</p><p>为 Office 产品执行计费和采购操作</p><p>查看报告审核日志</p>|<p>重置用户密码 </p><p>创建和管理用户视图 </p><p>创建、编辑和删除用户与组</p><p>管理域 </p><p>管理公司信息</p><p>向其他人委派管理角色 </p><p>使用目录同步</p>|


### <a name="billing-administrator"></a>计费管理员

| 有权执行的操作 | 无权执行的操作 |
| --- | --- |
|<p>查看公司信息和用户信息</p><p>管理 Office 支持票证</p><p>为 Office 产品执行计费和采购操作</p> |<p>重置用户密码</p><p>创建和管理用户视图</p><p>创建、编辑和删除用户与组，以及管理用户许可证</p><p>管理域</p><p>管理公司信息</p><p>向其他人委派管理角色</p><p>使用目录同步</p><p>查看审核日志</p>|

### <a name="global-administrator"></a>全局管理员
| 有权执行的操作 | 无权执行的操作 |
| --- | --- |
| <p>查看公司信息和用户信息</p><p>管理 Office 支持票证</p><p>为 Office 产品执行计费和采购操作</p><p>重置用户密码</p>
<p>重置其他管理员的密码</p> <p>创建和管理用户视图</p><p>创建、编辑和删除用户与组，以及管理用户许可证</p><p>管理域</p><p>管理公司信息</p><p>向其他人委派管理角色</p><p>使用目录同步</p><p>启用或禁用多重身份验证</p><p>查看审核日志</p> |不适用 |

### <a name="password-administrator"></a>密码管理员
| 有权执行的操作 | 无权执行的操作 |
| --- | --- |
| <p>查看公司信息和用户信息</p><p>管理 Office 支持票证</p><p>重置用户密码</p> <p>重置其他管理员的密码</p>|<p>为 Office 产品执行计费和采购操作</p><p>创建和管理用户视图</p><p>创建、编辑和删除用户与组，以及管理用户许可证</p><p>管理域</p><p>管理公司信息</p><p>向其他人委派管理角色</p><p>使用目录同步</p><p>查看报告</p>|

### <a name="service-administrator"></a>服务管理员
| 有权执行的操作 | 无权执行的操作 |
| --- | --- |
| <p>查看公司信息和用户信息</p><p>管理 Office 支持票证</p> |<p>重置用户密码</p><p>为 Office 产品执行计费和采购操作</p><p>创建和管理用户视图</p><p>创建、编辑和删除用户与组，以及管理用户许可证</p><p>管理域</p><p>管理公司信息</p><p>向其他人委派管理角色</p><p>使用目录同步</p><p>查看审核日志</p> |

### <a name="user-administrator"></a>用户管理员
| 有权执行的操作 | 无权执行的操作 |
| --- | --- |
| <p>查看公司信息和用户信息</p><p>管理 Office 支持票证</p><p>重置用户密码，但有限制。</p><p>重置其他管理员的密码</p><p>重置其他用户的密码</p><p>创建和管理用户视图</p><p>创建、编辑和删除用户与组，以及管理用户许可证，但有限制。 他/她不能删除全局管理员或创建其他管理员。</p> |<p>为 Office 产品执行计费和采购操作</p><p>管理域</p><p>管理公司信息</p><p>向其他人委派管理角色</p><p>使用目录同步</p><p>启用或禁用多重身份验证</p><p>查看审核日志</p> |

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

## <a name="details-about-the-global-administrator-role"></a>有关全局管理员角色的详细信息
全局管理员有权访问所有管理功能。 默认情况下，系统会将注册 Azure 订阅的人员指派为目录的全局管理员角色。 只有全局管理员才能分配其他管理员角色。

## <a name="assign-or-remove-administrator-roles"></a>分配或删除管理员角色
若要了解如何在 Azure Active Directory 中向用户分配管理角色，请参阅[在 Azure Active Directory 预览版中向用户分配管理角色](active-directory-users-assign-role-azure-portal.md)。

## <a name="next-steps"></a>后续步骤

* 若要了解有关如何更改 Azure 订阅管理员的详细信息，请参阅 [如何添加或更改 Azure 管理员角色](../billing-add-change-azure-subscription-administrator.md)
* 若要了解有关如何在 Microsoft Azure 中控制资源访问的详细信息，请参阅 [Understanding resource access in Azure](active-directory-understanding-resource-access.md)（了解 Azure 中的资源访问）
* 有关 Azure Active Directory 如何与 Azure 订阅相关联的详细信息，请参阅 [How Azure subscriptions are associated with Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)（Azure 订阅与 Azure Active Directory 的关联方式）
* [管理用户](active-directory-create-users.md)
* [管理密码](active-directory-manage-passwords.md)
* [管理组](active-directory-manage-groups.md)

