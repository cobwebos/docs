---
title: 条件访问策略中的授予控件 - Azure 活动目录
description: Azure AD 条件访问策略中的授予控件是什么
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02ec8dace971cd4dc1407c9e8d20839504c9ecc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331839"
---
# <a name="conditional-access-grant"></a>条件访问：授予

在条件访问策略中，管理员可以使用访问控件来授予或阻止对资源的访问。

![具有需要多重身份验证的授予控制的条件访问策略](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>阻止访问

块会考虑任何分配，并阻止基于条件访问策略配置的访问。

块是一个强大的控件，应该用适当的知识来运用。 管理员应在启用之前使用[仅报告模式](concept-conditional-access-report-only.md)进行测试。

## <a name="grant-access"></a>授予访问权限

管理员可以选择在授予访问权限时强制实施一个或多个控件。 这些控件包括以下选项： 

- [要求进行多重身份验证（Azure 多重身份验证）](../authentication/concept-mfa-howitworks.md)
- [要求设备标记为符合要求（微软 Intune）](/intune/protect/device-compliance-get-started)
- [需要混合 Azure AD 联接设备](../devices/concept-azure-ad-join-hybrid.md)
- [需要批准的客户端应用](app-based-conditional-access.md)
- [需要应用保护策略](app-protection-based-conditional-access.md)

当管理员选择组合这些选项时，他们可以选择以下方法：

- 要求所有选定的控件（**控制和**控件）
- 需要其中一个选定的控件（控件**或**控件）

默认情况下，条件访问需要所有选定的控件。

### <a name="require-multi-factor-authentication"></a>需要多重身份验证

选择此复选框需要用户执行 Azure 多重身份验证。 有关部署 Azure 多重身份验证的详细信息，请参阅规划[基于云的 Azure 多重身份验证部署](../authentication/howto-mfa-getstarted.md)的文章。

### <a name="require-device-to-be-marked-as-compliant"></a>要求将设备标记为合规

部署 Microsoft Intune 的组织可以使用从其设备返回的信息来标识满足特定合规性要求的设备。 此策略合规性信息从 Intune 转发到 Azure AD，其中条件访问可以做出授予或阻止对资源的访问的决定。 有关合规性策略的详细信息，请参阅文章["在设备上设置规则，允许使用 Intune 访问组织中的资源](/intune/protect/device-compliance-get-started)"。

Intune（对于任何设备操作系统）或 Windows 10 设备的第三方 MDM 系统可以标记为符合标准。 Jamf pro 是唯一受支持的第三方 MDM 系统。 有关集成的详细信息，请参阅文章，[将 Jamf Pro 与 Intune 集成，以便符合性](/intune/protect/conditional-access-integrate-jamf)。

设备必须在 Azure AD 中注册，然后才能标记为符合要求。 有关设备注册的详细信息，请参阅文章"[什么是设备标识](../devices/overview.md)"。

### <a name="require-hybrid-azure-ad-joined-device"></a>需要混合 Azure AD 联接设备

组织可以选择将设备标识用作其条件访问策略的一部分。 组织可以要求设备是使用此复选框联接的混合 Azure AD。 有关设备标识的详细信息，请参阅文章["什么是设备标识？"。](../devices/overview.md)

### <a name="require-approved-client-app"></a>需要批准的客户端应用

组织可以要求从批准的客户端应用进行对所选云应用的访问尝试。 这些批准的客户端应用支持[Intune 应用保护策略](/intune/app-protection-policy)，独立于任何移动设备管理 （MDM） 解决方案。

为了利用此授予控件，条件访问要求将设备注册在 Azure 活动目录中，这需要使用代理应用。 代理应用可以是适用于 iOS 的 Microsoft Authenticator，也可以是适用于 Android 设备的 Microsoft 公司门户。 如果用户尝试进行身份验证时未在设备上安装代理应用，则用户将重定向到应用商店以安装代理应用。

此设置适用于以下 iOS 和 Android 应用：

- Microsoft Azure 信息保护
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- 微软办公中心
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
- Microsoft 白板

**备注**

- 批准的客户端应用支持 Intune 移动应用管理功能。
- “需要批准的客户端应用”**** 要求：
   - 仅支持 iOS 和 Android 作为设备平台条件。
   - 注册设备需要代理应用。 在 iOS 上，代理应用程序是微软身份验证器，在 Android 上，它是 Intune 公司门户应用。
- 条件访问无法将 InPrivate 模式下的 Microsoft Edge 视为已批准的客户端应用。

请参阅文章"[如何操作：需要经过批准的客户端应用才能使用条件访问进行云应用访问](app-based-conditional-access.md)"，了解配置示例。

### <a name="require-app-protection-policy"></a>需要应用保护策略

在条件访问策略中，您可以要求在客户端应用上存在[Intune 应用保护策略](/intune/app-protection-policy)，然后才能访问选定的云应用。 

为了利用此授予控件，条件访问要求将设备注册在 Azure 活动目录中，这需要使用代理应用。 代理应用可以是适用于 iOS 的 Microsoft Authenticator，也可以是适用于 Android 设备的 Microsoft 公司门户。 如果用户尝试进行身份验证时未在设备上安装代理应用，则用户将重定向到应用商店以安装代理应用。

此设置适用于以下客户端应用：

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**备注**

- 适用于应用保护策略的应用支持带策略保护的 Intune 移动应用程序管理功能。
- “需要应用保护策略”要求：****
    - 仅支持 iOS 和 Android 作为设备平台条件。
    - 注册设备需要代理应用。 在 iOS 上，代理应用程序是微软身份验证器，在 Android 上，它是 Intune 公司门户应用。

请参阅文章"[如何：需要应用保护策略和已批准的客户端应用，以便使用条件访问访问云应用](app-protection-based-conditional-access.md)访问"进行配置示例。

### <a name="terms-of-use"></a>使用条款

如果您的组织已创建使用条款，则授予控件下可能可以看到其他选项。 这些选项允许管理员要求确认使用条款，作为访问受策略保护的资源的条件。 有关使用条款的详细信息，请参阅["Azure 活动目录"的使用条款](terms-of-use.md)。

## <a name="next-steps"></a>后续步骤

- [条件访问：会话控件](concept-conditional-access-session.md)

- [条件访问常见策略](concept-conditional-access-policy-common.md)

- [“仅报表”模式](concept-conditional-access-report-only.md)
