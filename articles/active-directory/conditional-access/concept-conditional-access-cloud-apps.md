---
title: 条件访问策略中的云应用或操作 - Azure Active Directory
description: Azure AD 条件访问策略中的云应用或操作是什么
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2512602b0c8c00c696253a708af13278bc513a1f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258738"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>条件访问：云应用或操作

云应用或操作是条件访问策略中的一个关键信号。 管理员可以使用条件访问策略将控制措施分配给特定的应用程序或操作。

- 管理员可以从包括内置 Microsoft 应用程序以及任何 [Azure AD 集成应用程序](../manage-apps/what-is-application-management.md)（包括库、非库和通过[应用程序代理](../manage-apps/what-is-application-proxy.md)发布的应用程序）的应用程序列表进行选择。
- 管理员可以选择定义一个并非基于云应用程序，而是基于用户操作的策略。 唯一支持的操作是“注册安全信息”（预览版），此操作使条件访问能够围绕[组合安全信息注册体验](../authentication/howto-registration-mfa-sspr-combined.md)强制实施控制。

![定义条件访问策略并指定云应用](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft 云应用程序

许多现有 Microsoft 云应用程序都包含在可供选择的应用程序的列表中。 

管理员可以向 Microsoft 提供的以下云应用分配条件访问策略。 某些应用（如 Office 365 和 Microsoft Azure 管理）包含多个相关的子应用或服务。 以下列表并不完整，且随时可能会更改。

- [Office 365](#office-365)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL 数据库和数据仓库](../../azure-sql/database/conditional-access-configure.md)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- [Microsoft Azure 信息保护](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure 管理](#microsoft-azure-management)
- Microsoft Azure 订阅管理
- Microsoft Cloud App Security
- Microsoft Commerce Tools 访问控制门户
- Microsoft Commerce Tools 身份验证服务
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune 注册](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Microsoft 必应搜索
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Exchange Online
- SharePoint
- Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Power BI 服务
- Project Online
- Skype for Business Online
- 虚拟专用网络 (VPN)
- Windows Defender ATP

### <a name="office-365"></a>Office 365

Microsoft 365 提供了基于云的生产力和协作服务，如 Exchange、SharePoint 和 Microsoft 团队。 Microsoft 365 云服务进行了深层集成，以确保顺利且协作体验。 在创建策略时，这种集成可能会造成混淆，因为某些应用（如 Microsoft Teams）依赖于 SharePoint 或 Exchange 等其他一些应用。

Office 365 应用使你可以一次同时定位这些服务。 建议使用新的 Office 365 应用，而不是面向单个云应用，以避免 [服务依赖项](service-dependencies.md)的问题。 将这一组应用程序作为目标有助于避免因策略和依赖关系不一致而导致的问题。

管理员可以选择从策略中排除特定的应用（如果需要），只需包含 Office 365 应用，并排除其在策略中选择的特定应用即可。

Office 365 客户端应用程序中包含的关键应用程序：

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - 微软待办
   - Microsoft Teams
   - Exchange Online
   - SharePoint Online
   - Microsoft 365 搜索服务
   - Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype for Business Online
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure 管理

Microsoft Azure 管理应用程序包括多个基础服务。 

   - Azure 门户
   - Azure 资源管理器提供程序
   - 经典部署模型 API
   - Azure PowerShell
   - Visual Studio 订阅管理员门户
   - Azure DevOps
   - Azure 数据工厂门户

> [!NOTE]
> Microsoft Azure 管理应用程序适用于 Azure PowerShell，后者调用 Azure 资源管理器 API。 它不适用于 Azure AD PowerShell，后者调用 Microsoft Graph。

## <a name="other-applications"></a>其他应用程序

除 Microsoft 应用以外，管理员还可以将任何已在 Azure AD 中注册的应用程序添加到条件访问策略。 这些应用程序包括： 

- 通过 [Azure AD 应用程序代理](../manage-apps/what-is-application-proxy.md)发布的应用程序
- [从库中添加的应用程序](../manage-apps/add-application-portal.md)
- [不在库中的自定义应用程序](../manage-apps/view-applications-portal.md)
- [通过应用交付控制器和网络发布的传统应用程序](../manage-apps/secure-hybrid-access.md)
- 使用[基于密码的单一登录](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)的应用程序

> [!NOTE]
> 由于条件访问策略规定了有关访问服务的要求，因此你无法将其应用于客户端（公共/本机）应用程序。 换句话说，该策略不是直接在客户端（公共/本机）应用程序上设置的，而是在客户端调用服务时应用的。 例如，在 SharePoint 服务上设置的策略将应用于调用 SharePoint 的客户端。 在 Exchange 上设置的策略将应用于使用 Outlook 客户端访问电子邮件的尝试。 正因如此，云应用选取器没有客户端（公共/本机）应用程序可供选择，并且在租户中注册的客户端（公共/本机）应用程序的应用程序设置中未提供条件访问选项。 


## <a name="user-actions"></a>用户操作

用户操作是可由用户执行的任务。 目前支持的唯一操作是“注册安全信息”。使用此操作，可以在启用了组合注册的用户尝试注册其安全信息时强制实施条件访问策略。 在[组合安全信息注册](../authentication/concept-registration-mfa-sspr-combined.md)一文中可以找到详细信息。

## <a name="next-steps"></a>后续步骤

- [条件访问：条件](concept-conditional-access-conditions.md)

- [条件访问常见策略](concept-conditional-access-policy-common.md)
- [客户端应用程序依赖关系](service-dependencies.md)