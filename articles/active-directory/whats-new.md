---
title: "新增功能 Azure Active Directory 发行说明 | Microsoft Docs"
description: "了解 Azure Active Directory (Azure AD) 中的新增功能，例如最新的发行说明、已知问题、bug 修复、已弃用的功能和即将应用的更改。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: f32fc2d2df73f4a01c300d565595e2c00b2baf4b
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2018
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 中的新增功能




> 请订阅 [![RSS](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us) [源](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us)，随时了解 Azure Active Directory (Azure AD) 中的新增功能。



Azure AD 会不断改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

-   最新版本
-   已知问题
-   Bug 修复
-   已弃用的功能
-   更改计划

本页面每月更新，请不时回来查看。


## <a name="january-2018"></a>2018 年 1 月
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD 应用库中提供了新的联合应用 

**类型：**新功能  
**服务类别：**企业应用  
**产品功能：**第三方集成
 

2018 年 1 月，应用库中添加了支持联合的以下新应用：

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698)、[OneTrust 隐私管理软件](https://go.microsoft.com/fwlink/?linkid=861660)、[Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk Federated Directory](https://go.microsoft.com/fwlink/?linkid=864699) 和 [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701)。

有关所有可用教程的完整概述，请参阅 [SaaS 应用程序与 Azure Active Directory 的集成](https://aka.ms/appstutorial)。
 

---
 


### <a name="sign-in-with-additional-risk-detected"></a>登录时检测到其他风险

**类型：**新功能  
**服务类别：**标识保护  
**产品功能：**标识安全和保护
 

从检测到的风险事件获得的见解会绑定到 Azure AD 订阅。 使用 Azure AD Premium P2 版本时，可以获取有关所有基础检测的最详细的信息。

使用 Azure AD Premium P1 版本时，许可证未涵盖的检测项会显示为风险事件“登录时检测到其他风险”。

有关详细信息，请参阅 [Azure Active Directory 风险事件](active-directory-reporting-risk-events.md)。
 

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>在最终用户的访问面板中隐藏 Office 365 应用程序

**类型：**新功能  
**服务类别：**我的应用  
**产品功能：**SSO
 

现在，可以通过新的用户设置来更好地管理 Office 365 应用程序显示用户访问面板的方式。 如果只想在 Office 门户中显示 Office 应用，可以借助此选项来减少用户访问面板中的应用数量。 该设置位于“用户设置”中，带有“用户只能在 Office 365 门户中查看 Office 365 应用”标签。
 

有关详细信息，请参阅[在 Azure Active Directory 的用户体验中隐藏应用程序](active-directory-coreapps-hide-third-party-app.md)。

---
 


### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>直接从应用的 URL 无缝登录到启用了密码 SSO 的应用 

**类型：**新功能  
**服务类别：**我的应用  
**产品功能：**SSO
 

现已通过一个便捷工具提供“我的应用”浏览器扩展。该工具以快捷方式的形式显示在浏览器中，可实现“我的应用”单一登录功能。 安装后，用户将在其浏览器中看到一个堆积圆点图标，单击该图标可快速访问应用。 用户现在可以：

- 从应用登录页直接登录到基于密码 SSO 的应用
- 使用快速搜索功能启动任何应用
- 在扩展中使用快捷方式访问最近使用的应用
- 该扩展适用于 Edge、Chrome 和 Firefox。
 
有关详细信息，请参阅[我的应用安全登录扩展](active-directory-saas-access-panel-introduction.md#my-apps-secure-sign-in-extension)。

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure 经典门户中的 Azure AD 管理体验已停用

**类型：**已弃用   
**服务类别：**Azure AD  
**产品功能：**目录
 

从 2018 年 1 月 8 日起，Azure 经典门户中的 Azure AD 管理体验已停用。 Azure 经典门户本身在同一时间也已停用。 今后，应使用 [Azure AD 管理员中心](https://aad.portal.azure.com)来执行所有的基于门户的 Azure AD 管理。
 
---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure 经典门户中的 Azure AD 管理体验已停用

**类型：**已弃用  
**服务类别：**Azure AD  
**产品功能：**目录
 

从 2018 年 1 月 8 日起，PhoneFactor Web 门户已停用。 此门户用于管理 MFA 服务器，但这些功能已移至 Azure 门户 (portal.azure.com)。 

MFA 配置位于：“Azure Active Directory”\>“MFA 服务器”
 
---
 
### <a name="deprecate-azure-ad-reports"></a>弃用 Azure AD 报告


**类型：**已弃用  
**服务类别：**报告  
**产品功能：**标识生命周期管理  


随着新 Azure Active Directory 管理控制台的正式发布以及用于活动和安全报告的新 API 的推出，“/reports”终结点下面的报告 API 从 2017 年 12 月 31 日开始已停用。


**可用功能**

在过渡到新管理控制台的过程中，我们开发了 2 个新 API 用于检索 Azure AD 活动日志。 新的 API 集提供更丰富的筛选和排序功能，此外还提供更丰富的审核和登录活动。 现在可以通过 Microsoft Graph 中的 Identity Protection 风险事件 API 来访问以前通过安全报告提供的数据。

有关详细信息，请参阅：

- [Azure Active Directory 报告 API 入门](active-directory-reporting-api-getting-started-azure-portal.md)

- [Azure Active Directory Identity Protection 和 Microsoft Graph 入门](active-directory-identityprotection-graph-getting-started.md)


---


## <a name="december-2017"></a>2017 年 12 月
 

### <a name="terms-of-use-in-the-access-panel"></a>访问面板中的使用条款

**类型：**新功能  
**服务类别：**使用条款  
**产品功能：**调控/符合性
 
现在，可以转到访问面板并查看以前接受的使用条款。

执行以下步骤:

1. 转到 [MyApps 门户](https://myapps.microsoft.com)并登录。

2. 在右上角选择自己的姓名，然后从列表中选择“个人资料”。 

3. 在“个人资料”中选择“查看使用条款”。 

4. 现在，可以查看已接受的使用条款。 

有关详细信息，请参阅 [Azure AD 使用条款功能（预览版）](https://docs.microsoft.com/azure/active-directory/active-directory-tou)。
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>新 Azure AD 登录体验

**类型：**新功能  
**服务类别：**Azure AD  
**产品功能：**用户身份验证
 
Azure AD 和 Microsoft 帐户标识系统 UI 经过重新设计，现在拥有一致的外观。 此外，Azure AD 登录页会先收集用户名，然后在第二个屏幕上收集凭据。

有关详细信息，请参阅[现已推出新 Azure AD 登录体验的公共预览版](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)。
 
---
 

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>更少的登录提示：Azure AD 登录的新“使我保持登录状态”体验

**类型：**新功能  
**服务类别：**Azure AD  
**产品功能：**用户身份验证
 
Azure AD 登录页上的“使我保持登录状态”复选框已被替换为新提示，成功通过身份验证后会显示该提示。 

如果对此提示回答“是”，则服务会提供永久刷新令牌。 此行为与在旧体验中选中“使我保持登录状态”复选框时相同。 对于联合租户，此提示在使用联合服务成功完成身份验证后显示。

有关详细信息，请参阅[更少的登录提示：现已推出 Azure AD 新的“使我保持登录状态”体验的预览版](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/)。 

---
 

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>添加配置以要求在接受使用条款之前先将其展开。

**类型：**新功能  
**服务类别：**使用条款  
**产品功能：**调控
 
为管理员添加了一个选项，要求其用户在接受使用条款之前先将其展开。

对于要求用户展开使用条款这一选项，可选择“打开”或“关闭”。 “打开”设置要求用户在接受使用条款之前先查看条款。

有关详细信息，请参阅 [Azure AD 使用条款功能（预览版）](active-directory-tou.md)。
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>符合条件的角色分配的作用域激活

**类型：**新功能  
**服务类别：**Privileged Identity Management  
**产品功能：**Privileged Identity Management
 
可以使用范围激活功能来激活符合条件的 Azure 资源角色分配，其自治性比原始分配的默认值小。 例如，你被分配为租户中某个订阅的所有者。 使用范围激活可以激活订阅中包含的最多五个资源（例如资源组和虚拟机）的“所有者”角色。 划分激活范围可能会降低对关键 Azure 资源执行不必要更改的可能性。

有关详细信息，请参阅[什么是 Azure AD Privileged Identity Management？](active-directory-privileged-identity-management-configure.md)。
 
---
 

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Azure AD 应用库中提供了新的联合应用

**类型：**新功能  
**服务类别：**企业应用  
**产品功能：**第三方集成
 
2017 年 12 月，应用库中添加了支持联合的以下新应用：

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523)、Adobe Experience Manager、[EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685)、[Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe、[FactSet](https://go.microsoft.com/fwlink/?linkid=863525)、[IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517)、[MOBI](https://go.microsoft.com/fwlink/?linkid=863521)、[MobileIron Azure AD 集成](https://go.microsoft.com/fwlink/?linkid=858027)、[Reflektive](https://go.microsoft.com/fwlink/?linkid=863518)、[SAML SSO for Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520)、[SAML SSO for Bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519)、[Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522)、WebHR、Zenegy Azure AD 集成。

有关所有可用教程的完整概述，请参阅 [SaaS 应用程序与 Azure Active Directory 的集成](https://aka.ms/appstutorial)。

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Azure AD 目录角色的审批工作流

**类型：**已更改的功能  
**服务类别：**Privileged Identity Management  
**产品功能：**Privileged Identity Management
 
Azure AD 目录角色的审批工作流程已正式发布。

通过审批工作流程，特权角色管理员可要求符合条件的角色成员先请求角色激活才能使用特权角色。 可向多个用户和组委派审批职责。 符合条件的角色成员会在审批完成且角色激活时收到通知。

---
 

### <a name="pass-through-authentication-skype-for-business-support"></a>传递身份验证 - Skype for Business 支持

**类型：**已更改的功能  
**服务类别：**身份验证（登录）  
**产品功能：**用户身份验证


传递身份验证现在支持用户登录到支持新式身份验证的 Skype for Business 客户端应用程序，包括联机和混合拓扑。 

有关详细信息，请参阅[支持新式身份验证的 Skype for Business 拓扑](https://technet.microsoft.com/library/mt803262.aspx)。
 
---
 

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>更新到 Azure RBAC 的 Azure AD Privileged Identity Management（预览版）

**类型：**已更改的功能  
**服务类别：**Privileged Identity Management  
**产品功能：**Privileged Identity Management
 
通过 Azure 基于角色的访问控制 (RBAC) 的 Azure AD Privileged Identity Management (PIM) 公共预览版刷新，现在可以：

* 使用 Just Enough Administration。
* 需要批准才能激活资源角色。
* 安排将来激活需要批准 Azure AD 和 Azure RBAC 角色的角色。

 
有关详细信息，请参阅 [Azure 资源的 Privileged Identity Management（预览版）](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)。

 
---
 
## <a name="november-2017"></a>2017 年 11 月
 
### <a name="access-control-service-retirement"></a>访问控制服务停用



**类型：**更改计划  
**服务类别：**访问控制服务  
**产品功能：**访问控制服务 


 Azure Active Directory 访问控制（也称作访问控制服务）将在 2018 年底停用。 接下来的几周内会提供更多信息，包括详细的计划和高级迁移指南。 有关访问控制服务的任何问题，请在本页面留言，团队成员将予以解答。

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>限制对 Intune 托管浏览器的浏览器访问 


**类型：**更改计划  
**服务类别：**条件访问  
**产品功能：**标识安全和保护




可以使用 Intune 托管浏览器作为批准的应用，限制对 Office 365 以及其他已连接 Azure AD 的云应用的浏览器访问。 

现在，可为基于应用程序的条件访问配置以下条件：

**客户端应用：**浏览器

**此更改会产生什么影响？**

目前，使用此条件时会阻止访问。 推出预览版后，所有访问都需要使用托管的浏览器应用程序。 

可在即将发布的博客和发行说明中了解此功能和详细信息。 

有关详细信息，请参阅 [Azure AD 中的条件访问](active-directory-conditional-access-azure-portal.md)。

 
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
- [Azure AD 基于应用的条件访问](active-directory-conditional-access-mam.md)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>使用条款支持多种语言



**类型：**新功能    
**服务类别：**使用条款  
**产品功能：**调控/符合性





管理员现在可以创建包含多个 PDF 文档的新使用条款。 可以使用相应语言来标记这些 PDF 文档。 将会根据用户的偏好，以匹配的语言显示 PDF。 如果没有匹配的语言，则以默认语言显示。


---
 

### <a name="real-time-password-writeback-client-status"></a>实时密码写回客户端状态



**类型：**新功能  
**服务类别：**自助密码重置  
**产品功能：**用户身份验证


 

现在可以查看本地密码写回客户端的状态。 此选项位于[密码重置](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)页的“本地集成”部分。 

如果与本地写回客户端的连接出现问题，将会显示一条错误消息，其中包含：

- 有关无法连接到本地写回客户端的原因的信息。
- 可帮助解决此问题的文档链接。 


有关详细信息，请参阅[本地集成](active-directory-passwords-how-it-works.md#on-premises-integration)。

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Azure AD 基于应用的条件访问 



 
**类型：**新功能  
**服务类别：**Azure AD  
**产品功能：**标识安全和保护





现在可将对 Office 365 和其他已连接 Azure AD 的云应用的访问权限限于[批准的客户端应用](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)，这些应用支持使用[基于 Azure AD 的应用的条件访问](active-directory-conditional-access-mam.md)的 Intune 应用保护策略。 Intune 应用保护策略用于配置和保护这些客户端应用程序中的公司数据。

通过组合[基于应用](active-directory-conditional-access-mam.md)的条件访问策略和[基于设备](active-directory-conditional-access-policy-connected-applications.md)的条件访问策略，可以灵活地保护个人和公司设备的数据。

以下条件和控制现可用于基于应用的条件访问：

**支持的平台条件**

- iOS
- Android

**客户端应用条件**

- 移动应用和桌面客户端

**访问控制**

- 需要批准的客户端应用


有关详细信息，请参阅[基于 Azure AD 应用的条件访问](active-directory-conditional-access-mam.md)。

 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>在 Azure 门户中管理 Azure AD 设备



**类型：**新功能  
**服务类别：**设备注册和管理  
**产品功能：**标识安全和保护

 



现在可在一个位置找到连接到 Azure AD 的所有设备以及与设备相关的活动。 在 Azure 门户中管理所有设备标识和设置有新的管理体验。 在此版本中，可以：

- 查看可用于 Azure AD 中的条件访问的所有设备。
- 查看属性，包括已加入混合 Azure AD 的设备。
- 查找已加入 Azure AD 的设备的 BitLocker 密钥、使用 Intune 管理设备，等等。
- 管理与 Azure AD 设备相关的设置。

有关详细信息，请参阅[使用 Azure 门户管理设备](device-management-azure-portal.md)。



 
---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>支持将 macOS 作为 Azure AD 条件访问的设备平台 



**类型：**新功能    
**服务类别：**条件访问  
**产品功能：**标识安全和保护 
 

现在可以包含（或排除）macOS 作为 Azure AD 条件访问策略中的设备平台条件。 通过将 macOS 添加到支持的设备平台，可以：

- **使用 Intune 登记和管理 macOS 设备。** 类似于 iOS 和 Android 等其他平台，macOS 可通过公司门户应用程序执行统一注册。 可以使用适用于 macOS 的新公司门户应用，通过 Intune 登记设备并将其注册到 Azure AD。
- **确保 macOS 设备符合组织在 Intune 中定义的符合性策略。** 可以在 Azure 门户上的 Intune 中设置 macOS 设备的符合性策略。 
- **只允许符合条件的 macOS 设备访问 Azure AD 中的应用程序。** 条件访问策略创作将 macOS 用作单独的设备平台选项。 现在，可以针对 Azure 中设置的目标应用程序创作特定于 macOS 的条件访问策略。

有关详细信息，请参阅：

- [使用 Intune 创建适用于 macOS 设备的设备符合性策略](https://aka.ms/macoscompliancepolicy)
- [Azure AD 中的条件访问](active-directory-conditional-access-azure-portal.md)


 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>适用于 Azure 多重身份验证的网络策略服务器扩展 


**类型：**新功能    
**服务类别：**多重身份验证  
**产品功能：**用户身份验证




适用于 Azure 多重身份验证的网络策略服务器扩展使用现有的服务器在身份验证基础结构中添加基于云的多重身份验证功能。 使用网络策略服务器扩展，可将电话呼叫、短信或电话应用验证添加到现有的身份验证流。 无需安装、配置和维护新服务器。 

此扩展是为想要保护虚拟专用网络连接，但不部署 Azure 多重身份验证服务器的组织创建的。 网络策略服务器扩展充当 RADIUS 与基于云的 Azure 多重身份验证之间的适配器，以为联合用户或已同步用户提供身份验证的第二个因素。


有关详细信息，请参阅[将现有网络策略服务器基础结构与 Azure 多重身份验证集成](../multi-factor-authentication/multi-factor-authentication-nps-extension.md)。

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>还原或永久删除已删除的用户


**类型：**新功能    
**服务类别：**用户管理  
**产品功能：**目录 



在 Azure AD 管理中心，可以：

- 还原已删除的用户。 
- 永久删除用户。


**试试看：**

1. 在 Azure AD 管理中心，选择“管理”部分中的“[所有用户](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All)”。 

2. 从“显示”列表中，选择“最近删除的用户”。 

3. 选择一个或多个最近删除的用户，然后将其还原或永久删除。

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>新批准的客户端应用，适用于基于 Azure AD 应用的条件访问

 
**类型：**已更改的功能  
**服务类别：**条件访问  
**产品功能：**标识安全和保护


以下应用已添加到[批准的客户端应用](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)列表：

- Microsoft Planner
- Azure 信息保护 


有关详细信息，请参阅：

- [批准的客户端应用要求](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)
- [Azure AD 基于应用的条件访问](active-directory-conditional-access-mam.md)


---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>在条件访问策略中的控制条件之间使用“OR” 


**类型：**已更改的功能    
**服务类别：**条件访问  
**产品功能：**标识安全和保护

 
现在，可对条件访问控制使用“OR”（需要一个选定控制条件）。 可以使用此功能创建在访问控制条件之间包含“OR”的策略。 例如，可以使用此功能创建一个策略，要求用户使用多重身份验证登录“OR”用户位于符合条件的设备上。

有关详细信息，请参阅 [Azure AD 条件访问中的控制](active-directory-conditional-access-controls.md)。

 
---

### <a name="aggregation-of-real-time-risk-events"></a>实时风险事件的聚合


**类型：**已更改的功能    
**服务类别：**标识保护  
**产品功能：**标识安全和保护


在 Azure AD Identity Protection 中，某一天源自同一 IP 地址的所有实时风险事件现按照每种风险事件类型聚合在一起。 此更改限制了显示的风险事件数量，但不会对用户安全性造成任何影响。

每当用户登录时，基础实时检测都会运行。 如果针对多重身份验证或阻止访问设置了登录风险安全策略，则每次存在风险的登录期间都会触发该设置。

 
---
 




## <a name="october-2017"></a>2017 年 10 月


### <a name="deprecate-azure-ad-reports"></a>弃用 Azure AD 报告


**类型：**更改计划  
**服务类别：**报告  
**产品功能：**标识生命周期管理  



Azure 门户提供：

- 新的 Azure AD 管理控制台。
- 用于活动和安全报告的新 API。
 
由于推出了这些新功能，/reports 终结点下的报告 API 将在 2017 年 12 月 10 日停用。 

---

### <a name="automatic-sign-in-field-detection"></a>自动登录字段检测


**类型：**已修复   
**服务类别：**我的应用  
**产品功能：**单一登录  



对于显示 HTML 用户名和密码字段的应用程序，Azure AD 支持自动登录字段检测。 [如何自动捕获应用程序的登录字段](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application)中介绍了这些步骤。 在 [Azure 门户](http://aad.portal.azure.com)中的“企业应用程序”页面上添加一个“非库”应用程序，即可找到此功能。 此外，可在此新应用程序中将“单一登录”模式配置为“基于密码的单一登录”，输入 Web URL，然后保存页面。
 
由于某个服务问题，此功能曾经暂时禁用过。 该问题现已得到解决，自动登录字段检测功能再次可用。

---

### <a name="new-multi-factor-authentication-features"></a>新的多重身份验证功能


**类型：**新功能  
**服务类别：**多重身份验证  
**产品功能：**标识安全和保护  



多重身份验证 (MFA) 是保护组织不可或缺的组成部分。 为使凭证的适应能力更强，体验更顺畅，添加了以下功能： 

- 将多重身份验证质询结果直接集成到 Azure AD 登录报告，包括以编程方式访问 MFA 结果。
- 将 MFA 配置更深入地集成到 Azure 门户中的 Azure AD 配置体验。

在此公共预览版中，MFA 管理和报告集成到核心 Azure AD 配置体验中。 现在，可以在 Azure AD 体验中管理 MFA 管理门户功能。

有关详细信息，请参阅 [Azure 门户中的 MFA 报告参考](active-directory-reporting-activity-sign-ins-mfa.md)。 


---

### <a name="terms-of-use"></a>使用条款



**类型：**新功能  
**服务类别：**使用条款  
**产品功能：**调控  



可以使用 Azure AD 的使用条款功能向用户显示法律要求或符合性要求的相关免责声明。

可在以下情况下使用 Azure AD 使用条款：

- 针对组织中所有用户的常规使用条款
- 基于用户属性（例如，动态组中的医生与护士或国内员工和国际员工）的具体使用条款
- 有关访问业务影响性较高的应用（例如 Salesforce）的具体使用条款

有关详细信息，请参阅 [Azure AD 使用条款](active-directory-tou.md)。


---

### <a name="enhancements-to-privileged-identity-management"></a>Privileged Identity Management 的增强


**类型：**新功能  
**服务类别：**Privileged Identity Management  
**产品功能：**Privileged Identity Management  


使用 Azure AD Privileged Identity Management，可以管理、控制和监视对组织中 Azure 资源（预览版）的访问：

- 订阅
- 资源组
- 虚拟机 

Azure 门户中使用 Azure RBAC 功能的所有资源都可以利用 Azure AD Privileged Identity Management 提供的所有安全和生命周期管理功能。

有关详细信息，请参阅 [Azure 资源的 Privileged Identity Management](privileged-identity-management/azure-pim-resource-rbac.md)。


---

### <a name="access-reviews"></a>访问评审


**类型：**新功能  
**服务类别：**访问评审  
**产品功能：**调控  



组织可以使用访问评审（预览版）有效管理组成员身份以及对企业应用程序的访问权限： 

- 可以使用访问评审鉴定来宾用户对应用程序的访问权限及其组成员身份。 评审者可以根据访问评审提供的见解，有效决定是否允许来宾继续访问。
- 可以使用访问评审再次验证员工对应用程序和组成员身份的访问权限。

可以收集与组织相关的程序中的访问评审控件，跟踪符合性或风险敏感应用程序的审阅。

有关详细信息，请参阅 [Azure AD 访问评审](active-directory-azure-ad-controls-access-reviews-overview.md)。


---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>在“我的应用”和 Office 365 应用启动器中隐藏第三方应用程序



**类型：**新功能  
**服务类别：**我的应用  
**产品功能：**单一登录  



现在，可以通过“隐藏应用”属性更好地管理用户门户中显示的应用。 如果为后端服务显示的应用磁贴或重复的磁贴导致用户的应用启动器变得混杂，隐藏应用可帮助解决问题。 切换开关位于第三方应用的“属性”部分中，带有“对用户可见?”标签 还可以通过 PowerShell 以编程方式隐藏应用。 

有关详细信息，请参阅[在 Azure AD 的用户体验中隐藏第三方应用程序](active-directory-coreapps-hide-third-party-app.md)。 


**可用功能**

 在过渡到新管理控制台的过程中，开发了两个新 API 用于检索可用的 Azure AD 活动日志。 新的 API 集提供更丰富的筛选和排序功能，此外还提供更丰富的审核和登录活动。 现在可以通过 Microsoft Graph 中的 Identity Protection 风险事件 API 来访问以前通过安全报告提供的数据。


## <a name="september-2017"></a>2017 年 9 月

### <a name="hotfix-for-identity-manager"></a>适用于 Identity Manager 的修补程序


**类型：**已更改的功能  
**服务类别：**Identity Manager  
**产品功能：**标识生命周期管理  



现已推出 Identity Manager 2016 Service Pack 1 截止 2017 年 9 月 25 日的修补程序汇总包（内部版本 4.4.1642.0）。 此汇总包：

- 解决了一些问题并添加了改进项。
- 是一项累积更新，可取代 Identity Manager 2016 内部版本 4.4.1459.0 及其之前的所有 Identity Manager 2016 Service Pack 1 更新。 
- 要求安装 Identity Manager 2016 内部版本 4.4.1302.0。 

有关详细信息，请参阅[为 Identity Manager 2016 Service Pack 1 推出了修补程序汇总包（内部版本 4.4.1642.0）](https://support.microsoft.com/help/4021562)。 

---
