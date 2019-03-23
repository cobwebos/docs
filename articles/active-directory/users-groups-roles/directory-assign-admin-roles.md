---
title: 管理员角色说明和权限 - Azure Active Directory | Microsoft Docs
description: 管理员角色可以添加用户、分配管理角色、重置用户密码、管理用户许可证，或者管理域。
services: active-directory
author: curtand
manager: mtillman
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/12/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9793ad76859fdd14b3194dfeb77e68905ff58fe5
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58362061"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Azure Active Directory 中的管理员角色权限

使用 Azure Active Directory (Azure AD)，可以指定限制的管理员来管理在较低权限角色的标识任务。 管理员可以添加或更改用户、 分配管理角色、 重置用户密码、 管理用户许可证和管理域名作为此类目的的分配。 只能在 Azure AD 的用户设置中更改默认用户权限。

全局管理员有权使用所有管理功能。 默认情况下，系统会将注册 Azure 订阅的人员指派为目录的全局管理员角色。 只有全局管理员和特权角色管理员可以委托管理员角色。 为了降低业务风险，我们建议仅将此角色分配给你的公司中的少数人。


## <a name="assign-or-remove-administrator-roles"></a>分配或删除管理员角色

若要了解如何在 Azure Active Directory 中向用户分配管理角色，请参阅[在 Azure Active Directory 中查看和分配管理员角色](directory-manage-roles-portal.md)。

## <a name="available-roles"></a>可用的角色

提供以下管理员角色：

