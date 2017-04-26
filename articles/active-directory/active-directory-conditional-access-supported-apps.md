---
title: "使用 Azure Active Directory 中条件访问规则的应用程序和浏览器 | Microsoft Docs"
description: "借助条件性访问控制，Azure Active Directory 会在验证用户身份时检查特定条件，以允许应用程序访问。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: 569777b7034ebeccddf6d9ef89ee2f9fb667cc3f
ms.lasthandoff: 04/12/2017


---
# <a name="applications-and-browsers-that-use-conditional-access-rules-in-azure-active-directory"></a>使用 Azure Active Directory 中条件访问规则的应用程序和浏览器

条件性访问规则在以下应用程序中受支持：已连接 Azure Active Directory (Azure AD) 的应用程序、预集成联合软件即服务 (SaaS) 应用程序、使用密码单一登录 (SSO) 的应用程序、业务线应用程序以及使用 Azure AD 应用程序代理的应用程序。 有关可以对其使用条件性访问的应用程序的详细列表，请参阅[启用了条件性访问的服务](active-directory-conditional-access-technical-reference.md)。 条件性访问可与使用新式身份验证的移动和桌面应用程序搭配使用。 在本文中，我们将介绍条件性访问在移动和桌面应用中的工作原理。

可以在使用新式身份验证的应用程序中使用 Azure AD 登录页面。 在登录页面中，会提示用户进行多重身份验证。 如果用户的访问被阻止，则会显示一条消息。 设备需要先进行新式身份验证才能使用 Azure AD 进行身份验证，以便评估基于设备的条件性访问策略。

请务必了解哪些应用程序可以使用条件性访问规则，以及保护其他应用程序入口点可能需要采取的步骤。

## <a name="applications-that-use-modern-authentication"></a>使用新式身份验证的应用程序
> [!NOTE]
> 如果在 Azure AD 中有某个条件性访问策略，且该策略在 Office 365 中具有等效项，请同时配置这两个条件性访问策略。 这将适用于诸如 Exchange Online 或 SharePoint Online 的条件性访问策略。
>
>

以下应用程序支持对 Office 365 及其他连接了 Azure AD 的服务应用程序的条件性访问：

| 目标服务 | 平台 | 应用程序 |
| --- | --- | --- |
| Office 365 Exchange Online |Windows 10 |邮件/日历/人脉应用、Outlook 2016、Outlook 2013（采用新式身份验证）、Skype for Business（采用新式身份验证） |
| Office 365 Exchange Online |Windows 8.1、Windows 7 |Outlook 2016、Outlook 2013（采用新式身份验证）、Skype for Business（采用新式身份验证） |
| Office 365 Exchange Online |iOS、Android |Outlook 移动应用 |
| Office 365 Exchange Online |Mac OS X |仅限多重身份验证和位置的 Outlook 2016；计划将来提供的基于设备的策略支持、计划将来提供的 Skype for Business 支持 |
| Office 365 SharePoint Online |Windows 10 |Office 2016 应用、通用 Office 应用、Office 2013（采用新式身份验证）、OneDrive 同步客户端（参见[说明](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)）、计划将来提供的 Office 组支持、计划将来提供的 SharePoint 应用支持 |
| Office 365 SharePoint Online |Windows 8.1、Windows 7 |Office 2016 应用、Office 2013（采用新式身份验证）、OneDrive 同步客户端（参见[说明](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)） |
| Office 365 SharePoint Online |iOS、Android |Office 移动应用 |
| Office 365 SharePoint Online |Mac OS X |仅限多重身份验证和位置的 Office 2016 应用；计划将来提供的基于设备的策略支持 |
| Office 365 Yammer |Windows 10、iOS；计划将来提供 Android 支持 |Office Yammer 应用 |
| Dynamics CRM |Windows 10、Windows 8.1、Windows 7、iOS 和 Android |Dynamics CRM 应用 |
| PowerBI 服务 |Windows 10、Windows 8.1、Windows 7、iOS 和 Android |PowerBI 应用 |
| Azure 远程应用服务 |Windows 10、Windows 8.1、Windows 7、iOS、Android 和 Mac OS X |Azure 远程应用 |
| 任何“我的应用”应用服务 |Android 和 iOS |任何“我的应用”应用服务 |

