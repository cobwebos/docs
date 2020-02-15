---
title: 管理员角色说明和权限-Azure AD |Microsoft Docs
description: 管理员角色可以添加用户、分配管理角色、重置用户密码、管理用户许可证，或者管理域。
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 11/12/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7595ef1b8742c2ba18a262d5afc5eea1b4d7a15c
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251611"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Azure Active Directory 中的管理员角色权限

使用 Azure Active Directory （Azure AD），可以指定有限的管理员来管理不太特权角色中的标识任务。 管理员可以分配给用户添加或更改用户、分配管理角色、重置用户密码、管理用户许可证以及管理域名。 只能在 Azure AD 的用户设置中更改默认用户权限。

## <a name="limit-the-use-of-global-administrator"></a>限制全局管理员的使用

分配到全局管理员角色的用户可以读取和修改 Azure AD 组织中的每个管理设置。 默认情况下，会向注册 Azure 订阅的人员分配 Azure AD 组织的全局管理员角色。 只有全局管理员和特权角色管理员才能委派管理员角色。 为了降低业务风险，我们建议你将此角色分配给组织中尽可能少的人员。

作为最佳做法，我们建议你将此角色分配给组织中少于5人的人员。 如果你的组织中有5个以上的用户分配到全局管理员角色，则可以通过以下方法减少其使用。

### <a name="find-the-role-you-need"></a>查找所需的角色

如果很难找到许多角色的列表所需的角色，Azure AD 可以根据角色类别显示角色子集。 请查看我们的新**类型**筛选器，以便[Azure AD 角色和管理员](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)仅向你显示所选类型的角色。

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>由于你分配了 "全局管理员" 角色，因此存在一个不存在的角色

