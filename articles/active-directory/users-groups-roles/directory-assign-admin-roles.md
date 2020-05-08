---
title: 管理员角色说明和权限 - Azure AD | Microsoft Docs
description: 管理员角色可以添加用户、分配管理角色、重置用户密码、管理用户许可证，或者管理域。
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c16ba26d1c59bd932e657f41882fbd23dc3a69e
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592224"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Azure Active Directory 中的管理员角色权限

使用 Azure Active Directory (Azure AD) 时，可以指定有限的管理员以权限较低的角色来管理标识任务。 可以分配管理员来执行各种任务，例如，添加或更改用户、分配管理角色、重置用户密码、管理用户许可证，以及管理域名。 只能在 Azure AD 中的用户设置中更改[默认用户权限](../fundamentals/users-default-permissions.md)。

## <a name="limit-use-of-global-administrator"></a>限制使用全局管理员

已分配到全局管理员角色的用户可以读取和修改 Azure AD 组织中的每项管理设置。 默认情况下，系统会将注册 Azure 订阅的人员分配为 Azure AD 组织的全局管理员角色。 只有全局管理员和特权角色管理员可以委托管理员角色。 为了降低业务风险，我们建议仅将此角色分配给组织中尽量少的人员。

建议将此角色分配给组织中五个以下的人员，这是最佳做法。 如果已将“全局管理员”角色分配给组织中五个以上的管理员，可通过以下方法减少该角色的使用。

### <a name="find-the-role-you-need"></a>找到所需的角色

如果你很难从包含众多角色的列表中找到所需的角色，Azure AD 可以根据角色类别显示角色的子集。 请查看适用于 [Azure AD 角色和管理员](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)的“类型”筛选器，使用它可以仅显示所选类型的角色。 

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>现有的某个角色在之前分配全局管理员角色时并不存在