## <a name="applications-that-do-not-use-modern-authentication"></a>未使用新式身份验证的应用程序
目前，必须使用其他方法来阻止访问未使用新式身份验证的应用。 不会通过条件性访问来强制实施未使用新式身份验证的应用的访问规则。 这主要是出于对 Exchange 和 SharePoint 访问的考量。 大多数早期版本的应用使用较旧的访问控制协议。

### <a name="control-access-in-office-365-sharepoint-online"></a>在 Office 365 SharePoint Online 中控制访问
可以通过使用 Set-SPOTenant cmdlet 来禁用用于 SharePoint 访问的旧版协议。 使用此 cmdlet 可阻止使用非新式身份验证协议的 Office 客户端访问 SharePoint Online 资源。

**示例命令**：    `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

### <a name="control-access-in-office-365-exchange-online"></a>在 Office 365 Exchange Online 中控制访问
Exchange 提供两种主要类型的协议。 查看以下选项，然后选择适合你组织的策略。

* **Exchange ActiveSync**。 默认情况下，不会对 Exchange ActiveSync 强制实施多重身份验证和位置的条件性访问策略。 需要通过直接配置 Exchange ActiveSync 策略，或通过使用 Active Directory 联合身份验证服务 (AD FS) 规则阻止 Exchange ActiveSync，以此来保护对这些服务的访问。
* **旧版协议**。 可以使用 AD FS 阻止旧版协议。 这会阻止访问旧版的 Office 客户端，例如未启用新式身份验证的 Office 2013 和早期版本的 Office。

### <a name="use-ad-fs-to-block-legacy-protocol"></a>使用 AD FS 阻止旧版协议
可使用以下示例规则在 AD FS 级别阻止旧版协议访问。 从两个常见配置中进行选择。

#### <a name="option-1-allow-exchange-activesync-and-allow-legacy-apps-but-only-on-the-intranet"></a>选项 1：允许 Exchange ActiveSync，并允许旧版应用（仅限 Intranet 中）
通过将以下三个规则应用于 Microsoft Office 365 标识平台的 AD FS 信赖方信任，就可允许 Exchange ActiveSync 流量以及浏览器和新式身份验证流量。 阻止来自 Extranet 的旧版应用。

##### <a name="rule-1"></a>规则 1
    @RuleName = "Allow all intranet traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-2"></a>规则 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-3"></a>规则 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

#### <a name="option-2-allow-exchange-activesync-and-block-legacy-apps"></a>选项 2：允许 Exchange ActiveSync，并阻止旧版应用
通过将以下三个规则应用于 Microsoft Office 365 标识平台的 AD FS 信赖方信任，就可允许 Exchange ActiveSync 流量以及浏览器和新式身份验证流量。 阻止来自任何位置的旧版应用。

##### <a name="rule-1"></a>规则 1
    @RuleName = "Allow all intranet traffic only for browser and modern authentication clients"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>规则 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>规则 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


## <a name="supported-browsers"></a>支持的浏览器

| 操作系统                     | 浏览器                 | 支持     |
| :--                    | :--                      | :-:         |
| Win 10                 | IE、Edge                 | ![勾选标记][1] |
| Win 10                 | Chrome                   | 即将支持 |
| Win 8/8.1            | IE、Chrome               | ![勾选标记][1] |
| Win 7                  | IE、Chrome               | ![勾选标记][1] |
| iOS                    | Safari                   | ![勾选标记][1] |
| Android                | Chrome                   | ![勾选标记][1] |
| Windows Phone          | IE、Edge                 | ![勾选标记][1] |
| Windows Server 2016    | IE、Edge                 | ![勾选标记][1] |
| Windows Server 2016    | Chrome                   | 即将支持 |
| Windows Server 2012 R2 | IE、Chrome               | ![勾选标记][1] |
| Windows Server 2008 R2 | IE、Chrome               | ![勾选标记][1] |
| Mac OS                 | Safari                   | ![勾选标记][1] |
| Mac OS                 | Chrome                   | 即将支持 |


## <a name="next-steps"></a>后续步骤

有关更多详细信息，请参阅 [Azure Active Directory 中的条件访问](active-directory-conditional-access.md)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-supported-apps/ic195031.png



