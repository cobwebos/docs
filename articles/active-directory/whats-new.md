---
title: "Azure Active Directory 中的新增功能 | Microsoft Docs"
description: "使用这些发行说明来随时了解 Azure Active Directory 中的**最新版本**、**已知问题**、**bug 修复**、**已弃用功能**和**更改计划**。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 0361d5b48af876af2ccc522351484d369554bfca
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 中的新增功能

我们一直在改进 Azure Active Directory。 为了让大家随时了解最新的开发成果，本主题提供了以下方面的信息：

-   最新版本 
-   已知问题 
-   Bug 修复 
-   已弃用的功能 
-   更改计划 

请经常返回本页面，因为我们每月都会更新。


## <a name="october-2017"></a>2017 年 10 月

**类型：**更改计划  
**服务类别：**报告  
**产品功能：**标识生命周期管理  


**即将弃用 `https://graph.windows.net/<tenant-name>/reports/` 节点下的 Azure AD 报告（beta 版）API**

Azure 门户提供：

- 新的 Azure Active Directory 管理控制台 
- 活动和安全报告的新 API
 
由于推出了这些新功能，**/reports** 终结点下的报告 API 将在 2017 年 12 月 10 日停用。 

---

**类型：**已修复   
**服务类别：**我的应用  
**产品功能：**SSO  


对于显示 HTML 用户名和密码字段的应用程序，Azure Active Directory 支持自动登录字段检测。  [如何自动捕获应用程序的登录字段](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application)中介绍了这些步骤。 在 [Azure 门户](http://aad.portal.azure.com)中的“企业应用程序”页面上添加一个“非库”应用程序，即可找到此功能。 此外，可在此新应用程序中将“单一登录”模式配置为“基于密码的单一登录”，输入 Web URL，然后保存页面。
 
由于某个服务问题，此功能曾经暂时禁用过一段时间。 该问题现已得到解决，自动登录字段检测功能再次可用。

---

**类型：**新功能  
**服务类别：**MFA  
**产品功能：**标识安全和保护  


在我们的世界中，多重身份验证 (MFA) 是保护组织的不可或缺组成部分。 Microsoft 的标识团队在不断地革新多重身份验证，使凭据的适应能力更强，体验更顺畅。 现在，我很高兴地宣布，我们已在此历程中抵达两个重要的里程碑： 

- 将多重身份验证质询结果直接集成到 Azure AD 登录报告，包括以编程方式访问 MFA 结果

- 将 MFA 配置更深入地集成到 Azure 门户中的核心 Azure AD 配置体验

随着此公共预览版的发布，MFA 管理和报告已是 Azure AD 核心配置体验的集成部分，可让我们在 Azure AD 体验中管理 MFA 管理门户的功能。


---
**类型：**新功能  
**服务类别：**使用条款  
**产品功能：**调控  


**Azure AD 使用条款**提供一种简单的方法用于向最终用户显示信息。 这可确保用户看到法律要求或合规性要求的相关免责声明。

可在以下情况下使用 Azure AD 使用条款：

- 针对组织中所有用户的常规使用条款。 

- 基于用户属性（例如， 动态组中的医生与护士或国内员工和国际员工）的具体使用条款。 

- 有关访问业务影响性较高的应用（例如 Salesforce）的具体使用条款。

有关详细信息，请参阅 [Azure Active Directory 使用条款](active-directory-tou.md)。


---
**类型：**新功能  
**服务类别：**PIM  
**产品功能：**Privileged Identity Management  


使用 Azure Active Directory Privileged Identity Management (PIM) 可以管理、控制和监视组织中对 Azure 资源（预览版）的访问。 这些资源包括订阅、资源组甚至虚拟机。 Azure 门户中利用 Azure 基于角色的访问控制 (RBAC) 功能的所有资源都可以利用 Azure AD PIM 提供的所有突出安全和生命周期管理功能，我们计划在不久后为 Azure AD 角色推出一些极佳的新功能。

有关详细信息，请参阅 [Azure 资源的 PIM](privileged-identity-management/azure-pim-resource-rbac.md)。


---
**类型：**新功能  
**服务类别：**访问评审  
**产品功能：**调控  


Azure AD 中的新增功能

访问评审（预览版）可使组织有效地管理组成员身份以及对企业应用程序的访问权限： 

- 可以使用访问评审鉴定来宾用户对应用程序的访问权限及其组成员身份。 评审者可以参考访问评审提供的见解来有效地决定来宾是否应该拥有持续访问权限。

- 可以使用访问评审再次验证员工对应用程序和组成员身份的访问权限。

可以收集与组织相关的程序中的访问评审控件，跟踪符合性或风险敏感应用程序的审阅。

有关详细信息，请参阅 [Azure AD 访问评审](active-directory-azure-ad-controls-access-reviews-overview.md)。


---
**类型：**新功能  
**服务类别：**我的应用  
**产品功能：**SSO  


**能够在“我的应用”和 Office 365 启动器中隐藏第三方应用程序**

现在，可以通过“隐藏应用”属性更好地管理用户门户中显示的应用。 如果为后端服务显示的应用磁贴和重复的磁贴导致用户的应用启动器变得混杂，此功能可帮助解决问题。 切换开关位于第三方应用的“属性”部分中，带有“对用户可见?”标签。 还可以通过 PowerShell 以编程方式隐藏应用。 

有关详细信息，请参阅[在 Azure Active Directory 的用户体验中隐藏第三方应用程序](active-directory-coreapps-hide-third-party-app.md)。 


**可用功能**

 在过渡到新管理控制台的过程中，我们开发了 2 个新 API 用于检索可用的 Azure AD 活动日志。 新的 API 集提供更丰富的筛选和排序功能，此外还提供更丰富的审核和登录活动。 现在可以通过 Microsoft Graph 中的 Identity Protection 风险事件 API 来访问以前通过安全报告提供的数据。


## <a name="september-2017"></a>2017 年 9 月

**类型：**已更改的功能  
**服务类别：**Microsoft Identity Manager  
**产品功能：**标识生命周期管理  


现已推出 Microsoft Identity Manager (MIM) 2016 2016 Service Pack 1 (SP1) 截止 2017 年 9 月 25 日的修补程序汇总包（内部版本 4.4.1642.0）。 此汇总包：

- 解决了一些问题并添加了改进项
- 是一项累积更新，可取代 Microsoft Identity Manager 2016 内部版本 4.4.1459.0 及其之前的所有 MIM 2016 SP1 更新。 
- 要求安装 **Microsoft Identity Manager 2016 内部版本 4.4.1302.0。** 

有关详细信息，请参阅[为 Microsoft Identity Manager 2016 SP1 推出了修补程序汇总包（内部版本 4.4.1642.0）](https://support.microsoft.com/en-us/help/4021562)。 

---