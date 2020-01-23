---
title: 条件访问策略中的客户端应用-Azure Active Directory
description: ''
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9afc25c906ecd3b7807e6bf3e0763ac1673ebd99
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76544049"
---
# <a name="conditional-access-cloud-apps-and-actions"></a>条件性访问：云应用和操作

云应用或操作是条件性访问策略的重要组成部分。 使用条件性访问策略，管理员可以将控件分配给特定的应用程序或操作。 

- 管理员可以从包含内置 Microsoft 应用程序的应用程序列表以及任何[Azure AD 集成的应用](../manage-apps/what-is-application-management.md)程序（包括库、非库和通过[应用程序代理](../manage-apps/what-is-application-proxy.md)发布的应用程序）中进行选择。
- 管理员可以选择基于云应用程序而不是在用户操作上定义策略。 唯一受支持的操作是注册安全信息（预览版），允许条件性访问强制实施围绕[组合的安全信息注册体验](../authentication/howto-registration-mfa-sspr-combined.md)的控制。

![定义条件访问策略并指定云应用](./media/concept-conditional-access-cloud-apps/conditional-access-define-policy-specify-cloud-apps.png)

## <a name="microsoft-cloud-applications"></a>Microsoft 云应用程序

许多现有的 Microsoft 云应用程序都包含在可从中进行选择的应用程序列表中。 

管理员可以将条件性访问策略分配给 Microsoft 的以下云应用。 某些应用（如 Office 365 （预览版）和 Microsoft Azure 管理）包含多个相关的子应用或服务。 以下列表并不完整，并且可能会发生更改。

- [Office 365 （预览版）](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL 数据库和数据仓库](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- [Microsoft Azure 信息保护](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure 管理](#microsoft-azure-management)
- Microsoft Azure 订阅管理
- Microsoft Cloud App Security
- Microsoft Commerce 工具访问控制门户
- Microsoft Commerce 工具身份验证服务
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune 注册](https://docs.microsoft.com/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Search in Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook 组
- Power BI 服务
- Project Online
- Skype for Business Online
- 虚拟专用网络 (VPN)
- Windows Defender ATP

### <a name="office-365-preview"></a>Office 365 （预览版）

Office 365 提供基于云的生产力和协作服务，如 Exchange、SharePoint 和 Microsoft 团队。 Office 365 云服务已深度集成，以确保顺利且协作体验。 在创建策略时，此集成可能会导致混淆，因为某些应用（如 Microsoft 团队）依赖于其他一些应用，例如 SharePoint 或 Exchange。

Office 365 （预览版）应用使你可以一次同时定位这些服务。 建议使用新的 Office 365 （预览版）应用，而不是面向单个云应用。 面向此组应用程序有助于避免因策略和依赖关系不一致而导致的问题。

管理员可以选择从策略中排除特定应用（如果需要），只需包括 Office 365 （预览版）应用，并排除在策略中选择的特定应用。

Office 365 （预览版）客户端应用程序中包含的关键应用程序：

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - 微软待办
   - Microsoft Teams
   - Office 365 Exchange Online
   - Office 365 SharePoint Online
   - Office 365 搜索服务
   - Office 365 Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApp
   - Skype for Business Online
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure 管理

Microsoft Azure 管理应用程序包括多个基础服务。 

   - Azure 门户
   - Azure 资源管理器提供程序
   - 经典部署模型 Api
   - Azure PowerShell
   - Visual Studio 订阅管理员门户
   - Azure DevOps
   - Azure 数据工厂门户

> [!NOTE]
> Microsoft Azure 管理应用程序适用于调用 Azure 资源管理器 API Azure PowerShell。 它不适用于调用 Microsoft Graph Azure AD PowerShell。

## <a name="other-applications"></a>其他应用程序

除 Microsoft 应用外，管理员还可以将任何 Azure AD 注册的应用程序添加到条件访问策略。 这些应用程序可能包括： 

- 通过[Azure AD 应用程序代理](../manage-apps/what-is-application-proxy.md)发布的应用程序
- [从库中添加的应用程序](../manage-apps/add-application-portal.md)
- [不在库中的自定义应用程序](../manage-apps/add-non-gallery-app.md)
- [通过应用传递控制器和网络发布的旧版应用程序](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>用户操作

用户操作是可以由用户执行的任务。 当前支持的唯一操作是**注册安全信息（预览版）** ，这允许在启用了组合注册的用户注册其安全信息时，强制实施条件性访问策略。 有关详细信息，请参阅[合并安全信息注册（预览版）](../authentication/concept-registration-mfa-sspr-combined.md)一文。

## <a name="next-steps"></a>后续步骤

- [条件性访问策略组件](concept-conditional-access-policies.md)
- [客户端应用程序依赖关系](service-dependencies.md)
- [Microsoft Intune：需要 MFA 进行设备注册](https://docs.microsoft.com/intune/enrollment/multi-factor-authentication)
