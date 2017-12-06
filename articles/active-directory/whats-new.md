---
title: "新增功能 Azure Active Directory 发行说明 | Microsoft Docs"
description: "了解 Azure Active Directory (Azure AD) 中的新增功能，其中包括最新的发行说明、已知问题、bug 修复、对立功能和即将进行的更改。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
featureFlags: clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: f1538e1c26cfe658c7f42ccdd57d8bf5aca0b1fb
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 中的新增功能




> 通过在你收藏的 RSS 源读取器中订阅此[源](https://docs.microsoft.com/api/search/rss?search=%22what%27s%20new%20in%20azure%20active%20directory%3F%22&locale=en-us)随时了解 Azure Active Directory 中的新增功能。



我们一直在改进 Azure Active Directory。 为了让大家随时了解最新的开发成果，本主题提供了以下方面的信息：

-   最新版本 
-   已知问题 
-   Bug 修复 
-   已弃用的功能 
-   更改计划 

请经常返回本页面，因为我们每月都会更新。

## <a name="november-2017"></a>2017 年 11 月
 
### <a name="retiring-acs"></a>即将停用 ACS



**类型：**更改计划  
服务类别：ACS  
产品功能：访问控制服务 


Microsoft Azure Active Directory 访问控制（也称作访问控制服务或 ACS）将于 2018 年底停用。  接下来几周内会提供更多信息，包括详细的计划和高级迁移指南。 在此期间，有关 ACS 的任何问题，请在此页留下评论，我们的团队成员会帮助解答。

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>限制对 Intune 托管浏览器的浏览器访问 


**类型：**更改计划  
**服务类别：**条件访问  
**产品功能：**标识安全和保护




通过此行为，可使用 Intune 托管浏览器作为批准应用，限制对 Office 365 以及其他已连接 Azure AD 的云应用的浏览器访问。 

通过此更改，可以为基于应用程序的条件访问配置以下条件：

**客户端应用：**浏览器

**此更改会产生什么影响？**

目前，使用此条件会阻止访问。 当此行为的预览可用时，所有访问都需要使用托管的浏览器应用程序。 

请在即将发布的博客和发行说明中查找有关此功能的信息。 

有关详细信息，请参阅 [Azure Active Directory 中的条件访问](active-directory-conditional-access-azure-portal.md)。

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>新批准的客户端应用，适用于基于 Azure AD 应用的条件访问

 
**类型：**更改计划  
**服务类别：**条件访问  
**产品功能：**标识安全和保护




计划将以下应用添加到[批准的客户端应用](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)列表：

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)

- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


有关详细信息，请参阅：

