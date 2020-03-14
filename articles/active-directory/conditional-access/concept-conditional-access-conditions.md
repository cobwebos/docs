---
title: 条件性访问策略中的条件-Azure Active Directory
description: Azure AD 条件访问策略中的条件是什么
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9e6d2d6cf602bca74c8899586eef0a8108fde3c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263330"
---
# <a name="conditional-access-conditions"></a>条件性访问：条件

在条件性访问策略中，管理员可以利用来自风险、设备平台或位置等条件的信号来增强其策略决策。 

![定义条件访问策略并指定条件](./media/concept-conditional-access-conditions/conditional-access-conditions.png)

可以结合多个条件来创建细化和特定的条件性访问策略。

例如，在访问敏感应用程序时，管理员可能会将登录风险信息从标识保护和位置考虑到其他控件（如多重身份验证）。

## <a name="sign-in-risk"></a>登录风险

对于有权访问[Identity Protection](../identity-protection/overview-identity-protection.md)的客户，可将登录风险评估为条件访问策略的一部分。 登录风险表示给定的身份验证请求未由标识所有者授权的概率。 有关登录风险的详细信息，请参阅文章：[风险](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)和[如何：配置和启用风险策略](../identity-protection/howto-identity-protection-configure-risk-policies.md)。

## <a name="device-platforms"></a>设备平台

设备平台的特征在于设备上运行的操作系统。 Azure AD 使用设备提供的信息（例如用户代理字符串）来标识平台。 由于可以修改用户代理字符串，因此此信息未经验证。 设备平台应与 Microsoft Intune 设备符合性策略一起使用，或者作为块语句的一部分使用。 默认值是应用于所有设备平台。

Azure AD 条件访问支持以下设备平台：

- Android
- iOS
- Windows Phone
- Windows
- macOS

如果使用**其他客户端**条件阻止旧身份验证，则还可以设置设备平台条件。

## <a name="locations"></a>位置

将位置配置为条件时，组织可以选择包括或排除位置。 这些命名位置可能包括公共 IPv4 网络信息、国家或地区，甚至不会映射到特定国家或地区的未知区域。 只有 IP 范围才能标记为受信任的位置。

当包含**任何位置**时，此选项包含 internet 上的任何 IP 地址，而不仅仅是配置的命名位置。 选择**任何位置**时，管理员可以选择排除**所有受信任**的或**选定的位置**。

例如，某些组织可能会选择在其用户连接到受信任位置（如其物理总部）的网络时不需要多重身份验证。 管理员可以创建一个包含任何位置的策略，但不包括为其总部网络选择的位置。

有关位置的详细信息，请参阅文章： [Azure Active Directory 条件访问中的位置条件](location-condition.md)。

## <a name="client-apps-preview"></a>客户端应用（预览）

默认情况下，条件访问策略适用于基于浏览器的应用程序和使用新式身份验证协议的应用程序。 除了这些应用程序，管理员还可以选择包括 Exchange ActiveSync 客户端和使用旧协议的其他客户端。

- 浏览者
   - 其中包括使用 SAML、WS 联合身份验证、OpenID Connect 或注册为 OAuth 机密客户端的服务等协议的基于 web 的应用程序。
