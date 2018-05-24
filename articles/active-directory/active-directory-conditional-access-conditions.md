---
title: Azure Active Directory 条件访问中的条件 | Microsoft Docs
description: 了解如何使用 Azure Active Directory 条件访问中的分配来触发策略。
services: active-directory
keywords: 对应用的条件性访问, 使用 Azure AD 进行条件性访问, 保护对公司资源的访问, 条件性访问策略
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 3cb8e598864bccfbea24a2aec5d9387ff903e51c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
ms.locfileid: "32770615"
---
# <a name="conditions-in-azure-active-directory-conditional-access"></a>Azure Active Directory 条件访问中的条件 

使用 [Azure Active Directory (Azure AD) 条件访问](active-directory-conditional-access-azure-portal.md)，可以控制授权用户访问云应用程序的方式。 在条件访问策略中，定义触发策略的诱因（“出现这种情况时”）的响应（“执行此操作”）。 

![控制](./media/active-directory-conditional-access-conditions/10.png)


在条件访问的上下文中：

- “出现这种情况时”称为**条件**。 
- “则执行此操作”称为**访问控制**。

条件与访问控制的组合表示一种条件访问策略。

![控制](./media/active-directory-conditional-access-conditions/61.png)


不会应用未在条件访问策略中配置的条件。 某些条件对于将条件访问策略应用到环境是[必需的](active-directory-conditional-access-best-practices.md#whats-required-to-make-a-policy-work)。 

本文概述条件，以及如何在条件访问策略中使用条件。 

## <a name="users-and-groups"></a>用户和组

用户和组条件在条件访问策略中是必需的。 在策略中，可以选择**所有用户**或选择特定的用户和组。

![控制](./media/active-directory-conditional-access-conditions/111.png)

如果选择：

- 所有用户：策略将应用到目录中的所有用户。 这包括来宾用户。

- 选择用户和组：你可以设置以下选项：

    - 所有来宾用户 - 可使你将策略面向 B2B 来宾用户。 此条件与将 *userType* 属性设置为“来宾”的所有用户帐户匹配。 如果一旦在 Azure AD 的邀请流中创建帐户后需要应用策略，则可以使用此设置。

    - 目录角色 - 可使你基于用户的角色分配定位策略。 此条件支持目录角色，如“全局管理员”或“密码管理员”。

    - 用户和组 - 可使你定位特定的用户集。 例如，将某个人力资源应用选作云应用时，可以选择包含人力资源部所有成员的组。

某个组，它可以是 Azure AD 中任何类型的组，包括动态组，或分配的安全组和通讯组。

此外，你还可以从策略中排除特定的用户或组。 例如，在策略强制实施多重身份验证 (MFA) 的情况下，往往会排除服务帐户。 

若要部署新策略，面向特定的用户集十分有用。 在新策略中，应该只将初始用户集用作目标来验证策略行为。 



## <a name="cloud-apps"></a>云应用 

云应用是网站或服务。 这包括 Azure 应用程序代理保护的网站。 有关受支持云应用的详细说明，请参阅[云应用分配](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments)。    

云应用条件在条件访问策略中是必需的。 在策略中，可以选择**所有云应用**或选择特定的应用。

![控制](./media/active-directory-conditional-access-conditions/03.png)

可以选择：

- **所有云应用**对要应用到整个组织的策略设置基准。 在要求执行多重身份验证（由于检测到任何云应用存在登录风险）的策略中，往往会选择此选项。 应用于**所有云应用**的策略将应用于对所有网站和服务的访问权限。 此设置并非仅限用于“选择云应用”列表上显示的云应用。

- 根据策略将特定服务指定为目标的单个云应用。 例如，可以要求用户使用[合规的设备](active-directory-conditional-access-policy-connected-applications.md)访问 SharePoint Online。 当其他服务访问 SharePoint 内容（例如 Microsoft Teams）时，也会对这些服务应用此策略。 

还可以从策略中排除特定的应用；但是，这些应用仍受到应用于它们访问的服务的策略的限制。 



## <a name="sign-in-risk"></a>登录风险

登录风险指不是由用户帐户合法拥有者执行的登录尝试的可能性（高、中、低）。 Azure AD 在用户登录期间会计算登录风险级别。 可以将计算得到的登录风险级别用作条件性访问策略中的条件。 

![条件](./media/active-directory-conditional-access-conditions/22.png)

若要使用此条件，需要启用 [Azure Active Directory Identity Protection](active-directory-identityprotection.md)。
 
此条件的常见用例包括执行以下操作的策略：

- 阻止高登录风险的用户，以防止潜在的非法用户访问云应用。 
- 要求中等登录风险的用户执行多重身份验证。 通过强制实施多重身份验证，可以更加肯定登录操作是由帐户的合法拥有者执行的。

有关详细信息，请参阅[风险登录](active-directory-identityprotection.md#risky-sign-ins)。  

## <a name="device-platforms"></a>设备平台

设备平台根据设备上运行的操作系统来定义特征。 Azure AD 使用设备（例如用户代理）提供的信息来标识平台。 由于此信息未经验证，因此我们建议在所有平台中应用某个策略，以便阻止访问、要求符合 Intune 政策，或要求设备加入域。 默认设置是将策略应用到所有设备平台。 


![条件](./media/active-directory-conditional-access-conditions/24.png)

有关所支持设备平台的完整列表，请参阅[设备平台条件](active-directory-conditional-access-technical-reference.md#device-platform-condition)。


此条件的一个常见用例是，策略将云应用的访问权限制为[托管设备](active-directory-conditional-access-policy-connected-applications.md#managed-devices)。 有关包含设备平台条件的其他方案，请参阅 [Azure Active Directory 基于应用的条件访问](active-directory-conditional-access-mam.md)。



## <a name="device-state"></a>设备状态

设备状态条件允许已加入混合 Azure AD 的设备和标记为符合的设备从条件访问策略中排除。 当策略应仅应用于非托管设备以使会话更安全时，此条件非常有用。 例如，当设备处于非托管状态时，仅强制执行 Microsoft Cloud App Security 会话控制。 


![条件](./media/active-directory-conditional-access-conditions/112.png)

如果想要阻止对非托管设备的访问，则应实现[基于设备的条件访问](active-directory-conditional-access-policy-connected-applications.md)。


## <a name="locations"></a>位置

通过位置，可以选择确定基于连接尝试的启动位置的条件。 
     
![条件](./media/active-directory-conditional-access-conditions/25.png)

此条件的常见用例包括执行以下操作的策略：

- 要求用户在企业网络外部访问服务时执行多重身份验证。  

- 阻止特定国家或地区的用户访问服务。 

有关详细信息，请参阅 [Azure Active Directory 条件访问中的位置条件](active-directory-conditional-access-locations.md)。


## <a name="client-apps"></a>客户端应用

使用客户端应用条件可将策略应用到不同类型的应用程序，例如：

- 网站和服务
- 移动应用和桌面应用程序。 

![条件](./media/active-directory-conditional-access-conditions/04.png)

应用程序分类为：

- 将 Web SSO 协议、SAML、WS-Fed 或 OpenID Connect 用作机密客户端的网站或服务。

- 将移动应用 OpenID Connect 用作本机客户端的移动应用或桌面应用程序。

对于可以在条件性访问策略中使用的完整客户端应用列表，请参阅 [Azure Active Directory 条件性访问技术参考](active-directory-conditional-access-technical-reference.md#client-apps-condition)。

此条件的常见用例包括执行以下操作的策略：

- 要求在移动和桌面应用程序中使用[合规的设备](active-directory-conditional-access-policy-connected-applications.md)将大量数量下载到设备，同时允许从任何设备上的浏览器进行访问。

- 阻止从 Web 应用程序访问，但允许从移动和桌面应用程序访问。

除了使用 Web SSO 和新式身份验证协议以外，还可以对使用 Exchange ActiveSync 的邮件应用程序（例如大多数智能手机上的本机邮件应用）应用此条件。 目前，对于使用旧式协议的客户端应用，需要通过 AD FS 对其进行保护。

 有关详细信息，请参阅：

- [为 Azure Active Directory 条件访问设置 SharePoint Online 和 Exchange Online](active-directory-conditional-access-no-modern-authentication.md)
 
- [基于 Azure Active Directory 应用的条件访问](active-directory-conditional-access-mam.md) 








## <a name="next-steps"></a>后续步骤

- 若要了解如何配置条件访问策略，请参阅 [Azure Active Directory 中的条件访问入门](active-directory-conditional-access-azure-portal-get-started.md)。

- 如果已准备好配置环境的条件访问策略，请参阅 [Azure Active Directory 中条件访问的最佳做法](active-directory-conditional-access-best-practices.md)。 