- [批准的客户端应用要求](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [基于 Azure Active Directory 应用的条件访问](active-directory-conditional-access-mam.md)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>使用条款支持多种语言



**类型：**新功能    
**服务类别：**使用条款  
**产品功能：**调控/符合性





管理员现可创建包含多个 PDF 文档的新使用条款 (TOU)。 可以使用相应语言来标记这些 PDF 文档。 属于相应范围内的用户可看到以基于其首选项的匹配语言显示的 PDF。 如果没有匹配的语言，则以默认语言显示。


---
 

### <a name="realtime-password-writeback-client-status"></a>实时密码写回客户端状态



**类型：**新功能  
**服务类别：**SSPR  
**产品功能：**用户身份验证


 

现可查看本地密码写回客户端的状态。 此选项位于[密码重置](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)页的“本地集成”部分中。 

如果与本地写回客户端的连接出现问题，可看到一条错误消息，其中提供了：

- 不能连接到本地写回客户端的原因 
- 可帮助解决此问题的文档链接。 


有关详细信息，请参阅[本地集成](active-directory-passwords-how-it-works.md#on-premises-integration)。

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Azure AD 基于应用的条件访问 



 
**类型：**新功能  
**服务类别：**Azure AD  
**产品功能：**标识安全和保护





现可将对 Office 365 和其他已连接 Azure AD 的云应用的访问权限限于[批准的客户端应用](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)，这些应用支持使用[基于 Azure AD 的应用的条件访问](active-directory-conditional-access-mam.md)的 Intune 应用保护策略。 Intune 应用保护策略用于配置和保护这些客户端应用程序中的公司数据。

通过组合[基于应用](active-directory-conditional-access-mam.md)的条件访问策略和[基于设备](active-directory-conditional-access-policy-connected-applications.md)的条件访问策略，可以灵活地保护个人和公司设备的数据。

以下条件和控制现可用于基于应用的条件访问：

**支持的平台条件**

- iOS
- Android

**客户端应用条件**

- 移动应用和桌面客户端

**访问控制**

- 需要批准的客户端应用


有关详细信息，请参阅[基于 Azure Active Directory 应用的条件访问](active-directory-conditional-access-mam.md)。

 
---

### <a name="managing-azure-ad-devices-in-the-azure-portal"></a>在 Azure 门户中管理 Azure AD 设备



**类型：**新功能  
**服务类别：**设备注册和管理  
**产品功能：**标识安全和保护

 



现可在一个位置找到连接到 Azure AD 的所有设备以及与设备相关的活动。 在 Azure 门户中管理所有设备标识和设置有新的管理体验。 在此版本中，可以：

- 查看可用于 Azure AD 中的条件访问的所有设备

- 查看属性，包括已加入混合 Azure AD 的设备

- 查找已加入 Azure AD 的设备的 BitLocker 密钥，使用 Intune 等管理设备。

- 管理与 Azure AD 设备相关的设置


有关详细信息，请参阅[使用 Azure 门户管理设备](device-management-azure-portal.md)。



 
---

### <a name="support-for-macos-as-device-platform-for-azure-ad-conditional-access"></a>支持将 macOS 作为 Azure AD 条件访问的设备平台 



**类型：**新功能    
**服务类别：**条件访问  
**产品功能：**标识安全和保护 
 

现可包括（或排除）macOS 作为 Azure AD 条件访问策略中的设备平台条件。 通过将 macOS 添加到支持的设备平台，可以：

- **使用 Intune 注册和管理 macOS 设备** - 类似于 iOS 和 Android 等其他平台，macOS 可通过公司门户应用程序执行统一注册。 通过适用于 macOS 的新公司门户应用，可以使用 Intune 注册设备并将其注册到 Azure AD。
 
- **确保 macOS 设备符合组织在 Intune 中定义的符合性策略** - 可在 Azure 门户的 Intune 中设置 macOS 设备的符合性策略。 
  
- **将对 Azure AD 中应用程序的访问权限限制为仅 macOS 设备** - 条件访问策略创作将 macOS 作为单独的设备平台选项。 通过此选项，可以为 Azure 中的目标应用程序集创作特定于 macOS 的条件访问策略。

有关详细信息，请参阅：

- [使用 Intune 创建适用于 macOS 设备的设备符合性策略](https://aka.ms/macoscompliancepolicy)
- [Azure Active Directory 中的条件性访问](active-directory-conditional-access-azure-portal.md)


 
---

### <a name="nps-extension-for-azure-mfa"></a>适用于 Azure MFA 的 NPS 扩展 


**类型：**新功能    
**服务类别：**MFA  
**产品功能：**用户身份验证




适用于 Azure MFA 的网络策略服务器 (NPS) 扩展可以使用现有的服务器将基于云的 MFA 功能添加到身份验证基础结构。 使用 NPS 扩展，可将电话呼叫、短信或电话应用验证添加到现有的身份验证流，而无需安装、配置和维护新服务器。 

此扩展是为想要保护 VPN 连接，但不部署 Azure MFA 服务器的组织创建的。 NPS 扩展充当 RADIUS 和基于云的 Azure MFA 之间的适配器，以为联合用户或已同步用户提供身份验证的第二个因素。


有关详细信息，请参阅[将现有 NPS 基础结构与 Azure 多重身份验证集成](../multi-factor-authentication/multi-factor-authentication-nps-extension.md)

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>还原或永久删除已删除的用户


**类型：**新功能    
**服务类别：**用户管理  
**产品功能：**目录 



在 Azure AD 管理中心，可以：

- 还原已删除的用户 
- 永久删除用户 


**试试看：**

1. 在 Azure AD 管理中心，选择“管理”部分中的[所有用户](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users)。 

2. 从“显示”列表中，选择“最近删除的用户”。 

4. 选择一个或多个最近删除的用户，然后将其还原或永久删除。

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>新批准的客户端应用，适用于基于 Azure AD 应用的条件访问

 
**类型：**已更改的功能  
**服务类别：**条件访问  
**产品功能：**标识安全和保护


以下应用已添加到[批准的客户端应用](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)列表：

- Microsoft Planner

- Microsoft Azure 信息保护 


有关详细信息，请参阅：

- [批准的客户端应用要求](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [基于 Azure Active Directory 应用的条件访问](active-directory-conditional-access-mam.md)


---

### <a name="ability-to-or-between-controls-in-a-conditional-access-policy"></a>条件访问策略中控制之间的“OR”功能 


**类型：**已更改的功能    
**服务类别：**条件访问  
**产品功能：**标识安全和保护

 
“OR”（需要一个选定控制）条件访问控制的功能已发布。 通过此功能，可创建访问控制间包含“OR”的策略。 例如，可以使用此功能创建一个策略，要求用户使用多重身份验证登录“OR”用户位于兼容设备上。

有关详细信息，请参阅 [Azure Active Directory 条件访问中的控制](active-directory-conditional-access-controls.md)。

 
---

### <a name="aggregation-of-realtime-risk-events"></a>实时风险事件的聚合


**类型：**已更改的功能    
**服务类别：**标识保护  
**产品功能：**标识安全和保护


为改进管理体验，在 Azure AD Identity Protection 中，某一天源自同一 IP 地址的所有实时风险事件现按照每种风险事件类型聚合在一起。 此更改限制了显示的风险事件数量，但不会对用户安全性造成任何影响。

每当用户登录时，基础实时检测都会运行。 如果针对 MFA 或阻止访问设置了登录风险安全策略，则每次存在风险的登录期间都会触发该设置。

 
---
 




## <a name="october-2017"></a>2017 年 10 月


### <a name="deprecating-azure-ad-reports"></a>弃用 Azure AD 报告


**类型：**更改计划  
**服务类别：**报告  
**产品功能：**标识生命周期管理  



Azure 门户提供：

- 新的 Azure Active Directory 管理控制台 
- 活动和安全报告的新 API
 
由于推出了这些新功能，**/reports** 终结点下的报告 API 将在 2017 年 12 月 10 日停用。 

---

### <a name="automatic-sign-in-field-detection"></a>自动登录字段检测


**类型：**已修复   
**服务类别：**我的应用  
**产品功能：**SSO  



对于显示 HTML 用户名和密码字段的应用程序，Azure Active Directory 支持自动登录字段检测。  [如何自动捕获应用程序的登录字段](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application)中介绍了这些步骤。 在 [Azure 门户](http://aad.portal.azure.com)中的“企业应用程序”页面上添加一个“非库”应用程序，即可找到此功能。 此外，可在此新应用程序中将“单一登录”模式配置为“基于密码的单一登录”，输入 Web URL，然后保存页面。
 
由于某个服务问题，此功能曾经暂时禁用过一段时间。 该问题现已得到解决，自动登录字段检测功能再次可用。

---

### <a name="new-mfa-features"></a>新 MFA 功能


**类型：**新功能  
**服务类别：**MFA  
**产品功能：**标识安全和保护  



多重身份验证 (MFA) 是保护组织不可或缺的组成部分。 为使凭证的适应能力更强，体验更顺畅，添加了以下功能： 

- 将多重身份验证质询结果直接集成到 Azure AD 登录报告，包括以编程方式访问 MFA 结果

- 将 MFA 配置更深入地集成到 Azure 门户中的 Azure AD 配置体验

在此公共预览版中，MFA 管理和报告集成到核心 Azure AD 配置体验中。 通过聚合这两项功能，可以在 Azure AD 体验中管理 MFA 管理门户功能。

有关详细信息，请参阅 [Azure 门户中报告的多重身份验证参考信息](active-directory-reporting-activity-sign-ins-mfa.md) 


---

### <a name="introducing-terms-of-use"></a>引入了使用使用条款



**类型：**新功能  
**服务类别：**使用条款  
**产品功能：**调控  



Azure AD 使用条款提供一种向最终用户显示信息的简单方法。 这可确保用户看到法律要求或合规性要求的相关免责声明。

可在以下情况下使用 Azure AD 使用条款：

- 针对组织中所有用户的常规使用条款。 

- 基于用户属性（例如， 动态组中的医生与护士或国内员工和国际员工）的具体使用条款。 

- 有关访问业务影响性较高的应用（例如 Salesforce）的具体使用条款。

有关详细信息，请参阅 [Azure Active Directory 使用条款](active-directory-tou.md)。


---

### <a name="enhancements-to-privileged-identity-management"></a>特权标识管理的增强功能


**类型：**新功能  
**服务类别：**PIM  
**产品功能：**Privileged Identity Management  


通过 Azure Active Directory Privileged Identity Management (PIM)，现在可管理、控制和监视对组织中 Azure 资源（预览版）的访问：

- 订阅
- 资源组
- 虚拟机。 

Azure 门户中利用 Azure 基于角色的访问控制 (RBAC) 功能的所有资源都可以利用 Azure AD PIM 提供的所有安全和生命周期管理功能。

有关详细信息，请参阅 [Azure 资源的 PIM](privileged-identity-management/azure-pim-resource-rbac.md)。


---

### <a name="introducing-access-reviews"></a>引入了访问评审


**类型：**新功能  
**服务类别：**访问评审  
**产品功能：**调控  



访问评审（预览版）可使组织有效地管理组成员身份以及对企业应用程序的访问权限： 

- 可以使用访问评审鉴定来宾用户对应用程序的访问权限及其组成员身份。 评审者可以参考访问评审提供的见解来有效地决定来宾是否应该拥有持续访问权限。

- 可以使用访问评审再次验证员工对应用程序和组成员身份的访问权限。

可以收集与组织相关的程序中的访问评审控件，跟踪符合性或风险敏感应用程序的审阅。

有关详细信息，请参阅 [Azure AD 访问评审](active-directory-azure-ad-controls-access-reviews-overview.md)。


---

### <a name="hiding-third-party-applications-from-my-apps-and-the-office-365-launcher"></a>在“我的应用”和 Office 365 启动器中隐藏第三方应用程序



**类型：**新功能  
**服务类别：**我的应用  
**产品功能：**SSO  



现在，可以通过“隐藏应用”属性更好地管理用户门户中显示的应用。 如果为后端服务显示的应用磁贴和重复的磁贴导致用户的应用启动器变得混杂，隐藏应用可帮助解决问题。 切换开关位于第三方应用的“属性”部分中，带有“对用户可见?”标签 还可以通过 PowerShell 以编程方式隐藏应用。 

有关详细信息，请参阅[在 Azure Active Directory 的用户体验中隐藏第三方应用程序](active-directory-coreapps-hide-third-party-app.md)。 


**可用功能**

 在过渡到新管理控制台的过程中，开发了 2 个新 API 用于检索可用的 Azure AD 活动日志。 新的 API 集提供更丰富的筛选和排序功能，此外还提供更丰富的审核和登录活动。 现在可以通过 Microsoft Graph 中的 Identity Protection 风险事件 API 来访问以前通过安全报告提供的数据。


## <a name="september-2017"></a>2017 年 9 月

### <a name="hotfix-for-microsoft-identity-manager"></a>适用于 Microsoft Identity Manager 的修补程序


**类型：**已更改的功能  
**服务类别：**Microsoft Identity Manager  
**产品功能：**标识生命周期管理  



现已推出 Microsoft Identity Manager (MIM) 2016 2016 Service Pack 1 (SP1) 截止 2017 年 9 月 25 日的修补程序汇总包（内部版本 4.4.1642.0）。 此汇总包：

- 解决了一些问题并添加了改进项
- 是一项累积更新，可取代 Microsoft Identity Manager 2016 内部版本 4.4.1459.0 及其之前的所有 MIM 2016 SP1 更新。 
- 要求安装 **Microsoft Identity Manager 2016 内部版本 4.4.1302.0。** 

有关详细信息，请参阅[为 Microsoft Identity Manager 2016 SP1 推出了修补程序汇总包（内部版本 4.4.1642.0）](https://support.microsoft.com/en-us/help/4021562)。 

---