有可能将一个或多个角色添加到 Azure AD 中，这些权限在您将某些用户提升为全局管理员时不是一个选项。 随着时间的推移，我们将推出其他角色来完成只有全局管理员角色才能执行的任务。 可以在以下[可用角色](#available-roles)中查看它们。

## <a name="assign-or-remove-administrator-roles"></a>分配或删除管理员角色

若要了解如何在 Azure Active Directory 中向用户分配管理角色，请参阅[在 Azure Active Directory 中查看和分配管理员角色](directory-manage-roles-portal.md)。

## <a name="available-roles"></a>可用的角色

提供以下管理员角色：

### <a name="application-administratorapplication-administrator-permissions"></a>[应用程序管理员](#application-administrator-permissions)

充当此角色的用户可以创建和管理企业应用程序、应用程序注册和应用程序代理设置的所有方面。 请注意，在创建新的应用程序注册或企业应用程序时，分配给此角色的用户不会添加为所有者。

应用程序管理员可以管理允许它们模拟应用程序的应用程序凭据。 因此，分配到此角色的用户只能管理那些未分配给任何 Azure AD 角色的应用程序或仅分配给以下管理角色的应用程序的应用程序凭据：
* 应用程序管理员
* 应用程序开发人员
* 云应用管理员
* 目录读者

如果将应用程序分配给上面未提到的任何其他角色，则应用程序管理员无法管理该应用程序的凭据。 
 
此角色还授予_同意_委派权限和应用程序权限的功能，但对 Microsoft Graph 和 Azure AD 关系图的权限除外。

> [!IMPORTANT]
> 此例外表示你仍同意_其他_应用（例如，已注册的第三方应用或应用）的权限，但不允许对 Azure AD 本身的权限。 你仍可以在应用注册过程中_请求_这些权限，但_授予_（即同意）这些权限需要 Azure AD 管理员。这意味着恶意用户不能轻松地提升其权限，例如，创建并同意可以写入整个目录的应用程序，并通过该应用程序的权限提升自身，使其成为全局管理员。

### <a name="application-developerapplication-developer-permissions"></a>[应用程序开发人员](#application-developer-permissions)

在将设置“用户可以注册应用程序”设置为“否”时，充当此角色的用户可以创建应用程序注册。 当 "用户可以同意应用代表他们访问公司数据" 设置设置为 "否" 时，此角色还会代表自己授予许可。 分配到此角色的用户在创建新的应用程序注册或企业应用程序时作为所有者添加。

### <a name="authentication-administratorauthentication-administrator-permissions"></a>[身份验证管理员](#authentication-administrator-permissions)

身份验证管理员角色目前为公共预览版。 具有此角色的用户可以设置或重置非密码凭据，并可以更新所有用户的密码。 身份验证管理员可以要求用户重新注册现有的非密码凭据（例如 MFA 或 FIDO），并在设备上撤消**记住 mfa**，这会提示用户在非管理员用户的下一次登录时进行 mfa，或者只分配了以下角色：

* 身份验证管理员
* 目录读者
* 来宾邀请者
* 消息中心读取者
* 报告读者

> [!IMPORTANT]
> 具有此角色的用户可以更改可能有权访问 Azure Active Directory 内外敏感或私有信息或关键配置的用户的凭据。 更改用户的凭据可能意味着假定用户标识和权限的能力。 例如：
>
>- 应用程序注册和企业应用程序所有者，可以管理他们拥有的应用的凭据。 这些应用程序可能在 Azure AD 或其他位置拥有未授予身份验证管理员的特权。 通过此路径，身份验证管理员可能能够假定应用程序所有者的身份，然后通过更新应用程序的凭据进一步假定特权应用程序的标识。
>- Azure 订阅所有者，可能对 Azure 中的敏感或私有信息或关键配置拥有访问权限。
>- 安全组和 Office 365 组所有者，可以管理组成员身份。 这些组可能会授予对 Azure AD 或其他位置敏感或私有信息或关键配置的访问权限。
>- Azure AD 之外的其他服务中的管理员，如 Exchange Online、Office 安全与合规中心以及人力资源系统。
>- 高级管理人员、法律顾问和人力资源员工之类的非管理员，可能有权访问敏感或私有信息。

### <a name="azure-devops-administratorazure-devops-administrator-permissions"></a>[Azure DevOps 管理员](#azure-devops-administrator-permissions)

具有此角色的用户可以管理 Azure DevOps 策略，以将新的 Azure DevOps 组织创建限制为一组可配置的用户或组。 此角色中的用户可以通过任何 Azure AD 公司组织的 Azure DevOps 组织来管理此策略。

此角色中的用户可以管理所有企业 Azure DevOps 策略。

### <a name="azure-information-protection-administratorazure-information-protection-administrator-permissions"></a>[Azure 信息保护管理员](#azure-information-protection-administrator-permissions)

具有此角色的用户拥有 Azure 信息保护服务中的所有权限。 此角色可以配置 Azure 信息保护策略的标签、管理保护模板，以及激活保护。 此角色不会授予标识保护中心、Privileged Identity Management、监视 Office 365 服务运行状况或 Office 365 安全与合规中心的权限。

### <a name="b2c-user-flow-administratorb2c-user-flow-administrator-permissions"></a>[B2C 用户流管理员](#b2c-user-flow-administrator-permissions)

具有此角色的用户可以创建和管理 Azure 门户中的 B2C 用户流（也称为 "内置" 策略）。 通过创建或编辑用户流，这些用户可以更改用户体验的 html/CSS/javascript 内容、更改每个用户流的 MFA 要求、更改令牌中的声明以及为租户中的所有策略调整会话设置。 另一方面，此角色不包括查看用户数据，或对租户架构中包含的属性进行更改的功能。 对标识体验框架（也称为自定义）策略的更改也不在此角色的作用域内。

### <a name="b2c-user-flow-attribute-administratorb2c-user-flow-attribute-administrator-permissions"></a>[B2C 用户流属性管理员](#b2c-user-flow-attribute-administrator-permissions)

具有此角色的用户可以添加或删除租户中所有用户流的自定义属性。 因此，具有此角色的用户可以更改或向最终用户架构中添加新元素并影响所有用户流的行为，并间接导致最终用户可能会询问哪些数据并最终将其作为声明发送到应用程序。 此角色无法编辑用户流。

### <a name="b2c-ief-keyset-administratorb2c-ief-keyset-administrator-permissions"></a>[B2C IEF 键集管理员](#b2c-ief-keyset-administrator-permissions)

用户可以创建和管理令牌加密、令牌签名和声明加密/解密的策略密钥和机密。 通过向现有密钥容器添加新密钥，此受限管理员可根据需要滚动更新机密，而不会影响现有应用程序。 此用户可以查看这些机密及其在创建后的到期日期的完整内容。

> [!IMPORTANT]
> 这是一种敏感的角色。 在预生产和生产过程中，应小心审核和分配密钥集管理员角色。

### <a name="b2c-ief-policy-administratorb2c-ief-policy-administrator-permissions"></a>[B2C IEF 策略管理员](#b2c-ief-policy-administrator-permissions)

此角色中的用户可以创建、读取、更新和删除 Azure AD B2C 中的所有自定义策略，并因此对相关 Azure AD B2C 租户中的标识体验框架具有完全控制权限。 通过编辑策略，此用户可以建立与外部标识提供者的直接联合、更改目录架构、更改所有面向用户的内容（HTML、CSS、JavaScript）、更改要求以完成身份验证、创建新用户、发送用户数据添加到外部系统（包括完全迁移），并编辑所有用户信息（包括密码和电话号码等敏感字段）。 相反，此角色无法更改加密密钥或在租户中编辑用于联合的机密。

> [!IMPORTANT]
> B2 IEF 策略管理员是高度敏感的角色，应在生产环境中为租户分配非常有限的角色。 应认真审核这些用户的活动，尤其是对于生产中的租户。

### <a name="billing-administratorbilling-administrator-permissions"></a>[计费管理员](#billing-administrator-permissions)

进行采购、管理订阅、管理支持票证，以及监视服务运行状况。

### <a name="cloud-application-administratorcloud-application-administrator-permissions"></a>[云应用程序管理员](#cloud-application-administrator-permissions)

充当角色的用户具有与应用程序管理员角色相同的权限，但不包括管理应用程序代理的权限。 此角色授予创建和管理企业应用程序和应用程序注册的所有方面的权限。 此角色还可以同意委派权限，以及除 Microsoft Graph 和 Azure AD Graph 之外的应用程序权限。 创建新的应用程序注册或企业应用程序时，分配给此角色的用户不会添加为所有者。

云应用程序管理员可以管理允许它们模拟应用程序的应用程序凭据。 因此，分配到此角色的用户只能管理那些未分配给任何 Azure AD 角色的应用程序或仅分配给以下管理角色的应用程序的应用程序凭据：
* 应用程序开发人员
* 云应用管理员
* 目录读者

如果将应用程序分配给上面未提到的任何其他角色，则云应用程序管理员无法管理该应用程序的凭据。

### <a name="cloud-device-administratorcloud-device-administrator-permissions"></a>[云设备管理员](#cloud-device-administrator-permissions)

充当此角色的用户可以在 Azure AD 中启用、禁用和删除设备，并可以在 Azure 门户中读取 Windows 10 BitLocker 密钥（如果有）。 该角色不能授予设备上其他任何属性的管理权限。

### <a name="compliance-administratorcompliance-administrator-permissions"></a>[合规性管理员](#compliance-administrator-permissions)

具有此角色的用户有权管理 Microsoft 365 合规中心、Microsoft 365 管理中心、Azure 和 Office 365 安全与合规中心中的合规性相关功能。 工作负责人还可以管理 Exchange 管理中心和团队 & Skype for Business 管理中心的所有功能，并为 Azure 和 Microsoft 365 创建支持票证。 [关于 Office 365 管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)中提供了详细信息。

In | 有权执行的操作
----- | ----------
[Microsoft 365 合规中心](https://protection.office.com) | 跨 Microsoft 365 服务保护和管理组织数据<br>管理合规性警报
[合规性管理器](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 跟踪、分配并验证组织的法规合规性活动
[Office 365 安全与合规中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理数据治理<br>执行法律和数据调查<br>管理数据主体请求<br><br>此角色具有与 Office 365 安全与合规中心基于[角色的访问](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)控制相同的权限。
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 查看所有 Intune 审核数据
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 拥有只读权限，可以管理警报<br>可以创建和修改文件策略并允许执行文件管理操作<br>可以查看数据管理下的所有内置报表

### <a name="compliance-data-administratorcompliance-data-administrator-permissions"></a>[相容性数据管理员](#compliance-data-administrator-permissions)

具有此角色的用户具有跟踪 Microsoft 365 符合性中心、Microsoft 365 管理中心和 Azure 中的数据的权限。 用户还可以在 Exchange 管理中心、合规性经理和团队中跟踪符合性数据 & Skype for Business 管理中心，并为 Azure 和 Microsoft 365 创建支持票证。

In | 有权执行的操作
----- | ----------
[Microsoft 365 合规中心](https://protection.office.com) | 跨 Microsoft 365 服务监视符合性相关的策略<br>管理合规性警报
[合规性管理器](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 跟踪、分配并验证组织的法规合规性活动
[Office 365 安全与合规中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理数据治理<br>执行法律和数据调查<br>管理数据主体请求<br><br>此角色具有与 Office 365 安全与合规中心基于角色的访问控制中的[符合性数据管理员 RoleGroup](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)相同的权限。
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 查看所有 Intune 审核数据
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 拥有只读权限，可以管理警报<br>可以创建和修改文件策略并允许执行文件管理操作<br>可以查看数据管理下的所有内置报表

### <a name="conditional-access-administratorconditional-access-administrator-permissions"></a>[条件访问管理员](#conditional-access-administrator-permissions)

具有此角色的用户能够管理 Azure Active Directory 条件访问设置。
> [!NOTE]
> 若要在 Azure 中部署 Exchange ActiveSync 条件访问策略，用户还必须是全局管理员。

### <a name="customer-lockbox-access-approvercustomer-lockbox-access-approver-permissions"></a>[客户密码箱访问审批者](#customer-lockbox-access-approver-permissions)

管理你的组织中的[客户密码箱请求](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests)。 他们接收客户密码箱请求的电子邮件通知，并且可以批准和拒绝来自 Microsoft 365 管理中心的请求。 他们还可以开启或关闭客户密码箱功能。 只有全局管理员可以重置分配到此角色的用户的密码。

### <a name="desktop-analytics-administratordesktop-analytics-administrator-permissions"></a>[桌面分析管理员](#desktop-analytics-administrator-permissions)


此角色中的用户可以 & 策略服务中管理桌面分析和 Office 自定义。 对于桌面分析，这包括查看资产清单、创建部署计划、查看部署和运行状况的功能。 对于 Office 自定义 & 策略服务，此角色使用户能够管理 Office 策略。

### <a name="device-administratordevice-administrators-permissions"></a>[设备管理员](#device-administrators-permissions)

此角色只能作为[设备设置](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)中的其他本地管理员进行分配。 拥有此角色的用户成为所有已加入 Azure Active Directory 的 Windows 10 设备上的本地计算机管理员。 他们无权管理 Azure Active Directory 中的设备对象。

### <a name="directory-readersdirectory-readers-permissions"></a>[目录读取器](#directory-readers-permissions)

此角色中的用户可以读取基本的目录信息。 此角色应用于：
* 授予一组特定的来宾用户读取访问权限，而不是将其授予所有来宾用户。
* 如果将一组特定的非管理员用户 "仅限管理员访问 Azure AD 门户" 设置为 "是"，则授予对 Azure 门户的访问权限。
* 向服务主体授予对目录读取权限的目录的访问权限。全部不是一个选项。

### <a name="directory-synchronization-accountsdirectory-synchronization-accounts-permissions"></a>[目录同步帐户](#directory-synchronization-accounts-permissions)

请勿使用。 此角色自动分配给 Azure AD Connect 服务，不可用于其他任何用途。

### <a name="directory-writersdirectory-writers-permissions"></a>[目录编写器](#directory-writers-permissions)

这是一个遗留的角色，分配给不支持[许可框架](../develop/quickstart-register-app.md)的应用程序。 不应将它分配给任何用户。

### <a name="dynamics-365-administrator--crm-administratorcrm-service-administrator-permissions"></a>[Dynamics 365 管理员/CRM 管理员](#crm-service-administrator-permissions)

具有此角色的用户具有 Microsoft Dynamics 365 Online 中的全局权限(如果该服务存在)，并且能够管理支持票证和监视服务运行状况。 有关详细信息，请参阅[使用服务管理员角色管理租户](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)。

> [!NOTE]
> 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“Dynamics 365 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Dynamics 365 管理员”。

### <a name="exchange-administratorexchange-service-administrator-permissions"></a>[Exchange 管理员](#exchange-service-administrator-permissions)

具有此角色的用户具有 Microsoft Exchange Online 内的全局权限（如果该服务存在）。 还能够创建和管理所有 Office 365 组、管理支持票证和监视服务运行状况。 有关详细信息，请参阅 [About Office 365 admin roles](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)（关于 Office 365 管理员角色）。

> [!NOTE]
> 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“Exchange 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Exchange 管理员”。 这是[exchange 管理中心](https://go.microsoft.com/fwlink/p/?LinkID=529144)中的 "exchange Online 管理员"。

### <a name="external-identity-provider-administratorexternal-identity-provider-administrator-permissions"></a>[外部标识提供者管理员](#external-identity-provider-administrator-permissions)

此管理员管理 Azure Active Directory 租户和外部标识提供者之间的联合身份验证。 使用此角色时，用户可以添加新的标识提供者并配置所有可用设置（例如身份验证路径、服务 ID、已分配密钥容器）。 此用户可以让租户信任来自外部标识提供者的身份验证。 对最终用户体验产生的影响取决于租户的类型：

* 为员工和合作伙伴 Azure Active Directory 租户：添加联合（例如，使用 Gmail）会立即影响尚未兑换的所有来宾邀请。 请参阅[添加 Google 作为 B2B 来宾用户的标识提供者](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)。
* Azure Active Directory B2C 租户：添加联合（例如，使用 Facebook 或其他 Azure AD 组织）不会立即影响最终用户流，直到将标识提供程序添加为用户流中的选项（也称为内置策略）。 有关示例，请参阅[将 Microsoft 帐户配置为标识提供者](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app)。 若要更改用户流，"B2C 用户流管理员" 的有限角色是必需的。

### <a name="global-administrator--company-administratorcompany-administrator-permissions"></a>[全局管理员/公司管理员](#company-administrator-permissions)

具有此角色的用户有权访问 Azure Active Directory 以及使用 Azure Active Directory 标识的服务（例如 Microsoft 365 安全中心、Microsoft 365 合规中心、Exchange Online、SharePoint Online 和 Skype for Business Online）中的所有管理功能。 注册 Azure Active Directory 租户的人员将成为全局管理员。 公司中可以有多个全局管理员。 全局管理员可以为任何用户和所有其他管理员重置密码。

> [!NOTE]
> 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“公司管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“全局管理员”。
>
>

### <a name="global-readerglobal-reader-permissions"></a>[全局读取器](#global-reader-permissions)

此角色中的用户可以跨 Microsoft 365 服务读取设置和管理信息，但不能执行管理操作。 全局读取器是全局管理员的只读对应项。 为计划、审核或调查分配全局读取器而不是全局管理员。 将全局读取器与其他有限的管理员角色（如 Exchange 管理员）结合使用，以便在没有分配全局管理员角色的情况下更轻松地完成工作。 全局读者适用于 Microsoft 365 管理中心、Exchange 管理中心、团队管理中心、安全中心、合规中心、Azure AD 管理中心和设备管理中心。

> [!NOTE]
> 全局读者角色有一些限制，
>
>- SharePoint 管理中心-SharePoint 管理中心不支持全局读者角色。 在[Microsoft 365 管理中心](https://admin.microsoft.com/Adminportal/Home#/homepage)"中的" 管理中心 "下，不会在左窗格中看到" SharePoint "。
>- [Onedrive 管理中心](https://admin.onedrive.com/)-onedrive 管理中心不支持全局读者角色。
>- [Azure AD 门户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)-全局读取器无法读取企业应用的预配模式。
>- [M365 管理中心](https://admin.microsoft.com/Adminportal/Home#/homepage)-全局读者无法读取客户密码箱请求。 在 M365 管理中心的左窗格中，你将找不到 "**支持**" 下的 "**客户密码箱请求**" 选项卡。
>- [M365 安全中心](https://security.microsoft.com/homepage)-全球读者无法读取敏感度和保留标签。 在 M365 安全中心的左窗格中找不到**灵敏度标签**、**保留标签**和**标签分析**选项卡。
>- [Office 安全与合规中心](https://sip.protection.office.com/homepage)-全局读取器无法读取 SCC 审核日志或执行内容搜索。
>- [团队管理员中心](https://admin.teams.microsoft.com)-全球读者无法阅读**团队生命周期**、**分析 & 报表**、 **IP 电话设备管理**和**应用程序目录**。
>- [Privileged Access Management （PAM）](https://docs.microsoft.com/office365/securitycompliance/privileged-access-management-overview)不支持全局读者角色。
>- [Azure 信息保护](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)-仅支持[适用于中央报告的](https://docs.microsoft.com/azure/information-protection/reports-aip)全局读取器，以及在 Azure AD 组织不在[统一标签平台](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)上时。
>
> 这些功能目前正在开发中。
>

### <a name="groups-administratorgroups-administrator-permissions"></a>[组管理员](#groups-administrator-permissions)

此角色中的用户可以创建/管理组及其设置，如命名和过期策略。 必须了解的是，将用户分配到此角色后，他们能够跨各种工作负荷（如团队、SharePoint、Yammer）以及 Outlook 管理租户中的所有组。 此外，用户还能够跨各种管理门户（如 Microsoft 管理中心、Azure 门户以及工作负荷（如团队和 SharePoint 管理中心）管理各种组设置。

### <a name="guest-inviterguest-inviter-permissions"></a>[来宾邀请者](#guest-inviter-permissions)

此角色的用户可在“成员可以邀请”用户设置设置为“否”时管理 Azure Active Directory B2B 来宾用户邀请。 [关于 Azure AD B2B 协作](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)中提供了有关 B2B 协作的详细信息。 它不包括任何其他权限。

### <a name="helpdesk-administratorhelpdesk-administrator-permissions"></a>[支持管理员](#helpdesk-administrator-permissions)

具有此角色的用户可以更改密码、使刷新令牌失效、管理服务请求和监视服务运行状况。 使刷新令牌失效会强制用户重新登录。 支持管理员可以重置密码，并使非管理员的其他用户的刷新令牌无效，或者仅分配有以下角色：

* 目录读者
* 来宾邀请者
* 支持管理员
* 消息中心读取者
* 报告读者

> [!IMPORTANT]
> 具有此角色的用户可以更改可能有权访问 Azure Active Directory 内外敏感或私有信息或关键配置的用户的密码。 更改用户的密码可能意味着假定用户标识和权限的能力。 例如：
>
>- 应用程序注册和企业应用程序所有者，可以管理他们拥有的应用的凭据。 这些应用程序可能在 Azure AD 或其他位置拥有未授予支持人员管理员的特权。 通过此路径，支持人员管理员可能能够假定应用程序所有者的身份，然后通过更新应用程序的凭据来进一步假定特权应用程序的标识。
>- 可能有权访问 Azure 中的敏感或私有信息或关键配置的 azure 订阅所有者。
>- 安全组和 Office 365 组所有者，可以管理组成员身份。 这些组可能会授予对 Azure AD 或其他位置敏感或私有信息或关键配置的访问权限。
>- Azure AD 之外的其他服务中的管理员，如 Exchange Online、Office 安全与合规中心以及人力资源系统。
>- 高级管理人员、法律顾问和人力资源员工之类的非管理员，可能有权访问敏感或私有信息。

可以通过[管理单元](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units)委托用户子集的管理权限，并将策略应用到部分用户。

此角色以前称为[Azure 门户](https://portal.azure.com/)中的 "密码管理员"。 Azure AD 中的 "支持人员管理员" 名称现在与其在 Azure AD PowerShell 中的名称匹配 Azure AD 图形 API 和 Microsoft Graph API。

### <a name="intune-administratorintune-service-administrator-permissions"></a>[Intune 管理员](#intune-service-administrator-permissions)

具有此角色的用户具有 Microsoft Intune Online 内的全局权限（如果该服务存在）。 此外，此角色包含管理以关联策略，以及创建和管理组的用户和设备的能力。 有关 Microsoft Intune 的[基于角色的管理控制（RBAC）](https://docs.microsoft.com/intune/role-based-access-control)的详细信息。

此角色可创建和管理所有安全组。 但是，Intune 管理员对 Office 组没有管理员权限。 这意味着管理员无法更新租户中所有 Office 组的所有者或成员身份。 但是，他/她可以管理他创建的 Office 组作为其最终用户权限的一部分。 因此，该用户创建的任何 Office 组（而非安全组）应根据其配额250进行计数。

> [!NOTE]
> 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“Intune 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Intune 管理员”。

### <a name="kaizala-administratorkaizala-administrator-permissions"></a>[Kaizala 管理员](#kaizala-administrator-permissions)

具有此角色的用户具有在 Microsoft Kaizala 内管理设置的全局权限（如果该服务存在），并且能够管理支持票证和监视服务运行状况。 此外，用户还可以访问与采用 & 使用 Kaizala 的相关报表，组织成员和使用 Kaizala 操作生成的业务报表。

### <a name="license-administratorlicense-administrator-permissions"></a>[许可证管理员](#license-administrator-permissions)

具有此角色的用户可以添加、删除和更新用户、组（使用基于组的许可）的许可分配，以及管理用户的使用位置。 该角色不授予在使用位置之外购买或管理订阅、创建或管理组，或者创建或管理用户的权限。 此角色无权查看、创建或管理支持票证。

### <a name="message-center-privacy-readermessage-center-privacy-reader-permissions"></a>[消息中心隐私读取器](#message-center-privacy-reader-permissions)

此角色中的用户可以监视消息中心中的所有通知，包括数据隐私消息。 消息中心隐私读取器会收到电子邮件通知，其中包括与数据隐私相关的通知，并且可以使用邮件中心首选项取消订阅。 只有全局管理员和消息中心隐私读者才能读取数据隐私消息。 此外，此角色还包含查看组、域和订阅的功能。 此角色无权查看、创建或管理服务请求。

### <a name="message-center-readermessage-center-reader-permissions"></a>[消息中心读取器](#message-center-reader-permissions)

具有此角色的用户可以在其组织的 [Office 365 消息中心](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)内，监视 Exchange、Intune 和 Microsoft Teams 等已配置服务的通知和公告运行状况更新。 消息中心读者会收到包含帖子和最新动态的每周电子邮件摘要，并能在 Office 365 内共享消息中心帖子。 在 Azure AD 中，分配到此角色的用户对 Azure AD 服务只拥有只读访问权限，如用户和组。 此角色无权查看、创建或管理支持票证。

### <a name="office-apps-administratoroffice-apps-administrator-permissions"></a>[Office 应用管理员](#office-apps-administrator-permissions)

此角色中的用户可以管理 Office 365 应用的云设置。 这包括管理云策略、自助下载管理和查看与 Office apps 相关的报表。 此角色还允许管理支持票证，并在主管理中心内监视服务运行状况。 分配到此角色的用户还可以管理 Office 应用中新功能的通信。 

### <a name="partner-tier1-supportpartner-tier1-support-permissions"></a>[合作伙伴 Tier1 支持](#partner-tier1-support-permissions)

请勿使用。 此角色已弃用，并将从 Azure AD 中删除。 此角色仅供少数 Microsoft 转售合作伙伴使用，不适用于一般用途。

### <a name="partner-tier2-supportpartner-tier2-support-permissions"></a>[合作伙伴 Tier2 支持](#partner-tier2-support-permissions)

请勿使用。 此角色已弃用，并将从 Azure AD 中删除。 此角色仅供少数 Microsoft 转售合作伙伴使用，不适用于一般用途。

### <a name="password-administratorpassword-administrator-permissions"></a>[密码管理员](#password-administrator-permissions)

具有此角色的用户具有管理密码的有限能力。 此角色不会授予管理服务请求或监视服务运行状况的功能。 密码管理员可以重置非管理员或以下角色成员的其他用户的密码：

* 目录读者
* 来宾邀请者
* 密码管理员

### <a name="power-bi-administratorpower-bi-service-administrator-permissions"></a>[Power BI 管理员](#power-bi-service-administrator-permissions)

具有此角色的用户具有 Microsoft Power BI 内的全局权限（如果该服务存在），并且能够管理支持票证和监视服务运行状况。 有关详细信息，请参阅[了解 Power BI 管理员角色](https://docs.microsoft.com/power-bi/service-admin-role)。

> [!NOTE]
> 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“Power BI 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“Power BI 管理员”。

### <a name="power-platform-administratorpower-platform-administrator-permissions"></a>[Power Platform 管理员](#power-platform-administrator-permissions)

此角色中的用户可创建和管理环境、PowerApps、流和数据丢失防护策略的所有方面。 此外，具有此角色的用户可以管理支持票证和监视服务运行状况。

### <a name="privileged-authentication-administratorprivileged-authentication-administrator-permissions"></a>[特权身份验证管理员](#privileged-authentication-administrator-permissions)

具有此角色的用户可以为所有用户（包括全局管理员）设置或重置非密码凭据，并可以更新所有用户的密码。 特权身份验证管理员可以强制用户重新注册现有的非密码凭据（例如 MFA、FIDO），并吊销 "在设备上记住 MFA"，在所有用户的下一次登录时提示进行 MFA。

### <a name="privileged-role-administratorprivileged-role-administrator-permissions"></a>[特权角色管理员](#privileged-role-administrator-permissions)

具有此角色的用户可以管理角色分配以及 Azure AD Privileged Identity Management 中的 Azure Active Directory。 此外，此角色还允许管理 Privileged Identity Management 和管理单元的所有方面。

> [!IMPORTANT]
> 此角色授予管理所有 Azure AD 角色（包括全局管理员角色）的分配的功能。 此角色不包括 Azure AD 中的任何其他权限功能，如创建或更新用户。 但是，分配到此角色的用户可通过分配其他角色，授予自己或其他人额外的特权。

### <a name="reports-readerreports-reader-permissions"></a>[报表读者](#reports-reader-permissions)

具有此角色的用户可以在 Microsoft 365 管理中心 "中查看使用情况报告数据和报告仪表板，并在 Power BI 中查看采用上下文包。 此外，此角色还提供对 Azure AD 中的登录报告和活动以及 Microsoft Graph 报告 API 返回的数据的访问权限。 分配到“报告读者”角色的用户只能访问相关使用情况和采用指标。 它们没有任何管理员权限，无法配置设置或访问产品特定的管理中心（如 Exchange）。 此角色无权查看、创建或管理支持票证。

### <a name="search-administratorsearch-administrator-permissions"></a>[搜索管理员](#search-administrator-permissions)

此角色中的用户对 Microsoft 365 管理中心中的所有 Microsoft 搜索管理功能具有完全访问权限。 搜索管理员可以将搜索管理员和搜索编辑器角色委托给用户，以及创建和管理内容，例如书签、Q & As 和位置。 此外，这些用户可以查看消息中心、监视服务运行状况和创建服务请求。

### <a name="search-editorsearch-editor-permissions"></a>[搜索编辑器](#search-editor-permissions)

此角色中的用户可以创建、管理和删除 Microsoft 365 管理中心中 Microsoft 搜索的内容，包括书签、Q & As 和位置。

### <a name="security-administratorsecurity-administrator-permissions"></a>[安全管理员](#security-administrator-permissions)

具有此角色的用户有权管理 Microsoft 365 安全中心、Azure Active Directory 标识保护、Azure 信息保护和 Office 365 安全与合规中心中与安全相关的功能。 [Office 365 安全与合规中心](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)提供了有关 Office 365 权限的详细信息。

In | 有权执行的操作
--- | ---
[Microsoft 365 安全中心](https://protection.office.com) | 跨 Microsoft 365 服务监视与安全相关的策略<br>管理安全威胁和警报<br>查看报表
标识保护中心 | 安全读取者角色的所有权限<br>此外，还能够执行除了重置密码以外的所有“标识保护中心”操作
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | 安全读取者角色的所有权限<br>**无法**管理 Azure AD 角色分配或设置
[Office 365 安全与合规中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理安全策略<br>查看、调查和响应安全威胁<br>查看报表
Azure 高级威胁防护 | 监视和响应可疑安全活动
Windows Defender ATP 和 EDR | 分配角色<br>管理计算机组<br>配置终结点威胁检测和自动修正<br>查看、调查并响应警报
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 视图用户、设备、注册、配置和应用程序信息<br>无法对 Intune 进行更改
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 添加管理员、添加策略和设置、上传日志以及执行管理操作
[Azure 安全中心](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | 可以查看安全策略、查看安全状态、编辑安全策略、查看警报和建议、关闭警报和建议
[Office 365 服务运行状况](https://docs.microsoft.com/office365/enterprise/view-service-health) | 查看 Office 365 服务的运行状况

### <a name="security-operatorsecurity-operator-permissions"></a>[安全操作员](#security-operator-permissions)

具有此角色的用户可以管理警报并拥有对安全相关功能的全局只读访问权限，包括 Microsoft 365 安全中心、Azure Active Directory、Identity Protection、Privileged Identity Management 和 Office 365 中的所有信息安全与合规中心。 [Office 365 安全与合规中心](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center)提供了有关 Office 365 权限的详细信息。

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

### <a name="security-readersecurity-reader-permissions"></a>[安全读取者](#security-reader-permissions)

具有此角色的用户对安全相关的功能具有全局只读访问权限，包括 Microsoft 365 安全中心、Azure Active Directory、标识保护、Privileged Identity Management 中的所有信息，并且能够阅读 Azure Active Directory 登录报告和审核日志，还授予了对 Office 365 安全与合规中心的只读权限。 [Office 365 安全与合规中心](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)提供了有关 Office 365 权限的详细信息。

In | 有权执行的操作
--- | ---
[Microsoft 365 安全中心](https://protection.office.com) | 跨 Microsoft 365 服务查看与安全相关的策略<br>查看安全威胁和警报<br>查看报表
标识保护中心 | 读取安全功能的所有安全报告和设置信息<br><ul><li>反垃圾邮件<li>加密<li>数据丢失预防<li>反恶意软件<li>高级威胁防护<li>防网络钓鱼<li>邮件流规则
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | 对 Azure AD Privileged Identity Management 中显示的所有信息具有只读访问权限：用于 Azure AD 角色分配和安全检查的策略和报表。<br>**无法**注册 Azure AD Privileged Identity Management 或对其进行任何更改。 在 Privileged Identity Management 门户中或通过 PowerShell，此角色中的用户可以激活其他角色（例如，全局管理员或特权角色管理员）（如果用户有资格获得）。
[Office 365 安全与合规中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 查看安全策略<br>查看并调查安全威胁<br>查看报表
Windows Defender ATP 和 EDR | 查看并调查警报。 当你在 Windows Defender ATP 中启用基于角色的访问控制时，具有只读权限（如 Azure AD 安全读者角色）的用户在分配到 Windows Defender ATP 角色之前将失去访问权限。
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 视图用户、设备、注册、配置和应用程序信息。 无法对 Intune 进行更改。
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 拥有只读权限，可以管理警报
[Azure 安全中心](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | 可以查看建议和警报、查看安全策略、查看安全状态，但不能进行更改
[Office 365 服务运行状况](https://docs.microsoft.com/office365/enterprise/view-service-health) | 查看 Office 365 服务的运行状况

### <a name="service-support-administratorservice-support-administrator-permissions"></a>[服务支持管理员](#service-support-administrator-permissions)

具有此角色的用户可以打开 Azure 和 Office 365 服务的 Microsoft 支持请求，并在[Azure 门户](https://portal.azure.com)和[Microsoft 365 管理中心](https://admin.microsoft.com)中查看服务仪表板和消息中心。 [有关管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)的详细信息。

> [!NOTE]
> 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“服务支持管理员”。 它是[Azure 门户](https://portal.azure.com)、 [Microsoft 365 管理中心](https://admin.microsoft.com)和 Intune 门户中的 "服务管理员"。

### <a name="sharepoint-administratorsharepoint-service-administrator-permissions"></a>[SharePoint 管理员](#sharepoint-service-administrator-permissions)

具有此角色的用户在 Microsoft SharePoint Online（如果存在此服务）中拥有全局权限，并且能够创建和管理所有 Office 365 组、管理支持票证和监视服务运行状况。 [有关管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)的详细信息。

> [!NOTE]
> 在 Microsoft 图形 API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为“SharePoint 服务管理员”。 它是 [Azure 门户](https://portal.azure.com)中的“SharePoint 管理员”。

### <a name="skype-for-business--lync-administratorlync-service-administrator-permissions"></a>[Skype for Business/Lync 管理员](#lync-service-administrator-permissions)

具有此角色的用户具有 Microsoft Skype for Business 中的全局权限，以及管理 Azure Active Directory 中的特定于 Skype 的用户属性。 此外，此角色可授予管理支持票证、监视服务运行状况以及访问 Teams 和 Skype for Business 管理中心的能力。 帐户必须获取 Teams 许可证，否则无法运行 Teams PowerShell cmdlet。 有关详细信息，请参阅[关于 Skype for Business 管理员角色](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5)；有关 Teams 许可信息，请参阅 [Skype for Business 和 Microsoft Teams 附加许可](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> 在 Microsoft Graph API、Azure AD 图形 API 和 Azure AD PowerShell 中，此角色标识为 "Lync 服务管理员"。 它是 [Azure 门户](https://portal.azure.com/)中的“Skype for Business 管理员”。

### <a name="teams-communications-administratorteams-communications-administrator-permissions"></a>[团队通信管理员](#teams-communications-administrator-permissions)

充当此角色的用户可以管理 Microsoft Teams 工作负荷的语音与电话相关方面。 这包括用于分配电话号码的管理工具、语音和会议策略，以及通话分析工具集的完全访问权限。

### <a name="teams-communications-support-engineerteams-communications-support-engineer-permissions"></a>[团队通信支持工程师](#teams-communications-support-engineer-permissions)

充当此角色的用户可以使用 Microsoft Teams 和 Skype for Business 管理中心的用户通话故障排除工具，来排查 Microsoft Teams 和 Skype for Business 中的通信问题。 充当此角色的用户可以查看所有参与方的完整通话记录信息。 此角色无权查看、创建或管理支持票证。

### <a name="teams-communications-support-specialistteams-communications-support-specialist-permissions"></a>[团队通信支持专家](#teams-communications-support-specialist-permissions)

充当此角色的用户可以使用 Microsoft Teams 和 Skype for Business 管理中心的用户通话故障排除工具，来排查 Microsoft Teams 和 Skype for Business 中的通信问题。 充当此角色的用户只能查看他们所查找的特定用户的通话中的用户详细信息。 此角色无权查看、创建或管理支持票证。

### <a name="teams-service-administratorteams-service-administrator-permissions"></a>[团队服务管理员](#teams-service-administrator-permissions)

充当此角色的用户可以通过 Microsoft Teams 和 Skype for Business 管理中心以及相应的 PowerShell 模块来管理 Microsoft Teams 工作负荷的所有方面。 这包括（但不限于）与电话、消息、会议和 Teams 自身相关的所有管理工具。 此外，此角色还能够创建和管理所有 Office 365 组、管理支持票证和监视服务运行状况。

### <a name="user-administratoruser-administrator-permissions"></a>[用户管理员](#user-administrator-permissions)

具有此角色的用户可以创建用户，并管理用户的所有方面（如下所示），并可以更新密码过期策略。 此外，具有此角色的用户可以创建和管理所有组。 此角色还能够创建和管理用户视图、管理支持票证和监视服务运行状况。 用户管理员无权为大多数管理员角色中的用户管理某些用户属性。 具有此角色的用户没有力来管理 MFA。 下表列出了作为此限制的例外的角色。

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
| microsoft. directory/Application/appProxyAuthentication/update | 在 Azure Active Directory 中的服务主体上更新应用程序代理身份验证属性。 |
| microsoft. directory/Application/appProxyUrlSettings/update | 更新 Azure Active Directory 中的应用程序代理内部和外部 URL。 |
| microsoft 目录/应用程序/applicationProxy/读取 | 读取所有应用程序代理属性。 |
| microsoft 目录/应用程序/applicationProxy/更新 | 更新所有应用程序代理属性。 |
| microsoft.directory/applications/audience/update | 更新 Azure Active Directory 中的 applications.audience 属性。 |
| microsoft.directory/applications/authentication/update | 更新 Azure Active Directory 中的 applications.authentication 属性。 |
| microsoft.directory/applications/basic/update | 更新 Azure Active Directory 中应用程序的基本属性。 |
| microsoft. 目录/应用程序/创建 | 在 Azure Active Directory 中创建应用程序。 |
| microsoft.directory/applications/credentials/update | 更新 Azure Active Directory 中的 applications.credentials 属性。 |
| microsoft.directory/applications/delete | 删除 Azure Active Directory 中的应用程序。 |
| microsoft.directory/applications/owners/update | 更新 Azure Active Directory 中的 applications.owners 属性。 |
| microsoft.directory/applications/permissions/update | 更新 Azure Active Directory 中的 applications.permissions 属性。 |
| microsoft.directory/applications/policies/update | 更新 Azure Active Directory 中的 applications.policies 属性。 |
| appRoleAssignments/create | 在 Azure Active Directory 中创建 appRoleAssignments。 |
| appRoleAssignments/read | 读取 Azure Active Directory 中的 appRoleAssignments。 |
| appRoleAssignments/update | 更新 Azure Active Directory 中的 appRoleAssignments。 |
| appRoleAssignments/删除 | 删除 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| connectorGroups/所有内容/读取 | 读取 Azure Active Directory 中的应用程序代理连接器组属性。 |
| connectorGroups/所有/更新 | 更新 Azure Active Directory 中的所有应用程序代理连接器组属性。 |
| connectorGroups/create | 在 Azure Active Directory 中创建应用程序代理连接器组。 |
| connectorGroups/删除 | 删除 Azure Active Directory 中的应用程序代理连接器组。 |
| microsoft. 目录/连接器/一切/读取 | 读取 Azure Active Directory 中的所有应用程序代理连接器属性。 |
| microsoft. 目录/连接器/创建 | 在 Azure Active Directory 中创建应用程序代理连接器。 |
| applicationConfiguration/basic/read | 读取 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| applicationConfiguration/basic/update | 更新 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft. directory/策略/applicationConfiguration/create | 在 Azure Active Directory 中创建策略。 |
| microsoft. directory/策略/applicationConfiguration/delete | 删除 Azure Active Directory 中的策略。 |
| applicationConfiguration/物主/物主/读取 | 读取 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft. directory/策略/applicationConfiguration/物主/更新 | 更新 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| applicationConfiguration/policyAppliedTo/read | 读取 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 属性。 |
| microsoft.directory/servicePrincipals/audience/update | 更新 Azure Active Directory 中的 servicePrincipals.audience 属性。 |
| microsoft.directory/servicePrincipals/authentication/update | 更新 Azure Active Directory 中的 servicePrincipals.authentication 属性。 |
| microsoft.directory/servicePrincipals/basic/update | 更新 Azure Active Directory 中 servicePrincipals 的基本属性。 |
| 服务主体/create | 在 Azure Active Directory 中创建 servicePrincipals。 |
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
| microsoft 目录/应用程序/createAsOwner | 在 Azure Active Directory 中创建应用程序。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| appRoleAssignments/createAsOwner | 在 Azure Active Directory 中创建 appRoleAssignments。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| oAuth2PermissionGrants/createAsOwner | 在 Azure Active Directory 中创建 oAuth2PermissionGrants。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| 服务主体/createAsOwner | 在 Azure Active Directory 中创建 servicePrincipals。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |

### <a name="authentication-administrator-permissions"></a>身份验证管理员权限

允许查看、设置和重置任何非管理员用户的身份验证方法信息。

| **操作** | **说明** |
| --- | --- |
| microsoft. directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft. directory/users/strongAuthentication/update | 更新强身份验证属性，如 MFA 凭据信息。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |
| microsoft. 目录/用户/密码/更新 | 更新 Office 365 组织中所有用户的密码。 有关详细信息，请参阅联机文档。 |

### <a name="azure-devops-administrator-permissions"></a>Azure DevOps 管理员权限

可管理 Azure DevOps 组织策略和设置。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的[角色说明](#azure-devops-administrator)。
>
>

| **操作** | **说明** |
| --- | --- |
| devOps/allEntities/allTasks | 阅读并配置 Azure DevOps。 |

### <a name="azure-information-protection-administrator-permissions"></a>Azure 信息保护管理员权限

可以管理 Azure 信息保护服务的所有方面。

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
| allTasks/userFlows/ | 在 Azure Active Directory B2C 中读取和配置用户流。 |

### <a name="b2c-user-flow-attribute-administrator-permissions"></a>B2C 用户流属性管理员权限

创建和管理可用于所有用户流的属性架构。

| **操作** | **说明** |
| --- | --- |
| allTasks/userAttributes/ | 在 Azure Active Directory B2C 中读取和配置用户属性。 |

### <a name="b2c-ief-keyset-administrator-permissions"></a>B2C IEF 键集管理员权限

在标识体验框架中管理用于联合身份验证和加密的机密。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | 在 Azure Active Directory B2C 中读取和配置密钥集。 |

### <a name="b2c-ief-policy-administrator-permissions"></a>B2C IEF 策略管理员权限

在标识体验框架中创建和管理信任框架策略。

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | 在 Azure Active Directory B2C 中读取和配置自定义策略。 |

### <a name="billing-administrator-permissions"></a>计费管理员权限

可以执行与常见计费相关的任务，例如更新付款信息。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft. directory/组织/基本/更新 | 更新 Azure Active Directory 中组织的基本属性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.commerce.billing/allEntities/allTasks | 管理 Office 365 计费的各个方面。 |
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
| microsoft. 目录/应用程序/创建 | 在 Azure Active Directory 中创建应用程序。 |
| microsoft.directory/applications/credentials/update | 更新 Azure Active Directory 中的 applications.credentials 属性。 |
| microsoft.directory/applications/delete | 删除 Azure Active Directory 中的应用程序。 |
| microsoft.directory/applications/owners/update | 更新 Azure Active Directory 中的 applications.owners 属性。 |
| microsoft.directory/applications/permissions/update | 更新 Azure Active Directory 中的 applications.permissions 属性。 |
| microsoft.directory/applications/policies/update | 更新 Azure Active Directory 中的 applications.policies 属性。 |
| appRoleAssignments/create | 在 Azure Active Directory 中创建 appRoleAssignments。 |
| appRoleAssignments/update | 更新 Azure Active Directory 中的 appRoleAssignments。 |
| appRoleAssignments/删除 | 删除 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| microsoft. directory/策略/applicationConfiguration/create | 在 Azure Active Directory 中创建策略。 |
| applicationConfiguration/basic/read | 读取 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| applicationConfiguration/basic/update | 更新 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft. directory/策略/applicationConfiguration/delete | 删除 Azure Active Directory 中的策略。 |
| applicationConfiguration/物主/物主/读取 | 读取 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft. directory/策略/applicationConfiguration/物主/更新 | 更新 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| applicationConfiguration/policyAppliedTo/read | 读取 Azure Active Directory 中的 policies.applicationConfiguration 属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 属性。 |
| microsoft.directory/servicePrincipals/audience/update | 更新 Azure Active Directory 中的 servicePrincipals.audience 属性。 |
| microsoft.directory/servicePrincipals/authentication/update | 更新 Azure Active Directory 中的 servicePrincipals.authentication 属性。 |
| microsoft.directory/servicePrincipals/basic/update | 更新 Azure Active Directory 中 servicePrincipals 的基本属性。 |
| 服务主体/create | 在 Azure Active Directory 中创建 servicePrincipals。 |
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
| microsoft. directory/设备/删除 | 删除 Azure Active Directory 中的设备。 |
| microsoft.directory/devices/disable | 禁用 Azure Active Directory 中的设备。 |
| microsoft. directory/设备/启用 | 启用 Azure Active Directory 中的设备。 |
| microsoft.directory/signInReports/allProperties/read | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |

### <a name="company-administrator-permissions"></a>公司管理员权限

可以管理 Azure AD 和使用 Azure AD 标识的 Microsoft 服务的所有方面。 此角色也称为 "全局管理员" 角色。 

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | 创建和删除所有资源，然后读取并更新 microsoft.aad.cloudAppSecurity 中的标准属性。 |
| administrativeUnits/allProperties/allTasks | 创建和删除 administrativeUnits，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft. directory/applications/allProperties/allTasks | 创建和删除应用程序，然后读取和更新 Azure Active Directory 中的所有属性。 |
| appRoleAssignments/allProperties/allTasks | 创建和删除 appRoleAssignments，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| microsoft. directory/contacts/allProperties/allTasks | 创建和删除联系人，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft. directory/协定/allProperties/allTasks | 创建和删除协定，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft. directory/devices/allProperties/allTasks | 创建和删除设备，然后读取和更新 Azure Active Directory 中的所有属性。 |
| directoryRoles/allProperties/allTasks | 创建和删除 directoryRoles，然后读取和更新 Azure Active Directory 中的所有属性。 |
| Directoryroletemplate/allProperties/allTasks | 创建和删除 directoryRoleTemplates，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft. directory/域/allProperties/allTasks | 创建和删除域，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft. directory/groups/allProperties/allTasks | 创建和删除组，然后读取和更新 Azure Active Directory 中的所有属性。 |
| groupSettings/allProperties/allTasks | 创建和删除 groupSettings，然后读取和更新 Azure Active Directory 中的所有属性。 |
| groupSettingTemplates/allProperties/allTasks | 创建和删除 groupSettingTemplates，然后读取和更新 Azure Active Directory 中的所有属性。 |
| loginTenantBranding/allProperties/allTasks | 创建和删除 loginTenantBranding，然后读取和更新 Azure Active Directory 中的所有属性。 |
| oAuth2PermissionGrants/allProperties/allTasks | 创建和删除 oAuth2PermissionGrants，然后读取和更新 Azure Active Directory 中的所有属性。 |
| allProperties/allTasks/ | 创建和删除组织，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft. directory/策略/allProperties/allTasks | 创建和删除策略，然后读取和更新 Azure Active Directory 中的所有属性。 |
| roleAssignments/allProperties/allTasks | 创建和删除 roleAssignments，然后读取和更新 Azure Active Directory 中的所有属性。 |
| roleDefinitions/allProperties/allTasks | 创建和删除 roleDefinitions，然后读取和更新 Azure Active Directory 中的所有属性。 |
| scopedRoleMemberships/allProperties/allTasks | 创建和删除 scopedRoleMemberships，然后读取和更新 Azure Active Directory 中的所有属性。 |
| serviceAction/activateService | 可以在 Azure Active Directory 中执行 Activateservice 服务操作 |
| serviceAction/disableDirectoryFeature | 可以在 Azure Active Directory 中执行 Disabledirectoryfeature 服务操作 |
| serviceAction/enableDirectoryFeature | 可以在 Azure Active Directory 中执行 Enabledirectoryfeature 服务操作 |
| serviceAction/getAvailableExtentionProperties | 可以在 Azure Active Directory 中执行 Getavailableextentionproperties 服务操作 |
| 服务主体/allProperties/allTasks | 创建和删除 servicePrincipals，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft.directory/signInReports/allProperties/read | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |
| subscribedSkus/allProperties/allTasks | 创建和删除 subscribedSkus，然后读取和更新 Azure Active Directory 中的所有属性。 |
| microsoft. directory/users/allProperties/allTasks | 创建和删除用户，然后读取和更新 Azure Active Directory 中的所有属性。 |
| directorySync/allEntities/allTasks | 在 Azure AD Connect 中执行所有操作。 |
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

### <a name="compliance-data-administrator-permissions"></a>相容性数据管理员权限

创建并管理符合性内容。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | 阅读并配置 Microsoft Cloud App Security。 |
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

### <a name="conditional-access-administrator-permissions"></a>条件性访问管理员权限

可以管理条件性访问功能。

| **操作** | **说明** |
| --- | --- |
| conditionalAccess/basic/read | 读取 Azure Active Directory 中的 policies.conditionalAccess 属性。 |
| conditionalAccess/basic/update | 更新 Azure Active Directory 中的 policies.conditionalAccess 属性。 |
| microsoft. directory/策略/conditionalAccess/create | 在 Azure Active Directory 中创建策略。 |
| microsoft. directory/策略/conditionalAccess/delete | 删除 Azure Active Directory 中的策略。 |
| conditionalAccess/物主/物主/读取 | 读取 Azure Active Directory 中的 policies.conditionalAccess 属性。 |
| microsoft. directory/策略/conditionalAccess/物主/更新 | 更新 Azure Active Directory 中的 policies.conditionalAccess 属性。 |
| conditionalAccess/policiesAppliedTo/read | 读取 Azure Active Directory 中的 policies.conditionalAccess 属性。 |
| conditionalAccess/tenantDefault/update | 更新 Azure Active Directory 中的 policies.conditionalAccess 属性。 |

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

可以 & 策略服务中管理桌面分析和 Office 自定义。 对于桌面分析，这包括查看资产清单、创建部署计划、查看部署和运行状况的功能。 对于 Office 自定义 & 策略服务，此角色使用户能够管理 Office 策略。

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
| groupSettings/basic/read | 读取 Azure Active Directory 中 groupSettings 的基本属性。 |
| groupSettingTemplates/basic/read | 读取 Azure Active Directory 中 groupSettingTemplates 的基本属性。 |

### <a name="directory-readers-permissions"></a>目录读取器权限
可以读取基本目录信息。 用于授予对应用程序的访问权限，不针对用户。

| **操作** | **说明** |
| --- | --- |
| administrativeUnits/basic/read | 读取 Azure Active Directory 中 administrativeUnits 的基本属性。 |
| administrativeUnits/成员/读取 | 读取 Azure Active Directory 中的 administrativeUnits.members 属性。 |
| microsoft 目录/应用程序/基本/读取 | 读取 Azure Active Directory 中应用程序的基本属性。 |
| microsoft. directory/应用程序/所有者/读取 | 读取 Azure Active Directory 中的 applications.owners 属性。 |
| microsoft. 目录/应用程序/策略/读取 | 读取 Azure Active Directory 中的 applications.policies 属性。 |
| microsoft. directory/contacts/basic/read | 读取 Azure Active Directory 中联系人的基本属性。 |
| microsoft. directory/contacts/memberOf/read | 读取 Azure Active Directory 中的 contacts.memberOf 属性。 |
| microsoft. directory/协定/基本/读取 | 读取 Azure Active Directory 中协定的基本属性。 |
| microsoft. directory/设备/基本/读取 | 读取 Azure Active Directory 中设备的基本属性。 |
| microsoft. directory/设备/memberOf/read | 读取 Azure Active Directory 中的 devices.memberOf 属性。 |
| microsoft 目录/设备/Devices.registeredowners/读取 | 读取 Azure Active Directory 中的 devices.registeredOwners 属性。 |
| microsoft 目录/设备/registeredUsers/读取 | 读取 Azure Active Directory 中的 devices.registeredUsers 属性。 |
| directoryRoles/basic/read | 读取 Azure Active Directory 中 directoryRoles 的基本属性。 |
| directoryRoles/eligibleMembers/read | 读取 Azure Active Directory 中的 directoryRoles.eligibleMembers 属性。 |
| directoryRoles/成员/读取 | 读取 Azure Active Directory 中的 directoryRoles.members 属性。 |
| microsoft. directory/域/基本/读取 | 读取 Azure Active Directory 中域的基本属性。 |
| microsoft. directory/groups/appRoleAssignments/read | 读取 Azure Active Directory 中的 groups.appRoleAssignments 属性。 |
| microsoft. 目录/组/基本/读取 | 读取 Azure Active Directory 中组的基本属性。 |
| microsoft. directory/groups/memberOf/read | 读取 Azure Active Directory 中的 groups.memberOf 属性。 |
| microsoft. 目录/组/成员/读取 | 读取 Azure Active Directory 中的 groups.members 属性。 |
| microsoft. 目录/组/所有者/读取 | 读取 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft. 目录/组/设置/读取 | 读取 Azure Active Directory 中的 groups.settings 属性。 |
| groupSettings/basic/read | 读取 Azure Active Directory 中 groupSettings 的基本属性。 |
| groupSettingTemplates/basic/read | 读取 Azure Active Directory 中 groupSettingTemplates 的基本属性。 |
| oAuth2PermissionGrants/basic/read | 读取 Azure Active Directory 中 oAuth2PermissionGrants 的基本属性。 |
| microsoft. directory/组织/基本/读取 | 读取 Azure Active Directory 中组织的基本属性。 |
| microsoft. directory/组织/trustedCAsForPasswordlessAuth/读取 | 读取 Azure Active Directory 中的 organization.trustedCAsForPasswordlessAuth 属性。 |
| roleAssignments/basic/read | 读取 Azure Active Directory 中 roleAssignments 上的基本属性。 |
| roleDefinitions/basic/read | 读取 Azure Active Directory 中 roleDefinitions 上的基本属性。 |
| 服务主体/appRoleAssignedTo/read | 读取 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 属性。 |
| 服务主体/appRoleAssignments/read | 读取 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 属性。 |
| 服务主体/basic/read | 读取 Azure Active Directory 中 servicePrincipals 的基本属性。 |
| 服务主体/memberOf/read | 读取 Azure Active Directory 中的 servicePrincipals.memberOf 属性。 |
| 服务主体/oAuth2PermissionGrants/basic/read | 读取 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 属性。 |
| 服务主体/ownedObjects/read | 读取 Azure Active Directory 中的 servicePrincipals.ownedObjects 属性。 |
| 服务主体/物主/物主/读取 | 读取 Azure Active Directory 中的 servicePrincipals.owners 属性。 |
| 服务主体/策略/读取 | 读取 Azure Active Directory 中的 servicePrincipals.policies 属性。 |
| subscribedSkus/basic/read | 读取 Azure Active Directory 中 subscribedSkus 的基本属性。 |
| microsoft. directory/users/appRoleAssignments/read | 读取 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft. directory/users/basic/read | 读取 Azure Active Directory 中用户的基本属性。 |
| microsoft. directory/users/directReports/read | 读取 Azure Active Directory 中的 users.directReports 属性。 |
| microsoft. 目录/用户/经理/读取 | 读取 Azure Active Directory 中的 users.manager 属性。 |
| microsoft. directory/users/memberOf/read | 读取 Azure Active Directory 中的 users.memberOf 属性。 |
| microsoft. directory/users/oAuth2PermissionGrants/basic/read | 读取 Azure Active Directory 中的 users.oAuth2PermissionGrants 属性。 |
| microsoft. directory/users/ownedDevices/read | 读取 Azure Active Directory 中的 users.ownedDevices 属性。 |
| microsoft. directory/users/ownedObjects/read | 读取 Azure Active Directory 中的 users.ownedObjects 属性。 |
| microsoft. directory/users/registeredDevices/read | 读取 Azure Active Directory 中的 users.registeredDevices 属性。 |

### <a name="directory-synchronization-accounts-permissions"></a>目录同步帐户权限

仅供 Azure AD Connect 服务使用。

| **操作** | **说明** |
| --- | --- |
| microsoft. directory/组织/dirSync/update | 更新 Azure Active Directory 中的 organization.dirSync 属性。 |
| microsoft. directory/策略/创建 | 在 Azure Active Directory 中创建策略。 |
| microsoft.directory/policies/delete | 删除 Azure Active Directory 中的策略。 |
| microsoft. directory/策略/基本/读取 | 读取 Azure Active Directory 中策略的基本属性。 |
| microsoft.directory/policies/basic/update | 更新 Azure Active Directory 中策略的基本属性。 |
| microsoft. directory/策略/所有者/读取 | 读取 Azure Active Directory 中的 policies.owners 属性。 |
| microsoft.directory/policies/owners/update | 更新 Azure Active Directory 中的 policies.owners 属性。 |
| microsoft. directory/策略/policiesAppliedTo/read | 读取 Azure Active Directory 中的 policies.policiesAppliedTo 属性。 |
| microsoft. directory/策略/tenantDefault/update | 更新 Azure Active Directory 中的 policies.tenantDefault 属性。 |
| 服务主体/appRoleAssignedTo/read | 读取 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 属性。 |
| 服务主体/appRoleAssignments/read | 读取 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 属性。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 属性。 |
| microsoft.directory/servicePrincipals/audience/update | 更新 Azure Active Directory 中的 servicePrincipals.audience 属性。 |
| microsoft.directory/servicePrincipals/authentication/update | 更新 Azure Active Directory 中的 servicePrincipals.authentication 属性。 |
| 服务主体/basic/read | 读取 Azure Active Directory 中 servicePrincipals 的基本属性。 |
| microsoft.directory/servicePrincipals/basic/update | 更新 Azure Active Directory 中 servicePrincipals 的基本属性。 |
| 服务主体/create | 在 Azure Active Directory 中创建 servicePrincipals。 |
| microsoft.directory/servicePrincipals/credentials/update | 更新 Azure Active Directory 中的 servicePrincipals.credentials 属性。 |
| 服务主体/memberOf/read | 读取 Azure Active Directory 中的 servicePrincipals.memberOf 属性。 |
| 服务主体/oAuth2PermissionGrants/basic/read | 读取 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 属性。 |
| 服务主体/物主/物主/读取 | 读取 Azure Active Directory 中的 servicePrincipals.owners 属性。 |
| microsoft.directory/servicePrincipals/owners/update | 更新 Azure Active Directory 中的 servicePrincipals.owners 属性。 |
| 服务主体/ownedObjects/read | 读取 Azure Active Directory 中的 servicePrincipals.ownedObjects 属性。 |
| microsoft.directory/servicePrincipals/permissions/update | 更新 Azure Active Directory 中的 servicePrincipals.permissions 属性。 |
| 服务主体/策略/读取 | 读取 Azure Active Directory 中的 servicePrincipals.policies 属性。 |
| microsoft.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 属性。 |
| directorySync/allEntities/allTasks | 在 Azure AD Connect 中执行所有操作。 |

### <a name="directory-writers-permissions"></a>目录写入者权限

可以读取和写入基本目录信息。 用于授予对应用程序的访问权限，不针对用户。

| **操作** | **说明** |
| --- | --- |
| microsoft. 目录/组/创建 | 在 Azure Active Directory 中创建组。 |
| microsoft. directory/groups/createAsOwner | 在 Azure Active Directory 中创建组。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.directory/groups/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.appRoleAssignments 属性。 |
| microsoft.directory/groups/basic/update | 更新 Azure Active Directory 中组的基本属性。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 属性。 |
| groupSettings/basic/update | 更新 Azure Active Directory 中 groupSettings 的基本属性。 |
| groupSettings/create | 在 Azure Active Directory 中创建 groupSettings 属性。 |
| groupSettings/删除 | 删除 Azure Active Directory 中的 groupSettings。 |
| microsoft. directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft. directory/users/assignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft. directory/users/basic/update | 更新 Azure Active Directory 中用户的基本属性。 |
| microsoft. directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft. 目录/用户/管理器/更新 | 更新 Azure Active Directory 中的 users.manager 属性。 |
| microsoft. directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 属性。 |

### <a name="exchange-service-administrator-permissions"></a>Exchange 服务管理员权限

可以管理 Exchange 产品的所有方面。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft. directory/groups/统一/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.unified 属性。 |
| microsoft. directory/groups/统一/基本/更新 | 更新 Office 365 组的基本属性。 |
| microsoft. directory/groups/统一/创建 | 创建 Office 365 组。 |
| microsoft. directory/groups/统一/删除 | 删除 Office 365 组。 |
| microsoft. directory/groups/统一/成员/更新 | 更新 Office 365 组的成员身份。 |
| microsoft. directory/groups/统一/所有者/更新 | 更新 Office 365 组的所有权。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.exchange/allEntities/allTasks | 管理 Exchange Online 的各个方面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="external-identity-provider-administrator-permissions"></a>外部标识提供者管理员权限

配置标识提供程序以在直接联合中使用。

| **操作** | **说明** |
| --- | --- |
| allTasks/identityProviders/ | 在 Azure Active Directory B2C 中读取和配置标识提供者。 |

### <a name="global-reader-permissions"></a>全局读者权限
可以读取全局管理员可以使用的所有内容，但不能编辑任何内容。 

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的[角色说明](#global-reader)。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft allEntities/读取   | 阅读 Office 365 帐单的所有方面。 |
| administrativeUnits/basic/read    | 读取 Azure Active Directory 中 administrativeUnits 的基本属性。 |
| administrativeUnits/成员/读取  | 读取 Azure Active Directory 中的 administrativeUnits.members 属性。 |
| microsoft 目录/应用程序/基本/读取   | 读取 Azure Active Directory 中应用程序的基本属性。 |
| microsoft. directory/应用程序/所有者/读取  | 读取 Azure Active Directory 中的 applications.owners 属性。 |
| microsoft. 目录/应用程序/策略/读取    | 读取 Azure Active Directory 中的 applications.policies 属性。 |
| microsoft. directory/contacts/basic/read   | 读取 Azure Active Directory 中联系人的基本属性。 |
| microsoft. directory/contacts/memberOf/read    | 读取 Azure Active Directory 中的 contacts.memberOf 属性。 |
| microsoft. directory/协定/基本/读取  | 读取 Azure Active Directory 中协定的基本属性。 |
| microsoft. directory/设备/基本/读取    | 读取 Azure Active Directory 中设备的基本属性。 |
| microsoft. directory/设备/memberOf/read | 读取 Azure Active Directory 中的 devices.memberOf 属性。 |
| microsoft 目录/设备/Devices.registeredowners/读取 | 读取 Azure Active Directory 中的 devices.registeredOwners 属性。 |
| microsoft 目录/设备/registeredUsers/读取  | 读取 Azure Active Directory 中的 devices.registeredUsers 属性。 |
| directoryRoles/basic/read | 读取 Azure Active Directory 中 directoryRoles 的基本属性。 |
| directoryRoles/eligibleMembers/read   | 读取 Azure Active Directory 中的 directoryRoles.eligibleMembers 属性。 |
| directoryRoles/成员/读取   | 读取 Azure Active Directory 中的 directoryRoles.members 属性。 |
| microsoft. directory/域/基本/读取    | 读取 Azure Active Directory 中域的基本属性。 |
| microsoft. directory/groups/appRoleAssignments/read    | 读取 Azure Active Directory 中的 groups.appRoleAssignments 属性。 |
| microsoft. 目录/组/基本/读取 | 读取 Azure Active Directory 中组的基本属性。 |
| microsoft. directory/groups/hiddenMembers/read | 读取 Azure Active Directory 中的 groups.hiddenMembers 属性。 |
| microsoft. directory/groups/memberOf/read  | 读取 Azure Active Directory 中的 groups.memberOf 属性。 |
| microsoft. 目录/组/成员/读取   | 读取 Azure Active Directory 中的 groups.members 属性。 |
| microsoft. 目录/组/所有者/读取    | 读取 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft. 目录/组/设置/读取  | 读取 Azure Active Directory 中的 groups.settings 属性。 |
| groupSettings/basic/read  | 读取 Azure Active Directory 中 groupSettings 的基本属性。 |
| groupSettingTemplates/basic/read  | 读取 Azure Active Directory 中 groupSettingTemplates 的基本属性。 |
| oAuth2PermissionGrants/basic/read | 读取 Azure Active Directory 中 oAuth2PermissionGrants 的基本属性。 |
| microsoft. directory/组织/基本/读取   | 读取 Azure Active Directory 中组织的基本属性。 |
| microsoft. directory/组织/trustedCAsForPasswordlessAuth/读取   | 读取 Azure Active Directory 中的 organization.trustedCAsForPasswordlessAuth 属性。 |
| microsoft. directory/策略/标准/读取    | 读取 Azure Active Directory 中的标准策略。 |
| roleAssignments/basic/read    | 读取 Azure Active Directory 中 roleAssignments 上的基本属性。 |
| roleDefinitions/basic/read    | 读取 Azure Active Directory 中 roleDefinitions 上的基本属性。 |
| 服务主体/appRoleAssignedTo/read  | 读取 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 属性。 |
| 服务主体/appRoleAssignments/read | 读取 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 属性。 |
| 服务主体/basic/read  | 读取 Azure Active Directory 中 servicePrincipals 的基本属性。 |
| 服务主体/memberOf/read   | 读取 Azure Active Directory 中的 servicePrincipals.memberOf 属性。 |
| 服务主体/oAuth2PermissionGrants/basic/read   | 读取 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 属性。 |
| 服务主体/ownedObjects/read   | 读取 Azure Active Directory 中的 servicePrincipals.ownedObjects 属性。 |
| 服务主体/物主/物主/读取 | 读取 Azure Active Directory 中的 servicePrincipals.owners 属性。 |
| 服务主体/策略/读取   | 读取 Azure Active Directory 中的 servicePrincipals.policies 属性。 |
| microsoft.directory/signInReports/allProperties/read  | 读取 Azure Active Directory 中 signInReports 上的所有属性（包括特权属性）。 |
| subscribedSkus/basic/read | 读取 Azure Active Directory 中 subscribedSkus 的基本属性。 |
| microsoft. directory/users/appRoleAssignments/read | 读取 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft. directory/users/basic/read  | 读取 Azure Active Directory 中用户的基本属性。 |
| microsoft. directory/users/directReports/read  | 读取 Azure Active Directory 中的 users.directReports 属性。 |
| microsoft. 目录/用户/经理/读取    | 读取 Azure Active Directory 中的 users.manager 属性。 |
| microsoft. directory/users/memberOf/read   | 读取 Azure Active Directory 中的 users.memberOf 属性。 |
| microsoft. directory/users/oAuth2PermissionGrants/basic/read   | 读取 Azure Active Directory 中的 users.oAuth2PermissionGrants 属性。 |
| microsoft. directory/users/ownedDevices/read   | 读取 Azure Active Directory 中的 users.ownedDevices 属性。 |
| microsoft. directory/users/ownedObjects/read   | 读取 Azure Active Directory 中的 users.ownedObjects 属性。 |
| microsoft. directory/users/registeredDevices/read  | 读取 Azure Active Directory 中的 users.registeredDevices 属性。 |
| microsoft. directory/users/strongAuthentication/read   | 读取强身份验证属性，如 MFA 凭据信息。 |
| office365/allEntities/read | 阅读 Exchange Online 的所有方面。 |
| microsoft.office365.messageCenter/messages/read   | 读取 microsoft.office365.messageCenter 中的消息。 |
| microsoft.office365.messageCenter/securityMessages/read   | 读取 microsoft.office365.messageCenter 中的安全消息。 |
| microsoft.office365.protectionCenter/allEntities/read | 读取 Office 365 防护中心的各个方面。 |
| office365. securityComplianceCenter/allEntities/read | 读取 office365. securityComplianceCenter 中的所有标准属性。 |
| microsoft.office365.usageReports/allEntities/read | 阅读 Office 365 使用情况报告。 |
| office365. webPortal/allEntities/standard/read   | 读取 office365. webPortal 中所有资源的标准属性。 |

### <a name="groups-administrator-permissions"></a>组管理员权限
可以管理组和组设置的所有方面，如命名和过期策略。

| **操作** | **说明** |
| --- | --- |
| microsoft. 目录/组/基本/读取 | 读取 Azure Active Directory 中组的标准属性。  |
| microsoft.directory/groups/basic/update | 更新 Azure Active Directory 中组的基本属性。 |
| microsoft. 目录/组/创建 | 在 Azure Active Directory 中创建组。 |
| microsoft. directory/groups/createAsOwner | 在 Azure Active Directory 中创建组。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.directory/groups/delete | 删除 Azure Active Directory 中的组。 |
| microsoft. directory/groups/hiddenMembers/read | 读取 Azure Active Directory 中的 groups.hiddenMembers 属性。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft.directory/groups/restore | 还原 Azure Active Directory 中的组。 |
| microsoft.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 属性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.messageCenter/messages/read | 读取 microsoft.office365.messageCenter 中的消息。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |
| microsoft.office365.usageReports/allEntities/read | 阅读 Office 365 使用情况报告。 |

### <a name="guest-inviter-permissions"></a>来宾邀请者权限
可以邀请与“成员可邀请来宾”设置无关的来宾用户。

| **操作** | **说明** |
| --- | --- |
| microsoft. directory/users/appRoleAssignments/read | 读取 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft. directory/users/basic/read | 读取 Azure Active Directory 中用户的基本属性。 |
| microsoft. directory/users/directReports/read | 读取 Azure Active Directory 中的 users.directReports 属性。 |
| microsoft. directory/users/inviteGuest | 邀请 Azure Active Directory 中的来宾用户。 |
| microsoft. 目录/用户/经理/读取 | 读取 Azure Active Directory 中的 users.manager 属性。 |
| microsoft. directory/users/memberOf/read | 读取 Azure Active Directory 中的 users.memberOf 属性。 |
| microsoft. directory/users/oAuth2PermissionGrants/basic/read | 读取 Azure Active Directory 中的 users.oAuth2PermissionGrants 属性。 |
| microsoft. directory/users/ownedDevices/read | 读取 Azure Active Directory 中的 users.ownedDevices 属性。 |
| microsoft. directory/users/ownedObjects/read | 读取 Azure Active Directory 中的 users.ownedObjects 属性。 |
| microsoft. directory/users/registeredDevices/read | 读取 Azure Active Directory 中的 users.registeredDevices 属性。 |

### <a name="helpdesk-administrator-permissions"></a>支持人员管理员权限

可以重置非管理员和支持理员的密码。

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | 读取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 属性。 |
| microsoft. directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft. 目录/用户/密码/更新 | 更新 Azure Active Directory 中所有用户的密码。 有关详细信息，请参阅联机文档。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
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
| microsoft. directory/contacts/基本/更新 | 更新 Azure Active Directory 中联系人的基本属性。 |
| microsoft. 目录/联系人/创建 | 在 Azure Active Directory 中创建联系人。 |
| microsoft. 目录/联系人/删除 | 删除 Azure Active Directory 中的联系人。 |
| microsoft. directory/设备/基本/更新 | 更新 Azure Active Directory 中设备的基本属性。 |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | 读取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 属性。 |
| microsoft. directory/设备/创建 | 在 Azure Active Directory 中创建设备。 |
| microsoft. directory/设备/删除 | 删除 Azure Active Directory 中的设备。 |
| microsoft. directory/devices/Devices.registeredowners/update | 更新 Azure Active Directory 中的 devices.registeredOwners 属性。 |
| microsoft. directory/devices/registeredUsers/update | 更新 Azure Active Directory 中的 devices.registeredUsers 属性。 |
| microsoft.directory/groups/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.appRoleAssignments 属性。 |
| microsoft.directory/groups/basic/update | 更新 Azure Active Directory 中组的基本属性。 |
| microsoft. 目录/组/创建 | 在 Azure Active Directory 中创建组。 |
| microsoft. directory/groups/createAsOwner | 在 Azure Active Directory 中创建组。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.directory/groups/delete | 删除 Azure Active Directory 中的组。 |
| microsoft. directory/groups/hiddenMembers/read | 读取 Azure Active Directory 中的 groups.hiddenMembers 属性。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft.directory/groups/restore | 还原 Azure Active Directory 中的组。 |
| microsoft.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 属性。 |
| microsoft. directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft. directory/users/basic/update | 更新 Azure Active Directory 中用户的基本属性。 |
| microsoft. 目录/用户/管理器/更新 | 更新 Azure Active Directory 中的 users.manager 属性。 |
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
| microsoft.office365.webPortal/allEntities/basic/read | 阅读 Office 365 管理中心。 |

### <a name="license-administrator-permissions"></a>许可证管理员权限

可以管理用户和组的产品许可证。

| **操作** | **说明** |
| --- | --- |
| microsoft. directory/users/assignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft. directory/users/usageLocation/update | 更新 Azure Active Directory 中的 users.usageLocation 属性。 |
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
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理 Skype for Business Online 的各个方面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="message-center-privacy-reader-permissions"></a>消息中心隐私读取器权限

可以读取消息中心发布、数据隐私消息、组、域和订阅。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.messageCenter/messages/read | 读取 microsoft.office365.messageCenter 中的消息。 |
| microsoft.office365.messageCenter/securityMessages/read | 读取 microsoft.office365.messageCenter 中的安全消息。 |

### <a name="message-center-reader-permissions"></a>消息中心读取器权限
只能在 Office 365 消息中心查看其组织的消息和更新。 

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.messageCenter/messages/read | 读取 microsoft.office365.messageCenter 中的消息。 |

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
| microsoft. directory/contacts/基本/更新 | 更新 Azure Active Directory 中联系人的基本属性。 |
| microsoft. 目录/联系人/创建 | 在 Azure Active Directory 中创建联系人。 |
| microsoft. 目录/联系人/删除 | 删除 Azure Active Directory 中的联系人。 |
| microsoft. 目录/组/创建 | 在 Azure Active Directory 中创建组。 |
| microsoft. directory/groups/createAsOwner | 在 Azure Active Directory 中创建组。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft. directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft. directory/users/assignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft. directory/users/basic/update | 更新 Azure Active Directory 中用户的基本属性。 |
| microsoft. 目录/用户/删除 | 删除 Azure Active Directory 中的用户。 |
| microsoft. directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft. 目录/用户/管理器/更新 | 更新 Azure Active Directory 中的 users.manager 属性。 |
| microsoft. 目录/用户/密码/更新 | 更新 Azure Active Directory 中所有用户的密码。 有关详细信息，请参阅联机文档。 |
| microsoft. directory/users/restore | 还原 Azure Active Directory 中已删除的用户。 |
| microsoft. directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 属性。 |
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
| microsoft. directory/contacts/基本/更新 | 更新 Azure Active Directory 中联系人的基本属性。 |
| microsoft. 目录/联系人/创建 | 在 Azure Active Directory 中创建联系人。 |
| microsoft. 目录/联系人/删除 | 删除 Azure Active Directory 中的联系人。 |
| microsoft. directory/域/allTasks | 创建和删除域，然后读取和更新 Azure Active Directory 中的标准属性。 |
| microsoft. 目录/组/创建 | 在 Azure Active Directory 中创建组。 |
| microsoft.directory/groups/delete | 删除 Azure Active Directory 中的组。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.directory/groups/restore | 还原 Azure Active Directory 中的组。 |
| microsoft. directory/组织/基本/更新 | 更新 Azure Active Directory 中组织的基本属性。 |
| microsoft. directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft. directory/users/assignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft. directory/users/basic/update | 更新 Azure Active Directory 中用户的基本属性。 |
| microsoft. 目录/用户/删除 | 删除 Azure Active Directory 中的用户。 |
| microsoft. directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft. 目录/用户/管理器/更新 | 更新 Azure Active Directory 中的 users.manager 属性。 |
| microsoft. 目录/用户/密码/更新 | 更新 Azure Active Directory 中所有用户的密码。 有关详细信息，请参阅联机文档。 |
| microsoft. directory/users/restore | 还原 Azure Active Directory 中已删除的用户。 |
| microsoft. directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 属性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="password-administrator-permissions"></a>密码管理员权限

可以重置非管理员和密码管理员的密码。

| **操作** | **说明** |
| --- | --- |
| microsoft. 目录/用户/密码/更新 | 更新 Azure Active Directory 中所有用户的密码。 有关详细信息，请参阅联机文档。 |
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
| dynamics365/allEntities/allTasks | 管理 Dynamics 365 的各个方面。 |
| allEntities/allTasks | 管理 Microsoft Flow 的所有方面。 |
| allEntities/allTasks | 管理 PowerApps 的所有方面。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="privileged-authentication-administrator-permissions"></a>特权身份验证管理员权限

允许查看、设置和重置任何用户（管理员或非管理员）的身份验证方法信息。

| **操作** | **说明** |
| --- | --- |
| microsoft. directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft. directory/users/strongAuthentication/update | 更新强身份验证属性，如 MFA 凭据信息。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |
| microsoft. 目录/用户/密码/更新 | 更新 Office 365 组织中所有用户的密码。 有关详细信息，请参阅联机文档。 |

### <a name="privileged-role-administrator-permissions"></a>特权角色管理员权限

可以管理 Azure AD 中的角色分配，以及 Privileged Identity Management 的所有方面。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | 创建和删除所有资源，然后读取和更新 microsoft.aad.privilegedIdentityManagement 中的标准属性。 |
| 服务主体/appRoleAssignedTo/allTasks | 读取并配置 Azure Active Directory 中的服务主体 appRoleAssignedTo 属性。 |
| 服务主体/oAuth2PermissionGrants/allTasks | 读取并配置 Azure Active Directory 中的服务主体 oAuth2PermissionGrants 属性。 |
| administrativeUnits/allProperties/allTasks | 创建和管理管理单元（包括成员） |
| roleAssignments/allProperties/allTasks | 创建和管理角色分配。 |
| roleDefinitions/allProperties/allTasks | 创建和管理角色定义。 |

### <a name="reports-reader-permissions"></a>报表读者权限

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

可以创建和管理 Microsoft 搜索设置的所有方面。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | 读取 microsoft.office365.messageCenter 中的消息。 |
| microsoft.office365.search/allEntities/allProperties/allTasks | 创建和删除所有资源，然后读取和更新 office365 中的所有属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |
| microsoft.office365.usageReports/allEntities/read | 阅读 Office 365 使用情况报告。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |

### <a name="search-editor-permissions"></a>搜索编辑器权限

可以创建和管理编辑内容，例如书签、Q 和 As、位置、floorplan。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | 读取 microsoft.office365.messageCenter 中的消息。 |
| microsoft.office365.search/content/allProperties/allTasks | 创建和删除内容，并读取和更新 office365 中的所有属性。 |
| microsoft.office365.usageReports/allEntities/read | 阅读 Office 365 使用情况报告。 |

### <a name="security-administrator-permissions"></a>安全管理员权限

可以读取安全信息和报告，并管理 Azure AD 和 Office 365 中的配置。

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
| microsoft. directory/策略/创建 | 在 Azure Active Directory 中创建策略。 |
| microsoft.directory/policies/delete | 删除 Azure Active Directory 中的策略。 |
| microsoft.directory/policies/owners/update | 更新 Azure Active Directory 中的 policies.owners 属性。 |
| microsoft. directory/策略/tenantDefault/update | 更新 Azure Active Directory 中的 policies.tenantDefault 属性。 |
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
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | 阅读并配置 Microsoft Cloud App Security。 |
| microsoft.aad.identityProtection/allEntities/read | 读取 microsoft.aad.identityProtection 中的所有资源。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 读取 microsoft.aad.privilegedIdentityManagement 中的所有资源。 |
| microsoft.azure.advancedThreatProtection/allEntities/read | 阅读并配置 Azure AD 高级威胁防护。 |
| microsoft.intune/allEntities/allTasks | 管理 Intune 的各个方面。 |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | 阅读并配置安全与合规中心。 |
| microsoft.office365.usageReports/allEntities/read | 阅读 Office 365 使用情况报告。 |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | 阅读并配置 Windows Defender 高级威胁防护。 |

### <a name="security-reader-permissions"></a>安全读者权限

可以读取 Azure AD 和 Office 365 中的安全信息和报表。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | 读取 Azure Active Directory 中 auditLogs 上的所有属性（包括特权属性）。 |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | 读取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 属性。 |
| conditionalAccess/basic/read | 读取 Azure Active Directory 中的 policies.conditionalAccess 属性。 |
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
| microsoft. directory/groups/统一/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.unified 属性。 |
| microsoft. directory/groups/统一/基本/更新 | 更新 Office 365 组的基本属性。 |
| microsoft. directory/groups/统一/创建 | 创建 Office 365 组。 |
| microsoft. directory/groups/统一/删除 | 删除 Office 365 组。 |
| microsoft. directory/groups/统一/成员/更新 | 更新 Office 365 组的成员身份。 |
| microsoft. directory/groups/统一/所有者/更新 | 更新 Office 365 组的所有权。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.sharepoint/allEntities/allTasks | 创建和删除所有资源，然后读取和更新 microsoft.office365.sharepoint 中的标准属性。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

### <a name="teams-communications-administrator-permissions"></a>团队通信管理员权限

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

### <a name="teams-communications-support-engineer-permissions"></a>团队通信支持工程师权限

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

### <a name="teams-communications-support-specialist-permissions"></a>团队通信支持专家权限

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

### <a name="teams-service-administrator-permissions"></a>团队服务管理员权限

可以管理 Microsoft Teams 服务。

> [!NOTE]
> 此角色拥有 Azure Active Directory 外部的其他权限。 有关详细信息，请参阅上面的角色说明。
>
>

| **操作** | **说明** |
| --- | --- |
| microsoft. directory/groups/hiddenMembers/read | 读取 Azure Active Directory 中的 groups.hiddenMembers 属性。 |
| microsoft. directory/groups/统一/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.unified 属性。 |
| microsoft. directory/groups/统一/基本/更新 | 更新 Office 365 组的基本属性。 |
| microsoft. directory/groups/统一/创建 | 创建 Office 365 组。 |
| microsoft. directory/groups/统一/删除 | 删除 Office 365 组。 |
| microsoft. directory/groups/统一/成员/更新 | 更新 Office 365 组的成员身份。 |
| microsoft. directory/groups/统一/所有者/更新 | 更新 Office 365 组的所有权。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |
| microsoft.office365.usageReports/allEntities/read | 阅读 Office 365 使用情况报告。 |

### <a name="user-administrator-permissions"></a>用户管理员权限
可以管理用户和组的所有方面，包括重置有限管理员的密码。

| **操作** | **说明** |
| --- | --- |
| appRoleAssignments/create | 在 Azure Active Directory 中创建 appRoleAssignments。 |
| appRoleAssignments/删除 | 删除 Azure Active Directory 中的 appRoleAssignments。 |
| appRoleAssignments/update | 更新 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft. directory/contacts/基本/更新 | 更新 Azure Active Directory 中联系人的基本属性。 |
| microsoft. 目录/联系人/创建 | 在 Azure Active Directory 中创建联系人。 |
| microsoft. 目录/联系人/删除 | 删除 Azure Active Directory 中的联系人。 |
| microsoft.directory/groups/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.appRoleAssignments 属性。 |
| microsoft.directory/groups/basic/update | 更新 Azure Active Directory 中组的基本属性。 |
| microsoft. 目录/组/创建 | 在 Azure Active Directory 中创建组。 |
| microsoft. directory/groups/createAsOwner | 在 Azure Active Directory 中创建组。 添加创建者作为第一个所有者，创建的对象根据创建者的 250 个创建对象配额计数。 |
| microsoft.directory/groups/delete | 删除 Azure Active Directory 中的组。 |
| microsoft. directory/groups/hiddenMembers/read | 读取 Azure Active Directory 中的 groups.hiddenMembers 属性。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 属性。 |
| microsoft.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 属性。 |
| microsoft.directory/groups/restore | 还原 Azure Active Directory 中的组。 |
| microsoft.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 属性。 |
| microsoft. directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 属性。 |
| microsoft. directory/users/assignLicense | 管理 Azure Active Directory 中用户的许可证。 |
| microsoft. directory/users/basic/update | 更新 Azure Active Directory 中用户的基本属性。 |
| microsoft. 目录/用户/创建 | 在 Azure Active Directory 中创建用户。 |
| microsoft. 目录/用户/删除 | 删除 Azure Active Directory 中的用户。 |
| microsoft. directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有用户刷新令牌无效。 |
| microsoft. 目录/用户/管理器/更新 | 更新 Azure Active Directory 中的 users.manager 属性。 |
| microsoft. 目录/用户/密码/更新 | 更新 Azure Active Directory 中所有用户的密码。 有关详细信息，请参阅联机文档。 |
| microsoft. directory/users/restore | 还原 Azure Active Directory 中已删除的用户。 |
| microsoft. directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 属性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 读取和配置 Azure 服务运行状况。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 创建和管理 Azure 支持票证。 |
| microsoft.office365.webPortal/allEntities/basic/read | 读取 microsoft.office365.webPortal 中所有资源的基本属性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 读取和配置 Office 365 服务运行状况。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 创建和管理 Office 365 支持票证。 |

## <a name="role-template-ids"></a>角色模板 Id

角色模板 Id 主要由图形 API 或 PowerShell 用户使用。

关系图 displayName | Azure 门户显示名称 | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
应用程序管理员 | 应用程序管理员 | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
应用程序开发人员 | 应用程序开发人员 | CF1C38E5-3621-4004-A7CB-879624DCED7C
身份验证管理员 | 身份验证管理员 | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure DevOps 管理员 | Azure DevOps 管理员 | e3973bdf-4987-49ae-837a-ba8e231c7286
Azure 信息保护管理员 | Azure 信息保护管理员 | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C 用户流管理员 | B2C 用户流管理员 | 6e591065-9bad-43ed-90f3-e9424366d2f0
B2C 用户流属性管理员 | B2C 用户流属性管理员 | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
B2C IEF 键集管理员 | B2C IEF 键集管理员 | aaf43236-0c0d-4d5f-883a-6955382ac081
B2C IEF 策略管理员 | B2C IEF 策略管理员 | 3edaf663-341e-4475-9f94-5c398ef6c070
计费管理员 | 计费管理员 | b0f54661-2d74-4c50-afa3-1ec803f12efe
云应用管理员 | 云应用程序管理员 | 158c047a-c907-4556-b7ef-446551a6b5f7
云设备管理员 | 云设备管理员 | 7698a772-787b-4ac8-901f-60d6b08affd2
公司管理员 | 全局管理员 | 62e90394-69f5-4237-9190-012177145e10
符合性管理员 | 法规管理员 | 17315797-102d-40b4-93e0-432062caca18
相容性数据管理员 | 相容性数据管理员 | e6d1a23a-da11-4be4-9570-befc86d067a7
条件访问管理员 | 条件访问管理员 | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
CRM 服务管理员 | Dynamics 365 管理员 | 44367163-eba1-44c3-98af-f5787879f96a
客户密码箱访问审批者 | 客户密码箱访问审批者 | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
桌面分析管理员 | 桌面分析管理员 | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
设备管理员 | 设备管理员 | 9f06204d-73c1-4d4c-880a-6edb90606fd8
设备联接 | 设备加入 | 9c094953-4995-41c8-84c8-3ebb9b32c93f
设备管理器 | 设备管理器 | 2b499bcd-da44-4968-8aec-78e1674fa64d
设备用户 | 设备用户 | d405c6df-0af8-4e3b-95e4-4d06e542189e
目录读者 | 目录读者 | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
目录同步帐户 | 目录同步帐户 | d29b2b05-8046-44ba-8758-1e26182fcf32
目录编写人员 | 目录写入者 | 9360feb5-f418-4baa-8175-e2a00bac4301
Exchange 服务管理员 | Exchange 管理员 | 29232cdf-9323-42fd-ade2-1d097af3e4de
外部标识提供者管理员 | 外部标识提供者管理员 | be2f45a1-457d-42af-a067-6ec1fa63bc45
全局读取器 | 全局读取器 | f2ef992c-3afb-46b9-b7cf-a126ee74c451
组管理员 | 组管理员 | fdd7a751-b60b-444a-984c-02652fe8fa1c 
来宾邀请者 | 来宾邀请者 | 95e79109-95c0-4d8e-aee3-d01accf2d47b
支持管理员 | 支持管理员 | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Intune 服务管理员 | Intune 管理员 | 3a2c62db-5318-420d-8d74-23affee5d9d5
Kaizala 管理员 | Kaizala 管理员 | 74ef975b-6605-40af-a5d2-b9539d836353
许可证管理员 | 许可证管理员 | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Lync 服务管理员 | Skype for Business 管理员 | 75941009-915a-4869-abe7-691bff18279e
消息中心隐私读取器 | 消息中心隐私读取器 | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
消息中心读取者 | 消息中心读取器 | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Office 应用管理员 | Office 应用管理员 | 2b745bdf-0803-4d80-aa65-822c4493daac
合作伙伴一线支持人员 | 合作伙伴一线支持人员 | 4ba39ca4-527c-499a-b93d-d9b492c50246
合作伙伴二线支持人员 | 合作伙伴二线支持人员 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
密码管理员 | 密码管理员 | 966707d0-3269-4727-9be2-8c3a10f19b9d
Power BI 服务管理员 | Power BI 管理员 | a9ea8996-122f-4c74-9520-8edcd192826c
Power Platform 管理员 | Power 平台管理员 | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
特权身份验证管理员 | 特权身份验证管理员 | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
特权角色管理员 | 特权角色管理员 | e8611ab8-c189-46e8-94e1-60213ab1f814
报告读者 | 报告读者 | 4a5d8f65-41da-4de4-8968-e035b65339cf
搜索管理员 | 搜索管理员 | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
搜索编辑器 | 搜索编辑器 | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
安全管理员 | 安全管理员 | 194ae4cb-b126-40b2-bd5b-6091b380977d
安全操作员 | 安全操作员 | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
安全读取者 | 安全读取者 | 5d6b6bb7-de71-4623-b4af-96380a352509
服务支持管理员 | 服务管理员 | f023fd81-a637-4b56-95fd-791ac0226033
SharePoint 服务管理员 | SharePoint 管理员 | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Teams 通信管理员 | Teams 通信管理员 | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Teams 通信支持工程师 | Teams 通信支持工程师 | f70938a0-fc10-4177-9e90-2178f8765737
Teams 通信支持专家 | Teams 通信支持专家 | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Teams 服务管理员 | Teams 服务管理员 | 69091246-20e8-4a56-aa4d-066075b2a7a8
用户 | 用户 | a0b1b346-4d3e-4e8b-98f8-753987be4970
用户帐户管理员 | 用户管理员 | fe930be7-5e62-47db-91af-98c3a49a38b1
工作区设备联接 | 工作区设备加入 | c34f683f-4d5a-4403-affd-6615e00e3a7f

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