有可能已将一个或多个角色添加到 Azure AD 中以提供更精细的权限，但在将某些用户提升为全局管理员时，这些角色并未作为一个选项列出。 随着时间的推移，我们会不断推出更多的角色来完成以前只有全局管理员角色才能执行的任务。 以下[可用的角色](#available-roles)中对此做了介绍。

## <a name="assign-or-remove-administrator-roles"></a>分配或删除管理员角色

若要了解如何在 Azure Active Directory 中向用户分配管理角色，请参阅[在 Azure Active Directory 中查看和分配管理员角色](directory-manage-roles-portal.md)。

## <a name="available-roles"></a>可用的角色

提供以下管理员角色：

### <a name="application-administrator"></a>[应用程序管理员](#application-administrator-permissions)

充当此角色的用户可以创建和管理企业应用程序、应用程序注册和应用程序代理设置的所有方面。 请注意，在创建新应用程序注册或企业应用程序时，不会将分配到此角色的用户添加为所有者。

应用程序管理员可以管理用于模拟应用程序的应用程序凭据。 因此，分配了此角色的用户只能管理那些尚未分配给任何 Azure AD 角色或仅分配给以下管理员角色的应用程序的应用程序凭据：

* 应用程序管理员
* 应用程序开发人员
* 云应用管理员
* 目录读者

如果应用程序分配给上面未提到的任何其他角色，则应用程序管理员不能管理该应用程序的凭据。

此角色还可以许可委托的权限和应用程序权限，但对 Microsoft Graph API 的权限除外。 

> [!IMPORTANT]
> 此例外表示你仍同意_其他_应用（例如，已注册的非 Microsoft 应用或应用）的权限，但不允许对 Azure AD 本身的权限。 你仍可以在应用注册过程中_请求_这些权限，但_授予_（即同意）这些权限需要 Azure AD 管理员。这意味着恶意用户不能轻松地提升其权限，例如，创建并同意可以写入整个目录的应用程序，并通过该应用程序的权限提升自身，使其成为全局管理员。

### <a name="application-developer"></a>[应用程序开发人员](#application-developer-permissions)

在将设置“用户可以注册应用程序”设置为“否”时，充当此角色的用户可以创建应用程序注册。 当“用户可以同意应用代表他们访问公司数据”设置设为“否”时，此角色还能够代表自己授权同意。 在创建新应用程序注册或企业应用程序时，会将分配到此角色的用户添加为所有者。

### <a name="authentication-administrator"></a>[身份验证管理员](#authentication-administrator-permissions)

具有此角色的用户可以为某些用户设置或重置非密码凭据，并可以更新所有用户的密码。 身份验证管理员可以要求非管理员用户或分配给某些角色的用户针对现有的非密码凭据（例如 MFA 或 FIDO）重新注册，还可以撤消设备上的**记住 mfa**，这会在下一次登录时提示进行 mfa。 这些操作仅适用于非管理员用户或分配了以下一个或多个角色的用户：

* 身份验证管理员
* 目录读者
* 来宾邀请者
* 消息中心读取者
* 报告读者

[特权身份验证管理员](#privileged-authentication-administrator)角色有权为所有用户强制重新注册和多重身份验证。

> [!IMPORTANT]
> 具有此角色的用户可以更改可能有权访问 Azure Active Directory 内外敏感或私有信息或关键配置的用户的凭据。 更改用户的凭据可能意味着假定用户标识和权限的能力。 例如：
>
>- 应用程序注册和企业应用程序所有者，可以管理他们拥有的应用的凭据。 这些应用程序可能在 Azure AD 或其他位置拥有未授予身份验证管理员的特权。 通过此路径，身份验证管理员可能能够假定应用程序所有者的身份，然后通过更新应用程序的凭据来进一步假定特权应用程序的标识。
>- Azure 订阅所有者，可能对 Azure 中的敏感或私有信息或关键配置拥有访问权限。
>- 安全组和 Office 365 组所有者，可以管理组成员身份。 这些组可能会授予对 Azure AD 或其他位置敏感或私有信息或关键配置的访问权限。
>- Azure AD 之外的其他服务中的管理员，如 Exchange Online、Office 安全与合规中心以及人力资源系统。
>- 高级管理人员、法律顾问和人力资源员工之类的非管理员，可能有权访问敏感或私有信息。

### <a name="azure-devops-administrator"></a>[Azure DevOps 管理员](#azure-devops-administrator-permissions)

具有此角色的用户可以管理 Azure DevOps 策略，以便仅限一组可配置的用户或组创建新的 Azure DevOps 组织。 充当此角色的用户可以通过有公司 Azure AD 组织作为保障的 Azure DevOps 组织来管理此策略。

充当此角色的用户可以管理所有企业 Azure DevOps 策略。

### <a name="azure-information-protection-administrator"></a>[Azure 信息保护管理员](#azure-information-protection-administrator-permissions)

具有此角色的用户拥有 Azure 信息保护服务中的所有权限。 此角色可以配置 Azure 信息保护策略的标签、管理保护模板，以及激活保护。 此角色不会授予标识保护中心、Privileged Identity Management、监视 Office 365 服务运行状况或 Office 365 安全与合规中心的权限。

### <a name="b2c-user-flow-administrator"></a>[B2C 用户流管理员](#b2c-user-flow-administrator-permissions)

具有此角色的用户可以在 Azure 门户中创建和管理 B2C 用户流（也称为“内置”策略）。通过创建或编辑用户流，这些用户可以更改用户体验的 html/CSS/javascript 内容、更改每个用户流的 MFA 要求、更改令牌中的声明，以及调整 Azure AD 组织中的所有策略的会话设置。 另一方面，此角色不包括查看用户数据或对组织架构中包含的属性进行更改的功能。 对 Identity Experience Framework（也称为“自定义”）策略的更改超出了此角色的权限范围。

### <a name="b2c-user-flow-attribute-administrator"></a>[B2C 用户流属性管理员](#b2c-user-flow-attribute-administrator-permissions)

具有此角色的用户可以添加或删除 Azure AD 组织中的所有用户流的自定义属性。因此，具有此角色的用户可以更改或向最终用户架构中添加新元素并影响所有用户流的行为，并间接导致最终用户可能会询问哪些数据并最终将其作为声明发送到应用程序。 此角色无法编辑用户流。

### <a name="b2c-ief-keyset-administrator"></a>[B2C IEF 密钥集管理员](#b2c-ief-keyset-administrator-permissions)

用户可以创建和管理策略密钥与机密用于令牌加密、令牌签名以及声明加密/解密。 通过将新密钥添加到现有密钥容器，此受限管理员可以根据需要滚动更新机密，而不会影响现有的应用程序。 即使是在创建这些机密之后，此用户也可以查看这些机密的完整内容及其过期日期。

> [!IMPORTANT]
> 这是一个敏感角色。 在生产前与生产期间，应该谨慎地审核和分配密钥集管理员角色。

### <a name="b2c-ief-policy-administrator"></a>[B2C IEF 策略管理员](#b2c-ief-policy-administrator-permissions)

此角色中的用户可以创建、读取、更新和删除 Azure AD B2C 中的所有自定义策略，并因此对相关 Azure AD B2C 组织中的标识体验框架具有完全控制权限。 通过编辑策略，此用户可以直接与外部标识提供者建立联合、更改目录架构、更改所有面向用户的内容（HTML、CSS、JavaScript）、更改完成身份验证所要符合的要求、创建新用户、将用户数据发送到外部系统（包括完整迁移），以及编辑所有用户信息（包括密码和电话号码等敏感字段）。 相反，此角色不能更改加密密钥，也不能编辑组织中用于联合的机密。

> [!IMPORTANT]
> B2 IEF 策略管理员是高度敏感的角色，应在生产环境中以非常有限的方式对其进行分配。应认真审核这些用户的活动，尤其是对于生产环境中的组织。

### <a name="billing-administrator"></a>[计费管理员](#billing-administrator-permissions)

进行采购、管理订阅、管理支持票证，以及监视服务运行状况。

### <a name="cloud-application-administrator"></a>[云应用程序管理员](#cloud-application-administrator-permissions)

充当此角色的用户具有与应用程序管理员角色相同的权限，但不包括管理应用程序代理的权限。 此角色授予创建和管理企业应用程序和应用程序注册的所有方面的权限。 此角色还可以同意委派权限以及除 Microsoft Graph API 之外的应用程序权限。 在创建新应用程序注册或企业应用程序时，不会将分配到此角色的用户添加为所有者。

云应用程序管理员可以管理用于模拟应用程序的应用程序凭据。 因此，分配了此角色的用户只能管理那些尚未分配给任何 Azure AD 角色或仅分配给以下管理员角色的应用程序的应用程序凭据：

* 应用程序开发人员
* 云应用管理员
* 目录读者

如果应用程序分配给上面未提到的任何其他角色，则云应用程序管理员不能管理该应用程序的凭据。

### <a name="cloud-device-administrator"></a>[云设备管理员](#cloud-device-administrator-permissions)

充当此角色的用户可以在 Azure AD 中启用、禁用和删除设备，并可以在 Azure 门户中读取 Windows 10 BitLocker 密钥（如果有）。 该角色不能授予设备上其他任何属性的管理权限。

### <a name="compliance-administrator"></a>[合规性管理员](#compliance-administrator-permissions)

具有此角色的用户有权管理 Microsoft 365 合规中心、Microsoft 365 管理中心、Azure 和 Office 365 安全与合规中心中的合规性相关功能。 被分配者还可以管理 Exchange 管理中心、Teams 和 Skype for Business 管理中心内的所有功能，并可创建适用于 Azure 和 Microsoft 365 的支持票证。 [关于 Office 365 管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)中提供了详细信息。

In | 有权执行的操作
----- | ----------
[Microsoft 365 合规中心](https://protection.office.com) | 跨 Microsoft 365 服务保护和管理组织数据<br>管理合规性警报
[合规性管理器](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 跟踪、分配并验证组织的法规合规性活动
[Office 365 安全与合规中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理数据治理<br>执行法律和数据调查<br>管理数据主体请求<br><br>此角色的权限与 Office 365 安全与合规中心基于角色的访问控制中的[合规性管理员角色组](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)相同。
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 查看所有 Intune 审核数据
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 拥有只读权限，可以管理警报<br>可以创建和修改文件策略并允许执行文件管理操作<br>可以查看数据管理下的所有内置报表

### <a name="compliance-data-administrator"></a>[合规性数据管理员](#compliance-data-administrator-permissions)

具有此角色的用户有权在 Microsoft 365 合规中心、Microsoft 365 管理中心和 Azure 中跟踪数据。 这些用户还可以在 Exchange 管理中心、Compliance Manager、Teams 和 Skype for Business 管理中心跟踪合规数据，并可创建适用于 Azure 和 Microsoft 365 的支持票证。

In | 有权执行的操作
----- | ----------
[Microsoft 365 合规中心](https://protection.office.com) | 跨 Microsoft 365 服务监视与合规性相关的策略<br>管理合规性警报
[合规性管理器](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 跟踪、分配并验证组织的法规合规性活动
[Office 365 安全与合规中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理数据治理<br>执行法律和数据调查<br>管理数据主体请求<br><br>此角色的权限与 Office 365 安全与合规中心基于角色的访问控制中的[合规性数据管理员角色组](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)相同。
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 查看所有 Intune 审核数据
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 拥有只读权限，可以管理警报<br>可以创建和修改文件策略并允许执行文件管理操作<br>可以查看数据管理下的所有内置报表

### <a name="conditional-access-administrator"></a>[条件访问管理员](#conditional-access-administrator-permissions)

具有此角色的用户能够管理 Azure Active Directory 条件访问设置。
> [!NOTE]
> 若要在 Azure 中部署 Exchange ActiveSync 条件访问策略，用户还必须是全局管理员。

### <a name="customer-lockbox-access-approver"></a>[客户密码箱访问审批者](#customer-lockbox-access-approver-permissions)

管理你的组织中的[客户密码箱请求](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests)。 他们接收客户密码箱请求的电子邮件通知，并且可以批准和拒绝来自 Microsoft 365 管理中心的请求。 他们还可以开启或关闭客户密码箱功能。 只有全局管理员可以重置分配到此角色的用户的密码。

### <a name="desktop-analytics-administrator"></a>[桌面分析管理员](#desktop-analytics-administrator-permissions)


充当此角色的用户可以管理 Desktop Analytics 以及 Office 自定义和策略服务。 对于 Desktop Analytics，此权限包括查看资产库存、创建部署计划、查看部署和运行状态。 对于 Office 自定义和策略服务，此角色可让用户管理 Office 策略。

### <a name="device-administrator"></a>[设备管理员](#device-administrators-permissions)

此角色只能作为[设备设置](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)中的其他本地管理员进行分配。 拥有此角色的用户成为所有已加入 Azure Active Directory 的 Windows 10 设备上的本地计算机管理员。 他们无权管理 Azure Active Directory 中的设备对象。

### <a name="directory-readers"></a>[目录读取者](#directory-readers-permissions)

充当此角色的用户可以读取基本的目录信息。 应将此角色用于：
* 为特定的一组来宾用户授予读取访问权限，而不是将此权限授予所有来宾用户。
* 当“仅限管理员访问 Azure AD 门户”设置为“是”时，为特定的一组非管理员用户授予对 Azure 门户的访问权限。
* 当“Directory.Read.All”不是可用选项时，为服务主体授予对目录的访问权限。

### <a name="directory-synchronization-accounts"></a>[目录同步帐户](#directory-synchronization-accounts-permissions)

请勿使用。 此角色自动分配给 Azure AD Connect 服务，不可用于其他任何用途。

### <a name="directory-writers"></a>[目录写入者](#directory-writers-permissions)

这是一个遗留的角色，分配给不支持[许可框架](../develop/quickstart-register-app.md)的应用程序。 不应将它分配给任何用户。

### <a name="dynamics-365-administrator--crm-administrator"></a>[Dynamics 365 管理员/CRM 管理员](#crm-service-administrator-permissions)

具有此角色的用户具有 Microsoft Dynamics 365 Online 内的全局权限（如果该服务存在），并且能够管理支持票证和监视服务运行状况。 有关详细信息，[请参阅使用服务管理员角色管理 Azure AD 组织](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，此角色标识为“Dynamics 365 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Dynamics 365 管理员”。

### <a name="exchange-administrator"></a>[Exchange 管理员](#exchange-service-administrator-permissions)

具有此角色的用户具有 Microsoft Exchange Online 内的全局权限（如果该服务存在）。 还能够创建和管理所有 Office 365 组、管理支持票证和监视服务运行状况。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)（关于 Office 365 管理员角色）。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，此角色标识为“Exchange 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Exchange 管理员”。 它是 [Exchange 管理中心](https://go.microsoft.com/fwlink/p/?LinkID=529144)内的“Exchange Online 管理员”。

### <a name="external-identity-provider-administrator"></a>[外部标识提供者管理员](#external-identity-provider-administrator-permissions)

此管理员管理 Azure AD 组织和外部标识提供者之间的联合。 用户可以使用此角色添加新的标识提供者及配置所有可用设置（例如身份验证路径、服务 ID 和分配的密钥容器）。此用户可以使 Azure AD 组织信任来自外部标识提供者的身份验证。最终用户体验产生的影响取决于组织类型：

* 为员工和合作伙伴 Azure AD 组织：添加联合（例如，使用 Gmail）会立即影响尚未兑换的所有来宾邀请。 请参阅[添加 Google 作为 B2B 来宾用户的标识提供者](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)。
* Azure Active Directory B2C 组织：添加联合（例如，使用 Facebook 或其他 Azure AD 组织）不会立即影响最终用户流，直到将标识提供程序作为用户流中的选项（也称为内置策略）添加。 有关示例，请参阅[将 Microsoft 帐户配置为标识提供者](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app)。若要更改用户流，需要使用受限角色“B2C 用户流管理员”。

### <a name="global-administrator--company-administrator"></a>[全局管理员/公司管理员](#company-administrator-permissions)

具有此角色的用户有权访问 Azure Active Directory 以及使用 Azure Active Directory 标识的服务（例如 Microsoft 365 安全中心、Microsoft 365 合规中心、Exchange Online、SharePoint Online 和 Skype for Business Online）中的所有管理功能。 注册 Azure AD 组织的人员将成为全局管理员。 公司中可以有多个全局管理员。 全局管理员可以为任何用户和所有其他管理员重置密码。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，此角色标识为“公司管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“全局管理员”。
>
>

### <a name="global-reader"></a>[全局读取者](#global-reader-permissions)

充当此角色的用户可以跨 Microsoft 365 服务读取设置和管理信息，但无法执行管理操作。 全局读取者是对应于全局管理员的只读角色。 满足规划、审核或调查目的时，请分配全局读取者，而不要分配全局管理员。 将全局读取者与其他受限管理员角色（例如 Exchange 管理员）结合使用可以更轻松地完成工作，且无需分配全局管理员角色。 全局读取者可使用 Microsoft 365 管理中心、Exchange 管理中心、SharePoint 管理中心、Teams 管理中心、安全中心、合规中心、Azure AD 管理中心和设备管理管理中心。

> [!NOTE]
> 全局读取者角色目前存在一些限制 -
>
>- [OneDrive 管理中心](https://admin.onedrive.com/) - OneDrive 管理中心不支持全局读取者角色。
>- [Azure AD 门户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) - 全局读取者无法读取企业应用的预配模式。
>- [M365 管理中心](https://admin.microsoft.com/Adminportal/Home#/homepage) - 全局读取者无法读取客户密码箱请求。 在 M365 管理中心左窗格中的“支持”下，看不到“客户密码箱请求”选项卡。  
>- [Office 安全与合规中心](https://sip.protection.office.com/homepage) - 全局读取者不能读取 SCC 审核日志、进行内容搜索或查看“安全分数”。
>- [Teams 管理中心](https://admin.teams.microsoft.com) - 全球读取者无法读取“Teams 生命周期”、“分析和报告”、“IP 电话设备管理”和“应用目录”。    
>- [Privileged Access Management (PAM)](https://docs.microsoft.com/office365/securitycompliance/privileged-access-management-overview) 不支持全局读取者角色。
>- [Azure 信息保护](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) - 仅支持全球读取者执行[中心报告](https://docs.microsoft.com/azure/information-protection/reports-aip)，并且仅当 Azure AD 组织不在[统一标记平台](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)上时才支持此角色。
>
> 这些功能目前正在开发中。
>

### <a name="groups-administrator"></a>[组管理员](#groups-administrator-permissions)

此角色中的用户可以创建/管理组及其设置，如命名和过期策略。 务必要了解的是，将用户分配到此角色后，他们还可以跨各种工作负荷（如团队、SharePoint、Yammer）管理组织中的所有组。 此外，用户还能够跨各种管理门户（如 Microsoft 管理中心、Azure 门户以及工作负荷（如团队和 SharePoint 管理中心）管理各种组设置。

### <a name="guest-inviter"></a>[来宾邀请者](#guest-inviter-permissions)

此角色的用户可在“成员可以邀请”用户设置设置为“否”时管理 Azure Active Directory B2B 来宾用户邀请。  [关于 Azure AD B2B 协作](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)中提供了有关 B2B 协作的详细信息。 它不包括任何其他权限。

### <a name="helpdesk-administrator"></a>[支持管理员](#helpdesk-administrator-permissions)

具有此角色的用户可以更改密码、使刷新令牌失效、管理服务请求和监视服务运行状况。 使刷新令牌失效会强制用户重新登录。 对于非管理员或仅分配有以下角色的其他用户，支持管理员可以重置其密码，以及使其刷新令牌失效：

* 目录读者
* 来宾邀请者
* 支持管理员
* 消息中心读取者
* 报告读者

> [!IMPORTANT]
> 具有此角色的用户可以更改可能有权访问 Azure Active Directory 内外敏感或私有信息或关键配置的用户的密码。 更改用户的密码可能意味着假定用户标识和权限的能力。 例如：
>
>- 应用程序注册和企业应用程序所有者，可以管理他们拥有的应用的凭据。 这些应用程序可能在 Azure AD 或其他位置拥有未授予支持人员管理员的特权。 通过此路径，支持人员管理员可能能够假定应用程序所有者的身份，然后通过更新应用程序的凭据来进一步假定特权应用程序的标识。
>- Azure 订阅所有者，可能对 Azure 中的敏感或私有信息或关键配置具有访问权限。
>- 安全组和 Office 365 组所有者，可以管理组成员身份。 这些组可能会授予对 Azure AD 或其他位置敏感或私有信息或关键配置的访问权限。
>- Azure AD 之外的其他服务中的管理员，如 Exchange Online、Office 安全与合规中心以及人力资源系统。
>- 高级管理人员、法律顾问和人力资源员工之类的非管理员，可能有权访问敏感或私有信息。

使用[管理单元（现已公开预览）](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units)，可以委派对一部分用户的管理权限并将策略应用于一部分用户。

在 [Azure 门户](https://portal.azure.com/)中，此角色以前称为“密码管理员”。 Azure AD 中的“支持管理员”名称现在与其在 Azure AD PowerShell 和 Microsoft Graph API 中的名称匹配。

### <a name="hybrid-identity-administrator"></a>[混合标识管理员](#hybrid-identity-administrator-permissions)

充当此角色的用户可以启用、配置和管理与在 Azure AD 中启用混合标识相关的服务及设置。 此角色授予将 Azure AD 配置为三种受支持的身份验证方法之一（密码哈希同步 (PHS)、直通身份验证 (PTA)，或联合身份验证（AD FS 或第三方联合身份验证提供程序）），以及部署相关本地基础结构来启用这些方法的能力。 本地基础结构包括预配和 PTA 代理。 此角色授予在非 Windows 10 设备或非 Windows Server 2016 计算机上启用无缝单一登录 (SSO) 以实现无缝身份验证的能力。 此外，此角色授予查看登录日志以及访问运行状况和分析数据的能力，以便进行监视和故障排除。 

### <a name="intune-administrator"></a>[Intune 管理员](#intune-service-administrator-permissions)

具有此角色的用户具有 Microsoft Intune Online 内的全局权限（如果该服务存在）。 此外，此角色包含管理以关联策略，以及创建和管理组的用户和设备的能力。 有关详细信息，请参阅[使用 Microsoft Intune 进行基于角色的管理控制 (RBAC)](https://docs.microsoft.com/intune/role-based-access-control)。

此角色可创建和管理所有安全组。 但是，Intune 管理员对 Office 组没有管理员权限。 这意味着管理员无法更新组织中所有 Office 组的所有者或成员身份。 但是，他/她可以管理他创建的 Office 组作为其最终用户权限的一部分。 因此，他们创建的任何 Office 组（非安全组）都应根据其 250 的配额进行计数。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，此角色标识为“Intune 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Intune 管理员”。

### <a name="kaizala-administrator"></a>[Kaizala 管理员](#kaizala-administrator-permissions)

具有此角色的用户拥有在 Microsoft Kaizala 中管理设置的全局权限（如果该服务存在），并且能够管理支持票证和监视服务运行状况。 此外，该用户还可根据组织成员以及使用 Kaizala 操作生成的业务报告，来访问与 Kaizala 的采纳和使用相关的报告。

### <a name="license-administrator"></a>[许可证管理员](#license-administrator-permissions)

具有此角色的用户可以添加、删除和更新用户、组（使用基于组的许可）的许可分配，以及管理用户的使用位置。 该角色不授予在使用位置之外购买或管理订阅、创建或管理组，或者创建或管理用户的权限。 此角色无权查看、创建或管理支持票证。

### <a name="message-center-privacy-reader"></a>[消息中心隐私读取者](#message-center-privacy-reader-permissions)

充当此角色的用户可以监视消息中心内的所有通知，包括数据隐私消息。 消息中心隐私读取者会收到电子邮件通知（包括与数据隐私相关的通知），并可以使用邮件中心首选项取消订阅。 只有全局管理员和消息中心隐私读取者才能阅读数据隐私消息。 此外，此角色还能查看组、域和订阅。 此角色无权查看、创建或管理服务请求。

### <a name="message-center-reader"></a>[消息中心读取者](#message-center-reader-permissions)

具有此角色的用户可以在其组织的 [Office 365 消息中心](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)内，监视 Exchange、Intune 和 Microsoft Teams 等已配置服务的通知和公告运行状况更新。 消息中心读者会收到包含帖子和最新动态的每周电子邮件摘要，并能在 Office 365 内共享消息中心帖子。 在 Azure AD 中，分配到此角色的用户对 Azure AD 服务只拥有只读访问权限，如用户和组。 此角色无权查看、创建或管理支持票证。

### <a name="network-administrator"></a>[网络管理员](#network-administrator-permissions)

充当此角色的用户可以查看 Microsoft 根据其用户位置发出的网络遥测数据提供的网络外围体系结构建议。 Office 365 的网络性能依赖于精心规划的企业客户网络外围体系结构，而该体系结构通常特定于用户位置。 此角色允许编辑已发现的用户位置以及配置这些位置的网络参数，以方便改善遥测措施和设计建议。 

### <a name="office-apps-administrator"></a>[Office 应用管理员](#office-apps-administrator-permissions)

此角色中的用户可以管理 Office 365 应用的云设置。 这包括管理云策略、自助下载管理和查看与 Office apps 相关的报表。 此角色还允许管理支持票证，并在主管理中心内监视服务运行状况。 分配到此角色的用户还可以管理 Office 应用中新功能的通信。 

### <a name="partner-tier1-support"></a>[合作伙伴 Tier1 支持](#partner-tier1-support-permissions)

请勿使用。 此角色已弃用，并将从 Azure AD 中删除。 此角色仅供少数 Microsoft 转售合作伙伴使用，不适用于一般用途。

### <a name="partner-tier2-support"></a>[合作伙伴 Tier2 支持](#partner-tier2-support-permissions)

请勿使用。 此角色已弃用，并将从 Azure AD 中删除。 此角色仅供少数 Microsoft 转售合作伙伴使用，不适用于一般用途。

### <a name="password-administrator"></a>[密码管理员](#password-administrator-permissions)

具有此角色的用户可以管理密码，但权限受限。 此角色不会授予管理服务请求或监视服务运行状况的能力。 密码管理员只能重置其他非管理员用户的密码，或具有以下角色的成员的密码：

* 目录读者
* 来宾邀请者
* 密码管理员

### <a name="power-bi-administrator"></a>[Power BI 管理员](#power-bi-service-administrator-permissions)

具有此角色的用户具有 Microsoft Power BI 内的全局权限（如果该服务存在），并且能够管理支持票证和监视服务运行状况。 有关详细信息，请参阅[了解 Power BI 管理员角色](https://docs.microsoft.com/power-bi/service-admin-role)。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，此角色标识为“Power BI 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Power BI 管理员”。

### <a name="power-platform-administrator"></a>[Power Platform 管理员](#power-platform-administrator-permissions)

充当此角色的用户可以创建和管理环境、PowerApps、Flows、数据丢失防护策略的所有方面。 另外，具有此角色的用户可以管理支持票证并监视服务运行状况。

### <a name="printer-administrator"></a>[打印机管理员](#printer-administrator-permissions)

充当此角色的用户可以在 Microsoft 通用打印解决方案中注册打印机和管理所有打印机配置的各个方面，包括“通用打印连接器”设置。 他们可以同意所有委托的打印权限请求。 打印机管理员还有权访问打印报告。

### <a name="printer-technician"></a>[打印机技术人员](#printer-technician-permissions)

具有此角色的用户可以在 Microsoft 通用打印解决方案中注册打印机和管理打印机状态。 他们还可以读取所有连接器信息。 打印机技术人员无法执行的重要任务是设置用户对打印机的权限以及共享打印机。

### <a name="privileged-authentication-administrator"></a>[特权身份验证管理员](#privileged-authentication-administrator-permissions)

具有此角色的用户可以设置或重置所有用户（包括全局管理员）的非密码凭据，并可以更新所有用户的密码。 特权身份验证管理员可以强制用户根据现有的非密码凭据（例如 MFA 或 FIDO）重新注册，并撤销 "在设备上记住 MFA"，在所有用户的下一次登录时提示进行 MFA。 [身份验证管理员](#authentication-administrator)角色仅可为分配到以下 Azure AD 角色的非管理员和用户强制进行重新注册和 MFA：

* 身份验证管理员
* 目录读者
* 来宾邀请者
* 消息中心读取者
* 报告读者

### <a name="privileged-role-administrator"></a>[特权角色管理员](#privileged-role-administrator-permissions)

具有此角色的用户可以管理 Azure Active Directory 和 Azure AD Privileged Identity Management 中的角色分配。 此外，此角色允许管理 Privileged Identity Management 和管理单元的各个方面。

> [!IMPORTANT]
> 此角色授予管理所有 Azure AD 角色（包括全局管理员角色）的分配的能力。 此角色不包括 Azure AD 中的任何其他权限功能，如创建或更新用户。 但是，分配到此角色的用户可通过分配其他角色，授予自己或其他人额外的特权。

### <a name="reports-reader"></a>[报告读取者](#reports-reader-permissions)

具有此角色的用户可在 Microsoft 365 管理中心和 Power BI 中的采用上下文包内查看使用情况报告数据和报告仪表板。 此外，此角色还提供对 Azure AD 中的登录报告和活动以及 Microsoft Graph 报告 API 返回的数据的访问权限。 分配到“报告读者”角色的用户只能访问相关使用情况和采用指标。 它们没有任何管理员权限，无法配置设置或访问产品特定的管理中心（如 Exchange）。 此角色无权查看、创建或管理支持票证。

### <a name="search-administrator"></a>[搜索管理员](#search-administrator-permissions)

充当此角色的用户对 Microsoft 365 管理中心内的所有 Microsoft 搜索管理功能拥有完全访问权限。 搜索管理员可将搜索管理员和搜索编辑员角色委托给用户，并可以创建和管理内容，例如书签、问答和位置。 此外，这些用户可以查看消息中心、监视服务运行状况和创建服务请求。

### <a name="search-editor"></a>[搜索编辑员](#search-editor-permissions)

充当此角色的用户可以在 Microsoft 365 管理中心创建、管理和删除 Microsoft 搜索的内容，包括书签、问答和位置。

### <a name="security-administrator"></a>[安全管理员](#security-administrator-permissions)

具有此角色的用户有权管理 Microsoft 365 安全中心、Azure Active Directory 标识保护、Azure 信息保护和 Office 365 安全与合规中心中与安全相关的功能。 [Office 365 安全与合规中心](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)提供了有关 Office 365 权限的详细信息。

In | 有权执行的操作
--- | ---
[Microsoft 365 安全中心](https://protection.office.com) | 跨 Microsoft 365 服务监视与安全相关的策略<br>管理安全威胁和警报<br>查看报告
标识保护中心 | 安全读取者角色的所有权限<br>此外，还能够执行除了重置密码以外的所有“标识保护中心”操作
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | 安全读取者角色的所有权限<br>无法管理 Azure AD 角色分配或设置 
[Office 365 安全与合规中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理安全策略<br>查看、调查和响应安全威胁<br>查看报告
Azure 高级威胁防护 | 监视和响应可疑安全活动
Windows Defender ATP 和 EDR | 分配角色<br>管理计算机组<br>配置终结点威胁检测和自动修正<br>查看、调查并响应警报
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 视图用户、设备、注册、配置和应用程序信息<br>无法对 Intune 进行更改
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 添加管理员、添加策略和设置、上传日志以及执行管理操作
[Azure 安全中心](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | 可以查看安全策略、查看安全状态、编辑安全策略、查看警报和建议、关闭警报和建议
[Office 365 服务运行状况](https://docs.microsoft.com/office365/enterprise/view-service-health) | 查看 Office 365 服务的运行状况

### <a name="security-operator"></a>[安全操作员](#security-operator-permissions)

具有此角色的用户可以管理警报并拥有对安全相关功能的全局只读访问权限，包括 Microsoft 365 安全中心、Azure Active Directory、Identity Protection、Privileged Identity Management 和 Office 365 安全 & 符合性中心中的所有信息。 [Office 365 安全与合规中心](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center)提供了有关 Office 365 权限的详细信息。

In | 有权执行的操作
--- | ---
[Microsoft 365 安全中心](https://protection.office.com) | 安全读取者角色的所有权限<br>查看、调查和响应安全威胁警报
标识保护中心 | 安全读取者角色的所有权限<br>此外，还能够执行除了重置密码以外的所有“标识保护中心”操作
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | 安全读取者角色的所有权限
[Office 365 安全与合规中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 安全读取者角色的所有权限<br>查看、调查和响应安全警报
Windows Defender ATP 和 EDR | 安全读取者角色的所有权限<br>查看、调查和响应安全警报
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 安全读取者角色的所有权限
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 安全读取者角色的所有权限
[Office 365 服务运行状况](https://docs.microsoft.com/office365/enterprise/view-service-health) | 查看 Office 365 服务的运行状况

### <a name="security-reader"></a>[安全读取者](#security-reader-permissions)

具有此角色的用户对安全相关的功能具有全局只读访问权限，包括 Microsoft 365 安全中心、Azure Active Directory、标识保护、Privileged Identity Management 中的所有信息，并且能够阅读 Azure Active Directory 登录报告和审核日志，还授予了对 Office 365 安全与合规中心的只读权限。 [Office 365 安全与合规中心](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)提供了有关 Office 365 权限的详细信息。

In | 有权执行的操作
--- | ---
[Microsoft 365 安全中心](https://protection.office.com) | 跨 Microsoft 365 服务查看与安全相关的策略<br>查看安全威胁和警报<br>查看报告
标识保护中心 | 读取安全功能的所有安全报告和设置信息<br><ul><li>反垃圾邮件<li>Encryption<li>数据丢失防护<li>反恶意软件<li>高级威胁防护<li>防网络钓鱼<li>邮件流规则
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | 以只读方式访问 Azure AD Privileged Identity Management 中显示的所有信息：Azure AD 角色分配的策略和报告以及安全评审。<br>**无法**注册 Azure AD Privileged Identity Management 或对其进行任何更改。 充当此角色的人员可以在 Privileged Identity Management 门户中或通过 PowerShell 为符合条件的用户激活其他角色（例如，全局管理员或特权角色管理员）。
[Office 365 安全与合规中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 查看安全策略<br>查看并调查安全威胁<br>查看报告
Windows Defender ATP 和 EDR | 查看并调查警报。 在 Windows Defender ATP 中启用基于角色的访问控制后，拥有只读权限的用户（例如 Azure AD 安全读取者角色）在被分配到 Windows Defender ATP 角色之前会失去访问权限。
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 视图用户、设备、注册、配置和应用程序信息。 无法对 Intune 进行更改。
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 拥有只读权限，可以管理警报
[Azure 安全中心](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | 可以查看建议和警报、查看安全策略、查看安全状态，但不能进行更改
[Office 365 服务运行状况](https://docs.microsoft.com/office365/enterprise/view-service-health) | 查看 Office 365 服务的运行状况

### <a name="service-support-administrator"></a>[服务支持管理员](#service-support-administrator-permissions)

具有此角色的用户可以打开 Azure 和 Office 365 服务的 Microsoft 支持请求，并在[Azure 门户](https://portal.azure.com)和[Microsoft 365 管理中心](https://admin.microsoft.com)中查看服务仪表板和消息中心。 有关详细信息，请参阅[关于管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

> [!NOTE]
> 以前，此角色在[Azure 门户](https://portal.azure.com)和[Microsoft 365 管理中心](https://admin.microsoft.com)名为 "服务管理员"。 我们已将其重命名为“服务支持管理员”，以与 Microsoft Graph API、Azure AD Graph API 和 Azure AD PowerShell 中的现有名称保持一致。

### <a name="sharepoint-administrator"></a>[SharePoint 管理员](#sharepoint-service-administrator-permissions)

具有此角色的用户在 Microsoft SharePoint Online（如果存在此服务）中拥有全局权限，并且能够创建和管理所有 Office 365 组、管理支持票证和监视服务运行状况。 有关详细信息，请参阅[关于管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，此角色标识为“SharePoint 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“SharePoint 管理员”。

### <a name="skype-for-business--lync-administrator"></a>[Skype for Business/Lync 管理员](#lync-service-administrator-permissions)

具有此角色的用户具有 Microsoft Skype for Business 中的全局权限，以及管理 Azure Active Directory 中的特定于 Skype 的用户属性。 此外，此角色可授予管理支持票证、监视服务运行状况以及访问 Teams 和 Skype for Business 管理中心的能力。 帐户必须获取 Teams 许可证，否则无法运行 Teams PowerShell cmdlet。 有关详细信息，请参阅[关于 Skype for Business 管理员角色](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5)；有关 Teams 许可信息，请参阅 [Skype for Business 和 Microsoft Teams 附加许可](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，此角色标识为“Lync 服务管理员”。 它是 [Azure 门户](https://portal.azure.com/)中的“Skype for Business 管理员”。

### <a name="teams-communications-administrator"></a>[Teams 通信管理员](#teams-communications-administrator-permissions)

充当此角色的用户可以管理 Microsoft Teams 工作负荷的语音与电话相关方面。 这包括用于分配电话号码的管理工具、语音和会议策略，以及通话分析工具集的完全访问权限。

### <a name="teams-communications-support-engineer"></a>[Teams 通信支持工程师](#teams-communications-support-engineer-permissions)

充当此角色的用户可以使用 Microsoft Teams 和 Skype for Business 管理中心的用户通话故障排除工具，来排查 Microsoft Teams 和 Skype for Business 中的通信问题。 充当此角色的用户可以查看所有参与方的完整通话记录信息。 此角色无权查看、创建或管理支持票证。

### <a name="teams-communications-support-specialist"></a>[Teams 通信支持专家](#teams-communications-support-specialist-permissions)

充当此角色的用户可以使用 Microsoft Teams 和 Skype for Business 管理中心的用户通话故障排除工具，来排查 Microsoft Teams 和 Skype for Business 中的通信问题。 充当此角色的用户只能查看他们所查找的特定用户的通话中的用户详细信息。 此角色无权查看、创建或管理支持票证。

### <a name="teams-service-administrator"></a>[Teams 服务管理员](#teams-service-administrator-permissions)

充当此角色的用户可以通过 Microsoft Teams 和 Skype for Business 管理中心以及相应的 PowerShell 模块来管理 Microsoft Teams 工作负荷的所有方面。 这包括（但不限于）与电话、消息、会议和 Teams 自身相关的所有管理工具。 此外，此角色还能够创建和管理所有 Office 365 组、管理支持票证和监视服务运行状况。

### <a name="user-administrator"></a>[用户管理员](#user-administrator-permissions)

具有此角色的用户可以创建用户、管理用户的所有方面（但存在一些限制，具体请参阅下文），以及更新密码过期策略。 此外，具有此角色的用户可以创建和管理所有组。 此角色还能够创建和管理用户视图、管理支持票证和监视服务运行状况。 用户管理员无权管理充当大部分管理员角色的用户的某些用户属性。 具有此角色的用户无权管理 MFA。 下表列出了不存在这种限制的角色。

| | |
| --- | --- |
|常规权限|<p>创建用户和组</p><p>创建和管理用户视图</p><p>管理 Office 支持票证<p>更新密码过期策略|
|<p>适用于所有用户，包括所有管理员</p>|<p>管理许可证</p><p>管理除用户主体名称之外的所有用户属性</p>
|仅适用于不是管理员或具有以下任一管理员角色（权限有限）的用户：<ul><li>目录读者<li>来宾邀请者<li>支持管理员<li>消息中心读取者<li>报告读者<li>用户管理员|<p>删除和还原</p><p>禁用和启用</p><p>使刷新令牌失效</p><p>管理包括用户主体名称在内的所有用户属性</p><p>重置密码</p><p>更新 (FIDO) 设备密钥</p>|

> [!IMPORTANT]
> 具有此角色的用户可以更改可能有权访问 Azure Active Directory 内外敏感或私有信息或关键配置的用户的密码。 更改用户的密码可能意味着假定用户标识和权限的能力。 例如：
>
>- 应用程序注册和企业应用程序所有者，可以管理他们拥有的应用的凭据。 这些应用程序可能在 Azure AD 或其他位置拥有未授予用户管理员的特权。 通过此路径，用户管理员可能能够假定应用程序所有者的身份，然后通过更新应用程序的凭据来进一步假定特权应用程序的标识。
>- Azure 订阅所有者，可能对 Azure 中的敏感或私有信息或关键配置拥有访问权限。
>- 安全组和 Office 365 组所有者，可以管理组成员身份。 这些组可能会授予对 Azure AD 或其他位置敏感或私有信息或关键配置的访问权限。
>- Azure AD 之外的其他服务中的管理员，如 Exchange Online、Office 安全与合规中心以及人力资源系统。
>- 高级管理人员、法律顾问和人力资源员工之类的非管理员，可能有权访问敏感或私有信息。

## <a name="role-permissions"></a>角色权限

下表描述 Azure Active Directory 中授予每个角色的特定权限。 某些角色可能在 Azure Active Directory 外部的 Microsoft 服务中拥有其他权限。

### <a name="application-administrator-permissions"></a>应用程序管理员权限

可以创建和管理应用注册和企业应用的所有方面。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/Application/appProxyAuthentication/update | 更新 Azure Active Directory 中服务主体的应用程序代理身份验证属性。 |
| microsoft.directory/Application/appProxyUrlSettings/update | 更新 Azure Active Directory 中的应用程序代理内部和外部 URL。 |
| microsoft.directory/applications/applicationProxy/read | 读取所有应用程序代理属性。 |
| microsoft.directory/applications/applicationProxy/update | 更新所有应用程序代理属性。 |
| microsoft.directory/applications/audience/update | 更新 Azure Active Directory 中的 applications.audience 属性。 |
| microsoft.directory/applications/authentication/update | 更新 Azure Active Directory 中的 applications.authentication 属性。 |
| microsoft.directory/applications/basic/update | 更新 Azure Active Directory 中应用程序的基本属性。 |
| microsoft.directory/applications/create | 在 Azure Active Directory 中创建应用程序。 |
| microsoft.directory/applications/credentials/update | 更新 Azure Active Directory 中的 applications.credentials 属性。 |
| microsoft.directory/applications/delete | 删除 Azure Active Directory 中的应用程序。 |
| microsoft.directory/applications/owners/update | 更新 Azure Active Directory 中的 applications.owners 属性。 |
| microsoft.directory/applications/permissions/update | 更新 Azure Active Directory 中的 applications.permissions 属性。 |
| microsoft.directory/applications/policies/update | 更新 Azure Active Directory 中的 applications.policies 属性。 |
| microsoft.directory/appRoleAssignments/create | 在 Azure Active Directory 中创建 appRoleAssignments。 |
| microsoft.directory/appRoleAssignments/read | 读取 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.directory/appRoleAssignments/update | 更新 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.directory/appRoleAssignments/delete | 删除 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| microsoft.directory/connectorGroups/everything/read | 读取 Azure Active Directory 中的应用程序代理连接器组属性。 |
| microsoft.directory/connectorGroups/everything/update | 更新 Azure Active Directory 中的应用程序代理连接器组属性。 |
| microsoft.directory/connectorGroups/create | 在 Azure Active Directory 中创建应用程序代理连接器组。 |
| microsoft.directory/connectorGroups/delete | 在 Azure Active Directory 中删除应用程序代理连接器组。 |
| microsoft.directory/connectors/everything/read | 读取 Azure Active Directory 中的所有应用程序代理连接器属性。 |
| microsoft.directory/connectors/create | 在 Azure Active Directory 中创建应用程序代理连接器。 |
| microsoft.directory/policies/applicationConfiguration/basic/read | 读取 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.directory/policies/applicationConfiguration/basic/update | 更新 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.directory/policies/applicationConfiguration/create | 在 Azure Active Directory 中创建策略。 |
| microsoft.directory/policies/applicationConfiguration/delete | 删除 Azure Active Directory 中的策略。 |
| microsoft.directory/policies/applicationConfiguration/owners/read | 读取 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.directory/policies/applicationConfiguration/owners/update | 更新 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | 读取 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 属性。 |
| microsoft.directory/servicePrincipals/audience/update | 更新 Azure Active Directory 中的 servicePrincipals.audience 属性。 |
| microsoft.directory/servicePrincipals/authentication/update | 更新 Azure Active Directory 中的 servicePrincipals.authentication 属性。 |
| microsoft.directory/servicePrincipals/basic/update | 更新 Azure Active Directory 中 servicePrincipals 的基本属性。 |
| microsoft.directory/servicePrincipals/create | 在 Azure Active Directory 中创建 servicePrincipals。 |
| microsoft.directory/servicePrincipals/credentials/update | 更新 Azure Active Directory 中的 servicePrincipals.credentials 属性。 |
| microsoft.directory/servicePrincipals/delete | 删除 Azure Active Directory 中的 servicePrincipals。 |
| microsoft.directory/servicePrincipals/owners/update | 更新 Azure Active Directory 中的 servicePrincipals.owners 属性。 |
| microsoft.directory/servicePrincipals/permissions/update | 更新 Azure Active Directory 中的 servicePrincipals.permissions 属性。 |
| microsoft.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 属性。 |
| microsoft.directory/signInReports/allProperties/read | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="application-developer-permissions"></a>应用程序开发人员权限

可以创建独立于“用户可注册应用程序”设置的应用程序注册。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/applications/createAsOwner | 在 Azure Active Directory 中创建应用程序。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.directory/appRoleAssignments/createAsOwner | 在 Azure Active Directory 中创建 appRoleAssignments。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.directory/oAuth2PermissionGrants/createAsOwner | 在 Azure Active Directory 中创建 oAuth2PermissionGrants。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.directory/servicePrincipals/createAsOwner | 在 Azure Active Directory 中创建 servicePrincipals。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |

### <a name="authentication-administrator-permissions"></a>身份验证管理员权限

允许查看、设置和重置任何非管理员用户的身份验证方法信息。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft.directory/users/strongAuthentication/update | 更新强身份验证属性，如 MFA 凭据信息。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |
| microsoft.directory/users/password/update | 更新 Office 365 组织中所有用户的密码。 有关详细信息，请参阅联机文档。 |

### <a name="azure-devops-administrator-permissions"></a>Azure DevOps 管理员权限

可以管理 Azure DevOps 的组织策略和设置。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的[角色说明](#azure-devops-administrator)。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.devOps/allEntities/allTasks | 读取和配置 Azure DevOps。 |

### <a name="azure-information-protection-administrator-permissions"></a>Azure 信息保护管理员权限

可以管理 Azure 信息保护服务的各个方面。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的[角色说明](#)。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 信息保护的各个方面。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="b2c-user-flow-administrator-permissions"></a>B2C 用户流管理员权限

创建和管理用户流的所有方面。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | 读取和配置 Azure Active Directory B2C 中的用户流。 |

### <a name="b2c-user-flow-attribute-administrator-permissions"></a>B2C 用户流属性管理员权限

创建和管理适用于所有用户流的属性架构。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | 读取和配置 Azure Active Directory B2C 中的用户属性。 |

### <a name="b2c-ief-keyset-administrator-permissions"></a>B2C IEF 密钥集管理员权限

管理 Identity Experience Framework 中的联合身份验证和加密所用的机密。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | 读取和配置 Azure Active Directory B2C 中的密钥集。 |

### <a name="b2c-ief-policy-administrator-permissions"></a>B2C IEF 策略管理员权限

在 Identity Experience Framework 中创建和管理信任框架策略。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | 读取和配置 Azure Active Directory B2C 中的自定义策略。 |

### <a name="billing-administrator-permissions"></a>计费管理员权限

可以执行与常见计费相关的任务，例如更新付款信息。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/organization/basic/update | 更新 Azure Active Directory 中组织的基本属性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.commerce.billing/allEntities/allTasks | 管理计费的所有方面。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="cloud-application-administrator-permissions"></a>云应用程序管理员权限

可以创建和管理应用注册和企业应用的所有方面，应用代理除外。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/applications/audience/update | 更新 Azure Active Directory 中的 applications.audience 属性。 |
| microsoft.directory/applications/authentication/update | 更新 Azure Active Directory 中的 applications.authentication 属性。 |
| microsoft.directory/applications/basic/update | 更新 Azure Active Directory 中应用程序的基本属性。 |
| microsoft.directory/applications/create | 在 Azure Active Directory 中创建应用程序。 |
| microsoft.directory/applications/credentials/update | 更新 Azure Active Directory 中的 applications.credentials 属性。 |
| microsoft.directory/applications/delete | 删除 Azure Active Directory 中的应用程序。 |
| microsoft.directory/applications/owners/update | 更新 Azure Active Directory 中的 applications.owners 属性。 |
| microsoft.directory/applications/permissions/update | 更新 Azure Active Directory 中的 applications.permissions 属性。 |
| microsoft.directory/applications/policies/update | 更新 Azure Active Directory 中的 applications.policies 属性。 |
| microsoft.directory/appRoleAssignments/create | 在 Azure Active Directory 中创建 appRoleAssignments。 |
| microsoft.directory/appRoleAssignments/update | 更新 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.directory/appRoleAssignments/delete | 删除 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| microsoft.directory/policies/applicationConfiguration/create | 在 Azure Active Directory 中创建策略。 |
| microsoft.directory/policies/applicationConfiguration/basic/read | 读取 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.directory/policies/applicationConfiguration/basic/update | 更新 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.directory/policies/applicationConfiguration/delete | 删除 Azure Active Directory 中的策略。 |
| microsoft.directory/policies/applicationConfiguration/owners/read | 读取 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.directory/policies/applicationConfiguration/owners/update | 更新 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | 读取 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 属性。 |
| microsoft.directory/servicePrincipals/audience/update | 更新 Azure Active Directory 中的 servicePrincipals.audience 属性。 |
| microsoft.directory/servicePrincipals/authentication/update | 更新 Azure Active Directory 中的 servicePrincipals.authentication 属性。 |
| microsoft.directory/servicePrincipals/basic/update | 更新 Azure Active Directory 中 servicePrincipals 的基本属性。 |
| microsoft.directory/servicePrincipals/create | 在 Azure Active Directory 中创建 servicePrincipals。 |
| microsoft.directory/servicePrincipals/credentials/update | 更新 Azure Active Directory 中的 servicePrincipals.credentials 属性。 |
| microsoft.directory/servicePrincipals/delete | 删除 Azure Active Directory 中的 servicePrincipals。 |
| microsoft.directory/servicePrincipals/owners/update | 更新 Azure Active Directory 中的 servicePrincipals.owners 属性。 |
| microsoft.directory/servicePrincipals/permissions/update | 更新 Azure Active Directory 中的 servicePrincipals.permissions 属性。 |
| microsoft.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 属性。 |
| microsoft.directory/signInReports/allProperties/read | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="cloud-device-administrator-permissions"></a>云设备管理员权限

用于在 Azure AD 中管理设备的完全访问权限。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | 读取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 属性。 |
| microsoft.directory/devices/delete | 删除 Azure Active Directory 中的设备。 |
| microsoft.directory/devices/disable | 禁用 Azure Active Directory 中的设备。 |
| microsoft.directory/devices/enable | 启用 Azure Active Directory 中的设备。 |
| microsoft.directory/signInReports/allProperties/read | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |

### <a name="company-administrator-permissions"></a>公司管理员权限

可以管理 Azure AD 和使用 Azure AD 标识的 Microsoft 服务的所有方面。 此角色也称为全局管理员角色。 

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | 创建和删除所有资源，然后读取并更新 microsoft.aad.cloudAppSecurity 中的标准属性。 |
| microsoft.directory/administrativeUnits/allProperties/allTasks | 创建和删除 administrativeUnits，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/applications/allProperties/allTasks | 创建和删除应用程序，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/appRoleAssignments/allProperties/allTasks | 创建和删除 appRoleAssignments，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| microsoft.directory/contacts/allProperties/allTasks | 创建和删除联系人，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/contracts/allProperties/allTasks | 创建和删除协定，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/devices/allProperties/allTasks | 创建和删除设备，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/directoryRoles/allProperties/allTasks | 创建和删除 directoryRoles，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/directoryRoleTemplates/allProperties/allTasks | 创建和删除 directoryRoleTemplates，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/domains/allProperties/allTasks | 创建和删除域，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/groups/allProperties/allTasks | 创建和删除组，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/groupSettings/allProperties/allTasks | 创建和删除 groupSettings，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/groupSettingTemplates/allProperties/allTasks | 创建和删除 groupSettingTemplates，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/loginTenantBranding/allProperties/allTasks | 创建和删除 loginTenantBranding，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | 创建和删除 oAuth2PermissionGrants，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/organization/allProperties/allTasks | 创建和删除组织，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/policies/allProperties/allTasks | 创建和删除策略，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/roleAssignments/allProperties/allTasks | 创建和删除 roleAssignments，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/roleDefinitions/allProperties/allTasks | 创建和删除 roleDefinitions，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/scopedRoleMemberships/allProperties/allTasks | 创建和删除 scopedRoleMemberships，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/serviceAction/activateService | 可以在 Azure Active Directory 中执行 Activateservice 服务操作 |
| microsoft.directory/serviceAction/disableDirectoryFeature | 可以在 Azure Active Directory 中执行 Disabledirectoryfeature 服务操作 |
| microsoft.directory/serviceAction/enableDirectoryFeature | 可以在 Azure Active Directory 中执行 Enabledirectoryfeature 服务操作 |
| microsoft.directory/serviceAction/getAvailableExtentionProperties | 可以在 Azure Active Directory 中执行 Getavailableextentionproperties 服务操作 |
| microsoft.directory/servicePrincipals/allProperties/allTasks | 创建和删除 servicePrincipals，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/signInReports/allProperties/read | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |
| microsoft.directory/subscribedSkus/allProperties/allTasks | 创建和删除 subscribedSkus，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/users/allProperties/allTasks | 创建和删除用户，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directorySync/allEntities/allTasks | 在 Azure AD Connect 中执行所有操作。 |
| microsoft.aad.identityProtection/allEntities/allTasks | 创建和删除所有资源，然后读取和更新 microsoft.aad.identityProtection 中的标准属性。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 读取 microsoft.aad.privilegedIdentityManagement 中的所有资源。 |
| microsoft.azure.advancedThreatProtection/allEntities/read | 读取 microsoft.azure.advancedThreatProtection 中的所有资源。 |
| microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 信息保护的各个方面。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.commerce.billing/allEntities/allTasks | 管理计费的所有方面。 |
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

### <a name="compliance-administrator-permissions"></a>合规性管理员权限

可以读取和管理 Azure AD 和 Office 365 中的符合性配置和报表。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
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

### <a name="compliance-data-administrator-permissions"></a>合规性数据管理员权限

创建和管理合规性内容。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | 读取和配置 Microsoft Cloud App Security。 |
| microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 信息保护的各个方面。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.complianceManager/allEntities/allTasks | 管理 Office 365 合规性管理器的各个方面 |
| microsoft.office365.exchange/allEntities/allTasks | 管理 Exchange Online 的各个方面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.sharepoint/allEntities/allTasks | 创建和删除所有资源，然后读取和更新 microsoft.office365.sharepoint 中的标准属性。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理 Skype for Business Online 的各个方面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="conditional-access-administrator-permissions"></a>条件访问管理员权限

可以管理条件访问功能。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/policies/conditionalAccess/basic/read | 读取 Azure Active Directory 中的 policies.conditionalAccess 属性。 |
| microsoft.directory/policies/conditionalAccess/basic/update | 更新 Azure Active Directory 中的 policies.conditionalAccess 属性。 |
| microsoft.directory/policies/conditionalAccess/create | 在 Azure Active Directory 中创建策略。 |
| microsoft.directory/policies/conditionalAccess/delete | 删除 Azure Active Directory 中的策略。 |
| microsoft.directory/policies/conditionalAccess/owners/read | 读取 Azure Active Directory 中的 policies.conditionalAccess 属性。 |
| microsoft.directory/policies/conditionalAccess/owners/update | 更新 Azure Active Directory 中的 policies.conditionalAccess 属性。 |
| microsoft.directory/policies/conditionalAccess/policiesAppliedTo/read | 读取 Azure Active Directory 中的 policies.conditionalAccess 属性。 |
| microsoft.directory/policies/conditionalAccess/tenantDefault/update | 更新 Azure Active Directory 中的 policies.conditionalAccess 属性。 |

### <a name="crm-service-administrator-permissions"></a>CRM 服务管理员权限

可以管理 Dynamics 365 产品的所有方面。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
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

### <a name="customer-lockbox-access-approver-permissions"></a>客户密码箱访问审批者权限

可以批准 Microsoft 支持人员访问客户组织数据的请求。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.lockbox/allEntities/allTasks | 管理 Office 365 客户密码箱的各个方面 |

### <a name="desktop-analytics-administrator-permissions"></a>桌面分析管理员权限

可以管理 Desktop Analytics 以及 Office 自定义和策略服务。 对于 Desktop Analytics，此权限包括查看资产库存、创建部署计划、查看部署和运行状态。 对于 Office 自定义和策略服务，此角色可让用户管理 Office 策略。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
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

### <a name="device-administrators-permissions"></a>设备管理员权限

分配到此角色的用户将添加到 Azure AD 联接的设备上的本地管理员组。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/groupSettings/basic/read | 读取 Azure Active Directory 中 groupSettings 的基本属性。 |
| microsoft.directory/groupSettingTemplates/basic/read | 读取 Azure Active Directory 中 groupSettingTemplates 的基本属性。 |

### <a name="directory-readers-permissions"></a>目录读取者权限
可以读取基本目录信息。 用于授予对应用程序的访问权限，不针对用户。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/administrativeUnits/basic/read | 读取 Azure Active Directory 中 administrativeUnits 的基本属性。 |
| microsoft.directory/administrativeUnits/members/read | 读取 Azure Active Directory 中的 administrativeUnits.members 属性。 |
| microsoft.directory/applications/basic/read | 读取 Azure Active Directory 中应用程序的基本属性。 |
| microsoft.directory/applications/owners/read | 读取 Azure Active Directory 中的 applications.owners 属性。 |
| microsoft.directory/applications/policies/read | 读取 Azure Active Directory 中的 applications.policies 属性。 |
| microsoft.directory/contacts/basic/read | 读取 Azure Active Directory 中联系人的基本属性。 |
| microsoft.directory/contacts/memberOf/read | 读取 Azure Active Directory 中的 contacts.memberOf 属性。 |
| microsoft.directory/contracts/basic/read | 读取 Azure Active Directory 中协定的基本属性。 |
| microsoft.directory/devices/basic/read | 读取 Azure Active Directory 中设备的基本属性。 |
| microsoft.directory/devices/memberOf/read | 读取 Azure Active Directory 中的 devices.memberOf 属性。 |
| microsoft.directory/devices/registeredOwners/read | 读取 Azure Active Directory 中的 devices.registeredOwners 属性。 |
| microsoft.directory/devices/registeredUsers/read | 读取 Azure Active Directory 中的 devices.registeredUsers 属性。 |
| microsoft.directory/directoryRoles/basic/read | 读取 Azure Active Directory 中 directoryRoles 的基本属性。 |
| microsoft.directory/directoryRoles/eligibleMembers/read | 读取 Azure Active Directory 中的 directoryRoles.eligibleMembers 属性。 |
| microsoft.directory/directoryRoles/members/read | 读取 Azure Active Directory 中的 directoryRoles.members 属性。 |
| microsoft.directory/domains/basic/read | 读取 Azure Active Directory 中域的基本属性。 |
| microsoft.directory/groups/appRoleAssignments/read | 读取 Azure Active Directory 中的 groups.appRoleAssignments 属性。 |
| microsoft.directory/groups/basic/read | 读取 Azure Active Directory 中组的基本属性。 |
| microsoft.directory/groups/memberOf/read | 读取 Azure Active Directory 中的 groups.memberOf 属性。 |
| microsoft.directory/groups/members/read | 读取 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.directory/groups/owners/read | 读取 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft.directory/groups/settings/read | 读取 Azure Active Directory 中的 groups.settings 属性。 |
| microsoft.directory/groupSettings/basic/read | 读取 Azure Active Directory 中 groupSettings 的基本属性。 |
| microsoft.directory/groupSettingTemplates/basic/read | 读取 Azure Active Directory 中 groupSettingTemplates 的基本属性。 |
| microsoft.directory/oAuth2PermissionGrants/basic/read | 读取 Azure Active Directory 中 oAuth2PermissionGrants 的基本属性。 |
| microsoft.directory/organization/basic/read | 读取 Azure Active Directory 中组织的基本属性。 |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | 读取 Azure Active Directory 中的 organization.trustedCAsForPasswordlessAuth 属性。 |
| microsoft.directory/roleAssignments/basic/read | 读取 Azure Active Directory 中 roleAssignments 上的基本属性。 |
| microsoft.directory/roleDefinitions/basic/read | 读取 Azure Active Directory 中 roleDefinitions 上的基本属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | 读取 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | 读取 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 属性。 |
| microsoft.directory/servicePrincipals/basic/read | 读取 Azure Active Directory 中 servicePrincipals 的基本属性。 |
| microsoft.directory/servicePrincipals/memberOf/read | 读取 Azure Active Directory 中的 servicePrincipals.memberOf 属性。 |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | 读取 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 属性。 |
| microsoft.directory/servicePrincipals/ownedObjects/read | 读取 Azure Active Directory 中的 servicePrincipals.ownedObjects 属性。 |
| microsoft.directory/servicePrincipals/owners/read | 读取 Azure Active Directory 中的 servicePrincipals.owners 属性。 |
| microsoft.directory/servicePrincipals/policies/read | 读取 Azure Active Directory 中的 servicePrincipals.policies 属性。 |
| microsoft.directory/subscribedSkus/basic/read | 读取 Azure Active Directory 中 subscribedSkus 的基本属性。 |
| microsoft.directory/users/appRoleAssignments/read | 读取 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft.directory/users/basic/read | 读取 Azure Active Directory 中用户的基本属性。 |
| microsoft.directory/users/directReports/read | 读取 Azure Active Directory 中的 users.directReports 属性。 |
| microsoft.directory/users/manager/read | 读取 Azure Active Directory 中的 users.manager 属性。 |
| microsoft.directory/users/memberOf/read | 读取 Azure Active Directory 中的 users.memberOf 属性。 |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | 读取 Azure Active Directory 中的 users.oAuth2PermissionGrants 属性。 |
| microsoft.directory/users/ownedDevices/read | 读取 Azure Active Directory 中的 users.ownedDevices 属性。 |
| microsoft.directory/users/ownedObjects/read | 读取 Azure Active Directory 中的 users.ownedObjects 属性。 |
| microsoft.directory/users/registeredDevices/read | 读取 Azure Active Directory 中的 users.registeredDevices 属性。 |

### <a name="directory-synchronization-accounts-permissions"></a>目录同步帐户权限

仅供 Azure AD Connect 服务使用。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/organization/dirSync/update | 更新 Azure Active Directory 中的 organization.dirSync 属性。 |
| microsoft.directory/policies/create | 在 Azure Active Directory 中创建策略。 |
| microsoft.directory/policies/delete | 删除 Azure Active Directory 中的策略。 |
| microsoft. directory/策略/基本/读取 | 读取 Azure Active Directory 中策略的基本属性。 |
| microsoft.directory/policies/basic/update | 更新 Azure Active Directory 中策略的基本属性。 |
| microsoft. directory/策略/所有者/读取 | 读取 Azure Active Directory 中的 policies.owners 属性。 |
| microsoft.directory/policies/owners/update | 更新 Azure Active Directory 中的 policies.owners 属性。 |
| microsoft. directory/策略/policiesAppliedTo/read | 读取 Azure Active Directory 中的 policies.policiesAppliedTo 属性。 |
| microsoft.directory/policies/tenantDefault/update | 更新 Azure Active Directory 中的 policies.tenantDefault 属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | 读取 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | 读取 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 属性。 |
| microsoft.directory/servicePrincipals/audience/update | 更新 Azure Active Directory 中的 servicePrincipals.audience 属性。 |
| microsoft.directory/servicePrincipals/authentication/update | 更新 Azure Active Directory 中的 servicePrincipals.authentication 属性。 |
| microsoft.directory/servicePrincipals/basic/read | 读取 Azure Active Directory 中 servicePrincipals 的基本属性。 |
| microsoft.directory/servicePrincipals/basic/update | 更新 Azure Active Directory 中 servicePrincipals 的基本属性。 |
| microsoft.directory/servicePrincipals/create | 在 Azure Active Directory 中创建 servicePrincipals。 |
| microsoft.directory/servicePrincipals/credentials/update | 更新 Azure Active Directory 中的 servicePrincipals.credentials 属性。 |
| microsoft.directory/servicePrincipals/memberOf/read | 读取 Azure Active Directory 中的 servicePrincipals.memberOf 属性。 |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | 读取 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 属性。 |
| microsoft.directory/servicePrincipals/owners/read | 读取 Azure Active Directory 中的 servicePrincipals.owners 属性。 |
| microsoft.directory/servicePrincipals/owners/update | 更新 Azure Active Directory 中的 servicePrincipals.owners 属性。 |
| microsoft.directory/servicePrincipals/ownedObjects/read | 读取 Azure Active Directory 中的 servicePrincipals.ownedObjects 属性。 |
| microsoft.directory/servicePrincipals/permissions/update | 更新 Azure Active Directory 中的 servicePrincipals.permissions 属性。 |
| microsoft.directory/servicePrincipals/policies/read | 读取 Azure Active Directory 中的 servicePrincipals.policies 属性。 |
| microsoft.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 属性。 |
| microsoft.directorySync/allEntities/allTasks | 在 Azure AD Connect 中执行所有操作。 |

### <a name="directory-writers-permissions"></a>目录写入者权限

可以读取和写入基本目录信息。 用于授予对应用程序的访问权限，不针对用户。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/groups/create | 在 Azure Active Directory 中创建组。 |
| microsoft.directory/groups/createAsOwner | 在 Azure Active Directory 中创建组。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.directory/groups/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.appRoleAssignments 属性。 |
| microsoft.directory/groups/basic/update | 更新 Azure Active Directory 中组的基本属性。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 属性。 |
| groupSettings/basic/update | 更新 Azure Active Directory 中 groupSettings 的基本属性。 |
| groupSettings/create | 在 Azure Active Directory 中创建 groupSettings 属性。 |
| groupSettings/删除 | 删除 Azure Active Directory 中的 groupSettings。 |
| microsoft.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft.directory/users/assignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft.directory/users/basic/update | 更新 Azure Active Directory 中用户的基本属性。 |
| microsoft.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 属性。 |
| microsoft.directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 属性。 |

### <a name="exchange-service-administrator-permissions"></a>Exchange Service Administrator permissions

可以管理 Exchange 产品的所有方面。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.directory/groups/unified/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.unified 属性。 |
| microsoft.directory/groups/unified/basic/update | 更新 Office 365 组的基本属性。 |
| microsoft.directory/groups/unified/create | 创建 Office 365 组。 |
| microsoft.directory/groups/unified/delete | 删除 Office 365 组。 |
| microsoft.directory/groups/unified/members/update | 更新 Office 365 组的成员身份。 |
| microsoft.directory/groups/unified/owners/update | 更新 Office 365 组的所有权。 |
| microsoft.office365.exchange/allEntities/allTasks | 管理 Exchange Online 的各个方面。 |
| microsoft.office365.network/performance/allProperties/read | 在 M365 管理中心内读取网络性能页。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |
| microsoft.office365.usageReports/allEntities/read | 阅读 Office 365 使用情况报告。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |

### <a name="external-identity-provider-administrator-permissions"></a>外部标识提供者管理员权限

配置直接联合身份验证中使用的标识提供者。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | 读取和配置 Azure Active Directory B2C 中的标识提供者。 |

### <a name="global-reader-permissions"></a>全局读取者权限
可以读取全局管理员可以读取的所有内容，但不能编辑任何内容。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的[角色说明](#global-reader)。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.commerce.billing/allEntities/read    | 阅读帐单的所有方面。 |
| microsoft.directory/administrativeUnits/basic/read    | 读取 Azure Active Directory 中 administrativeUnits 的基本属性。 |
| microsoft.directory/administrativeUnits/members/read    | 读取 Azure Active Directory 中的 administrativeUnits.members 属性。 |
| microsoft.directory/applications/basic/read    | 读取 Azure Active Directory 中应用程序的基本属性。 |
| microsoft.directory/applications/owners/read    | 读取 Azure Active Directory 中的 applications.owners 属性。 |
| microsoft.directory/applications/policies/read    | 读取 Azure Active Directory 中的 applications.policies 属性。 |
| microsoft.directory/contacts/basic/read    | 读取 Azure Active Directory 中联系人的基本属性。 |
| microsoft.directory/contacts/memberOf/read    | 读取 Azure Active Directory 中的 contacts.memberOf 属性。 |
| microsoft.directory/contracts/basic/read    | 读取 Azure Active Directory 中协定的基本属性。 |
| microsoft.directory/devices/basic/read    | 读取 Azure Active Directory 中设备的基本属性。 |
| microsoft.directory/devices/memberOf/read    | 读取 Azure Active Directory 中的 devices.memberOf 属性。 |
| microsoft.directory/devices/registeredOwners/read    | 读取 Azure Active Directory 中的 devices.registeredOwners 属性。 |
| microsoft.directory/devices/registeredUsers/read    | 读取 Azure Active Directory 中的 devices.registeredUsers 属性。 |
| microsoft.directory/directoryRoles/basic/read    | 读取 Azure Active Directory 中 directoryRoles 的基本属性。 |
| microsoft.directory/directoryRoles/eligibleMembers/read    | 读取 Azure Active Directory 中的 directoryRoles.eligibleMembers 属性。 |
| microsoft.directory/directoryRoles/members/read    | 读取 Azure Active Directory 中的 directoryRoles.members 属性。 |
| microsoft.directory/domains/basic/read    | 读取 Azure Active Directory 中域的基本属性。 |
| microsoft.directory/groups/appRoleAssignments/read    | 读取 Azure Active Directory 中的 groups.appRoleAssignments 属性。 |
| microsoft.directory/groups/basic/read    | 读取 Azure Active Directory 中组的基本属性。 |
| microsoft.directory/groups/hiddenMembers/read    | 读取 Azure Active Directory 中的 groups.hiddenMembers 属性。 |
| microsoft.directory/groups/memberOf/read    | 读取 Azure Active Directory 中的 groups.memberOf 属性。 |
| microsoft.directory/groups/members/read    | 读取 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.directory/groups/owners/read    | 读取 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft.directory/groups/settings/read    | 读取 Azure Active Directory 中的 groups.settings 属性。 |
| microsoft.directory/groupSettings/basic/read    | 读取 Azure Active Directory 中 groupSettings 的基本属性。 |
| microsoft.directory/groupSettingTemplates/basic/read    | 读取 Azure Active Directory 中 groupSettingTemplates 的基本属性。 |
| microsoft.directory/oAuth2PermissionGrants/basic/read    | 读取 Azure Active Directory 中 oAuth2PermissionGrants 的基本属性。 |
| microsoft.directory/organization/basic/read    | 读取 Azure Active Directory 中组织的基本属性。 |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read    | 读取 Azure Active Directory 中的 organization.trustedCAsForPasswordlessAuth 属性。 |
| microsoft.directory/policies/standard/read    | 读取 Azure Active Directory 中的标准策略。 |
| microsoft.directory/roleAssignments/basic/read    | 读取 Azure Active Directory 中 roleAssignments 上的基本属性。 |
| microsoft.directory/roleDefinitions/basic/read    | 读取 Azure Active Directory 中 roleDefinitions 上的基本属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read    | 读取 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/read    | 读取 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 属性。 |
| microsoft.directory/servicePrincipals/basic/read    | 读取 Azure Active Directory 中 servicePrincipals 的基本属性。 |
| microsoft.directory/servicePrincipals/memberOf/read    | 读取 Azure Active Directory 中的 servicePrincipals.memberOf 属性。 |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read    | 读取 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 属性。 |
| microsoft.directory/servicePrincipals/ownedObjects/read    | 读取 Azure Active Directory 中的 servicePrincipals.ownedObjects 属性。 |
| microsoft.directory/servicePrincipals/owners/read    | 读取 Azure Active Directory 中的 servicePrincipals.owners 属性。 |
| microsoft.directory/servicePrincipals/policies/read    | 读取 Azure Active Directory 中的 servicePrincipals.policies 属性。 |
| microsoft.directory/signInReports/allProperties/read    | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |
| microsoft.directory/subscribedSkus/basic/read    | 读取 Azure Active Directory 中 subscribedSkus 的基本属性。 |
| microsoft.directory/users/appRoleAssignments/read    | 读取 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft.directory/users/basic/read    | 读取 Azure Active Directory 中用户的基本属性。 |
| microsoft.directory/users/directReports/read    | 读取 Azure Active Directory 中的 users.directReports 属性。 |
| microsoft.directory/users/manager/read    | 读取 Azure Active Directory 中的 users.manager 属性。 |
| microsoft.directory/users/memberOf/read    | 读取 Azure Active Directory 中的 users.memberOf 属性。 |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read    | 读取 Azure Active Directory 中的 users.oAuth2PermissionGrants 属性。 |
| microsoft.directory/users/ownedDevices/read    | 读取 Azure Active Directory 中的 users.ownedDevices 属性。 |
| microsoft.directory/users/ownedObjects/read    | 读取 Azure Active Directory 中的 users.ownedObjects 属性。 |
| microsoft.directory/users/registeredDevices/read    | 读取 Azure Active Directory 中的 users.registeredDevices 属性。 |
| microsoft.directory/users/strongAuthentication/read    | 读取强身份验证属性，如 MFA 凭据信息。 |
| microsoft.office365.exchange/allEntities/read    | 读取 Exchange Online 的各个方面。 |
| microsoft.office365.messageCenter/messages/read    | 读取 microsoft.office365.messageCenter 中的消息。 |
| microsoft.office365.messageCenter/securityMessages/read    | 读取 microsoft.office365.messageCenter 中的安全消息。 |
| microsoft.office365.network/performance/allProperties/read | 在 M365 管理中心内读取网络性能页。 |
| microsoft.office365.protectionCenter/allEntities/read    | 读取 Office 365 防护中心的各个方面。 |
| microsoft.office365.securityComplianceCenter/allEntities/read    | 读取 microsoft.office365.securityComplianceCenter 中的所有标准属性。 |
| microsoft.office365.usageReports/allEntities/read    | 阅读 Office 365 使用情况报告。 |
| microsoft.office365.webPortal/allEntities/standard/read    | 读取 microsoft.office365.webPortal 中所有资源的标准属性。 |

### <a name="groups-administrator-permissions"></a>组管理员权限
可以管理组和组设置的所有方面，如命名策略和过期策略。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/groups/basic/read | 读取 Azure Active Directory 中组的标准属性。  |
| microsoft.directory/groups/basic/update | 更新 Azure Active Directory 中组的基本属性。 |
| microsoft.directory/groups/create | 在 Azure Active Directory 中创建组。 |
| microsoft.directory/groups/createAsOwner | 在 Azure Active Directory 中创建组。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.directory/groups/delete | 删除 Azure Active Directory 中的组。 |
| microsoft.directory/groups/hiddenMembers/read | 读取 Azure Active Directory 中的 groups.hiddenMembers 属性。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft.directory/groups/restore | 还原 Azure Active Directory 中的组。 |
| microsoft.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 属性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.messageCenter/messages/read | 读取 microsoft.office365.messageCenter 中的消息。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="guest-inviter-permissions"></a>来宾邀请者权限
可以无视“成员可邀请来宾”设置而邀请来宾用户。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/users/appRoleAssignments/read | 读取 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft.directory/users/basic/read | 读取 Azure Active Directory 中用户的基本属性。 |
| microsoft.directory/users/directReports/read | 读取 Azure Active Directory 中的 users.directReports 属性。 |
| microsoft.directory/users/inviteGuest | 邀请 Azure Active Directory 中的来宾用户。 |
| microsoft.directory/users/manager/read | 读取 Azure Active Directory 中的 users.manager 属性。 |
| microsoft.directory/users/memberOf/read | 读取 Azure Active Directory 中的 users.memberOf 属性。 |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | 读取 Azure Active Directory 中的 users.oAuth2PermissionGrants 属性。 |
| microsoft.directory/users/ownedDevices/read | 读取 Azure Active Directory 中的 users.ownedDevices 属性。 |
| microsoft.directory/users/ownedObjects/read | 读取 Azure Active Directory 中的 users.ownedObjects 属性。 |
| microsoft.directory/users/registeredDevices/read | 读取 Azure Active Directory 中的 users.registeredDevices 属性。 |

### <a name="helpdesk-administrator-permissions"></a>支持管理员权限

可以重置非管理员和支持理员的密码。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | 读取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 属性。 |
| microsoft.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft.directory/users/password/update | 更新 Azure Active Directory 中所有用户的密码。 有关详细信息，请参阅联机文档。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="hybrid-identity-administrator-permissions"></a>混合标识管理员权限

启用、部署、配置、管理、监视云配置和身份验证服务，并排除它们的故障。 

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.directory/applications/audience/update  | 更新 Azure Active Directory 中的 applications.audience 属性。 |
| microsoft.directory/applications/authentication/update | 更新 Azure Active Directory 中的 applications.authentication 属性。  |
| microsoft.directory/applications/basic/update | 更新 Azure Active Directory 中应用程序的基本属性。 |
| microsoft.directory/applications/create | 在 Azure Active Directory 中创建应用程序。 |
| microsoft.directory/applications/credentials/update | 更新 Azure Active Directory 中的 applications.credentials 属性。 |
| microsoft.directory/applications/delete | 删除 Azure Active Directory 中的应用程序。 |
| microsoft.directory/applications/owners/update | 更新 Azure Active Directory 中的 applications.owners 属性。 |
| microsoft.directory/applications/permissions/update | 更新 Azure Active Directory 中的 applications.permissions 属性。 |
| microsoft.directory/applications/policies/update | 更新 Azure Active Directory 中的 applications.policies 属性。 |
| microsoft.directory/applicationTemplates/instantiate | 从应用程序模板实例化库应用程序。 |
| microsoft.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| microsoft.directory/cloudProvisioning/allProperties/allTasks | 读取和配置 Azure AD 云配置服务的所有属性。 |
| microsoft.directory/federatedAuthentication/allProperties/allTasks | 在 Azure AD 中管理 Active Directory 联合身份验证服务 (ADFS) 或第三方联合身份验证提供程序的所有方面。 |
| microsoft.directory/organization/dirSync/update | 更新 Azure Active Directory 中的 organization.dirSync 属性。 |
| microsoft.directory/passwordHashSync/allProperties/allTasks | 在 Azure AD 中管理密码哈希同步 (PHS) 的所有方面。 |
| microsoft.directory/passThroughAuthentication/allProperties/allTasks | 在 Azure AD 中管理直通身份验证 (PTA) 的所有方面。 |
| microsoft.directory/seamlessSSO/allProperties/allTasks | 在 Azure AD 中管理无缝单一登录 (SSO) 的所有方面。 |
| microsoft.directory/servicePrincipals/audience/update | 更新 Azure Active Directory 中的 servicePrincipals.audience 属性。 |
| microsoft.directory/servicePrincipals/authentication/update | 更新 Azure Active Directory 中的 servicePrincipals.authentication 属性。 |
| microsoft.directory/servicePrincipals/basic/update | 更新 Azure Active Directory 中 servicePrincipals 的基本属性。 |
| microsoft.directory/servicePrincipals/create | 在 Azure Active Directory 中创建 servicePrincipals。 |
| microsoft.directory/servicePrincipals/credentials/update | 更新 Azure Active Directory 中的 servicePrincipals.credentials 属性。 |
| microsoft.directory/servicePrincipals/delete | 删除 Azure Active Directory 中的 servicePrincipals。 |
| microsoft.directory/servicePrincipals/owners/update | 更新 Azure Active Directory 中的 servicePrincipals.owners 属性。 |
| microsoft.directory/servicePrincipals/permissions/update | 更新 Azure Active Directory 中的 servicePrincipals.permissions 属性。 |
| microsoft.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 属性。 |
| microsoft.directory/servicePrincipals/synchronizationJobs/manage | 在 Azure AD 中管理同步作业的所有方面。 |
| microsoft.directory/servicePrincipals/synchronizationSchema/manage | 在 Azure AD 中管理同步架构的所有方面。 |
| microsoft.directory/servicePrincipals/synchronizationCredentials/manage | 在 Azure AD 中管理同步凭据的所有方面。 |
| microsoft.directory/servicePrincipals/tag/update | 更新 Azure Active Directory 中的 servicePrincipals.tag 属性。 |
| microsoft.directory/signInReports/allProperties/read | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |
| microsoft.office365.messageCenter/messages/read | 读取 microsoft.office365.messageCenter 中的消息。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |


### <a name="intune-service-administrator-permissions"></a>Intune 服务管理员权限

可以管理 Intune 产品的所有方面。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/contacts/basic/update | 更新 Azure Active Directory 中联系人的基本属性。 |
| microsoft.directory/contacts/create | 在 Azure Active Directory 中创建联系人。 |
| microsoft.directory/contacts/delete | 删除 Azure Active Directory 中的联系人。 |
| microsoft.directory/devices/basic/update | 更新 Azure Active Directory 中设备的基本属性。 |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | 读取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 属性。 |
| microsoft.directory/devices/create | 在 Azure Active Directory 中创建设备。 |
| microsoft.directory/devices/delete | 删除 Azure Active Directory 中的设备。 |
| microsoft.directory/devices/registeredOwners/update | 更新 Azure Active Directory 中的 devices.registeredOwners 属性。 |
| microsoft.directory/devices/registeredUsers/update | 更新 Azure Active Directory 中的 devices.registeredUsers 属性。 |
| microsoft.directory/groups/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.appRoleAssignments 属性。 |
| microsoft.directory/groups/basic/update | 更新 Azure Active Directory 中组的基本属性。 |
| microsoft.directory/groups/create | 在 Azure Active Directory 中创建组。 |
| microsoft.directory/groups/createAsOwner | 在 Azure Active Directory 中创建组。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.directory/groups/delete | 删除 Azure Active Directory 中的组。 |
| microsoft.directory/groups/hiddenMembers/read | 读取 Azure Active Directory 中的 groups.hiddenMembers 属性。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft.directory/groups/restore | 还原 Azure Active Directory 中的组。 |
| microsoft.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 属性。 |
| microsoft.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft.directory/users/basic/update | 更新 Azure Active Directory 中用户的基本属性。 |
| microsoft.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 属性。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.intune/allEntities/allTasks | 管理 Intune 的各个方面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |

### <a name="kaizala-administrator-permissions"></a>Kaizala 管理员权限

可以管理 Microsoft Kaizala 的设置。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 Office 365 管理中心。 |

### <a name="license-administrator-permissions"></a>许可证管理员权限

可以管理用户和组的产品许可证。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/users/assignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft.directory/users/usageLocation/update | 更新 Azure Active Directory 中的 users.usageLocation 属性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |

### <a name="lync-service-administrator-permissions"></a>Lync 服务管理员权限

可以管理 Skype for Business 产品的所有方面。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理 Skype for Business Online 的各个方面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |
| microsoft.office365.usageReports/allEntities/read    | 阅读 Office 365 使用情况报告。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |


### <a name="message-center-privacy-reader-permissions"></a>消息中心隐私读取者权限

可以读取消息中心发布内容、数据隐私消息、组、域和订阅。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.messageCenter/messages/read | 读取 microsoft.office365.messageCenter 中的消息。 |
| microsoft.office365.messageCenter/securityMessages/read | 读取 microsoft.office365.messageCenter 中的安全消息。 |

### <a name="message-center-reader-permissions"></a>消息中心读取者权限
只能在 Office 365 消息中心查看其组织的消息和更新。 

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.messageCenter/messages/read | 读取 microsoft.office365.messageCenter 中的消息。 |

### <a name="network-administrator-permissions"></a>网络管理员权限
可以管理网络位置，并审阅有关 Microsoft 365 软件即服务应用程序的企业网络设计见解。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.office365.network/performance/allProperties/read | 在 M365 管理中心内读取网络性能页。  |
| microsoft.office365.network/locations/allProperties/allTasks | 读取和配置每个位置的网络位置属性。 |

### <a name="office-apps-administrator-permissions"></a>Office 应用管理员权限
可以管理 Office 应用的云服务，包括策略和设置管理，并管理对最终用户的设备的选择、取消选择和发布 "新增功能" 功能内容的功能。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.messageCenter/messages/read | 读取 microsoft.office365.messageCenter 中的消息。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |
| microsoft.office365.usageReports/allEntities/read | 阅读 Office 365 使用情况报告。 |
| office365. userCommunication/allEntities/allTasks | 阅读并更新新增的消息可见性。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |

### <a name="partner-tier1-support-permissions"></a>合作伙伴 Tier1 支持权限

不要使用 - 不适用于常规用途。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/contacts/basic/update | 更新 Azure Active Directory 中联系人的基本属性。 |
| microsoft.directory/contacts/create | 在 Azure Active Directory 中创建联系人。 |
| microsoft.directory/contacts/delete | 删除 Azure Active Directory 中的联系人。 |
| microsoft.directory/groups/create | 在 Azure Active Directory 中创建组。 |
| microsoft.directory/groups/createAsOwner | 在 Azure Active Directory 中创建组。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft.directory/users/assignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft.directory/users/basic/update | 更新 Azure Active Directory 中用户的基本属性。 |
| microsoft.directory/users/delete | 删除 Azure Active Directory 中的用户。 |
| microsoft.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 属性。 |
| microsoft.directory/users/password/update | 更新 Azure Active Directory 中所有用户的密码。 有关详细信息，请参阅联机文档。 |
| microsoft.directory/users/restore | 还原 Azure Active Directory 中已删除的用户。 |
| microsoft.directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 属性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="partner-tier2-support-permissions"></a>合作伙伴 Tier2 支持权限

不要使用 - 不适用于常规用途。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/contacts/basic/update | 更新 Azure Active Directory 中联系人的基本属性。 |
| microsoft.directory/contacts/create | 在 Azure Active Directory 中创建联系人。 |
| microsoft.directory/contacts/delete | 删除 Azure Active Directory 中的联系人。 |
| microsoft. directory/域/allTasks | 创建和删除域，然后读取和更新 Azure Active Directory 中的标准属性。 |
| microsoft.directory/groups/create | 在 Azure Active Directory 中创建组。 |
| microsoft.directory/groups/delete | 删除 Azure Active Directory 中的组。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.directory/groups/restore | 还原 Azure Active Directory 中的组。 |
| microsoft.directory/organization/basic/update | 更新 Azure Active Directory 中组织的基本属性。 |
| microsoft.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft.directory/users/assignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft.directory/users/basic/update | 更新 Azure Active Directory 中用户的基本属性。 |
| microsoft.directory/users/delete | 删除 Azure Active Directory 中的用户。 |
| microsoft.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 属性。 |
| microsoft.directory/users/password/update | 更新 Azure Active Directory 中所有用户的密码。 有关详细信息，请参阅联机文档。 |
| microsoft.directory/users/restore | 还原 Azure Active Directory 中已删除的用户。 |
| microsoft.directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 属性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="password-administrator-permissions"></a>密码管理员权限

可以为非管理员和密码管理员重置密码。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/users/password/update | 更新 Azure Active Directory 中所有用户的密码。 有关详细信息，请参阅联机文档。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |

### <a name="power-bi-service-administrator-permissions"></a>Power BI 服务管理员权限

可以管理 Power BI 产品的所有方面。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
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


### <a name="power-platform-administrator-permissions"></a>Power Platform 管理员权限

可以创建和管理 Microsoft Dynamics 365、PowerApps 和 Microsoft Flow 的所有方面。 

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>
| **操作** | **说明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.dynamics365/allEntities/allTasks | 管理 Dynamics 365 的各个方面。 |
| microsoft.flow/allEntities/allTasks | 管理 Microsoft Flow 的所有方面。 |
| microsoft.powerApps/allEntities/allTasks | 管理 PowerApps 的各个方面。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="printer-administrator-permissions"></a>打印机管理员权限

可以管理打印机和打印机连接器的所有方面。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>
| **操作** | **说明** |
| --- | --- |
| microsoft.azure.print/allEntities/allProperties/allTasks | 在 Microsoft Print 中创建和删除打印机和连接器，并读取和更新所有属性。 |

### <a name="printer-technician-permissions"></a>打印机技术人员权限

可以注册和取消注册打印机，并更新打印机状态。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>
| **操作** | **说明** |
| --- | --- |
| microsoft.azure.print/connectors/allProperties/read | 在 Microsoft Print 中读取连接器的所有属性。 |
| microsoft.azure.print/printers/allProperties/read | 在 Microsoft Print 中读取打印机的所有属性。 |
| microsoft.azure.print/printers/basic/update | 在 Microsoft Print 中更新打印机的基本属性。 |
| microsoft.azure.print/printers/register | 在 Microsoft Print 中注册打印机。 |
| microsoft.azure.print/printers/unregister | 在 Microsoft Print 中取消注册打印机。 |

### <a name="privileged-authentication-administrator-permissions"></a>特权身份验证管理员权限

允许查看、设置和重置任何用户（管理员或非管理员）的身份验证方法信息。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft.directory/users/strongAuthentication/update | 更新强身份验证属性，如 MFA 凭据信息。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |
| microsoft.directory/users/password/update | 更新 Office 365 组织中所有用户的密码。 有关详细信息，请参阅联机文档。 |

### <a name="privileged-role-administrator-permissions"></a>特权角色管理员权限

可以管理 Azure AD 中的角色分配和 Privileged Identity Management 的所有方面。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | 创建和删除所有资源，然后读取和更新 microsoft.aad.privilegedIdentityManagement 中的标准属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/allTasks | 读取和配置 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 属性。 |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | 读取和配置 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 属性。 |
| microsoft.directory/administrativeUnits/allProperties/allTasks | 创建和管理管理单元（包括成员） |
| microsoft.directory/roleAssignments/allProperties/allTasks | 创建和管理角色分配。 |
| microsoft.directory/roleDefinitions/allProperties/allTasks | 创建和管理角色定义。 |

### <a name="reports-reader-permissions"></a>报告读取者权限

可以读取登录和审核报告。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| microsoft.directory/signInReports/allProperties/read | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.office365.usageReports/allEntities/read | 阅读 Office 365 使用情况报告。 |

### <a name="search-administrator-permissions"></a>搜索管理员权限

可以创建和管理 Microsoft 搜索设置的各个方面。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | 读取 microsoft.office365.messageCenter 中的消息。 |
| microsoft.office365.search/allEntities/allProperties/allTasks | 创建和删除所有资源，以及读取和更新 microsoft.office365.search 中的所有属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |

### <a name="search-editor-permissions"></a>搜索编辑员权限

可以创建和管理编辑内容，例如书签、问答、位置和平面布置图。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | 读取 microsoft.office365.messageCenter 中的消息。 |
| microsoft.office365.search/content/allProperties/allTasks | 创建和删除内容，以及读取和更新 microsoft.office365.search 中的所有属性。 |

### <a name="security-administrator-permissions"></a>安全管理员权限

可以读取安全信息和报表，以及管理 Azure AD 和 Office 365 中的配置。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/applications/policies/update | 更新 Azure Active Directory 中的 applications.policies 属性。 |
| microsoft.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | 读取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 属性。 |
| microsoft.directory/policies/basic/update | 更新 Azure Active Directory 中策略的基本属性。 |
| microsoft.directory/policies/create | 在 Azure Active Directory 中创建策略。 |
| microsoft.directory/policies/delete | 删除 Azure Active Directory 中的策略。 |
| microsoft.directory/policies/owners/update | 更新 Azure Active Directory 中的 policies.owners 属性。 |
| microsoft.directory/policies/tenantDefault/update | 更新 Azure Active Directory 中的 policies.tenantDefault 属性。 |
| microsoft.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 属性。 |
| microsoft.directory/signInReports/allProperties/read | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |
| microsoft.aad.identityProtection/allEntities/read | 读取 microsoft.aad.identityProtection 中的所有资源。 |
| microsoft.aad.identityProtection/allEntities/update | 更新 microsoft.aad.identityProtection 中的所有资源。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 读取 microsoft.aad.privilegedIdentityManagement 中的所有资源。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.protectionCenter/allEntities/read | 读取 Office 365 防护中心的各个方面。 |
| microsoft.office365.protectionCenter/allEntities/update | 更新 microsoft.office365.protectionCenter 中的所有资源。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |

### <a name="security-operator-permissions"></a>安全操作员权限

创建和管理安全事件。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | 读取和配置 Microsoft Cloud App Security。 |
| microsoft.aad.identityProtection/allEntities/read | 读取 microsoft.aad.identityProtection 中的所有资源。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 读取 microsoft.aad.privilegedIdentityManagement 中的所有资源。 |
| microsoft.azure.advancedThreatProtection/allEntities/read | 读取和配置 Azure AD 高级威胁防护。 |
| microsoft.intune/allEntities/allTasks | 管理 Intune 的各个方面。 |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | 读取和配置安全与合规中心。 |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | 读取和配置 Windows Defender 高级威胁防护。 |

### <a name="security-reader-permissions"></a>安全读取者权限

可以读取 Azure AD 和 Office 365 中的安全信息和报表。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | 读取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 属性。 |
| microsoft.directory/policies/conditionalAccess/basic/read | 读取 Azure Active Directory 中的 policies.conditionalAccess 属性。 |
| microsoft.directory/signInReports/allProperties/read | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |
| microsoft.aad.identityProtection/allEntities/read | 读取 microsoft.aad.identityProtection 中的所有资源。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 读取 microsoft.aad.privilegedIdentityManagement 中的所有资源。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.protectionCenter/allEntities/read | 读取 Office 365 防护中心的各个方面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |

### <a name="service-support-administrator-permissions"></a>服务支持管理员权限

可以读取服务运行状况信息和管理支持票证。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="sharepoint-service-administrator-permissions"></a>SharePoint 服务管理员权限

可以管理 SharePoint 服务的所有方面。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.directory/groups/unified/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.unified 属性。 |
| microsoft.directory/groups/unified/basic/update | 更新 Office 365 组的基本属性。 |
| microsoft.directory/groups/unified/create | 创建 Office 365 组。 |
| microsoft.directory/groups/unified/delete | 删除 Office 365 组。 |
| microsoft.directory/groups/unified/members/update | 更新 Office 365 组的成员身份。 |
| microsoft.directory/groups/unified/owners/update | 更新 Office 365 组的所有权。 |
| microsoft.office365.network/performance/allProperties/read | 在 M365 管理中心内读取网络性能页。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.sharepoint/allEntities/allTasks | 创建和删除所有资源，然后读取和更新 microsoft.office365.sharepoint 中的标准属性。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |
| microsoft.office365.usageReports/allEntities/read    | 阅读 Office 365 使用情况报告。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |

### <a name="teams-communications-administrator-permissions"></a>Teams 通信管理员权限

可以管理 Microsoft Teams 服务中的通话和会议功能。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
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

### <a name="teams-communications-support-engineer-permissions"></a>Teams 通信支持工程师权限

可以使用高级工具排查 Teams 中的通信问题。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |

### <a name="teams-communications-support-specialist-permissions"></a>Teams 通信支持专家权限

可以使用基本工具排查 Teams 中的通信问题。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |

### <a name="teams-service-administrator-permissions"></a>Teams 服务管理员权限

可以管理 Microsoft Teams 服务。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.directory/groups/hiddenMembers/read | 读取 Azure Active Directory 中的 groups.hiddenMembers 属性。 |
| microsoft.directory/groups/unified/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.unified 属性。 |
| microsoft.directory/groups/unified/basic/update | 更新 Office 365 组的基本属性。 |
| microsoft.directory/groups/unified/create | 创建 Office 365 组。 |
| microsoft.directory/groups/unified/delete | 删除 Office 365 组。 |
| microsoft.directory/groups/unified/members/update | 更新 Office 365 组的成员身份。 |
| microsoft.directory/groups/unified/owners/update | 更新 Office 365 组的所有权。 |
| microsoft.office365.network/performance/allProperties/read | 在 M365 管理中心内读取网络性能页。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |
| microsoft.office365.usageReports/allEntities/read | 阅读 Office 365 使用情况报告。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |

### <a name="user-administrator-permissions"></a>用户管理员权限
可以管理用户和组的所有方面，包括重置有限管理员的密码。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/appRoleAssignments/create | 在 Azure Active Directory 中创建 appRoleAssignments。 |
| microsoft.directory/appRoleAssignments/delete | 删除 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.directory/appRoleAssignments/update | 更新 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.directory/contacts/basic/update | 更新 Azure Active Directory 中联系人的基本属性。 |
| microsoft.directory/contacts/create | 在 Azure Active Directory 中创建联系人。 |
| microsoft.directory/contacts/delete | 删除 Azure Active Directory 中的联系人。 |
| microsoft.directory/groups/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.appRoleAssignments 属性。 |
| microsoft.directory/groups/basic/update | 更新 Azure Active Directory 中组的基本属性。 |
| microsoft.directory/groups/create | 在 Azure Active Directory 中创建组。 |
| microsoft.directory/groups/createAsOwner | 在 Azure Active Directory 中创建组。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.directory/groups/delete | 删除 Azure Active Directory 中的组。 |
| microsoft.directory/groups/hiddenMembers/read | 读取 Azure Active Directory 中的 groups.hiddenMembers 属性。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft.directory/groups/restore | 还原 Azure Active Directory 中的组。 |
| microsoft.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 属性。 |
| microsoft.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft.directory/users/assignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft.directory/users/basic/update | 更新 Azure Active Directory 中用户的基本属性。 |
| microsoft.directory/users/create | 在 Azure Active Directory 中创建用户。 |
| microsoft.directory/users/delete | 删除 Azure Active Directory 中的用户。 |
| microsoft.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 属性。 |
| microsoft.directory/users/password/update | 更新 Azure Active Directory 中所有用户的密码。 有关详细信息，请参阅联机文档。 |
| microsoft.directory/users/restore | 还原 Azure Active Directory 中已删除的用户。 |
| microsoft.directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 属性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

## <a name="role-template-ids"></a>角色模板 ID

角色模板 ID 主要由 Microsoft Graph API 或 PowerShell 用户使用。

图形 displayName | Azure 门户显示名称 | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
应用程序管理员 | 应用程序管理员 | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
应用程序开发人员 | 应用程序开发人员 | CF1C38E5-3621-4004-A7CB-879624DCED7C
身份验证管理员 | 身份验证管理员 | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure DevOps 管理员 | Azure DevOps 管理员 | e3973bdf-4987-49ae-837a-ba8e231c7286
Azure 信息保护管理员 | Azure 信息保护管理员 | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C 用户流管理员 | B2C 用户流管理员 | 6e591065-9bad-43ed-90f3-e9424366d2f0
B2C 用户流属性管理员 | B2C 用户流属性管理员 | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
B2C IEF 密钥集管理员 | B2C IEF 密钥集管理员 | aaf43236-0c0d-4d5f-883a-6955382ac081
B2C IEF 策略管理员 | B2C IEF 策略管理员 | 3edaf663-341e-4475-9f94-5c398ef6c070
计费管理员 | 计费管理员 | b0f54661-2d74-4c50-afa3-1ec803f12efe
云应用管理员 | 云应用程序管理员 | 158c047a-c907-4556-b7ef-446551a6b5f7
云设备管理员 | 云设备管理员 | 7698a772-787b-4ac8-901f-60d6b08affd2
公司管理员 | 全局管理员 | 62e90394-69f5-4237-9190-012177145e10
符合性管理员 | 法规管理员 | 17315797-102d-40b4-93e0-432062caca18
合规性数据管理员 | 合规性数据管理员 | e6d1a23a-da11-4be4-9570-befc86d067a7
条件访问管理员 | 条件访问管理员 | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
CRM 服务管理员 | Dynamics 365 管理员 | 44367163-eba1-44c3-98af-f5787879f96a
客户密码箱访问审批者 | 客户密码箱访问审批者 | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
桌面分析管理员 | 桌面分析管理员 | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
设备管理员 | 设备管理员 | 9f06204d-73c1-4d4c-880a-6edb90606fd8
设备联接 | 已放弃 | 9c094953-4995-41c8-84c8-3ebb9b32c93f
设备管理器 | 不推荐使用 | 2b499bcd-da44-4968-8aec-78e1674fa64d
设备用户 | 已弃用 | d405c6df-0af8-4e3b-95e4-4d06e542189e
目录读者 | 目录读者 | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
目录同步帐户 | 未显示，因为不应使用它 | d29b2b05-8046-44ba-8758-1e26182fcf32
目录写入者 | 未显示，因为不应使用它 | 9360feb5-f418-4baa-8175-e2a00bac4301
Exchange 服务管理员 | Exchange 管理员 | 29232cdf-9323-42fd-ade2-1d097af3e4de
外部标识提供者管理员 | 外部标识提供者管理员 | be2f45a1-457d-42af-a067-6ec1fa63bc45
全局读取者 | 全局读取者 | f2ef992c-3afb-46b9-b7cf-a126ee74c451
组管理员 | 组管理员 | fdd7a751-b60b-444a-984c-02652fe8fa1c 
来宾邀请者 | 来宾邀请者 | 95e79109-95c0-4d8e-aee3-d01accf2d47b
支持管理员 | 支持管理员 | 729827e3-9c14-49f7-bb1b-9608f156bbb8
混合标识管理员 | 混合标识管理员 | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2
Intune 服务管理员 | Intune 管理员 | 3a2c62db-5318-420d-8d74-23affee5d9d5
Kaizala 管理员 | Kaizala 管理员 | 74ef975b-6605-40af-a5d2-b9539d836353
许可证管理员 | 许可证管理员 | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Lync 服务管理员 | Skype for Business 管理员 | 75941009-915a-4869-abe7-691bff18279e
消息中心隐私读取者 | 消息中心隐私读取者 | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
消息中心读取者 | 消息中心读取者 | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
网络管理员 | 网络管理员 | d37c8bed-0711-4417-ba38-b4abe66ce4c2
Office 应用管理员 | Office 应用管理员 | 2b745bdf-0803-4d80-aa65-822c4493daac
合作伙伴一线支持人员 | 未显示，因为不应使用它 | 4ba39ca4-527c-499a-b93d-d9b492c50246
合作伙伴二线支持人员 | 未显示，因为不应使用它 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
密码管理员 | 密码管理员 | 966707d0-3269-4727-9be2-8c3a10f19b9d
Power BI 服务管理员 | Power BI 管理员 | a9ea8996-122f-4c74-9520-8edcd192826c
Power Platform 管理员 | Power Platform 管理员 | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
打印机管理员 | 打印机管理员 | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f
打印机技术人员 | 打印机技术人员 | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477
特权身份验证管理员 | 特权身份验证管理员 | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
特权角色管理员 | 特权角色管理员 | e8611ab8-c189-46e8-94e1-60213ab1f814
报告读者 | 报告读者 | 4a5d8f65-41da-4de4-8968-e035b65339cf
搜索管理员 | 搜索管理员 | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
搜索编辑员 | 搜索编辑员 | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
安全管理员 | 安全管理员 | 194ae4cb-b126-40b2-bd5b-6091b380977d
安全操作员 | 安全操作员 | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
安全读取者 | 安全读取者 | 5d6b6bb7-de71-4623-b4af-96380a352509
服务支持管理员 | 服务支持管理员 | f023fd81-a637-4b56-95fd-791ac0226033
SharePoint 服务管理员 | SharePoint 管理员 | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Teams 通信管理员 | Teams 通信管理员 | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Teams 通信支持工程师 | Teams 通信支持工程师 | f70938a0-fc10-4177-9e90-2178f8765737
Teams 通信支持专家 | Teams 通信支持专家 | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Teams 服务管理员 | Teams 服务管理员 | 69091246-20e8-4a56-aa4d-066075b2a7a8
用户 | 未显示，因为无法使用它 | a0b1b346-4d3e-4e8b-98f8-753987be4970
用户帐户管理员 | 用户管理员 | fe930be7-5e62-47db-91af-98c3a49a38b1
工作区设备联接 | 已弃用 | c34f683f-4d5a-4403-affd-6615e00e3a7f

## <a name="deprecated-roles"></a>已弃用的角色

不应使用以下角色。 这些角色已弃用，并将从 Azure AD 中删除。

* 即席许可证管理员
* 设备联接
* 设备管理器
* 设备用户
* 经电子邮件验证的用户创建者
* 邮箱管理员
* 工作区设备联接

## <a name="roles-not-shown-in-the-portal"></a>门户中未显示的角色

Azure 门户中不一定会显示 PowerShell 或 MS Graph API 返回的每个角色。 下表整理了这些差异。

API 名称 | Azure 门户名称 | 注释
-------- | ------------------- | -------------
公司管理员 | 全局管理员角色 | [为便于阅读，名称已更改](directory-assign-admin-roles.md#role-template-ids)
CRM 服务管理员 | Dynamics 365 管理员 | [反映当前产品品牌](directory-assign-admin-roles.md#role-template-ids)
设备联接 | 已放弃 | [已弃用角色的文档](directory-assign-admin-roles.md#deprecated-roles)
设备管理器 | 已弃用 | [已弃用角色的文档](directory-assign-admin-roles.md#deprecated-roles)
设备用户 | 已弃用 | [已弃用角色的文档](directory-assign-admin-roles.md#deprecated-roles)
目录同步帐户 | 未显示，因为不应使用它 | [目录同步帐户文档](directory-assign-admin-roles.md#directory-synchronization-accounts)
目录写入者 | 未显示，因为不应使用它 | [目录写入者文档](directory-assign-admin-roles.md#directory-writers)
来宾用户 | 未显示，因为无法使用它  | 不可用
Lync 服务管理员 | Skype for Business 管理员 | [反映当前产品品牌](directory-assign-admin-roles.md#role-template-ids)
合作伙伴一线支持人员 | 未显示，因为不应使用它 | [合作伙伴一线支持人员文档](directory-assign-admin-roles.md#partner-tier1-support)
合作伙伴二线支持人员 | 未显示，因为不应使用它 | [合作伙伴二线支持人员文档](directory-assign-admin-roles.md#partner-tier2-support)
受限来宾用户 | 未显示，因为无法使用它 | 不可用
用户 | 未显示，因为无法使用它 | 不可用
工作区设备联接 | 已弃用 | [已弃用角色的文档](directory-assign-admin-roles.md#deprecated-roles)

## <a name="next-steps"></a>后续步骤

* 若要详细了解如何将用户分配为 Azure 订阅的管理员，请参阅[使用 Azure 角色 (Azure RBAC) 管理访问权限](../../role-based-access-control/role-assignments-portal.md)
* 若要详细了解 Microsoft Azure 中控制资源访问的方式，请参阅[了解不同的角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* 有关 Azure Active Directory 如何与 Azure 订阅相关联的详细信息，请参阅 [How Azure subscriptions are associated with Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)（Azure 订阅与 Azure Active Directory 的关联方式）
