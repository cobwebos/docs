---
title: Azure Active Directory 条件访问中的条件是什么？ | Microsoft Docs
description: 了解如何在 Azure Active Directory 条件访问中使用条件来触发策略。
services: active-directory
keywords: 对应用的条件性访问, 使用 Azure AD 进行条件性访问, 保护对公司资源的访问, 条件性访问策略
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 59ae7e83bc2b76cb679b0baf1f5739f28ec7046c
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248274"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Azure Active Directory 条件访问中的条件是什么？ 

使用 [Azure Active Directory (Azure AD) 条件访问](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)，可以控制授权用户访问云应用的方式。 在条件访问策略中，定义对策略触发原因的响应。 示例响应是“则执行此操作”。 示例原因是“出现这种情况时”。

![原因和响应](./media/active-directory-conditional-access-conditions/10.png)


在条件访问的上下文中，“出现这种情况时”称为“条件”。 “则执行此操作”称为“访问控制”。 条件与访问控制的组合表示一种条件访问策略。

![条件访问策略](./media/active-directory-conditional-access-conditions/61.png)


不会应用未在条件访问策略中配置的条件。 某些条件对于将条件访问策略应用到环境是[必需的](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-best-practices)。 

本文概述条件，以及如何在条件访问策略中使用条件。 

## <a name="users-and-groups"></a>用户和组

用户和组条件在条件访问策略中是必需的。 在策略中，可以选择**所有用户**或选择特定的用户和组。

![用户和组](./media/active-directory-conditional-access-conditions/111.png)

选择“所有用户”时，策略将应用到目录中的所有用户，包括来宾用户。

**选择用户和组**时，可以设置以下选项：

* “所有来宾用户”使策略面向 B2B 来宾用户。 此条件与将 **userType** 属性设置为“来宾”的所有用户帐户匹配。 如果在 Azure AD 的邀请流中创建帐户后需要应用策略，则可以使用此设置。

* “目录角色”基于用户的角色分配定位策略。 此条件支持目录角色，如“全局管理员”或“密码管理员”。

* “用户和组”面向特定的用户集。 例如，将某个人力资源应用选作云应用时，可以选择包含人力资源部所有成员的组。 某个组可以是 Azure AD 中任何类型的组，包括动态组，或分配的安全组和通讯组。

还可以从策略中排除特定的用户或组。 例如，在策略强制实施多重身份验证 (MFA) 的情况下，往往会排除服务帐户。 

若要部署新策略，面向特定的用户集十分有用。 在新策略中，应该只将初始用户集用作目标来验证策略行为。 



## <a name="cloud-apps"></a>云应用 