- 移动应用和桌面客户端
   - 新式身份验证客户端
      - 此选项包括 Office desktop 和 phone 应用程序之类的应用程序。
   - Exchange ActiveSync 客户端
      - 默认情况下，这包括 Exchange ActiveSync （EAS）协议的所有使用。 将**策略仅应用到受支持的平台**会限制为 IOS、Android 和 Windows 等支持的平台。
      - 当策略阻止使用 Exchange ActiveSync 时，受影响的用户将收到单个隔离电子邮件。 此电子邮件提供了阻止阻止的原因，并提供了修正说明（如果可以）。
   - 其他客户端
      - 此选项包括使用不支持新式身份验证的基本/旧身份验证协议的客户端。
         - 经过身份验证的 SMTP-POP 和 IMAP 客户端使用它来发送电子邮件。
         - 自动发现-由 Outlook 和 EAS 客户端用于查找和连接到 Exchange Online 中的邮箱。
         - Exchange Online PowerShell-用于通过远程 PowerShell 连接到 Exchange Online。 如果阻止 Exchange Online PowerShell 的基本身份验证，则需要使用 Exchange Online PowerShell 模块进行连接。 有关说明，请参阅[使用多重身份验证连接到 Exchange Online PowerShell](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)。
         - Exchange Web 服务（EWS）-Outlook、Outlook for Mac 和第三方应用程序使用的编程接口。
         - IMAP4-由 IMAP 电子邮件客户端使用。
         - MAPI over HTTP （MAPI/HTTP）-由 Outlook 2010 及更高版本使用。
         - 脱机通讯簿（OAB）-Outlook 下载并使用的地址列表集合的副本。
         - Outlook Anywhere （RPC over HTTP）-由 Outlook 2016 及更早版本使用。
         - Outlook 服务-适用于 Windows 10 的邮件和日历应用程序使用。
         - POP3-由 POP 电子邮件客户端使用。
         - 报表 Web 服务-用于在 Exchange Online 中检索报表数据。

这些条件通常用于以下情况：需要托管设备、阻止旧身份验证和阻止 web 应用程序，但允许移动或桌面应用。

### <a name="supported-browsers"></a>支持的浏览器

此设置适用于所有浏览器。 但是，若要符合设备策略（如兼容设备要求），支持以下操作系统和浏览器：

| OS | 浏览器 |
| :-- | :-- |
| Windows 10 | Microsoft Edge、Internet Explorer、Chrome |
| Windows 8/8.1 | Internet Explorer、Chrome |
| Windows 7 | Internet Explorer、Chrome |
| iOS | Microsoft Edge，Intune Managed Browser，Safari |
| Android | Microsoft Edge、Intune Managed Browser、Chrome |
| Windows Phone | Microsoft Edge、Internet Explorer |
| Windows Server 2019 | Microsoft Edge、Internet Explorer、Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome、Safari |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>为什么在浏览器中看到证书提示

在 Windows 7、iOS、Android 和 macOS 上 Azure AD 使用在 Azure AD 中注册设备时设置的客户端证书来标识设备。  当用户通过浏览器首次登录时，系统将提示用户选择证书。 用户必须先选择此证书，然后才能使用浏览器。

#### <a name="chrome-support"></a>Chrome 支持

对于**windows 10 创意者更新（版本1703）** 或更高版本中的 Chrome 支持，请安装[windows 10 帐户扩展](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)。 如果条件访问策略需要特定于设备的详细信息，则需要此扩展。

若要自动将此扩展部署到 Chrome 浏览器，请创建以下注册表项：

|    |    |
| --- | --- |
| 路径 | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| 名称 | 1 |
| 类型 | REG_SZ (String) |
| data | ppnbnpeolgkicgegkbkbjmhlideopiji; https\://clients2.google.com/service/update2/crx |

对于 Windows 8.1 和 7 中的 Chrome 支持，请创建以下注册表项：

|    |    |
| --- | --- |
| 路径 | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| 名称 | 1 |
| 类型 | REG_SZ (String) |
| data | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

这些浏览器支持设备身份验证，允许根据策略对设备进行识别和验证。 如果浏览器以专用模式运行，设备检查将失败。

### <a name="supported-mobile-applications-and-desktop-clients"></a>支持的移动应用程序和桌面客户端

组织可以选择**移动应用和桌面客户**端作为客户端应用。

此设置会影响通过以下移动应用和桌面客户端做出的访问尝试：

