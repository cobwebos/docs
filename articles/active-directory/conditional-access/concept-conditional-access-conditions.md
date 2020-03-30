---
title: 条件访问策略中的条件 - Azure 活动目录
description: Azure AD 条件访问策略中的条件是什么
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
ms.openlocfilehash: bb14369275a111476867f2263766e1bb87b7c87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295334"
---
# <a name="conditional-access-conditions"></a>条件访问：条件

在条件访问策略中，管理员可以利用来自风险、设备平台或位置等条件的信号来增强其策略决策。 

![定义条件访问策略并指定条件](./media/concept-conditional-access-conditions/conditional-access-conditions.png)

可以组合多个条件以创建细粒度和特定的条件访问策略。

例如，在访问敏感应用程序时，管理员除了考虑其他控件（如多重身份验证）外，还可以将身份保护和位置的登录风险信息纳入其访问决策中。

## <a name="sign-in-risk"></a>登录风险

对于有权访问[身份保护](../identity-protection/overview-identity-protection.md)的客户，登录风险可以作为条件访问策略的一部分进行评估。 登录风险表示给定身份验证请求未由标识所有者授权的概率。 有关登录风险的详细信息，请参阅文章"[什么是风险](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)以及如何[：配置和启用风险策略](../identity-protection/howto-identity-protection-configure-risk-policies.md)"。

## <a name="device-platforms"></a>设备平台

设备平台的特点是在设备上运行的操作系统。 Azure AD 使用设备提供的信息（如用户代理字符串）标识平台。 由于可以修改用户代理字符串，因此此信息未经验证。 设备平台应与 Microsoft Intune 设备合规性策略一起使用，或作为块语句的一部分使用。 默认值是应用于所有设备平台。

Azure AD 条件访问支持以下设备平台：

- Android
- iOS
- Windows Phone
- Windows
- macOS

如果使用**其他客户端**条件来阻止旧身份验证，还可以设置设备平台条件。

## <a name="locations"></a>位置

将位置配置为条件时，组织可以选择包括或排除位置。 这些命名位置可能包括公共 IPv4 网络信息、国家或地区，甚至未映射到特定国家或地区的未知区域。 只有 IP 范围可以标记为受信任的位置。

当包括**任何位置**时，此选项包括互联网上的任何 IP 地址，而不仅仅是配置命名位置。 选择**任何位置**时，管理员可以选择排除**所有受信任的**位置或**选定位置**。

例如，当用户连接到受信任位置（如其物理总部）的网络时，某些组织可能选择不要求多重身份验证。 管理员可以创建包含任何位置但排除其总部网络所选位置的策略。

