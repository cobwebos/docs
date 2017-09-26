---
title: "Azure Active Directory 条件性访问技术参考 | Microsoft 文档"
description: "借助条件性访问控制，Azure Active Directory 会在验证用户身份时先检查选取的特定条件，然后才允许访问应用程序。 一旦符合这些条件，用户就会通过身份验证并获权访问应用程序。"
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
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: spunukol
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: f3d8bdbfc29ca1008006837512c0e6ae8cb8f6fe
ms.contentlocale: zh-cn
ms.lasthandoff: 09/02/2017

---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Azure Active Directory 条件性访问技术参考

使用 [Azure Active Directory (Azure AD) 条件访问](active-directory-conditional-access-azure-portal.md)，可以精细控制经授权的用户如何访问资源。  
本主题提供了有关以下条件性访问策略各项的支持信息： 

- 云应用分配

- 设备平台条件 

- 客户端应用条件

- 批准的客户端应用要求 



## <a name="cloud-apps-assignments"></a>云应用分配

在配置条件性访问策略时，需要[选择要向其应用策略的云应用](active-directory-conditional-access-azure-portal.md#who)。 

![控制](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-apps"></a>Microsoft 云应用

可以从 Microsoft 为以下云应用分配条件性访问策略：

- Azure 远程应用

- Dynamics CRM

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online（包括 OneDrive for Business）

- Microsoft Power BI 

- Visual Studio Team Services

- Microsoft Teams


### <a name="other-apps"></a>其他应用 

除了 Microsoft 云应用程序，可以将条件性访问策略分配给以下类型的云应用：

- Azure Active Directory (Azure AD) 连接的应用程序

- 预先集成的联合服务型软件 (SaaS) 应用程序

- 使用密码单一登录 (SSO) 的应用程序

- 业务线应用程序

- 使用 Azure AD 应用程序代理的应用程序。 


## <a name="device-platforms-condition"></a>设备平台条件

在条件性访问策略中，可配置设备平台条件，将策略绑定到客户端上运行的操作系统。

![控制](./media/active-directory-conditional-access-technical-reference/41.png)

Azure AD 条件性访问支持以下设备平台：

- Android

- iOS

- Windows Phone

- Windows

- macOS（预览）



## <a name="client-apps-condition"></a>客户端应用条件 

当配置条件性访问策略时，可以设置[客户端应用条件](active-directory-conditional-access-azure-portal.md#client-apps)。 客户端应用条件允许你在用户尝试从这些类型的客户端应用进行访问时授予其访问权限或阻止访问：

- 浏览器
- 移动应用和桌面应用

![控制](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>支持的浏览器 

如果你在条件性访问策略中选择“浏览器”来授予对资源的访问权限，那么仅在使用支持的浏览器尝试访问时才会授予访问权限。 当尝试使用不受支持的浏览器访问时，此尝试操作将受阻。

![支持的浏览器](./media/active-directory-conditional-access-technical-reference/05.png)

在条件性访问策略中，支持以下浏览器： 


| 操作系统                     | 浏览器                 | 支持     |
| :--                    | :--                      | :-:         |
| Win 10                 | IE、Edge                 | ![勾选标记][1] |
| Win 10                 | Chrome                   | 预览     |
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

> [!NOTE]
> 对于 Chrome 支持，必须使用 Windows 10 创建者更新并安装位于[此处](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)的扩展。


### <a name="supported-mobile-apps-and-desktop-clients"></a>支持的移动应用和桌面客户端

如果在条件性访问策略中选择“移动应用和桌面客户端”来授予对资源的访问权限，那么仅在使用受支持的移动应用或桌面客户端尝试访问时才会授予访问权限。 当尝试使用不受支持的移动应用或桌面客户端访问时，此尝试操作将受阻。

![控制](./media/active-directory-conditional-access-technical-reference/06.png)

以下移动应用和桌面客户端支持对 Office 365 及其他连接了 Azure AD 的服务应用程序的条件性访问：


| 客户端应用| 目标服务| 平台 |
| --- | --- | --- |
| 用于应用的 MFA 和位置策略。 不支持基于设备的策略。| 任何“我的应用”应用服务| Android 和 iOS|
| Azure 远程应用| Azure 远程应用服务| Windows 10、Windows 8.1、Windows 7、iOS、Android 和 Mac OS X|
| Dynamics CRM 应用| Dynamics CRM| Windows 10、Windows 8.1、Windows 7、iOS 和 Android|
| Microsoft Teams 服务 - 控制支持 Microsoft Teams 及其所有客户端应用（Windows 桌面、iOS、Android、WP 和 Web 客户端）的所有服务| Microsoft Teams| Windows 10、Windows 8.1、Windows 7、iOS 和 Android|
| 邮件/日历/人脉应用、Outlook 2016、Outlook 2013（采用新式身份验证）、Skype for Business（采用新式身份验证）| Office 365 Exchange Online| Windows 10|
| Outlook 2016、Outlook 2013（采用新式身份验证）、Skype for Business（采用新式身份验证）| Office 365 Exchange Online| Windows 8.1、Windows 7|
| Outlook 移动应用| Office 365 Exchange Online| iOS|
| Outlook 2016 (Office for macOS)| Office 365 Exchange Online| Mac OS X|
| Office 2016 应用、通用 Office 应用、Office 2013（采用新式身份验证）、OneDrive 同步客户端（参见[说明](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)）、计划将来提供的 Office 组支持、计划将来提供的 SharePoint 应用支持| Office 365 SharePoint Online| Windows 10|
| Office 2016 应用、Office 2013（采用新式身份验证）、OneDrive 同步客户端（参见[说明](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)）| Office 365 SharePoint Online| Windows 8.1、Windows 7|
| Office 移动应用| Office 365 SharePoint Online| iOS、Android|
| Office 2016 for macOS（只有 Word、Excel、PowerPoint、OneNote）。 将来计划提供 OneDrive for Business 支持| Office 365 SharePoint Online| Mac OS X|
| Office Yammer 应用| Office 365 Yammer| Windows 10、iOS、Android|
| PowerBI 应用。 适用于 Android 的 Power BI 应用当前不支持基于设备的条件访问。| PowerBI 服务| Windows 10、Windows 8.1、Windows 7 和 iOS|
| Visual Studio Team Services 应用| Visual Studio Team Services| Windows 10、Windows 8.1、Windows 7、iOS 和 Android|



## <a name="approved-client-app-requirement"></a>批准的客户端应用要求 

配置条件性访问策略时，可选择后列要求：仅当批准的客户端应用发出连接请求时，才授予访问权限。 

![控制](./media/active-directory-conditional-access-technical-reference/21.png)

针对此设置批准的客户端应用有：

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


**备注：**

- 这些应用都支持 Microsoft Intune 移动应用程序管理 (MAM)。

- 此要求：

    - 仅支持将 IOS 和 Android 选为[设备平台条件](#device-platforms-condition) 

    - 不支持将浏览器选为[客户端应用条件](#supported-browsers) 
    
    - 选中后，会取代移动应用和桌面客户端作为[客户端应用条件](#supported-mobile-apps-and-desktop-clients)  


## <a name="next-steps"></a>后续步骤

- 有关条件性访问的概述，请参阅 [Azure Active Directory 中的条件性访问](active-directory-conditional-access-azure-portal.md)
- 如果已准备好在环境中配置条件性访问策略，请参阅 [Azure Active Directory 中条件性访问的最佳做法](active-directory-conditional-access-best-practices.md)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png



