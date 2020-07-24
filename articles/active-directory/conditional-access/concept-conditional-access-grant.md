---
title: 条件访问策略中的授权控制 - Azure Active Directory
description: Azure AD 条件访问策略中的授权控制是什么
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85beb04e39c5c47b9ee71ee96c2dff8910766ea3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87009045"
---
# <a name="conditional-access-grant"></a>条件访问：授予

在条件访问策略中，管理员可以利用访问控制来授予或阻止对资源的访问权限。

![包含授权控制且需要多重身份验证的条件访问策略](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>阻止访问

阻止操作会考虑到任何分配，根据条件访问策略配置阻止访问。

阻止是一种强有力的控制，运用此项控制时，应具备相应的知识。 带有块语句的策略可能会产生意外的副作用。 在大规模启用之前，正确的测试和验证至关重要。 在进行更改时，管理员应使用条件访问[仅报告模式](concept-conditional-access-report-only.md)和[What If 工具](what-if-tool.md)等工具进行更改。

## <a name="grant-access"></a>授予访问权限

在授予访问权限时，管理员可以选择强制实施一项或多项控制。 这些控制包括以下选项： 

- [需要多重身份验证（Azure 多重身份验证）](../authentication/concept-mfa-howitworks.md)
- [要求设备标记为符合（Microsoft Intune）](/intune/protect/device-compliance-get-started)
- [要求使用已建立混合 Azure AD 联接的设备](../devices/concept-azure-ad-join-hybrid.md)
- [“需要已批准的客户端应用”](app-based-conditional-access.md)
- [需要应用保护策略](app-protection-based-conditional-access.md)
- [要求更改密码](#require-password-change)

当管理员组合使用这些选项时，可以选择以下方法：

- 需要所有选定控制（控制**和**控制）
- 需要某一选定控制（控制**或**控制）

默认情况下，条件访问需要所有选定控制。

### <a name="require-multi-factor-authentication"></a>需要多重身份验证

选中此复选框会要求用户执行 Azure 多重身份验证。 在[规划基于云的 Azure 多重身份验证部署](../authentication/howto-mfa-getstarted.md)一文中可以找到有关部署 Azure 多重身份验证的详细信息。

### <a name="require-device-to-be-marked-as-compliant"></a>要求将设备标记为合规

部署了 Microsoft Intune 的组织可以根据从其设备返回的信息来识别符合具体合规要求的设备。 此策略合规性信息将从 Intune 转发到 Azure AD，其中的条件访问可以决定是要授予还是阻止对资源的访问。 有关合规性策略的详细信息，请参阅文章[使用 Intune 设置设备上的规则以允许访问你的组织中的资源](/intune/protect/device-compliance-get-started)。

设备可以被 Intune （适用于任何设备操作系统）或第三方 MDM 系统（适用于 Windows 10 设备）标记为合规。 Jamf pro 是唯一受支持的第三方 MDM 系统。 有关集成的详细信息，请参阅将[Jamf Pro 与 Intune 集成以实现符合性](/intune/protect/conditional-access-integrate-jamf)。

设备必须在 Azure AD 中进行注册，然后才能将其标记为合规。 有关设备注册的详细信息，请参阅[什么是设备标识](../devices/overview.md)。

### <a name="require-hybrid-azure-ad-joined-device"></a>要求使用已建立混合 Azure AD 联接的设备

组织可以选择使用设备标识作为其条件性访问策略的一部分。 组织可以要求使用此复选框联接设备 Azure AD 混合。 有关设备标识的详细信息，请参阅[什么是设备标识？一](../devices/overview.md)文。

使用[设备代码 OAuth 流](../develop/v2-oauth2-device-code.md)时，"需要托管设备授权控制" 或 "设备状态" 条件不受支持。 这是因为执行身份验证的设备无法向提供代码的设备提供其设备状态，并且令牌中的设备状态会锁定到执行身份验证的设备。 改为使用 "需要多重身份验证" 授权控制。

### <a name="require-approved-client-app"></a>需要批准的客户端应用

组织可以要求只能尝试从已批准的客户端应用访问选定的云应用。 这些已批准的客户端应用支持 [Intune 应用保护策略](/intune/app-protection-policy)，而不受任何移动设备管理 (MDM) 解决方案影响。

为了利用此授权控制，条件访问要求在 Azure Active Directory 中注册设备，这需要使用代理应用。 代理应用可以是适用于 iOS 的 Microsoft Authenticator，也可以是适用于 Android 设备的 Microsoft 公司门户。 如果用户尝试进行身份验证时设备上未安装代理应用，则会将用户重定向到应用商店来安装代理应用。

此设置适用于以下 iOS 和 Android 应用：

- Microsoft Azure 信息保护
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype for Business
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- 微软待办
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard

**备注**

- 批准的客户端应用支持 Intune 移动应用管理功能。
- “需要批准的客户端应用”要求：
   - 仅支持 iOS 和 Android 作为设备平台条件。
   - 注册设备需要代理应用。 在 iOS 上，代理应用是 Microsoft Authenticator；在 Android 上，代理应用是 Intune 公司门户应用。
- 条件访问无法将 InPrivate 模式下的 Microsoft Edge 视为已批准的客户端应用。

请参阅文章[如何：使用条件访问要求使用批准的设备应用访问云应用](app-based-conditional-access.md)，以获取配置示例。

### <a name="require-app-protection-policy"></a>需要应用保护策略

在条件访问策略中，你可以要求客户端应用上先存在 [Intune 应用保护策略](/intune/app-protection-policy)，然后才能访问所选云应用。 

为了利用此授权控制，条件访问要求在 Azure Active Directory 中注册设备，这需要使用代理应用。 代理应用可以是适用于 iOS 的 Microsoft Authenticator，也可以是适用于 Android 设备的 Microsoft 公司门户。 如果用户尝试进行身份验证时设备上未安装代理应用，则会将用户重定向到应用商店来安装代理应用。

此设置适用于以下客户端应用：

- Microsoft Cortana
- Microsoft Edge
- Microsoft Excel
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Word
- Intune 多行
- 九封电子邮件 & 日历

> [!NOTE]
> Microsoft Kaizala、Microsoft Skype for Business 和 Microsoft Visio 不支持**需要应用保护策略**授予。 如果要求这些应用正常工作，请使用 "仅**要求批准的应用**" 授权。 对于这三个应用程序，在两个授予之间使用或子句将不起作用。

**备注**

- 适用于应用保护策略的应用支持带策略保护的 Intune 移动应用程序管理功能。
- “需要应用保护策略”要求：
    - 仅支持 iOS 和 Android 作为设备平台条件。
    - 注册设备需要代理应用。 在 iOS 上，代理应用是 Microsoft Authenticator；在 Android 上，代理应用是 Intune 公司门户应用。

有关配置示例，请参阅[如何：要求应用保护策略和批准的客户端应用，以进行 cloud 应用访问](app-protection-based-conditional-access.md)。

### <a name="require-password-change"></a>要求更改密码 

如果检测到用户风险，则使用用户风险策略条件，管理员可以选择让用户使用 Azure AD 自助密码重置来安全地更改密码。 如果检测到用户风险，用户可以执行自助服务密码重置以自行修正，这会关闭用户风险事件，以防管理员不必要的干扰。 

当提示用户更改其密码时，他们将首先需要完成多重身份验证。 你将需要确保你的所有用户都已注册了多重身份验证，因此他们已准备好为其帐户检测到的风险。  

> [!WARNING]
> 用户必须预先注册了自助式密码重置，然后才能触发用户风险策略。 

使用密码更改控件配置策略时，存在几个限制。  

1. 必须将策略分配到 "所有云应用"。 这可防止攻击者使用不同的应用程序更改用户的密码并重置帐户风险，只需登录到不同的应用程序即可。 
1. 要求密码更改不能与其他控件一起使用，如需要兼容的设备。  
1. 密码更改控制只能与用户和组分配条件、云应用分配条件（必须设置为全部）和用户风险条件一起使用。 

### <a name="terms-of-use"></a>使用条款

如果你的组织已创建使用条款，则 "授权控制" 下可能会显示其他选项。 这些选项允许管理员要求确认使用条款，作为访问受策略保护的资源的条件。 有关使用条款的详细信息，请参阅[Azure Active Directory 使用条款](terms-of-use.md)。

## <a name="next-steps"></a>后续步骤

- [条件访问：会话控制](concept-conditional-access-session.md)

- [条件访问常见策略](concept-conditional-access-policy-common.md)

- [“仅报表”模式](concept-conditional-access-report-only.md)
