---
title: "Azure Active Directory 条件访问技术参考 | Microsoft Docs"
description: "了解如何使用 Azure Active Directory 中的条件访问控制。 指定对用户进行身份验证和控制应用程序访问权限的条件。 满足指定条件时，可对用户进行身份验证，并向其授予对应用程序的访问权限。"
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 4bcca3ee5862a0455b6064d0f9c0a91388db9516
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2017
---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Azure Active Directory 条件访问技术参考

可使用 [Azure Active Directory (Azure AD) 条件访问](active-directory-conditional-access-azure-portal.md)来精细控制经授权的用户如何访问资源。  

本主题提供有关以下条件访问策略配置选项的支持信息： 

- 云应用程序分配

- 设备平台条件 

- 客户端应用程序条件

- 批准的客户端应用程序要求



## <a name="cloud-apps-assignments"></a>云应用分配

配置条件访问策略时，需要[选择应用策略的云应用](active-directory-conditional-access-azure-portal.md#who)。 

![为策略选择云应用](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Microsoft 云应用程序

可以从 Microsoft 为以下云应用分配条件性访问策略：

- Azure 信息保护 - [了解详细信息](https://docs.microsoft.com/information-protection/get-started/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Azure RemoteApp

- Microsoft Dynamics 365

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online（包括 OneDrive for Business）

- Microsoft Power BI 

- Microsoft Visual Studio Team Services

- Microsoft Teams


### <a name="other-applications"></a>其他应用程序 

除了 Microsoft 云应用程序，可以将条件性访问策略分配给以下类型的云应用：

- 已连接 Azure AD 的应用程序

- 预先集成的联合服务型软件 (SaaS) 应用程序

- 使用密码单一登录 (SSO) 的应用程序

- 业务线应用程序

- 使用 Azure AD 应用程序代理的应用程序


## <a name="device-platform-condition"></a>设备平台条件

在条件访问策略中，可配置设备平台条件，将策略绑定到客户端上的操作系统。

![将访问策略绑定到客户端 OS](./media/active-directory-conditional-access-technical-reference/41.png)

Azure AD 条件性访问支持以下设备平台：

- Android

- iOS

- Windows Phone

- Windows

- macOS（预览）



## <a name="client-apps-condition"></a>客户端应用条件 

配置条件访问策略时，可针对客户端应用条件[选择客户端应用](active-directory-conditional-access-azure-portal.md#client-apps)。 设置客户端应用条件，在用户尝试从以下类型的客户端应用进行访问时授予其访问权限或阻止访问：

- 浏览器
- 移动应用和桌面应用

![控制客户端应用的访问](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>支持的浏览器 

通过在条件访问策略中使用“浏览器”选项，控制浏览器访问。 只有支持的浏览器尝试访问时，才授予其访问权限。 不受支持的浏览器尝试访问时，会阻止该操作。

![控制受支持浏览器的访问](./media/active-directory-conditional-access-technical-reference/05.png)

在条件性访问策略中，支持以下浏览器： 


| 操作系统                     | 浏览器                    | 支持     |
| :--                    | :--                         | :-:         |
| Windows 10             | Internet Explorer、Microsoft Edge     | ![勾选标记][1] |
| Windows 10             | Chrome                      | ![勾选标记][1] |
| Windows 8/8.1        | Internet Explorer、Chrome   | ![勾选标记][1] |
| Windows 7              | Internet Explorer、Chrome   | ![勾选标记][1] |
| iOS                    | Safari、Intune Managed Browser                      | ![勾选标记][1] |
| Android                | Chrome、Intune Managed Browser                      | ![勾选标记][1] |
| Windows Phone          | Internet Explorer、Microsoft Edge     | ![勾选标记][1] |
| Windows Server 2016    | Internet Explorer、Microsoft Edge     | ![勾选标记][1] |
| Windows Server 2016    | Chrome                      | 即将支持 |
| Windows Server 2012 R2 | Internet Explorer、Chrome   | ![勾选标记][1] |
| Windows Server 2008 R2 | Internet Explorer、Chrome   | ![勾选标记][1] |
| macOS                  | Safari                      | ![勾选标记][1] |
| macOS                  | Chrome                      | 即将支持 |

> [!NOTE]
> 对于 Chrome 支持，必须使用 Windows 10 创意者更新（版本 1703）或更高版本。<br>
> 可安装[此扩展](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)。

### <a name="supported-mobile-applications-and-desktop-clients"></a>支持的移动应用程序和桌面客户端

通过在条件访问策略中使用“移动应用和桌面客户端”选项，控制应用和客户端访问。 只有支持的移动应用或桌面客户端尝试访问时，才授予其访问权限。 不受支持的应用或客户端尝试访问时，会阻止该操作。

![控制受支持移动应用或桌面客户端的访问](./media/active-directory-conditional-access-technical-reference/06.png)

以下移动应用和桌面客户端支持对 Office 365 及其他连接了 Azure AD 的服务应用程序的条件性访问：


| 客户端应用| 目标服务| 平台 |
| --- | --- | --- |
| Azure 远程应用| Azure 远程应用服务| Windows 10、Windows 8.1、Windows 7、iOS、Android 和 Mac OS X|
| Dynamics CRM 应用| Dynamics CRM| Windows 10、Windows 8.1、Windows 7、iOS 和 Android|
| “邮件/日历/联系人”应用、Outlook 2016、Outlook 2013（使用新式身份验证）| Office 365 Exchange Online| Windows 10|
| 用于应用的 MFA 和位置策略。 不支持基于设备的策略。| 任何“我的应用”应用服务| Android 和 iOS|
| Microsoft Teams 服务 - 控制支持 Microsoft Teams 及其所有客户端应用（Windows 桌面、iOS、Android、WP 和 Web 客户端）的所有服务| Microsoft Teams| Windows 10、Windows 8.1、Windows 7、iOS 和 Android|
| Office 2016 应用、Office 2013（采用新式身份验证）、OneDrive 同步客户端（参见[说明](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)）| Office 365 SharePoint Online| Windows 8.1、Windows 7|
| Office 2016 应用、通用 Office 应用、Office 2013（采用新式身份验证）、OneDrive 同步客户端（参见[说明](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)）、计划将来提供的 Office 组支持、计划将来提供的 SharePoint 应用支持| Office 365 SharePoint Online| Windows 10|
| Office 2016 for macOS（只有 Word、Excel、PowerPoint、OneNote）。 将来计划提供 OneDrive for Business 支持| Office 365 SharePoint Online| Mac OS X|
| Office 移动应用| Office 365 SharePoint Online| iOS、Android|
| Office Yammer 应用| Office 365 Yammer| Windows 10、iOS、Android|
| Outlook 2016 (Office for macOS)| Office 365 Exchange Online| Mac OS X|
| Outlook 2016、Outlook 2013（采用新式身份验证）、Skype for Business（采用新式身份验证）| Office 365 Exchange Online| Windows 8.1、Windows 7|
| Outlook 移动应用| Office 365 Exchange Online| iOS|
| PowerBI 应用。 适用于 Android 的 Power BI 应用当前不支持基于设备的条件访问。| PowerBI 服务| Windows 10、Windows 8.1、Windows 7 和 iOS|
| Skype for Business| Office 365 Exchange Online| Android、IOS|
| Visual Studio Team Services 应用| Visual Studio Team Services| Windows 10、Windows 8.1、Windows 7、iOS 和 Android|



## <a name="approved-client-app-requirement"></a>批准的客户端应用要求 

通过在条件访问策略中使用“需要批准的客户端应用”选项来控制客户端连接。 只有批准的客户端应用尝试连接时，才授予其访问权限。

![控制已批准客户端应用的访问](./media/active-directory-conditional-access-technical-reference/21.png)

以下客户端应用可与批准的客户端应用程序要求配合使用：

- Microsoft Excel

- Microsoft OneDrive

- Microsoft Outlook

- Microsoft OneNote

- Microsoft PowerPoint

- Microsoft SharePoint

- Microsoft Skype for Business

- Microsoft Teams

- Microsoft Visio

- Microsoft Word


**备注**

- 批准的客户端应用支持 Intune 移动应用管理功能。

- “需要批准的客户端应用”要求：

    - 仅支持 iOS 和 Android 作为[设备平台条件](#device-platforms-condition)。

    - 不支持“浏览器”选项作为[客户端应用条件](#supported-browsers)。
    
    - 选中该选项后，会取代“移动应用和桌面客户端”作为[客户端应用条件](#supported-mobile-apps-and-desktop-clients)。


## <a name="next-steps"></a>后续步骤

- 有关条件访问的概述，请参阅 [Azure Active Directory 中的条件访问](active-directory-conditional-access-azure-portal.md)。
- 如果已准备好在环境中配置条件访问策略，请参阅 [Azure Active Directory 中条件访问的推荐做法](active-directory-conditional-access-best-practices.md)。



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png