有关位置的详细信息，请参阅["Azure 活动目录条件访问中的位置条件是什么](location-condition.md)"

## <a name="client-apps-preview"></a>客户端应用（预览）

默认情况下，条件访问策略适用于使用现代身份验证协议的基于浏览器的应用程序和应用程序。 除了这些应用程序之外，管理员还可以选择包括 Exchange ActiveSync 客户端和其他使用旧协议的客户端。

- 浏览者
   - 其中包括使用 SAML、WS-联合、OpenID Connect 或注册为 OAuth 机密客户端的服务等协议的基于 Web 的应用程序。
- 移动应用和桌面客户端
   - 现代身份验证客户端
      - 此选项包括 Office 桌面和电话应用程序等应用程序。
   - Exchange ActiveSync 客户端
      - 默认情况下，这包括所有使用 Exchange ActiveSync （EAS） 协议。 **仅选择"仅应用于受支持的平台应用策略**"将限制为受支持的平台（如 iOS、Android 和 Windows）。
      - 当策略阻止使用 Exchange ActiveSync 时，受影响的用户将收到一封隔离电子邮件。 此电子邮件提供有关它们被阻止的原因的信息，并包括修复说明（如果可以）。
   - 其他客户端
      - 此选项包括使用不支持现代身份验证的基本/遗留身份验证协议的客户端。
         - 经过身份验证的 SMTP - POP 和 IMAP 客户端用于发送电子邮件。
         - 自动发现 - Outlook 和 EAS 客户端用于在"在线交换"中查找和连接到邮箱。
         - 交换在线电源外壳 - 用于与远程 PowerShell 连接到在线交换。 如果阻止 Exchange 在线 PowerShell 的基本身份验证，则需要使用 Exchange 在线 PowerShell 模块进行连接。 有关说明，请参阅[使用多重身份验证连接到交换在线 PowerShell。](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)
         - 交换 Web 服务 （EWS） - Outlook、Mac Outlook 和第三方应用使用的编程界面。
         - IMAP4 - IMAP 电子邮件客户端使用。
         - 通过 HTTP（MAPI/HTTP） 的 MAPI - 由 Outlook 2010 及更高版本使用。
         - 脱机通讯簿 （OAB） - Outlook 下载和使用的地址列表集合的副本。
         - 随时随地的 Outlook（HTTP 的 RPC） - 由 Outlook 2016 及更早版本使用。
         - Outlook 服务 - 由 Windows 10 的邮件和日历应用使用。
         - POP3 - 由 POP 电子邮件客户端使用。
         - 报告 Web 服务 - 用于在"联机交换"中检索报表数据。

这些条件通常用于需要托管设备、阻止旧版身份验证和阻止 Web 应用程序，但允许移动或桌面应用。

### <a name="supported-browsers"></a>支持的浏览器

此设置适用于所有浏览器。 但是，若要符合设备策略（如兼容设备要求），支持以下操作系统和浏览器：

| OS | 浏览器 |
| :-- | :-- |
| Windows 10 | Microsoft Edge、Internet Explorer、Chrome |
| Windows 8/8.1 | Internet Explorer、Chrome |
| Windows 7 | Internet Explorer、Chrome |
| iOS | Microsoft Edge、Intune Managed Browser、Safari |
| Android | Microsoft Edge、Intune Managed Browser、Chrome |
| Windows Phone | Microsoft Edge、Internet Explorer |
| Windows Server 2019 | Microsoft Edge、Internet Explorer、Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome、Safari |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>为何我在浏览器中看到证书提示

在 Windows 7、iOS、Android 和 macOS 上，Azure AD 使用客户端证书来标识设备，该证书是在将设备注册到 Azure AD 时预配的。  用户首次通过浏览器登录时，系统会提示用户选择此证书。 用户必须在使用浏览器之前选择此证书。

#### <a name="chrome-support"></a>Chrome 支持

有关 Windows **10 创建者更新（版本 1703）** 或更高版本的 Chrome 支持，请安装[Windows 10 帐户扩展名](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)。 当条件访问策略需要特定于设备的详细信息时，此扩展是必需的。

若要自动将此扩展部署到 Chrome 浏览器，请创建以下注册表项：

|    |    |
| --- | --- |
| 路径 | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| “属性” | 1 |
| 类型 | REG_SZ (String) |
| 数据 | ppnbnpeolgkicgegkbkbjmhlideopiji;https\://clients2.google.com/service/update2/crx |

对于 Windows 8.1 和 7**** 中的 Chrome 支持，请创建以下注册表项：

|    |    |
| --- | --- |
| 路径 | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| “属性” | 1 |
| 类型 | REG_SZ (String) |
| 数据 | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

这些浏览器支持设备身份验证，允许根据策略对设备进行识别和验证。 如果浏览器以专用模式运行，设备检查将失败。

### <a name="supported-mobile-applications-and-desktop-clients"></a>支持的移动应用程序和桌面客户端

组织可以选择**移动应用和桌面客户端**作为客户端应用。

此设置会影响通过以下移动应用和桌面客户端做出的访问尝试：

| 客户端应用 | 目标服务 | Platform |
| --- | --- | --- |
| Dynamics CRM 应用 | Dynamics CRM | Windows 10、Windows 8.1、iOS 和 Android |
| “邮件/日历/联系人”应用、Outlook 2016、Outlook 2013（使用新式身份验证）| Office 365 Exchange Online | Windows 10 |
| 用于应用的 MFA 和位置策略。 不支持基于设备的策略。| 任何“我的应用”应用服务 | Android 和 iOS |
| Microsoft Teams 服务 - 控制支持 Microsoft Teams 及其所有客户端应用（Windows 桌面、iOS、Android、WP 和 Web 客户端）的所有服务 | Microsoft Teams | Windows 10、Windows 8.1、Windows 7、iOS、Android 和 macOS |
| Office 2016 应用，Office 2013（具有现代身份验证[）、OneDrive 同步客户端](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 8.1、Windows 7 |
| Office 2016 应用、通用 Office 应用、Office 2013（具有现代身份验证[）、OneDrive 同步客户端](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 10 |
| Office 2016（仅限 Word、Excel、PowerPoint、OneNote）。 | Office 365 SharePoint Online | macOS |
| Office 2019| Office 365 SharePoint Online | Windows 10、macOS |
| Office 移动应用 | Office 365 SharePoint Online | Android、iOS |
| Office Yammer 应用 | Office 365 Yammer | Windows 10、iOS、Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10、macOS |
| Outlook 2016 (Office for macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016、Outlook 2013（采用新式身份验证）、Skype for Business（采用新式身份验证） | Office 365 Exchange Online | Windows 8.1、Windows 7 |
| Outlook 移动应用 | Office 365 Exchange Online | Android、iOS |
| Power BI 应用 | Power BI 服务 | Windows 10、Windows 8.1、Windows 7、Android 和 iOS |
| Skype for Business | Office 365 Exchange Online| Android、iOS |
| Visual Studio Team Services 应用 | Visual Studio Team Services | Windows 10、Windows 8.1、Windows 7、iOS 和 Android |

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync 客户端

- 组织只能在将策略分配给用户或组时选择 Exchange ActiveSync 客户端。 选择**所有用户**、**所有来宾和外部用户**或**目录角色**将导致所有用户被阻止。
- 创建分配给 Exchange ActiveSync 客户端的策略时 **，Office 365 在线交换**应该是分配给该策略的唯一云应用程序。 
- 组织可以使用**设备平台**条件将此策略的范围缩小到特定平台。

如果分配给策略的访问控制使用 **"需要批准的客户端应用**"，则用户将定向安装和使用 Outlook 移动客户端。 如果需要**多重身份验证**，受影响的用户将被阻止，因为基本身份验证不支持多重身份验证。

有关详细信息，请参阅以下文章：

- [使用条件访问阻止旧版身份验证](block-legacy-authentication.md)
- [要求具有条件访问的已批准客户端应用](app-based-conditional-access.md)

### <a name="other-clients"></a>其他客户端

通过选择“其他客户端”****，可以指定一个条件，该条件会影响通过邮件协议（如 IMAP、MAPI、POP、SMTP）使用基本身份验证的应用和不使用新式身份验证的旧版 Office 应用。

## <a name="device-state-preview"></a>设备状态（预览）

设备状态条件可用于从组织的条件访问策略中排除混合 Azure AD 联接和/或标记为符合 Microsoft Intune 合规性策略的设备。

例如，访问 Microsoft Azure*管理*云应用*的所有用户*，包括除**设备混合 Azure AD 联接**的所有**设备状态**，**以及标记为"已兼容的设备"** 和 *"访问控件*"**块**"。 
   - 此示例将创建一个策略，该策略只允许从混合 Azure AD 联接的设备/或标记为合规的设备访问 Microsoft Azure 管理。

## <a name="next-steps"></a>后续步骤

- [条件访问：授予](concept-conditional-access-grant.md)

- [条件访问常见策略](concept-conditional-access-policy-common.md)