云应用是网站或服务。 Azure AD 应用程序代理保护的网站也是云应用。 有关受支持云应用的详细说明，请参阅[云应用分配](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference#cloud-apps-assignments)。 

**云应用**条件在条件访问策略中是必需的。 在策略中，可以选择**所有云应用**或选择特定的应用。

![包括云应用](./media/active-directory-conditional-access-conditions/03.png)

- 选择“所有云应用”可对要应用到整个组织的策略设置基准。 对于要求执行多重身份验证（由于检测到任何云应用存在登录风险）的策略，请使用此选项。 应用到“所有云应用”的策略将应用于对所有网站和服务的访问权限。 此设置并非仅限用于“选择应用”列表上显示的云应用。 

- 根据策略选择以特定服务为目标的单个云应用。 例如，可以要求用户使用[合规的设备](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online)访问 SharePoint Online。 当其他服务访问 SharePoint 内容时，也会对这些服务应用此策略。 例如 Microsoft Teams。 

可以从策略中排除特定的应用。 但是，这些应用仍受到应用于它们访问的服务的策略的限制。 



## <a name="sign-in-risk"></a>登录风险

登录风险指不是由用户帐户合法所有者执行的登录尝试的可能性（高、中、低）。 Azure AD 在用户登录期间会计算登录风险级别。 可将计算出的登录风险级别用作条件性访问策略中的条件。 

![登录风险级别](./media/active-directory-conditional-access-conditions/22.png)

若要使用此条件，需要启用 [Azure Active Directory Identity Protection](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-identityprotection-enable)。
 
此条件的常见用例包括提供以下保护的策略： 

- 阻止存在高登录风险的用户。 此项保护可防止潜在的非法用户访问你的云应用。 
- 要求中等登录风险的用户执行多重身份验证。 通过强制实施多重身份验证，可以更加肯定登录操作是由帐户的合法所有者执行的。

有关详细信息，请参阅[风险登录](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-security-risky-sign-ins)。  

## <a name="device-platforms"></a>设备平台

设备平台根据设备上运行的操作系统来定义特征。 Azure AD 使用设备（例如用户代理）提供的信息来标识平台。 此信息未经验证。 我们建议在所有平台中应用某个策略。 该策略应阻止访问、要求符合 Microsoft Intune 政策，或要求设备加入域。 默认设置是将策略应用到所有设备平台。 


![配置设备平台](./media/active-directory-conditional-access-conditions/24.png)

有关支持的设备平台列表，请参阅[设备平台条件](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference#device-platform-condition)。


此条件的一个常见用例是，策略将云应用的访问权限制为[托管设备](active-directory-conditional-access-policy-connected-applications.md#managed-devices)。 有关包含设备平台条件的其他方案，请参阅 [Azure Active Directory 基于应用的条件访问](active-directory-conditional-access-mam.md)。



## <a name="device-state"></a>设备状态

设备状态条件从条件访问策略中排除已加入混合 Azure AD 的设备，以及标记为合规的设备。 当策略仅应用到非托管设备以使会话更安全时，此条件非常有用。 例如，当设备处于非托管状态时，仅强制执行 Microsoft Cloud App Security 会话控制。 


![配置设备状态](./media/active-directory-conditional-access-conditions/112.png)

若要阻止对非托管设备的访问，请实现[基于设备的条件访问](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online)。


## <a name="locations"></a>位置

使用位置可以基于尝试连接的位置定义条件。 

![配置位置](./media/active-directory-conditional-access-conditions/25.png)

此条件的常见用例包括提供以下保护的策略：

- 要求用户在企业网络外部访问服务时执行多重身份验证。  

- 阻止特定国家或地区的用户访问服务。 

有关详细信息，请参阅 [Azure Active Directory 条件访问中的位置条件是什么？](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-locations)。


## <a name="client-apps"></a>客户端应用

使用客户端应用条件可将策略应用到不同类型的应用程序。 示例包括网站、服务、移动应用和桌面应用程序。 



应用程序分类为：

- 将 Web SSO 协议、SAML、WS-Fed 或 OpenID Connect 用作机密客户端的网站或服务。

- 将移动应用 OpenID Connect 用作本机客户端的移动应用或桌面应用程序。

有关可在条件访问策略中使用的客户端应用列表，请参阅“Azure Active Directory 条件访问技术参考”中的[客户端应用条件](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference#client-apps-condition)。

此条件的常见用例包括提供以下保护的策略： 

- 要求在移动和桌面应用程序中使用[合规设备](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online)将大量数量下载到设备。 同时，允许从任何设备上的浏览器进行访问。

- 阻止从 Web 应用程序访问，但允许从移动和桌面应用程序访问。

可将此条件应用到 Web SSO 和新式身份验证协议。 还可将其应用到使用 Microsoft Exchange ActiveSync 的邮件应用程序。 示例包括大多数智能手机上的本机邮件应用。 目前，需要通过 Azure AD 联合身份验证服务来保护使用旧式协议的客户端应用。

仅当 Microsoft Office 365 Exchange Online 是所选的唯一云应用时，才能选择此客户端应用条件。

![云应用](./media/active-directory-conditional-access-conditions/32.png)

仅当未在策略中配置其他条件时，才支持选择“Exchange ActiveSync”作为客户端应用条件。 但是，可以缩小此条件的范围，使之仅应用到支持的平台。

 
![仅将策略应用到支持的平台](./media/active-directory-conditional-access-conditions/33.png)

仅向支持的平台应用此条件相当于将所有设备平台置于一个[设备平台条件](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online)中。

![配置设备平台](./media/active-directory-conditional-access-conditions/34.png)


 有关详细信息，请参阅以下文章：

- [为 Azure Active Directory 条件访问设置 SharePoint Online 和 Exchange Online](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-no-modern-authentication)。
 
- [基于 Azure Active Directory 应用的条件访问](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam)。 


### <a name="legacy-authentication"></a>旧式身份验证  

条件访问现在适用于不支持新式身份验证的旧式 Microsoft Office 客户端。 此外，它还适用于使用 POP、IMAP、SMTP 等邮件协议的客户端。 使用旧式身份验证可以配置类似于“阻止来自其他客户端的访问”的策略。


![配置客户端应用](./media/active-directory-conditional-access-conditions/160.png)  


#### <a name="known-issues"></a>已知问题

- 为“其他客户端”配置策略导致整个组织无法与 SPConnect 之类的特定客户端通信。 之所以发生这种阻止，是因为旧式客户端使用非预期的方式进行身份验证。 此问题不存在于主要的 Office 应用程序（例如旧式 Office 客户端）中。 

- 策略生效可能需要长达 24 小时的时间。 


#### <a name="frequently-asked-questions"></a>常见问题

**问：** 此身份验证是否会阻止 Microsoft Exchange Web 服务？

这取决于 Exchange Web 服务使用的身份验证协议。 如果 Exchange Web 服务应用程序使用新式身份验证，则此身份验证受“移动应用和桌面客户端”客户端应用的控制。 基本身份验证受“其他客户端”客户端应用的控制。


**问：** 可对“其他客户端”使用哪些控制？

可对“其他客户端”配置任何控制。 但是，最终用户体验将会是所有情况下都无法访问。 “其他客户端”不支持 MFA、符合标准的设备、域加入等控制。 
 
**问：** 可对“其他客户端”使用哪些条件？

可对“其他客户端”配置任何条件。

**问：** Exchange ActiveSync 是否支持所有条件和控制？

不是。 以下列表汇总了 Exchange ActiveSync 的支持情况： 

- Exchange ActiveSync 仅支持用户和组目标。 它不支持来宾或角色。 如果配置了来宾或角色条件，则会阻止所有用户。 Exchange ActiveSync 之所以阻止所有用户，是因为它无法确定是否要将策略应用到用户。

- Exchange ActiveSync 仅适用于充当云应用的 Microsoft Exchange Online。 

- Exchange ActiveSync 不支持除客户端应用本身以外的任何条件。 

- 可在 Exchange ActiveSync 中配置任何控制。 配置除设备符合性以外的任何控制会导致阻止。

**问：** 默认情况下，这些策略是否会持续应用到所有客户端应用？

不是。 默认的策略行为不会有任何变化。 默认情况下，这些策略将持续应用到浏览器、移动应用程序和桌面客户端。



## <a name="next-steps"></a>后续步骤

- 若要了解如何配置条件访问策略，请参阅[快速入门：通过 Azure Active Directory 条件访问要求特定应用必须使用 MFA](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-app-based-mfa)。

- 若要为环境配置条件访问策略，请参阅 [Azure Active Directory 中条件访问的最佳做法](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-best-practices)。 