| 客户端应用 | 目标服务 | 平台 |
| --- | --- | --- |
| Dynamics CRM 应用 | Dynamics CRM | Windows 10、Windows 8.1、iOS 和 Android |
| “邮件/日历/联系人”应用、Outlook 2016、Outlook 2013（使用新式身份验证）| Office 365 Exchange Online | Windows 10 |
| 用于应用的 MFA 和位置策略。 不支持基于设备的策略。| 任何“我的应用”应用服务 | Android 和 iOS |
| Microsoft Teams 服务 - 控制支持 Microsoft Teams 及其所有客户端应用（Windows 桌面、iOS、Android、WP 和 Web 客户端）的所有服务 | Microsoft Teams | Windows 10、Windows 8.1、Windows 7、iOS、Android 和 macOS |
| Office 2016 应用、Office 2013 （采用新式身份验证）、 [OneDrive 同步客户端](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 8.1、Windows 7 |
| Office 2016 应用、通用 Office 应用、Office 2013 （采用新式身份验证）、 [OneDrive 同步客户端](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 10 |
| Office 2016（仅限 Word、Excel、PowerPoint、OneNote）。 | Office 365 SharePoint Online | macOS |
| Office 2019| Office 365 SharePoint Online | Windows 10、macOS |
| Office 移动应用 | Office 365 SharePoint Online | Android、iOS |
| Office Yammer 应用 | Office 365 Yammer | Windows 10、iOS、Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10、macOS |
| Outlook 2016 (Office for macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016、Outlook 2013（采用新式身份验证）、Skype for Business（采用新式身份验证） | Office 365 Exchange Online | Windows 8.1、Windows 7 |
| Outlook 移动应用 | Office 365 Exchange Online | Android、iOS |
| Power BI 应用程序 | Power BI 服务 | Windows 10、Windows 8.1、Windows 7、Android 和 iOS |
| Skype for Business | Office 365 Exchange Online| Android、iOS |
| Visual Studio Team Services 应用 | Visual Studio Team Services | Windows 10、Windows 8.1、Windows 7、iOS 和 Android |

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync 客户端

- 组织在将策略分配给用户或组时只能选择 Exchange ActiveSync 客户端。 选择 "**所有用户**"、"**所有来宾和外部用户**" 或 "**目录角色**" 将导致所有用户被阻止。
- 创建分配给 Exchange ActiveSync 客户端的策略时， **Office 365 Exchange Online**应为仅分配给该策略的云应用程序。 
- 组织可以使用**设备平台**条件将此策略的范围缩小到特定平台。

如果分配给策略的访问控制使用**要求批准的客户端应用**，则会将该用户定向到安装并使用 Outlook mobile 客户端。 如果需要**多重身份验证**，受影响的用户将被阻止，因为基本身份验证不支持多重身份验证。

有关详细信息，请参阅以下文章：

- [使用条件性访问阻止旧身份验证](block-legacy-authentication.md)
- [需要具有条件访问的批准的客户端应用](app-based-conditional-access.md)

### <a name="other-clients"></a>其他客户端

通过选择“其他客户端”，可以指定一个条件，该条件会影响通过邮件协议（如 IMAP、MAPI、POP、SMTP）使用基本身份验证的应用和不使用新式身份验证的旧版 Office 应用。

## <a name="device-state-preview"></a>设备状态（预览）

设备状态条件可用于从组织的条件性访问策略中排除已加入混合 Azure AD 的设备，并将其标记为符合 Microsoft Intune 符合性策略。

例如，所有访问*Microsoft Azure 管理*云应用程序的*用户*（**包括** **设备混合 Azure AD 加入设备**，并将设备**标记为合规**，并将设备标记为合规，并**阻止***访问控制*。 
   - 此示例将创建一个策略，该策略仅允许从已加入混合 Azure AD 的设备和/或标记为合规的设备访问 Microsoft Azure 管理。

## <a name="next-steps"></a>后续步骤

- [条件性访问： Grant](concept-conditional-access-grant.md)

- [条件访问公用策略](concept-conditional-access-policy-common.md)