* **[应用程序管理员](#application-administrator)**：充当此角色的用户可以创建和管理企业应用程序、应用程序注册和应用程序代理设置的所有方面。 此角色还可以同意委派权限，以及除 Microsoft Graph 和 Azure AD Graph 之外的应用程序权限。 创建新的应用程序注册或企业应用程序时，分配给此角色的用户不会添加为所有者。

  <b>重要说明</b>：此角色授予管理应用程序凭据这一功能。 分配有此角色的用户可以将凭据添加到应用程序，并使用这些凭据模拟应用程序的标识。 如果已向应用程序的标识授予 Azure Active Directory 访问权限，如创建或更新用户或其他对象，那么分配到此角色的用户在模拟应用程序时可以执行这些操作。 这种模拟应用程序标识的能力可能是用户在 Azure AD 中角色分配的基础上的权限提升。 请务必了解，向用户分配应用程序管理员角色，会赋予其模拟应用程序标识的能力。

* **[应用程序开发人员](#application-developer)**：在将设置“用户可以注册应用程序”设置为“否”时，充当此角色的用户可以创建应用程序注册。 此角色还授予自己代表同意的权限时"用户可以同意应用代表他们的公司数据访问"设置设为 no。 创建新的应用程序注册或企业应用程序时，分配给此角色的用户添加为所有者。

* **[身份验证管理员](#authentication-administrator)**：具有此角色的用户可以设置或重置非密码凭据。 身份验证管理员可以要求用户重新注册现有的非密码凭据 （例如，MFA 或 FIDO） 针对和撤销**在设备上记住 MFA**，在下一步登录的用户的 mfa 提示非管理员或分配以下角色：
  * 身份验证管理员
  * 目录读者
  * 来宾邀请者
  * 消息中心读取者
  * 报告读者

  身份验证的管理员角色当前处于公共预览状态。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

  <b>重要说明</b>：具有此角色的用户可以更改可能有权访问 Azure Active Directory 内外敏感或私有信息或关键配置的用户的凭据。 更改用户的凭据可能意味着假定用户标识和权限的能力。 例如：

  * 应用程序注册和企业应用程序所有者，可以管理他们拥有的应用的凭据。 这些应用程序可能在 Azure AD 或其他位置拥有未授予身份验证管理员的特权。 通过身份验证管理员可能能够假定应用程序所有者的身份，然后进一步此路径通过更新应用程序的凭据来假设特权的应用程序的标识。
  * Azure 订阅所有者，可能对 Azure 中的敏感或私有信息或关键配置拥有访问权限。
  * 安全组和 Office 365 组所有者，可以管理组成员身份。 这些组可能会授予对 Azure AD 或其他位置敏感或私有信息或关键配置的访问权限。
  * Azure AD 之外的其他服务中的管理员，如 Exchange Online、Office 安全与合规中心以及人力资源系统。
  * 高级管理人员、法律顾问和人力资源员工之类的非管理员，可能有权访问敏感或私有信息。

* **[计费管理员](#billing-administrator)**：进行采购、管理订阅、管理支持票证，以及监视服务运行状况。

* **[云应用程序管理员](#cloud-application-administrator)**：充当角色的用户具有与应用程序管理员角色相同的权限，但不包括管理应用程序代理的权限。 此角色授予创建和管理企业应用程序和应用程序注册的所有方面的权限。 此角色还可以同意委派权限，以及除 Microsoft Graph 和 Azure AD Graph 之外的应用程序权限。 创建新的应用程序注册或企业应用程序时，分配给此角色的用户不会添加为所有者。

  <b>重要说明</b>：此角色授予管理应用程序凭据这一功能。 分配有此角色的用户可以将凭据添加到应用程序，并使用这些凭据模拟应用程序的标识。 如果已向应用程序的标识授予 Azure Active Directory 访问权限，如创建或更新用户或其他对象，那么分配到此角色的用户在模拟应用程序时可以执行这些操作。 这种模拟应用程序标识的能力可能是用户在 Azure AD 中角色分配的基础上的权限提升。 请务必了解，向用户分配云应用程序管理员角色，会赋予其模拟应用程序标识的能力。

* **[云设备管理员](#cloud-device-administrator)**：充当此角色的用户可以在 Azure AD 中启用、禁用和删除设备，并可以在 Azure 门户中读取 Windows 10 BitLocker 密钥（如果有）。 该角色不能授予设备上其他任何属性的管理权限。

* **[合规性管理员](#compliance-administrator)**：具有此角色的用户有权管理 Microsoft 365 合规中心、Microsoft 365 管理中心、Azure 和 Office 365 安全与合规中心中的合规性相关功能。 这些用户还可以管理 Exchange 管理中心、Teams 和 Skype for Business 管理中心内的所有功能，并可创建适用于 Azure 和 Microsoft 365 的支持票证。 [关于 Office 365 管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)中提供了详细信息。

  In | 有权执行的操作
  ----- | ----------
  [Microsoft 365 合规中心](https://protection.microsoft.com) | 跨 Microsoft 365 服务保护和管理组织数据<br>管理合规性警报
  [合规性管理器](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 跟踪、分配并验证组织的法规合规性活动
  [Office 365 安全与合规中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理数据治理<br>执行法律和数据调查<br>管理数据主体请求
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | 查看所有 Intune 审核数据
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 拥有只读权限，可以管理警报<br>可以创建和修改文件策略并允许执行文件管理操作<br> 可以查看数据管理下的所有内置报表

<!--* **[Compliance Data Administrator](#compliance-data-administrator)**: Users with this role have permissions to protect and track data in the Microsoft 365 compliance center, Microsoft 365 admin center, and Azure. Users can also manage all features within the Exchange admin center, Compliance Manager, and Teams & Skype for Business admin center and create support tickets for Azure and Microsoft 365.

  In | Can do
  ----- | ----------
  [Microsoft 365 compliance center](https://protection.microsoft.com) | Monitor compliance-related policies across Microsoft 365 services<br>Manage compliance alerts
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Track, assign, and verify your organization's regulatory compliance activities
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Manage data governance<br>Perform legal and data investigation<br>Manage Data Subject Request
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | View all Intune audit data
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Has read-only permissions and can manage alerts<br>Can create and modify file policies and allow file governance actions<br> Can view all the built-in reports under Data Management
-->
* **[条件访问管理员](#conditional-access-administrator)**：具有此角色的用户能够管理 Azure Active Directory 条件访问设置。
  > [!NOTE]
  > 若要在 Azure 中部署 Exchange ActiveSync 条件访问策略，用户还必须是全局管理员。
  
* **[客户密码箱访问审批者](#customer-lockbox-access-approver)**：管理你的组织中的[客户密码箱请求](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests)。 他们接收客户密码箱请求的电子邮件通知，并且可以批准和拒绝来自 Microsoft 365 管理中心的请求。 他们还可以开启或关闭客户密码箱功能。 只有全局管理员可以重置分配到此角色的用户的密码。
  <!--  This was announced in August of 2018. https://techcommunity.microsoft.com/t5/Security-Privacy-and-Compliance/Customer-Lockbox-Approver-Role-Now-Available/ba-p/223393-->

* **[设备管理员](#device-administrators)**：此角色只能作为[设备设置](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)中的其他本地管理员进行分配。 拥有此角色的用户成为所有已加入 Azure Active Directory 的 Windows 10 设备上的本地计算机管理员。 他们无权管理 Azure Active Directory 中的设备对象。 

* **[目录读取者](#directory-readers)**：这是一个遗留的角色，分配给不支持[许可框架](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)的应用程序。 不应将它分配给任何用户。

* **[目录同步帐户](#directory-synchronization-accounts)**：请勿使用。 此角色自动分配给 Azure AD Connect 服务，不可用于其他任何用途。

* **[目录写入者](#directory-writers)**：这是一个遗留的角色，分配给不支持[许可框架](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)的应用程序。 不应将它分配给任何用户。

* **[Dynamics 365 管理员/CRM 管理员](#crm-service-administrator)**：具有此角色的用户具有 Microsoft Dynamics 365 Online 中的全局权限(如果该服务存在)，并且能够管理支持票证和监视服务运行状况。 有关详细信息，请参阅[使用服务管理员角色管理租户](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)。
  > [!NOTE] 
  > 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“Dynamics 365 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Dynamics 365 管理员”。

* **[Exchange 管理员](#exchange-service-administrator)**：具有此角色的用户具有 Microsoft Exchange Online 内的全局权限（如果该服务存在）。 还能够创建和管理所有 Office 365 组、管理支持票证和监视服务运行状况。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)（关于 Office 365 管理员角色）。
  > [!NOTE]
  > 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“Exchange 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Exchange 管理员”。 它是 [Exchange 管理中心](https://go.microsoft.com/fwlink/p/?LinkID=529144)中的“Exchange Online 管理员”。 


* **[全局管理员/公司管理员](#company-administrator)**：具有此角色的用户有权访问 Azure Active Directory 以及使用 Azure Active Directory 标识的服务（例如 Microsoft 365 安全中心、Microsoft 365 合规中心、Exchange Online、SharePoint Online 和 Skype for Business Online）中的所有管理功能。 注册 Azure Active Directory 租户的人员将成为全局管理员。 只有全局管理员才能分配其他管理员角色。 公司中可以有多个全局管理员。 全局管理员可以为任何用户和所有其他管理员重置密码。

  > [!NOTE]
  > 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“公司管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“全局管理员”。
  >
  >

* **[来宾邀请者](#guest-inviter)**：此角色的用户可在“成员可以邀请”用户设置设置为“否”时管理 Azure Active Directory B2B 来宾用户邀请。 [关于 Azure AD B2B 协作](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)中提供了有关 B2B 协作的详细信息。 它不包括任何其他权限。

* **[信息保护管理员](#information-protection-administrator)**：具有此角色的用户拥有 Azure 信息保护服务中的所有权限。 此角色可以配置 Azure 信息保护策略的标签、管理保护模板，以及激活保护。 此角色不会授予 Identity Protection Center、Privileged Identity Management、监视 Office 365 服务运行状况或 Office 365 安全与合规中心的权限。

* **[Intune 管理员](#intune-service-administrator)**：具有此角色的用户具有 Microsoft Intune Online 内的全局权限（如果该服务存在）。 此外，此角色包含管理以关联策略，以及创建和管理组的用户和设备的能力。 有关详细信息，请参阅[使用 Microsoft Intune 进行基于角色的管理控制 (RBAC)](https://docs.microsoft.com/intune/role-based-access-control)
  > [!NOTE]
  > 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“Intune 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Intune 管理员”。

* **[许可证管理员](#license-administrator)**：具有此角色的用户可以添加、删除和更新用户、组（使用基于组的许可）的许可分配，以及管理用户的使用位置。 该角色不授予在使用位置之外购买或管理订阅、创建或管理组，或者创建或管理用户的权限。 此角色无权查看、创建或管理支持票证。

* **[消息中心读取者](#message-center-reader)**：具有此角色的用户可以在其组织的 [Office 365 消息中心](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)内，监视 Exchange、Intune 和 Microsoft Teams 等已配置服务的通知和公告运行状况更新。 消息中心读者会收到包含帖子和最新动态的每周电子邮件摘要，并能在 Office 365 内共享消息中心帖子。 在 Azure AD 中，分配到此角色的用户对 Azure AD 服务只拥有只读访问权限，如用户和组。 此角色无权查看、创建或管理支持票证。

* **[合作伙伴一线支持人员](#partner-tier1-support)**：请勿使用。 此角色已弃用，并将从 Azure AD 中删除。 此角色仅供少数 Microsoft 转售合作伙伴使用，不适用于一般用途。

* **[合作伙伴二线支持人员](#partner-tier2-support)**：请勿使用。 此角色已弃用，并将从 Azure AD 中删除。 此角色仅供少数 Microsoft 转售合作伙伴使用，不适用于一般用途。

* **[支持 （密码） 管理员](#helpdesk-administrator)**:具有此角色的用户可以更改密码、使刷新令牌失效、管理服务请求和监视服务运行状况。 使刷新令牌失效会强制用户重新登录。 支持人员管理员可以重置密码，并会使非管理员或分配以下角色的其他用户的刷新令牌无效：
  * 目录读者
  * 来宾邀请者
  * 支持管理员
  * 消息中心读取者
  * 报告读者
  
  <b>重要说明</b>：具有此角色的用户可以更改可能有权访问 Azure Active Directory 内外敏感或私有信息或关键配置的用户的密码。 更改用户的密码可能意味着假定用户标识和权限的能力。 例如：
  * 应用程序注册和企业应用程序所有者，可以管理他们拥有的应用的凭据。 这些应用程序可能在 Azure AD 或其他位置拥有未授予支持人员管理员的特权。 通过此路径，支持人员管理员可能能够假定应用程序所有者的身份，然后通过更新应用程序的凭据来进一步假定特权应用程序的标识。
  * Azure 订阅所有者，可能对 Azure 中的敏感或私有信息或关键配置拥有访问权限。
  * 安全组和 Office 365 组所有者，可以管理组成员身份。 这些组可能会授予对 Azure AD 或其他位置敏感或私有信息或关键配置的访问权限。
  * Azure AD 之外的其他服务中的管理员，如 Exchange Online、Office 安全与合规中心以及人力资源系统。
  * 高级管理人员、法律顾问和人力资源员工之类的非管理员，可能有权访问敏感或私有信息。

  > [!NOTE]
  > 此角色在中，以前称为"密码管理员" [Azure 门户](https://portal.azure.com/)。 我们正在更改其名称为"支持人员管理员"，以匹配其 Azure AD PowerShell、 Azure AD 图形 API 和 Microsoft Graph API 中的名称。 在短时间，我们将更改为"支持人员 （密码） Administrator"的名称，对"支持人员管理员"的更改之前在 Azure 门户中。
  >
  
* **[Power BI 管理员](#power-bi-service-administrator)**：具有此角色的用户具有 Microsoft Power BI 内的全局权限（如果该服务存在），并且能够管理支持票证和监视服务运行状况。 有关详细信息，请参阅[了解 Power BI 管理员角色](https://docs.microsoft.com/power-bi/service-admin-role)。
  > [!NOTE]
  > 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“Power BI 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Power BI 管理员”。

* **[特权身份验证管理员](#privileged-authentication-administrator)**:具有此角色的用户可以设置或重置所有用户，包括全局管理员的非密码凭据。 特权身份验证管理员可以强制用户重新注册现有的非密码凭据 (例如 MFA，FIDO) 针对和撤销在设备上记住 MFA，所有用户在下次登录 mfa 提示。 特权身份验证管理员可以：
  * 强制用户重新注册针对现有非密码凭据 (例如 MFA，FIDO)
  * 撤消在设备上记住 MFA，在下次登录 mfa 提示

* **[特权角色管理员](#privileged-role-administrator)**：具有此角色的用户可以管理角色分配以及 Azure AD Privileged Identity Management 中的 Azure Active Directory。 此外，此角色允许 Privileged Identity Management 的所有方面。

  <b>重要说明</b>：此角色授予管理的所有 Azure AD 角色，包括全局管理员角色分配的功能。 此角色不包括 Azure AD 中的任何其他权限功能，如创建或更新用户。 但是，分配到此角色的用户可通过分配其他角色，授予自己或其他人额外的特权。

* **[报告读取者](#reports-reader)**：具有此角色的用户可以查看使用情况报告数据和 Microsoft 365 管理中心中的报表仪表板和采用上下文包在 Power BI 中。 此外，此角色还提供对 Azure AD 中的登录报告和活动以及 Microsoft Graph 报告 API 返回的数据的访问权限。 分配到“报告读者”角色的用户只能访问相关使用情况和采用指标。 它们没有任何管理员权限，无法配置设置或访问产品特定的管理中心（如 Exchange）。 此角色无权查看、创建或管理支持票证。

* **[安全管理员](#security-administrator)**：具有此角色的用户有权管理 Microsoft 365 安全中心、Azure Active Directory Identity Protection、Azure 信息保护和 Office 365 安全与合规中心中与安全相关的功能。 [Office 365 安全与合规中心](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)提供了有关 Office 365 权限的详细信息。
  
  In | 有权执行的操作
  --- | ---
  [Microsoft 365 安全中心](https://protection.microsoft.com) | 跨 Microsoft 365 服务监视与安全相关的策略<br>管理安全威胁和警报<br>查看报告
  Identity Protection Center | 安全读取者角色的所有权限<br>此外，还能够执行除了重置密码以外的所有 Identity Protection Center 操作
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | 安全读取者角色的所有权限<br>**不能**管理 Azure AD 角色分配或设置
  [Office 365 安全与合规中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理安全策略<br>查看、调查和响应安全威胁<br>查看报告
  Azure 高级威胁防护 | 监视和响应可疑安全活动
  Windows Defender ATP 和 EDR | 分配角色<br>管理计算机组<br>配置终结点威胁检测和自动修正<br>查看、调查并响应警报
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | 视图用户、设备、注册、配置和应用程序信息<br>无法对 Intune 进行更改
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 添加管理员、添加策略和设置、上传日志以及执行管理操作
  [Azure 安全中心](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | 可以查看安全策略、查看安全状态、编辑安全策略、查看警报和建议、关闭警报和建议
  [Office 365 服务运行状况](https://docs.microsoft.com/office365/enterprise/view-service-health) | 查看 Office 365 服务的运行状况

<!--* **[Security operator](#security-operator)**: Users with this role can manage alerts and have global read-only access on security-related feature, including all information in Microsoft 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management, as well as the ability to read Azure Active Directory sign-in reports and audit logs, and in Office 365 Security & Compliance Center.

  In | Can do
  --- | ---
  [Microsoft 365 security center](https://protection.microsoft.com) | All permissions of the Security Reader role<br>View, investigate, and respond to security threats alerts
  Identity Protection Center | All permissions of the Security Reader role<br>Additionally, the ability to perform all Identity Protection Center operations except for resetting passwords
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | All permissions of the Security Reader role
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | All permissions of the Security Reader role<br>View, investigate, and respond to security alerts
  Windows Defender ATP and EDR | All permissions of the Security Reader role<br>View, investigate, and respond to security alerts
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | All permissions of the Security Reader role
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | All permissions of the Security Reader role
  [Office 365 service health](https://docs.microsoft.com/office365/enterprise/view-service-health) | View the health of Office 365 services
-->
* **[安全读取者](#security-reader)**：具有此角色的用户对安全相关的功能具有全局只读访问权限，包括 Microsoft 365 安全中心、Azure Active Directory、Identity Protection、Privileged Identity Management 中的所有信息，并且能够阅读 Azure Active Directory 登录报告和审核日志，还授予了对 Office 365 安全与合规中心的只读权限。 [Office 365 安全与合规中心](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)提供了有关 Office 365 权限的详细信息。

  In | 有权执行的操作
  --- | ---
  [Microsoft 365 安全中心](https://protection.microsoft.com) | 跨 Microsoft 365 服务查看与安全相关的策略<br>查看安全威胁和警报<br>查看报告
  Identity Protection Center | 读取安全功能的所有安全报告和设置信息<br><ul><li>反垃圾邮件<li>加密<li>数据丢失预防<li>反恶意软件<li>高级威胁防护<li>防网络钓鱼<li>邮件流规则
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | 以只读方式访问 Azure AD PIM 中所显示的一切信息：Azure AD 角色分配的策略和报告、安全审阅，以及在未来还可通过读取来访问 Azure AD 角色分配以外的方案的策略数据和报告。<br>**不能**注册 Azure AD PIM 或对其进行任何更改。 在 PIM 门户或通过 PowerShell 中，此角色中的某人可以激活其他角色 （例如，全局管理员或特权角色管理员），如果用户有资格获得它们。
  [Office 365 安全与合规中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 查看安全策略<br>查看并调查安全威胁<br>查看报告
  Windows Defender ATP 和 EDR | 查看并调查警报
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | 视图用户、设备、注册、配置和应用程序信息。 无法对 Intune 进行更改。
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 拥有只读权限，可以管理警报
  [Azure 安全中心](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | 可以查看建议和警报、查看安全策略、查看安全状态，但不能进行更改
  [Office 365 服务运行状况](https://docs.microsoft.com/office365/enterprise/view-service-health) | 查看 Office 365 服务的运行状况

* **[服务支持管理员](#service-support-administrator)**：具有此角色的用户可以开具支持请求 Microsoft Azure 和 Office 365 服务和服务仪表板和消息中心中的视图[Azure 门户](https://portal.azure.com)并[Microsoft 365 管理中心内](https://admin.microsoft.com)。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)（关于 Office 365 管理员角色）。
  > [!NOTE]
  > 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“服务支持管理员”。 它是在"服务管理员" [Azure 门户](https://portal.azure.com)，则[Microsoft 365 管理中心内](https://admin.microsoft.com)，并在 Intune 门户。

* **[SharePoint 管理员](#sharepoint-service-administrator)**：具有此角色的用户在 Microsoft SharePoint Online（如果存在此服务）中拥有全局权限，并且能够创建和管理所有 Office 365 组、管理支持票证和监视服务运行状况。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)（关于 Office 365 管理员角色）。
  > [!NOTE]
  > 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“SharePoint 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“SharePoint 管理员”。

* **[Skype for Business/Lync 管理员](#lync-service-administrator)**：具有此角色的用户具有 Microsoft Skype for Business 中的全局权限，以及管理 Azure Active Directory 中的特定于 Skype 的用户属性。 此外，此角色可授予管理支持票证、监视服务运行状况以及访问 Teams 和 Skype for Business 管理中心的能力。 帐户必须获取 Teams 许可证，否则无法运行 Teams PowerShell cmdlet。 有关详细信息，请参阅[关于 Skype for Business 管理员角色](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5)；有关 Teams 许可信息，请参阅 [Skype for Business 和 Microsoft Teams 附加许可](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“Lync 服务管理员”。 它是 [Azure 门户](https://portal.azure.com/)中的“Skype for Business 管理员”。

* **[Teams 管理员](#teams-service-administrator)**：充当此角色的用户可以通过 Microsoft Teams 和 Skype for Business 管理中心以及相应的 PowerShell 模块来管理 Microsoft Teams 工作负荷的所有方面。 这包括（但不限于）与电话、消息、会议和 Teams 自身相关的所有管理工具。 此外，此角色还能够创建和管理所有 Office 365 组、管理支持票证和监视服务运行状况。
  > [!NOTE]
  > 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“Teams 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Teams 管理员”。

* **[Teams 通信管理员](#teams-communications-administrator)**：充当此角色的用户可以管理 Microsoft Teams 工作负荷的语音与电话相关方面。 这包括用于分配电话号码的管理工具、语音和会议策略，以及通话分析工具集的完全访问权限。

* **[Teams 通信支持工程师](#teams-communications-support-engineer)**：充当此角色的用户可以使用 Microsoft Teams 和 Skype for Business 管理中心的用户通话故障排除工具，来排查 Microsoft Teams 和 Skype for Business 中的通信问题。 充当此角色的用户可以查看所有参与方的完整通话记录信息。 此角色无权查看、创建或管理支持票证。

* **[Teams 通信支持专家](#teams-communications-support-specialist)**：充当此角色的用户可以使用 Microsoft Teams 和 Skype for Business 管理中心的用户通话故障排除工具，来排查 Microsoft Teams 和 Skype for Business 中的通信问题。 充当此角色的用户只能查看他们所查找的特定用户的通话中的用户详细信息。 此角色无权查看、创建或管理支持票证。

* **[用户管理员](#user-account-administrator)**:此角色可以具有的用户创建用户，和管理的用户的所有方面，有一些限制 （见下文），可以更新密码过期策略。 此外，具有此角色的用户可以创建和管理所有组。 此角色还能够创建和管理用户视图、管理支持票证和监视服务运行状况。

  | | |
  | --- | --- |
  |常规权限|<p>创建用户和组</p><p>创建和管理用户视图</p><p>管理 Office 支持票证<p>更新密码过期策略|
  |<p>适用于所有用户，包括所有管理员</p>|<p>管理许可证</p><p>管理除用户主体名称之外的所有用户属性</p>
  |仅适用于不是管理员或具有以下任一管理员角色（权限有限）的用户：<ul><li>目录读者<li>来宾邀请者<li>支持管理员<li>消息中心读取者<li>报告读者<li>用户管理员|<p>删除和还原</p><p>禁用和启用</p><p>使刷新令牌失效</p><p>管理包括用户主体名称在内的所有用户属性</p><p>重置密码</p><p>更新 (FIDO) 设备密钥</p>
  
  <b>重要说明</b>：具有此角色的用户可以更改可能有权访问 Azure Active Directory 内外敏感或私有信息或关键配置的用户的密码。 更改用户的密码可能意味着假定用户标识和权限的能力。 例如：
  * 应用程序注册和企业应用程序所有者，可以管理他们拥有的应用的凭据。 这些应用程序可能在 Azure AD 或其他位置拥有未授予用户管理员的特权。 通过此路径，用户管理员可能能够假定应用程序所有者的身份，然后通过更新应用程序的凭据来进一步假定特权应用程序的标识。
  * Azure 订阅所有者，可能对 Azure 中的敏感或私有信息或关键配置拥有访问权限。
  * 安全组和 Office 365 组所有者，可以管理组成员身份。 这些组可能会授予对 Azure AD 或其他位置敏感或私有信息或关键配置的访问权限。
  * Azure AD 之外的其他服务中的管理员，如 Exchange Online、Office 安全与合规中心以及人力资源系统。
  * 高级管理人员、法律顾问和人力资源员工之类的非管理员，可能有权访问敏感或私有信息。

## <a name="role-permissions"></a>角色权限
下表描述 Azure Active Directory 中授予每个角色的特定权限。 某些角色可能在 Azure Active Directory 外部的 Microsoft 服务中拥有其他权限。

### <a name="application-administrator"></a>应用程序管理员
可以创建和管理应用注册和企业应用的所有方面。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | 更新 Azure Active Directory 中的 applications.audience 属性。 |
| microsoft.aad.directory/applications/authentication/update | 更新 Azure Active Directory 中的 applications.authentication 属性。 |
| microsoft.aad.directory/applications/basic/update | 更新 Azure Active Directory 中应用程序的基本属性。 |
| microsoft.aad.directory/applications/create | 在 Azure Active Directory 中创建应用程序。 |
| microsoft.aad.directory/applications/credentials/update | 更新 Azure Active Directory 中的 applications.credentials 属性。 |
| microsoft.aad.directory/applications/delete | 删除 Azure Active Directory 中的应用程序。 |
| microsoft.aad.directory/applications/owners/update | 更新 Azure Active Directory 中的 applications.owners 属性。 |
| microsoft.aad.directory/applications/permissions/update | 更新 Azure Active Directory 中的 applications.permissions 属性。 |
| microsoft.aad.directory/applications/policies/update | 更新 Azure Active Directory 中的 applications.policies 属性。 |
| microsoft.aad.directory/appRoleAssignments/create | 在 Azure Active Directory 中创建 appRoleAssignments。 |
| microsoft.aad.directory/appRoleAssignments/read | 读取 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.aad.directory/appRoleAssignments/update | 更新 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.aad.directory/appRoleAssignments/delete | 删除 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.aad.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | 读取 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | 更新 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.aad.directory/policies/applicationConfiguration/create | 在 Azure Active Directory 中创建策略。 |
| microsoft.aad.directory/policies/applicationConfiguration/delete | 删除 Azure Active Directory 中的策略。 |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | 读取 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | 更新 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | 读取 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 属性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 属性。 |
| microsoft.aad.directory/servicePrincipals/audience/update | 更新 Azure Active Directory 中的 servicePrincipals.audience 属性。 |
| microsoft.aad.directory/servicePrincipals/authentication/update | 更新 Azure Active Directory 中的 servicePrincipals.authentication 属性。 |
| microsoft.aad.directory/servicePrincipals/basic/update | 更新 Azure Active Directory 中 servicePrincipals 的基本属性。 |
| microsoft.aad.directory/servicePrincipals/create | 在 Azure Active Directory 中创建 servicePrincipals。 |
| microsoft.aad.directory/servicePrincipals/credentials/update | 更新 Azure Active Directory 中的 servicePrincipals.credentials 属性。 |
| microsoft.aad.directory/servicePrincipals/delete | 删除 Azure Active Directory 中的 servicePrincipals。 |
| microsoft.aad.directory/servicePrincipals/owners/update | 更新 Azure Active Directory 中的 servicePrincipals.owners 属性。 |
| microsoft.aad.directory/servicePrincipals/permissions/update | 更新 Azure Active Directory 中的 servicePrincipals.permissions 属性。 |
| microsoft.aad.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 属性。 |
| microsoft.aad.directory/signInReports/allProperties/read | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="application-developer"></a>应用程序开发人员
可以创建独立于“用户可注册应用程序”设置的应用程序注册。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | 在 Azure Active Directory 中创建应用程序。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | 在 Azure Active Directory 中创建 appRoleAssignments。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | 在 Azure Active Directory 中创建 oAuth2PermissionGrants。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.aad.directory/servicePrincipals/createAsOwner | 在 Azure Active Directory 中创建 servicePrincipals。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |

### <a name="authentication-administrator"></a>身份验证管理员
允许查看、设置和重置任何非管理员用户的身份验证方法信息。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft.aad.directory/users/strongAuthentication/update | 更新强身份验证属性，如 MFA 凭据信息。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="billing-administrator"></a>计费管理员
可以执行与常见计费相关的任务，例如更新付款信息。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/organization/basic/update | 更新 Azure Active Directory 中组织的基本属性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.commerce.billing/allEntities/allTasks | 管理 Office 365 计费的各个方面。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="desktop-analytics-administrator"></a>桌面分析管理员
可以访问和管理桌面管理工具和服务，包括 Intune。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | 管理桌面分析的各个方面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="cloud-application-administrator"></a>云应用管理员
可以创建和管理应用注册和企业应用的所有方面，应用代理除外。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | 更新 Azure Active Directory 中的 applications.audience 属性。 |
| microsoft.aad.directory/applications/authentication/update | 更新 Azure Active Directory 中的 applications.authentication 属性。 |
| microsoft.aad.directory/applications/basic/update | 更新 Azure Active Directory 中应用程序的基本属性。 |
| microsoft.aad.directory/applications/create | 在 Azure Active Directory 中创建应用程序。 |
| microsoft.aad.directory/applications/credentials/update | 更新 Azure Active Directory 中的 applications.credentials 属性。 |
| microsoft.aad.directory/applications/delete | 删除 Azure Active Directory 中的应用程序。 |
| microsoft.aad.directory/applications/owners/update | 更新 Azure Active Directory 中的 applications.owners 属性。 |
| microsoft.aad.directory/applications/permissions/update | 更新 Azure Active Directory 中的 applications.permissions 属性。 |
| microsoft.aad.directory/applications/policies/update | 更新 Azure Active Directory 中的 applications.policies 属性。 |
| microsoft.aad.directory/appRoleAssignments/create | 在 Azure Active Directory 中创建 appRoleAssignments。 |
| microsoft.aad.directory/appRoleAssignments/update | 更新 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.aad.directory/appRoleAssignments/delete | 删除 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.aad.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| microsoft.aad.directory/policies/applicationConfiguration/create | 在 Azure Active Directory 中创建策略。 |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | 读取 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | 更新 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.aad.directory/policies/applicationConfiguration/delete | 删除 Azure Active Directory 中的策略。 |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | 读取 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | 更新 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | 读取 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 属性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 属性。 |
| microsoft.aad.directory/servicePrincipals/audience/update | 更新 Azure Active Directory 中的 servicePrincipals.audience 属性。 |
| microsoft.aad.directory/servicePrincipals/authentication/update | 更新 Azure Active Directory 中的 servicePrincipals.authentication 属性。 |
| microsoft.aad.directory/servicePrincipals/basic/update | 更新 Azure Active Directory 中 servicePrincipals 的基本属性。 |
| microsoft.aad.directory/servicePrincipals/create | 在 Azure Active Directory 中创建 servicePrincipals。 |
| microsoft.aad.directory/servicePrincipals/credentials/update | 更新 Azure Active Directory 中的 servicePrincipals.credentials 属性。 |
| microsoft.aad.directory/servicePrincipals/delete | 删除 Azure Active Directory 中的 servicePrincipals。 |
| microsoft.aad.directory/servicePrincipals/owners/update | 更新 Azure Active Directory 中的 servicePrincipals.owners 属性。 |
| microsoft.aad.directory/servicePrincipals/permissions/update | 更新 Azure Active Directory 中的 servicePrincipals.permissions 属性。 |
| microsoft.aad.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 属性。 |
| microsoft.aad.directory/signInReports/allProperties/read | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="cloud-device-administrator"></a>云设备管理员
用于在 Azure AD 中管理设备的完全访问权限。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | 读取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 属性。 |
| microsoft.aad.directory/devices/delete | 删除 Azure Active Directory 中的设备。 |
| microsoft.aad.directory/devices/disable | 禁用 Azure Active Directory 中的设备。 |
| microsoft.aad.directory/devices/enable | 启用 Azure Active Directory 中的设备。 |
| microsoft.aad.directory/signInReports/allProperties/read | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |

### <a name="company-administrator"></a>公司管理员
可以管理 Azure AD 和使用 Azure AD 标识的 Microsoft 服务的所有方面。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | 创建和删除所有资源，然后读取并更新 microsoft.aad.cloudAppSecurity 中的标准属性。 |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | 创建和删除 administrativeUnits，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/applications/allProperties/allTasks | 创建和删除应用程序，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | 创建和删除 appRoleAssignments，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| microsoft.aad.directory/contacts/allProperties/allTasks | 创建和删除联系人，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/contracts/allProperties/allTasks | 创建和删除协定，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/devices/allProperties/allTasks | 创建和删除设备，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | 创建和删除 directoryRoles，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | 创建和删除 directoryRoleTemplates，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/domains/allProperties/allTasks | 创建和删除域，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/groups/allProperties/allTasks | 创建和删除组，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | 创建和删除 groupSettings，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | 创建和删除 groupSettingTemplates，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | 创建和删除 loginTenantBranding，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | 创建和删除 oAuth2PermissionGrants，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/organization/allProperties/allTasks | 创建和删除组织，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/policies/allProperties/allTasks | 创建和删除策略，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | 创建和删除 roleAssignments，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | 创建和删除 roleDefinitions，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | 创建和删除 scopedRoleMemberships，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/serviceAction/activateService | 可以在 Azure Active Directory 中执行 Activateservice 服务操作 |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | 可以在 Azure Active Directory 中执行 Disabledirectoryfeature 服务操作 |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | 可以在 Azure Active Directory 中执行 Enabledirectoryfeature 服务操作 |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | 可以在 Azure Active Directory 中执行 Getavailableextentionproperties 服务操作 |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | 创建和删除 servicePrincipals，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/signInReports/allProperties/read | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | 创建和删除 subscribedSkus，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directory/users/allProperties/allTasks | 创建和删除用户，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.aad.directorySync/allEntities/allTasks | 在 Azure AD Connect 中执行所有操作。 |
| microsoft.aad.identityProtection/allEntities/allTasks | 创建和删除所有资源，然后读取和更新 microsoft.aad.identityProtection 中的标准属性。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 读取 microsoft.aad.privilegedIdentityManagement 中的所有资源。 |
| microsoft.azure.advancedThreatProtection/allEntities/read | 读取 microsoft.azure.advancedThreatProtection 中的所有资源。 |
| microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 信息保护的各个方面。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.commerce.billing/allEntities/allTasks | 管理 Office 365 计费的各个方面。 |
| microsoft.intune/allEntities/allTasks | 管理 Intune 的各个方面。 |
| microsoft.office365.complianceManager/allEntities/allTasks | 管理 Office 365 合规性管理器的各个方面 |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | 管理桌面分析的各个方面。 |
| microsoft.office365.exchange/allEntities/allTasks | 管理 Exchange Online 的各个方面。 |
| microsoft.office365.lockbox/allEntities/allTasks | 管理 Office 365 客户密码箱的各个方面 |
| microsoft.office365.messageCenter/messages/read | 读取 microsoft.office365.messageCenter 中的消息。 |
| microsoft.office365.messageCenter/securityMessages/read | 读取 microsoft.office365.messageCenter 中的安全消息。 |
| microsoft.office365.protectionCenter/allEntities/allTasks | 管理 Office 365 防护中心的各个方面。 |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | 创建和删除所有资源，然后读取和更新 microsoft.office365.securityComplianceCenter 中的标准属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.sharepoint/allEntities/allTasks | 创建和删除所有资源，然后读取和更新 microsoft.office365.sharepoint 中的标准属性。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理 Skype for Business Online 的各个方面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |
| microsoft.office365.usageReports/allEntities/read | 阅读 Office 365 使用情况报告。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.powerApps.dynamics365/allEntities/allTasks | 管理 Dynamics 365 的各个方面。 |
| microsoft.powerApps.powerBI/allEntities/allTasks | 管理 Power BI 的各个方面。 |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | 读取 microsoft.windows.defenderAdvancedThreatProtection 中的所有资源。 |

### <a name="compliance-administrator"></a>符合性管理员
可以读取和管理 Azure AD 和 Office 365 中的符合性配置和报表。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.complianceManager/allEntities/allTasks | 管理 Office 365 合规性管理器的各个方面 |
| microsoft.office365.exchange/allEntities/allTasks | 管理 Exchange Online 的各个方面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.sharepoint/allEntities/allTasks | 创建和删除所有资源，然后读取和更新 microsoft.office365.sharepoint 中的标准属性。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理 Skype for Business Online 的各个方面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="conditional-access-administrator"></a>条件访问管理员
可以管理条件访问功能。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | 读取 Azure Active Directory 中的 policies.conditionalAccess 属性。 |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | 更新 Azure Active Directory 中的 policies.conditionalAccess 属性。 |
| microsoft.aad.directory/policies/conditionalAccess/create | 在 Azure Active Directory 中创建策略。 |
| microsoft.aad.directory/policies/conditionalAccess/delete | 删除 Azure Active Directory 中的策略。 |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | 读取 Azure Active Directory 中的 policies.conditionalAccess 属性。 |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | 更新 Azure Active Directory 中的 policies.conditionalAccess 属性。 |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | 读取 Azure Active Directory 中的 policies.conditionalAccess 属性。 |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | 更新 Azure Active Directory 中的 policies.conditionalAccess 属性。 |

### <a name="crm-service-administrator"></a>CRM 服务管理员
可以管理 Dynamics 365 产品的所有方面。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.powerApps.dynamics365/allEntities/allTasks | 管理 Dynamics 365 的各个方面。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="customer-lockbox-access-approver"></a>客户密码箱访问审批者
可以批准 Microsoft 支持人员访问客户组织数据的请求。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.lockbox/allEntities/allTasks | 管理 Office 365 客户密码箱的各个方面 |

### <a name="device-administrators"></a>设备管理员
分配给此角色的用户添加到 Azure AD 加入设备上的本地管理员组。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | 读取 Azure Active Directory 中 groupSettings 的基本属性。 |
| microsoft.aad.directory/groupSettingTemplates/basic/read | 读取 Azure Active Directory 中 groupSettingTemplates 的基本属性。 |

### <a name="directory-readers"></a>目录读者
可以读取基本目录信息。 用于授予对应用程序的访问权限，不针对用户。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | 读取 Azure Active Directory 中 administrativeUnits 的基本属性。 |
| microsoft.aad.directory/administrativeUnits/members/read | 读取 Azure Active Directory 中的 administrativeUnits.members 属性。 |
| microsoft.aad.directory/applications/basic/read | 读取 Azure Active Directory 中应用程序的基本属性。 |
| microsoft.aad.directory/applications/owners/read | 读取 Azure Active Directory 中的 applications.owners 属性。 |
| microsoft.aad.directory/applications/policies/read | 读取 Azure Active Directory 中的 applications.policies 属性。 |
| microsoft.aad.directory/contacts/basic/read | 读取 Azure Active Directory 中联系人的基本属性。 |
| microsoft.aad.directory/contacts/memberOf/read | 读取 Azure Active Directory 中的 contacts.memberOf 属性。 |
| microsoft.aad.directory/contracts/basic/read | 读取 Azure Active Directory 中协定的基本属性。 |
| microsoft.aad.directory/devices/basic/read | 读取 Azure Active Directory 中设备的基本属性。 |
| microsoft.aad.directory/devices/memberOf/read | 读取 Azure Active Directory 中的 devices.memberOf 属性。 |
| microsoft.aad.directory/devices/registeredOwners/read | 读取 Azure Active Directory 中的 devices.registeredOwners 属性。 |
| microsoft.aad.directory/devices/registeredUsers/read | 读取 Azure Active Directory 中的 devices.registeredUsers 属性。 |
| microsoft.aad.directory/directoryRoles/basic/read | 读取 Azure Active Directory 中 directoryRoles 的基本属性。 |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | 读取 Azure Active Directory 中的 directoryRoles.eligibleMembers 属性。 |
| microsoft.aad.directory/directoryRoles/members/read | 读取 Azure Active Directory 中的 directoryRoles.members 属性。 |
| microsoft.aad.directory/domains/basic/read | 读取 Azure Active Directory 中域的基本属性。 |
| microsoft.aad.directory/groups/appRoleAssignments/read | 读取 Azure Active Directory 中的 groups.appRoleAssignments 属性。 |
| microsoft.aad.directory/groups/basic/read | 读取 Azure Active Directory 中组的基本属性。 |
| microsoft.aad.directory/groups/memberOf/read | 读取 Azure Active Directory 中的 groups.memberOf 属性。 |
| microsoft.aad.directory/groups/members/read | 读取 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.aad.directory/groups/owners/read | 读取 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft.aad.directory/groups/settings/read | 读取 Azure Active Directory 中的 groups.settings 属性。 |
| microsoft.aad.directory/groupSettings/basic/read | 读取 Azure Active Directory 中 groupSettings 的基本属性。 |
| microsoft.aad.directory/groupSettingTemplates/basic/read | 读取 Azure Active Directory 中 groupSettingTemplates 的基本属性。 |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | 读取 Azure Active Directory 中 oAuth2PermissionGrants 的基本属性。 |
| microsoft.aad.directory/organization/basic/read | 读取 Azure Active Directory 中组织的基本属性。 |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | 读取 Azure Active Directory 中的 organization.trustedCAsForPasswordlessAuth 属性。 |
| microsoft.aad.directory/roleAssignments/basic/read | 读取 Azure Active Directory 中 roleAssignments 上的基本属性。 |
| microsoft.aad.directory/roleDefinitions/basic/read | 读取 Azure Active Directory 中 roleDefinitions 上的基本属性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | 读取 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 属性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | 读取 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 属性。 |
| microsoft.aad.directory/servicePrincipals/basic/read | 读取 Azure Active Directory 中 servicePrincipals 的基本属性。 |
| microsoft.aad.directory/servicePrincipals/memberOf/read | 读取 Azure Active Directory 中的 servicePrincipals.memberOf 属性。 |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | 读取 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 属性。 |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | 读取 Azure Active Directory 中的 servicePrincipals.ownedObjects 属性。 |
| microsoft.aad.directory/servicePrincipals/owners/read | 读取 Azure Active Directory 中的 servicePrincipals.owners 属性。 |
| microsoft.aad.directory/servicePrincipals/policies/read | 读取 Azure Active Directory 中的 servicePrincipals.policies 属性。 |
| microsoft.aad.directory/subscribedSkus/basic/read | 读取 Azure Active Directory 中 subscribedSkus 的基本属性。 |
| microsoft.aad.directory/users/appRoleAssignments/read | 读取 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft.aad.directory/users/basic/read | 读取 Azure Active Directory 中用户的基本属性。 |
| microsoft.aad.directory/users/directReports/read | 读取 Azure Active Directory 中的 users.directReports 属性。 |
| microsoft.aad.directory/users/manager/read | 读取 Azure Active Directory 中的 users.manager 属性。 |
| microsoft.aad.directory/users/memberOf/read | 读取 Azure Active Directory 中的 users.memberOf 属性。 |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | 读取 Azure Active Directory 中的 users.oAuth2PermissionGrants 属性。 |
| microsoft.aad.directory/users/ownedDevices/read | 读取 Azure Active Directory 中的 users.ownedDevices 属性。 |
| microsoft.aad.directory/users/ownedObjects/read | 读取 Azure Active Directory 中的 users.ownedObjects 属性。 |
| microsoft.aad.directory/users/registeredDevices/read | 读取 Azure Active Directory 中的 users.registeredDevices 属性。 |

### <a name="directory-synchronization-accounts"></a>目录同步帐户
仅供 Azure AD Connect 服务使用。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | 更新 Azure Active Directory 中的 organization.dirSync 属性。 |
| microsoft.aad.directory/policies/create | 在 Azure Active Directory 中创建策略。 |
| microsoft.aad.directory/policies/delete | 删除 Azure Active Directory 中的策略。 |
| microsoft.aad.directory/policies/basic/read | 读取 Azure Active Directory 中策略的基本属性。 |
| microsoft.aad.directory/policies/basic/update | 更新 Azure Active Directory 中策略的基本属性。 |
| microsoft.aad.directory/policies/owners/read | 读取 Azure Active Directory 中的 policies.owners 属性。 |
| microsoft.aad.directory/policies/owners/update | 更新 Azure Active Directory 中的 policies.owners 属性。 |
| microsoft.aad.directory/policies/policiesAppliedTo/read | 读取 Azure Active Directory 中的 policies.policiesAppliedTo 属性。 |
| microsoft.aad.directory/policies/tenantDefault/update | 更新 Azure Active Directory 中的 policies.tenantDefault 属性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | 读取 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 属性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 属性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | 读取 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 属性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 属性。 |
| microsoft.aad.directory/servicePrincipals/audience/update | 更新 Azure Active Directory 中的 servicePrincipals.audience 属性。 |
| microsoft.aad.directory/servicePrincipals/authentication/update | 更新 Azure Active Directory 中的 servicePrincipals.authentication 属性。 |
| microsoft.aad.directory/servicePrincipals/basic/read | 读取 Azure Active Directory 中 servicePrincipals 的基本属性。 |
| microsoft.aad.directory/servicePrincipals/basic/update | 更新 Azure Active Directory 中 servicePrincipals 的基本属性。 |
| microsoft.aad.directory/servicePrincipals/create | 在 Azure Active Directory 中创建 servicePrincipals。 |
| microsoft.aad.directory/servicePrincipals/credentials/update | 更新 Azure Active Directory 中的 servicePrincipals.credentials 属性。 |
| microsoft.aad.directory/servicePrincipals/memberOf/read | 读取 Azure Active Directory 中的 servicePrincipals.memberOf 属性。 |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | 读取 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 属性。 |
| microsoft.aad.directory/servicePrincipals/owners/read | 读取 Azure Active Directory 中的 servicePrincipals.owners 属性。 |
| microsoft.aad.directory/servicePrincipals/owners/update | 更新 Azure Active Directory 中的 servicePrincipals.owners 属性。 |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | 读取 Azure Active Directory 中的 servicePrincipals.ownedObjects 属性。 |
| microsoft.aad.directory/servicePrincipals/permissions/update | 更新 Azure Active Directory 中的 servicePrincipals.permissions 属性。 |
| microsoft.aad.directory/servicePrincipals/policies/read | 读取 Azure Active Directory 中的 servicePrincipals.policies 属性。 |
| microsoft.aad.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 属性。 |
| microsoft.aad.directorySync/allEntities/allTasks | 在 Azure AD Connect 中执行所有操作。 |

### <a name="directory-writers"></a>目录编写人员
可以读取和写入基本目录信息。 用于授予对应用程序的访问权限，不针对用户。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/groups/create | 在 Azure Active Directory 中创建组。 |
| microsoft.aad.directory/groups/createAsOwner | 在 Azure Active Directory 中创建组。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.aad.directory/groups/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.appRoleAssignments 属性。 |
| microsoft.aad.directory/groups/basic/update | 更新 Azure Active Directory 中组的基本属性。 |
| microsoft.aad.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.aad.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft.aad.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 属性。 |
| microsoft.aad.directory/groupSettings/basic/update | 更新 Azure Active Directory 中 groupSettings 的基本属性。 |
| microsoft.aad.directory/groupSettings/create | 在 Azure Active Directory 中创建 groupSettings 属性。 |
| microsoft.aad.directory/groupSettings/delete | 删除 Azure Active Directory 中的 groupSettings。 |
| microsoft.aad.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft.aad.directory/users/assignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft.aad.directory/users/basic/update | 更新 Azure Active Directory 中用户的基本属性。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft.aad.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 属性。 |
| microsoft.aad.directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 属性。 |

### <a name="exchange-service-administrator"></a>Exchange 服务管理员
可以管理 Exchange 产品的所有方面。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.unified 属性。 |
| microsoft.aad.directory/groups/unified/basic/update | 更新 Office 365 组的基本属性。 |
| microsoft.aad.directory/groups/unified/create | 创建 Office 365 组。 |
| microsoft.aad.directory/groups/unified/delete | 删除 Office 365 组。 |
| microsoft.aad.directory/groups/unified/members/update | 更新 Office 365 组的成员身份。 |
| microsoft.aad.directory/groups/unified/owners/update | 更新 Office 365 组的所有权。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.exchange/allEntities/allTasks | 管理 Exchange Online 的各个方面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="guest-inviter"></a>来宾邀请者
可以邀请与“成员可邀请来宾”设置无关的来宾用户。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | 读取 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft.aad.directory/users/basic/read | 读取 Azure Active Directory 中用户的基本属性。 |
| microsoft.aad.directory/users/directReports/read | 读取 Azure Active Directory 中的 users.directReports 属性。 |
| microsoft.aad.directory/users/inviteGuest | 邀请 Azure Active Directory 中的来宾用户。 |
| microsoft.aad.directory/users/manager/read | 读取 Azure Active Directory 中的 users.manager 属性。 |
| microsoft.aad.directory/users/memberOf/read | 读取 Azure Active Directory 中的 users.memberOf 属性。 |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | 读取 Azure Active Directory 中的 users.oAuth2PermissionGrants 属性。 |
| microsoft.aad.directory/users/ownedDevices/read | 读取 Azure Active Directory 中的 users.ownedDevices 属性。 |
| microsoft.aad.directory/users/ownedObjects/read | 读取 Azure Active Directory 中的 users.ownedObjects 属性。 |
| microsoft.aad.directory/users/registeredDevices/read | 读取 Azure Active Directory 中的 users.registeredDevices 属性。 |

### <a name="helpdesk-administrator"></a>支持管理员
可以重置非管理员和支持理员的密码。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | 读取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 属性。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft.aad.directory/users/password/update | 更新 Azure Active Directory 中所有用户的密码。 有关详细信息，请参阅联机文档。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="information-protection-administrator"></a>信息保护管理员
可以管理 Azure 信息保护产品的所有方面。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 信息保护的各个方面。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="intune-service-administrator"></a>Intune 服务管理员
可以管理 Intune 产品的所有方面。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | 更新 Azure Active Directory 中联系人的基本属性。 |
| microsoft.aad.directory/contacts/create | 在 Azure Active Directory 中创建联系人。 |
| microsoft.aad.directory/contacts/delete | 删除 Azure Active Directory 中的联系人。 |
| microsoft.aad.directory/devices/basic/update | 更新 Azure Active Directory 中设备的基本属性。 |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | 读取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 属性。 |
| microsoft.aad.directory/devices/create | 在 Azure Active Directory 中创建设备。 |
| microsoft.aad.directory/devices/delete | 删除 Azure Active Directory 中的设备。 |
| microsoft.aad.directory/devices/registeredOwners/update | 更新 Azure Active Directory 中的 devices.registeredOwners 属性。 |
| microsoft.aad.directory/devices/registeredUsers/update | 更新 Azure Active Directory 中的 devices.registeredUsers 属性。 |
| microsoft.aad.directory/groups/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.appRoleAssignments 属性。 |
| microsoft.aad.directory/groups/basic/update | 更新 Azure Active Directory 中组的基本属性。 |
| microsoft.aad.directory/groups/create | 在 Azure Active Directory 中创建组。 |
| microsoft.aad.directory/groups/createAsOwner | 在 Azure Active Directory 中创建组。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.aad.directory/groups/delete | 删除 Azure Active Directory 中的组。 |
| microsoft.aad.directory/groups/hiddenMembers/read | 读取 Azure Active Directory 中的 groups.hiddenMembers 属性。 |
| microsoft.aad.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.aad.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft.aad.directory/groups/restore | 还原 Azure Active Directory 中的组。 |
| microsoft.aad.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 属性。 |
| microsoft.aad.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft.aad.directory/users/basic/update | 更新 Azure Active Directory 中用户的基本属性。 |
| microsoft.aad.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 属性。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.intune/allEntities/allTasks | 管理 Intune 的各个方面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |

### <a name="license-administrator"></a>许可证管理员
可以管理用户和组的产品许可证。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft.aad.directory/users/usageLocation/update | 更新 Azure Active Directory 中的 users.usageLocation 属性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |

### <a name="lync-service-administrator"></a>Lync 服务管理员
可以管理 Skype for Business 产品的所有方面。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理 Skype for Business Online 的各个方面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="message-center-reader"></a>消息中心读取者
只能在 Office 365 消息中心查看其组织的消息和更新。 

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.messageCenter/messages/read | 读取 microsoft.office365.messageCenter 中的消息。 |

### <a name="partner-tier1-support"></a>合作伙伴一线支持人员
不要使用 - 不适用于常规用途。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | 更新 Azure Active Directory 中联系人的基本属性。 |
| microsoft.aad.directory/contacts/create | 在 Azure Active Directory 中创建联系人。 |
| microsoft.aad.directory/contacts/delete | 删除 Azure Active Directory 中的联系人。 |
| microsoft.aad.directory/groups/create | 在 Azure Active Directory 中创建组。 |
| microsoft.aad.directory/groups/createAsOwner | 在 Azure Active Directory 中创建组。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.aad.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.aad.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft.aad.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft.aad.directory/users/assignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft.aad.directory/users/basic/update | 更新 Azure Active Directory 中用户的基本属性。 |
| microsoft.aad.directory/users/delete | 删除 Azure Active Directory 中的用户。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft.aad.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 属性。 |
| microsoft.aad.directory/users/password/update | 更新 Azure Active Directory 中所有用户的密码。 有关详细信息，请参阅联机文档。 |
| microsoft.aad.directory/users/restore | 还原 Azure Active Directory 中已删除的用户。 |
| microsoft.aad.directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 属性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="partner-tier2-support"></a>合作伙伴二线支持人员
不要使用 - 不适用于常规用途。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | 更新 Azure Active Directory 中联系人的基本属性。 |
| microsoft.aad.directory/contacts/create | 在 Azure Active Directory 中创建联系人。 |
| microsoft.aad.directory/contacts/delete | 删除 Azure Active Directory 中的联系人。 |
| microsoft.aad.directory/domains/allTasks | 创建和删除域，然后读取和更新 Azure Active Directory 中的标准属性。 |
| microsoft.aad.directory/groups/create | 在 Azure Active Directory 中创建组。 |
| microsoft.aad.directory/groups/delete | 删除 Azure Active Directory 中的组。 |
| microsoft.aad.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.aad.directory/groups/restore | 还原 Azure Active Directory 中的组。 |
| microsoft.aad.directory/organization/basic/update | 更新 Azure Active Directory 中组织的基本属性。 |
| microsoft.aad.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft.aad.directory/users/assignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft.aad.directory/users/basic/update | 更新 Azure Active Directory 中用户的基本属性。 |
| microsoft.aad.directory/users/delete | 删除 Azure Active Directory 中的用户。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft.aad.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 属性。 |
| microsoft.aad.directory/users/password/update | 更新 Azure Active Directory 中所有用户的密码。 有关详细信息，请参阅联机文档。 |
| microsoft.aad.directory/users/restore | 还原 Azure Active Directory 中已删除的用户。 |
| microsoft.aad.directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 属性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="power-bi-service-administrator"></a>Power BI 服务管理员
可以管理 Power BI 产品的所有方面。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.powerApps.powerBI/allEntities/allTasks | 管理 Power BI 的各个方面。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="privileged-authentication-administrator"></a>特权身份验证管理员
允许查看、设置和重置任何用户(管理员或非管理员)的身份验证方法信息。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft.aad.directory/users/strongAuthentication/update | 更新强身份验证属性，如 MFA 凭据信息。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="privileged-role-administrator"></a>特权角色管理员
可以管理 Azure AD 中的角色分配和 Privileged Identity Management 的所有方面。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/directoryRoles/update | 更新 Azure Active Directory 中的 directoryRoles。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | 创建和删除所有资源，然后读取和更新 microsoft.aad.privilegedIdentityManagement 中的标准属性。 |

### <a name="reports-reader"></a>报告读者
可以读取登录和审核报告。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| microsoft.aad.directory/signInReports/allProperties/read | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.usageReports/allEntities/read | 阅读 Office 365 使用情况报告。 |

### <a name="security-administrator"></a>安全管理员
可以读取安全信息和报表，以及管理 Azure AD 和 Office 365 中的配置。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | 更新 Azure Active Directory 中的 applications.policies 属性。 |
| microsoft.aad.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | 读取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 属性。 |
| microsoft.aad.directory/policies/basic/update | 更新 Azure Active Directory 中策略的基本属性。 |
| microsoft.aad.directory/policies/create | 在 Azure Active Directory 中创建策略。 |
| microsoft.aad.directory/policies/delete | 删除 Azure Active Directory 中的策略。 |
| microsoft.aad.directory/policies/owners/update | 更新 Azure Active Directory 中的 policies.owners 属性。 |
| microsoft.aad.directory/policies/tenantDefault/update | 更新 Azure Active Directory 中的 policies.tenantDefault 属性。 |
| microsoft.aad.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 属性。 |
| microsoft.aad.directory/signInReports/allProperties/read | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |
| microsoft.aad.identityProtection/allEntities/read | 读取 microsoft.aad.identityProtection 中的所有资源。 |
| microsoft.aad.identityProtection/allEntities/update | 更新 microsoft.aad.identityProtection 中的所有资源。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 读取 microsoft.aad.privilegedIdentityManagement 中的所有资源。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.protectionCenter/allEntities/read | 读取 Office 365 防护中心的各个方面。 |
| microsoft.office365.protectionCenter/allEntities/update | 更新 microsoft.office365.protectionCenter 中的所有资源。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |

### <a name="security-reader"></a>安全读取者
可以读取 Azure AD 和 Office 365 中的安全信息和报表。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | 读取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 属性。 |
| microsoft.aad.directory/signInReports/allProperties/read | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |
| microsoft.aad.identityProtection/allEntities/read | 读取 microsoft.aad.identityProtection 中的所有资源。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 读取 microsoft.aad.privilegedIdentityManagement 中的所有资源。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.protectionCenter/allEntities/read | 读取 Office 365 防护中心的各个方面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |

### <a name="service-support-administrator"></a>服务支持管理员
可以读取服务运行状况信息和管理支持票证。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="sharepoint-service-administrator"></a>SharePoint 服务管理员
可以管理 SharePoint 服务的所有方面。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.unified 属性。 |
| microsoft.aad.directory/groups/unified/basic/update | 更新 Office 365 组的基本属性。 |
| microsoft.aad.directory/groups/unified/create | 创建 Office 365 组。 |
| microsoft.aad.directory/groups/unified/delete | 删除 Office 365 组。 |
| microsoft.aad.directory/groups/unified/members/update | 更新 Office 365 组的成员身份。 |
| microsoft.aad.directory/groups/unified/owners/update | 更新 Office 365 组的所有权。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.sharepoint/allEntities/allTasks | 创建和删除所有资源，然后读取和更新 microsoft.office365.sharepoint 中的标准属性。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="teams-communications-administrator"></a>Teams 通信管理员
可以管理 Microsoft Teams 服务中的通话和会议功能。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |
| microsoft.office365.usageReports/allEntities/read | 阅读 Office 365 使用情况报告。 |

### <a name="teams-communications-support-engineer"></a>Teams 通信支持工程师
可以使用高级工具排查 Teams 中的通信问题。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |

### <a name="teams-communications-support-specialist"></a>Teams 通信支持专家
可以使用基本工具排查 Teams 中的通信问题。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |

### <a name="teams-service-administrator"></a>Teams 服务管理员
可以管理 Microsoft Teams 服务。

  > [!NOTE]
  > 此角色拥有 Azure Active Directory 之外的其他权限。 有关详细信息，请参阅上面的角色说明。
  >
  >

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | 读取 Azure Active Directory 中的 groups.hiddenMembers 属性。 |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.unified 属性。 |
| microsoft.aad.directory/groups/unified/basic/update | 更新 Office 365 组的基本属性。 |
| microsoft.aad.directory/groups/unified/create | 创建 Office 365 组。 |
| microsoft.aad.directory/groups/unified/delete | 删除 Office 365 组。 |
| microsoft.aad.directory/groups/unified/members/update | 更新 Office 365 组的成员身份。 |
| microsoft.aad.directory/groups/unified/owners/update | 更新 Office 365 组的所有权。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |
| microsoft.office365.usageReports/allEntities/read | 阅读 Office 365 使用情况报告。 |

### <a name="user-administrator"></a>用户管理员
可以管理用户和组的所有方面，包括重置有限管理员的密码。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | 在 Azure Active Directory 中创建 appRoleAssignments。 |
| microsoft.aad.directory/appRoleAssignments/delete | 删除 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.aad.directory/appRoleAssignments/update | 更新 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.aad.directory/contacts/basic/update | 更新 Azure Active Directory 中联系人的基本属性。 |
| microsoft.aad.directory/contacts/create | 在 Azure Active Directory 中创建联系人。 |
| microsoft.aad.directory/contacts/delete | 删除 Azure Active Directory 中的联系人。 |
| microsoft.aad.directory/groups/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.appRoleAssignments 属性。 |
| microsoft.aad.directory/groups/basic/update | 更新 Azure Active Directory 中组的基本属性。 |
| microsoft.aad.directory/groups/create | 在 Azure Active Directory 中创建组。 |
| microsoft.aad.directory/groups/createAsOwner | 在 Azure Active Directory 中创建组。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.aad.directory/groups/delete | 删除 Azure Active Directory 中的组。 |
| microsoft.aad.directory/groups/hiddenMembers/read | 读取 Azure Active Directory 中的 groups.hiddenMembers 属性。 |
| microsoft.aad.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.aad.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft.aad.directory/groups/restore | 还原 Azure Active Directory 中的组。 |
| microsoft.aad.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 属性。 |
| microsoft.aad.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft.aad.directory/users/assignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft.aad.directory/users/basic/update | 更新 Azure Active Directory 中用户的基本属性。 |
| microsoft.aad.directory/users/create | 在 Azure Active Directory 中创建用户。 |
| microsoft.aad.directory/users/delete | 删除 Azure Active Directory 中的用户。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft.aad.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 属性。 |
| microsoft.aad.directory/users/password/update | 更新 Azure Active Directory 中所有用户的密码。 有关详细信息，请参阅联机文档。 |
| microsoft.aad.directory/users/restore | 还原 Azure Active Directory 中已删除的用户。 |
| microsoft.aad.directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 属性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

## <a name="role-template-ids"></a>角色模板 Id

主要 Graph API 或 PowerShell 用户使用角色模板 Id。

关系图显示名称 | Azure 门户显示名称 | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
应用程序管理员 | 应用程序管理员 | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
应用程序开发人员 | 应用程序开发人员 | CF1C38E5-3621-4004-A7CB-879624DCED7C
计费管理员 | 计费管理员 | b0f54661-2d74-4c50-afa3-1ec803f12efe
桌面分析管理员 | 桌面分析管理员 | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
云应用管理员 | 云应用程序管理员 | 158c047a-c907-4556-b7ef-446551a6b5f7
云设备管理员 | 云设备管理员 | 7698a772-787b-4ac8-901f-60d6b08affd2
公司管理员 | 全局管理员 | 62e90394-69f5-4237-9190-012177145e10
符合性管理员 | 法规管理员 | 17315797-102d-40b4-93e0-432062caca18
条件访问管理员 | 条件访问管理员 | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
CRM 服务管理员 | Dynamics 365 管理员 | 44367163-eba1-44c3-98af-f5787879f96a
客户密码箱访问审批者 | 客户密码箱访问审批者 | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
设备管理员 | 设备管理员 | 9f06204d-73c1-4d4c-880a-6edb90606fd8
设备联接 | 设备联接 | 9c094953-4995-41c8-84c8-3ebb9b32c93f
设备管理器 | 设备管理器 | 2b499bcd-da44-4968-8aec-78e1674fa64d
设备用户 | 设备用户 | d405c6df-0af8-4e3b-95e4-4d06e542189e
目录读者 | 目录读者 | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
目录同步帐户 | 目录同步帐户 | d29b2b05-8046-44ba-8758-1e26182fcf32
目录编写人员 | 目录写入者 | 9360feb5-f418-4baa-8175-e2a00bac4301
Exchange 服务管理员 | Exchange 管理员 | 29232cdf-9323-42fd-ade2-1d097af3e4de
来宾邀请者 | 来宾邀请者 | 95e79109-95c0-4d8e-aee3-d01accf2d47b
支持管理员 | 密码管理员 | 729827e3-9c14-49f7-bb1b-9608f156bbb8
信息保护管理员 | 信息保护管理员 | 7495fdc4-34c4-4d15-a289-98788ce399fd
Intune 服务管理员 | Intune 管理员 | 3a2c62db-5318-420d-8d74-23affee5d9d5
许可证管理员 | 许可证管理员 | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Lync 服务管理员 | Skype for Business 管理员 | 75941009-915a-4869-abe7-691bff18279e
消息中心读取者 | 消息中心读者 | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
合作伙伴一线支持人员 | 合作伙伴一线支持人员 | 4ba39ca4-527c-499a-b93d-d9b492c50246
合作伙伴二线支持人员 | 合作伙伴二线支持人员 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Power BI 服务管理员 | Power BI 管理员 | a9ea8996-122f-4c74-9520-8edcd192826c
特权角色管理员 | 特权角色管理员 | e8611ab8-c189-46e8-94e1-60213ab1f814
报告读者 | 报告读者 | 4a5d8f65-41da-4de4-8968-e035b65339cf
安全管理员 | 安全管理员 | 194ae4cb-b126-40b2-bd5b-6091b380977d
安全读取者 | 安全读者 | 5d6b6bb7-de71-4623-b4af-96380a352509
服务支持管理员 | 服务管理员 | f023fd81-a637-4b56-95fd-791ac0226033
SharePoint 服务管理员 | Sharepoint 管理员 | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Teams 通信管理员 | Teams 通信管理员 | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Teams 通信支持工程师 | Teams 通信支持工程师 | f70938a0-fc10-4177-9e90-2178f8765737
Teams 通信支持专家 | Teams 通信支持专家 | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Teams 服务管理员 | Teams 服务管理员 | 69091246-20e8-4a56-aa4d-066075b2a7a8
用户 | 用户 | a0b1b346-4d3e-4e8b-98f8-753987be4970
用户帐户管理员 | 用户管理员 | fe930be7-5e62-47db-91af-98c3a49a38b1
工作区设备联接 | 工作区设备联接 | c34f683f-4d5a-4403-affd-6615e00e3a7f


## <a name="deprecated-roles"></a>已弃用的角色

不应使用以下角色。 这些角色已弃用，并将从 Azure AD 中删除。

* 即席许可证管理员
* 设备联接
* 设备管理器
* 设备用户
* 经电子邮件验证的用户创建者
* 邮箱管理员
* 工作区设备联接

## <a name="next-steps"></a>后续步骤

* 若要详细了解如何将用户分配为 Azure 订阅的管理员，请参阅[使用 RBAC 和 Azure 门户管理访问权限](../../role-based-access-control/role-assignments-portal.md)
* 若要了解有关如何在 Microsoft Azure 中控制资源访问的详细信息，请参阅 [了解 Azure 中的资源访问权限](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* 有关 Azure Active Directory 如何与 Azure 订阅相关联的详细信息，请参阅 [How Azure subscriptions are associated with Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)（Azure 订阅与 Azure Active Directory 的关联方式）
