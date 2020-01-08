---
title: Azure Active Directory 条件访问设置引用 |Microsoft Docs
description: 大致了解 Azure Active Directory 条件访问策略中支持的设置。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: reference
ms.date: 07/10/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1023583b5527e4d565580e8f094dc2f68d38f4ba
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424797"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Azure Active Directory 条件访问设置参考

可以使用[Azure Active Directory （Azure AD）条件访问](../active-directory-conditional-access-azure-portal.md)来控制授权用户访问资源的方式。

本文提供了有关条件访问策略中的下列配置选项的支持信息：

- 云应用程序分配
- 设备平台条件
- 客户端应用程序条件
- 批准的客户端应用程序要求

如果这不是你要查找的信息，请在本文末尾留下评论。

## <a name="cloud-apps-assignments"></a>云应用分配

使用条件性访问策略，可以控制用户访问[云应用](conditions.md#cloud-apps-and-actions)的方式。 配置条件访问策略时，需要至少选择一个云应用。 

![为策略选择云应用](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Microsoft 云应用程序

可以将条件性访问策略分配给 Microsoft 的以下云应用：

- Azure Analysis Services
- Azure DevOps
- Azure SQL 数据库和数据仓库-[了解详细信息](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- Microsoft Azure 信息保护-[了解详细信息](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Microsoft Azure 管理-[了解详细信息](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Microsoft Azure 订阅管理
- Microsoft Cloud App Security
- Microsoft Commerce 工具访问控制门户
- Microsoft Commerce 工具身份验证服务
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Microsoft Intune 注册
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

### <a name="other-applications"></a>其他应用程序

除了 Microsoft cloud apps 外，还可以将条件性访问策略分配给以下类型的云应用：

- 已连接 Azure AD 的应用程序
- 预先集成的联合服务型软件 (SaaS) 应用程序
- 使用密码单一登录 (SSO) 的应用程序
- 业务线应用程序
- 使用 Azure AD 应用程序代理的应用程序

## <a name="device-platform-condition"></a>设备平台条件

在条件访问策略中，可以配置设备平台条件，将策略绑定到客户端上的操作系统。 Azure AD 条件访问支持以下设备平台：

- Android
- iOS
- Windows Phone
- Windows
- macOS

![将访问策略绑定到客户端 OS](./media/technical-reference/41.png)

如果使用**其他客户端**条件阻止旧身份验证，则还可以设置设备平台条件。

## <a name="client-apps-condition"></a>客户端应用条件

在条件访问策略中，可以配置[客户端应用](conditions.md#client-apps)条件，将策略绑定到已启动访问尝试的客户端应用。 设置客户端应用条件，在用户尝试从以下类型的客户端应用进行访问时授予其访问权限或阻止访问：

- 浏览器
- 移动应用和桌面应用

![控制客户端应用的访问](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>支持的浏览器

在条件访问策略中，可以选择 "**浏览器**" 作为客户端应用。

![控制受支持浏览器的访问](./media/technical-reference/05.png)

此设置适用于所有浏览器。 但是，若要符合设备策略（如兼容设备要求），支持以下操作系统和浏览器：

| OS                     | 浏览器                                        |
| :--                    | :--                                             |
| Windows 10             | Microsoft Edge、Internet Explorer、Chrome       |
| Windows 8/8.1        | Internet Explorer、Chrome                       |
| Windows 7              | Internet Explorer、Chrome                       |
| iOS                    | Microsoft Edge，Intune Managed Browser，Safari  |
| Android                | Microsoft Edge、Intune Managed Browser、Chrome  |
| Windows Phone          | Microsoft Edge、Internet Explorer               |
| Windows Server 2019    | Microsoft Edge、Internet Explorer、Chrome       |
| Windows Server 2016    | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer                       |
| Windows Server 2008 R2 | Internet Explorer                       |
| macOS                  | Chrome、Safari                                  |


#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>为什么在浏览器中看到证书提示

在 Windows 7、iOS、Android 和 macOS 上 Azure AD 使用在 Azure AD 中注册设备时设置的客户端证书来标识设备。  当用户通过浏览器首次登录时，系统将提示用户选择证书。 用户必须先选择此证书，然后才能使用浏览器。

#### <a name="chrome-support"></a>Chrome 支持

对于**windows 10 创意者更新（版本1703）** 或更高版本中的 Chrome 支持，请安装[windows 10 帐户扩展](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)。 如果条件访问策略需要特定于设备的详细信息，则需要此扩展。

若要自动将此扩展部署到 Chrome 浏览器，请创建以下注册表项：

|    |    |
| --- | --- |
| 路径 | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| 名称 | 第 |
| 类型 | REG_SZ (String) |
| 数据 | ppnbnpeolgkicgegkbkbjmhlideopiji; https\://clients2.google.com/service/update2/crx |

对于 Windows 8.1 和 7 中的 Chrome 支持，请创建以下注册表项：

|    |    |
| --- | --- |
| 路径 | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| 名称 | 第 |
| 类型 | REG_SZ (String) |
| 数据 | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

这些浏览器支持设备身份验证，允许根据策略对设备进行识别和验证。 如果浏览器以专用模式运行，设备检查将失败。

### <a name="supported-mobile-applications-and-desktop-clients"></a>支持的移动应用程序和桌面客户端

在条件访问策略中，可以选择 "**移动应用和桌面客户**端" 作为客户端应用。

![控制受支持移动应用或桌面客户端的访问](./media/technical-reference/06.png)

此设置会影响通过以下移动应用和桌面客户端做出的访问尝试：

| 客户端应用 | 目标服务 | 平台 |
| --- | --- | --- |
| Dynamics CRM 应用 | Dynamics CRM | Windows 10、Windows 8.1、iOS 和 Android |
| “邮件/日历/联系人”应用、Outlook 2016、Outlook 2013（使用新式身份验证）| Office 365 Exchange Online | Windows 10 |
| 用于应用的 MFA 和位置策略。 不支持基于设备的策略。| 任何“我的应用”应用服务| Android 和 iOS |
| Microsoft Teams 服务 - 控制支持 Microsoft Teams 及其所有客户端应用（Windows 桌面、iOS、Android、WP 和 Web 客户端）的所有服务 | Microsoft Teams | Windows 10、Windows 8.1、Windows 7、iOS、Android 和 macOS |
| Office 2016 应用、Office 2013（采用新式身份验证）、OneDrive 同步客户端（参见[说明](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)） | Office 365 SharePoint Online | Windows 8.1、Windows 7 |
| Office 2016 应用、通用 Office 应用、Office 2013（采用新式身份验证）、OneDrive 同步客户端（参见[说明](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)）、计划将来提供的 Office 组支持、计划将来提供的 SharePoint 应用支持 | Office 365 SharePoint Online | Windows 10 |
| Office 2016（仅限 Word、Excel、PowerPoint、OneNote）。 将来计划提供 OneDrive for Business 支持| Office 365 SharePoint Online| macOS|
| Office 2019| Office 365 SharePoint Online | Windows 10、macOS |
| Office 移动应用 | Office 365 SharePoint Online | Android、iOS |
| Office Yammer 应用 | Office 365 Yammer | Windows 10、iOS、Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10、macOS |
| Outlook 2016 (Office for macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016、Outlook 2013（采用新式身份验证）、Skype for Business（采用新式身份验证） | Office 365 Exchange Online | Windows 8.1、Windows 7 |
| Outlook 移动应用 | Office 365 Exchange Online | Android、iOS |
| Power BI 应用程序 | Power BI 服务 | Windows 10、Windows 8.1、Windows 7、Android 和 iOS |
| Skype for Business | Office 365 Exchange Online| Android、IOS |
| Visual Studio Team Services 应用 | Visual Studio Team Services | Windows 10、Windows 8.1、Windows 7、iOS 和 Android |

## <a name="support-for-legacy-authentication"></a>支持旧式身份验证

通过选择“其他客户端”，可以指定一个条件，该条件会影响通过邮件协议（如 IMAP、MAPI、POP、SMTP）使用基本身份验证的应用和不使用新式身份验证的旧版 Office 应用。  

![其他客户端](./media/technical-reference/11.png)

有关详细信息，请参阅[客户端应用](conditions.md#client-apps)。

## <a name="approved-client-app-requirement"></a>批准的客户端应用要求

在条件访问策略中，可以要求从已批准的客户端应用对所选云应用进行的访问尝试。 

![控制已批准客户端应用的访问](./media/technical-reference/21.png)

此设置适用于以下客户端应用：

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
   - 仅支持 iOS 和 Android 作为[设备平台条件](#device-platform-condition)。
- 条件性访问无法在 InPrivate 模式下将 Microsoft Edge 视为批准的客户端应用。

## <a name="app-protection-policy-requirement"></a>应用保护策略要求 

在条件访问策略中，你可以要求在客户端应用上提供应用保护策略，然后才能访问所选的云应用。 

![使用应用保护策略控制访问](./media/technical-reference/22.png)

此设置适用于以下客户端应用：

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**注释**

- 适用于应用保护策略的应用支持 Intune 移动应用程序管理功能与策略保护。
- **需要应用保护策略**要求：
    - 仅支持 iOS 和 Android 作为[设备平台条件](#device-platform-condition)。

## <a name="next-steps"></a>后续步骤

- 有关条件性访问的概述，请参阅[什么是 Azure Active Directory 中的条件性访问？](../active-directory-conditional-access-azure-portal.md)
- 如果已准备好在环境中配置条件性访问策略，请参阅[Azure Active Directory 中条件性访问的建议做法](best-practices.md)。

<!--Image references-->
[1]: ./media/technical-reference/01.png
