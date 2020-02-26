---
title: 在条件访问策略中授予控件-Azure Active Directory
description: 什么是 Azure AD 条件访问策略中的授权控件
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5df7eedcd92d338d3f741f7092ff6ef73f3442d
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585877"
---
# <a name="conditional-access-grant"></a>条件性访问： Grant

在条件性访问策略中，管理员可以使用访问控制来授予或阻止对资源的访问权限。

![具有要求多重身份验证的授权控制的条件性访问策略](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>阻止访问

块将考虑任何分配，并根据条件性访问策略配置阻止访问。

块是一项功能强大的控件，应年长相关知识。 在启用之前，管理员应该使用[仅报告模式](concept-conditional-access-report-only.md)来进行测试。

## <a name="grant-access"></a>授予访问权限

在授予访问权限时，管理员可以选择强制实施一个或多个控件。 这些控件包括以下选项： 

- [需要多重身份验证（Azure 多重身份验证）](../authentication/concept-mfa-howitworks.md)
- [要求设备标记为符合（Microsoft Intune）](https://docs.microsoft.com/intune/protect/device-compliance-get-started)
- [需要混合 Azure AD 联接设备](../devices/concept-azure-ad-join-hybrid.md)
- [需要批准的客户端应用](app-based-conditional-access.md)
- [需要应用保护策略](app-protection-based-conditional-access.md)

当管理员选择组合这些选项时，他们可以选择以下方法：

- 要求所有选定控件（控件**和**控件）
- 需要一个选定控件（控件**或**控件）

默认情况下，条件访问要求所有选定的控件。

### <a name="require-multi-factor-authentication"></a>需要多重身份验证

选中此复选框将要求用户执行 Azure 多重身份验证。 有关部署 Azure 多重身份验证的详细信息，请参阅[规划基于云的 Azure 多重身份验证部署](../authentication/howto-mfa-getstarted.md)一文。

### <a name="require-device-to-be-marked-as-compliant"></a>要求将设备标记为合规

已部署 Microsoft Intune 的组织可以使用从其设备返回的信息来识别满足特定符合性要求的设备。 此策略符合性信息将从 Intune 转发到 Azure AD，在这种情况下，条件访问可以决定是允许还是阻止对资源的访问。 有关合规性策略的详细信息，请参阅文章[使用 Intune 设置设备上的规则以允许访问你的组织中的资源](https://docs.microsoft.com/intune/protect/device-compliance-get-started)。

设备可以被 Intune （适用于任何设备操作系统）或第三方 MDM 系统（适用于 Windows 10 设备）标记为合规。 不支持除 Windows 10 以外的设备 OS 类型的第三方 MDM 系统。

设备必须在 Azure AD 中进行注册，然后才能将其标记为合规。 有关设备注册的详细信息，请参阅[什么是设备标识](../devices/overview.md)。

### <a name="require-hybrid-azure-ad-joined-device"></a>需要混合 Azure AD 联接设备

组织可以选择使用设备标识作为其条件性访问策略的一部分。 组织可以要求使用此复选框联接设备 Azure AD 混合。 有关设备标识的详细信息，请参阅[什么是设备标识？一](../devices/overview.md)文。

### <a name="require-approved-client-app"></a>需要批准的客户端应用

组织可能需要从批准的客户端应用进行对所选云应用的访问尝试。 这些批准的客户端应用支持独立于任何移动设备管理（MDM）解决方案的[Intune 应用保护策略](/intune/app-protection-policy)。

此设置适用于以下客户端应用：

- Microsoft Azure 信息保护
- Microsoft 预订
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
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

**注释**

- 批准的客户端应用支持 Intune 移动应用管理功能。
- “需要批准的客户端应用”要求：
   - 仅支持 iOS 和 Android 设备平台条件。
- 条件性访问无法在 InPrivate 模式下将 Microsoft Edge 视为批准的客户端应用。

### <a name="require-app-protection-policy"></a>需要应用保护策略

在条件访问策略中，你可以要求在客户端应用上提供[Intune 应用保护策略](/intune/app-protection-policy)，然后才能访问所选的云应用。 

此设置适用于以下客户端应用：

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**注释**

- 适用于应用保护策略的应用支持 Intune 移动应用程序管理功能与策略保护。
- **需要应用保护策略**要求：
    - 仅支持 iOS 和 Android 设备平台条件。

### <a name="terms-of-use"></a>使用条款

如果你的组织已创建使用条款，则 "授权控制" 下可能会显示其他选项。 这些选项允许管理员要求确认使用条款，作为访问受策略保护的资源的条件。 有关使用条款的详细信息，请参阅[Azure Active Directory 使用条款](terms-of-use.md)。

## <a name="next-steps"></a>后续步骤

- [条件性访问：会话控件](concept-conditional-access-session.md)

- [条件访问公用策略](concept-conditional-access-policy-common.md)

- [仅限报表模式](concept-conditional-access-report-only.md)
