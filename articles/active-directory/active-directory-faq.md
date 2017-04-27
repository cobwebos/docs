---
title: "Azure Active Directory 常见问题解答 | Microsoft Docs"
description: "“Azure Active Directory 常见问题”提供的问题解答涉及如何访问 Azure 和 Azure Active Directory、密码管理和应用程序访问。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/24/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 025e8c9e575123a3ad9863a35061ebd0af212486
ms.lasthandoff: 03/30/2017


---
# <a name="azure-active-directory-faq"></a>Azure Active Directory 常见问题
Azure Active Directory (Azure AD) 是综合性的标识即服务 (IDaaS) 解决方案，涉及到标识、访问管理和安全的方方面面。

有关详细信息，请参阅[什么是 Azure Active Directory？](active-directory-whatis.md)。


## <a name="access-azure-and-azure-active-directory"></a>访问 Azure 和 Azure Active Directory
**问：尝试在 Azure 经典门户(https://manage.windowsazure.com)中访问 Azure AD 时，为何收到“找不到订阅”？**

**答：**若要访问 Azure 经典门户，每个用户都需要 Azure 订阅的权限。 如果订阅为付费型 Office 365 订阅或 Azure AD 订阅，请访问 [http://aka.ms/accessAAD](http://aka.ms/accessAAD)，了解一次性激活步骤。 否则需激活免费型 [Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)或某个付费型订阅。

有关详细信息，请参阅：

* [Azure 订阅与 Azure Active Directory 的关联方式](active-directory-how-subscriptions-associated-directory.md)
* [在 Azure 中管理 Office 365 订阅的目录](active-directory-manage-o365-subscription.md)

- - -
**问：Azure AD、Office 365 与 Azure 之间有哪种关系？**

**答：** Azure AD 为所有 Web 服务提供通用的标识和访问功能。 不管使用的是 Office 365、Microsoft Azure、Intune 还是其他服务，都是在使用 Azure AD 为上述所有服务启用登录和访问管理。

可以将所有已设置为使用 Web 服务的用户定义为一个或多个 Azure AD 实例中的用户帐户。 可以在设置这些帐户时启用免费的 Azure AD 功能，例如云应用程序访问。

Azure AD 付费型服务（例如企业移动性 + 安全性）可通过综合性的企业级管理和安全解决方案来弥补其他 Web 服务（例如 Office 365 和 Microsoft Azure）的不足。
- - -
**问：为什么可以登录到 Azure 门户，而不能登录到 Azure 经典门户？**

**答：**Azure 门户不需要有效的订阅，而经典门户则需要有效的订阅。  如果没有订阅，则无法登录到经典门户。
- - -
**问：订阅管理员和目录管理员的区别是什么？**

**答：**默认情况下，当用户注册 Azure 时，系统会为其分配订阅管理员角色。 订阅管理员可以使用 Microsoft 帐户，也可以使用 Azure 订阅与之关联的目录中的工作或学校帐户。  此角色有权管理 Azure 门户中的服务。

如果其他人需要使用同一个订阅登录和访问服务，则可将其添加为共同管理员。 此角色具有与服务管理员一样的访问特权，但不能更改订阅与 Azure 目录之间的关联关系。  有关订阅管理员的其他信息，请参阅[如何添加或更改 Azure 管理员角色](../billing-add-change-azure-subscription-administrator.md)和[Azure 订阅与 Azure Active Directory 的关联方式](active-directory-how-subscriptions-associated-directory.md)。


Azure AD 提供另一组管理员角色来管理与目录和标识相关的功能。  这些管理员将有权访问 Azure 门户或 Azure 经典门户中的各种功能。 管理员的角色决定了其所能执行的操作，例如创建或编辑用户、向其他用户分配管理角色、重置用户密码、管理用户许可证，或者管理域。  有关 Azure AD 目录管理员及其角色的其他信息，请参阅[在 Azure Active Directory 中分配管理员角色](active-directory-assign-admin-roles.md)。

另外，Azure AD 付费型服务（例如企业移动性 + 安全性）可通过综合性的企业级管理和安全解决方案来弥补其他 Web 服务（例如 Office 365 和 Microsoft Azure）的不足。

- - -
**问：是否可以通过报表来查看我的 Azure AD 用户许可证何时会过期？**

**答：**否。  此功能目前不可用。

- - -

## <a name="get-started-with-hybrid-azure-ad"></a>混合 Azure AD 入门


**问：如果我已被添加为协作者，该如何离开原来的租户？**

**答：**如果被作为协作者添加到另一组织的租户，可使用右上角的“租户切换器”在租户之间切换。  目前还无法主动离开邀请组织，Microsoft 正致力于提供该功能。  在该功能可用之前，可以要求邀请阻止将你从其租户中删除。
- - -
**问：如何将我的本地目录连接到 Azure AD？**

**答：**可以使用 Azure AD Connect 将本地目录连接到 Azure AD。

有关详细信息，请参阅[将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)。

- - -
**问：如何设置本地目录与云应用程序之间的 SSO？**

**答：**只需在本地目录与 Azure AD 之间设置单一登录 (SSO)。 只要通过 Azure AD 访问云应用程序，该服务就会自动让用户使用其本地凭据正确进行身份验证。

可以通过联合身份验证解决方案（例如 Active Directory 联合身份验证服务 (AD FS)）或通过配置密码哈希同步，轻松地从本地实现 SSO。 可以使用 Azure AD Connect 配置向导轻松部署这两个选项。

有关详细信息，请参阅[将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)。

- - -
**问：Azure AD 是否为组织中的用户提供自助服务门户？**

**答：** 是的，Azure AD 提供 [Azure AD 访问面板](http://myapps.microsoft.com) ，方便用户使用自助服务以及进行应用程序访问。 如果你是 Office 365 客户，可以在 Office 365 门户中找到许多相同的功能。

有关详细信息，请参阅[访问面板简介](active-directory-saas-access-panel-introduction.md)。

- - -
**问：Azure AD 是否可以帮助我管理本地基础结构？**

**答：**可以。 Azure AD Premium Edition 提供 Azure AD Connect Health。 Azure AD Connect Health 可帮助你监视和深入了解本地标识基础结构和同步服务。  

有关详细信息，请参阅[在云中监视本地标识基础结构和同步服务](active-directory-aadconnect-health.md)。  

- - -
## <a name="password-management"></a>密码管理
**问：是否可以使用 Azure AD 密码写回但不使用密码同步？（在这种情况下，是否可以结合密码写回使用 Azure AD 自助服务密码重置 (SSPR)，而不将密码存储在云中？）**

**答：**无需将 Active Directory 密码同步到 Azure AD 即可启用写回。 在联合环境中，Azure AD 单一登录 (SSO) 依赖本地目录对用户进行身份验证。 在这种情况下，并不需要在 Azure AD 中跟踪本地密码。

- - -
**问：需要多长时间才能将密码写回到 Active Directory 本地？**

**答：**密码写回实时进行。

有关详细信息，请参阅[密码管理入门](active-directory-passwords-getting-started.md)。

- - -
**问：是否可以对管理员管理的密码使用密码写回？**

**答：**可以。如果已启用密码写回，管理员执行的密码操作将写回到用户的本地环境。  

有关密码相关问题的详细解答，请参阅[密码管理常见问题](active-directory-passwords-faq.md)。
- - -
**问：如果我在尝试更改 Office 365/Azure AD 密码时忘记了现有的密码，该怎么办？**

**答：**对于这种情况，有几个选项。  在可行的情况下，使用自助服务密码重置 (SSPR)。  SSPR 是否适用取决于其配置方式。  有关详细信息，请参阅[密码重置门户的工作原理](active-directory-passwords-learn-more.md#how-does-the-password-reset-portal-work)。

对于 Office 365 用户，管理员可以使用 [Reset user passwords](https://support.office.com/en-us/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US)（重置用户密码）中概述的步骤重置密码。

对于 Azure AD 帐户，管理员可以使用以下选项之一重置密码：

- [在 Azure 门户中重置帐户](active-directory-users-reset-password-azure-portal.md)
- [在经典门户中重置帐户](active-directory-create-users-reset-password.md)
- [使用 PowerShell](https://docs.microsoft.com/en-us/powershell/msonline/v1/Set-MsolUserPassword?redirectedfrom=msdn)


- - -
## <a name="security"></a>安全
**问：帐户在经过特定次数的失败尝试后被锁定还是使用了更复杂的策略？**</br>
我们使用更复杂的策略来锁定帐户。  这基于请求的 IP 和输入的密码。 锁定的持续时间也会根据存在攻击的可能性而延长。  

**问：某些（通用）密码会被拒绝并且显示消息“此密码已使用了许多次”，这是否是指当前 Active Directory 中使用的密码？**</br>
这指的是全局通用的密码，例如“Password”和“123456”的任何变体。

**问：B2C 租户中就会阻止来自可疑来源（僵尸网络、Tor 终结点）的登录请求还是需要使用基本或高级版租户才能阻止？**</br>
我们有一个网关，它会筛选请求并针对僵尸网络提供一定的防护，它适用于所有 B2C 租户。 

## <a name="application-access"></a>应用程序访问
**问：在哪里可以找到与 Azure AD 预先集成的应用程序及其功能的列表？**

**答：**Azure AD 中包含 Microsoft、应用程序服务提供商和合作伙伴提供的 2600 多个预先集成的应用程序。 所有预先集成的应用程序都支持单一登录 (SSO)。 SSO 允许用户使用组织凭据来访问应用。 某些应用程序还支持自动预配和自动取消预配。

有关预先集成的应用程序的完整列表，请参阅 [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/)（Active Directory 应用商店）。

- - -
**问：如果 Azure AD 应用商店中没有我需要的应用程序怎么办？**

**答：**使用 Azure AD Premium，可以添加和配置所需的任何应用程序。 你可以根据应用程序的功能和你的喜好来配置 SSO 和自动预配。  

有关详细信息，请参阅：

* [针对不在 Azure Active Directory 应用程序库中的应用程序配置单一登录](active-directory-saas-custom-apps.md)
* [使用 SCIM 启用从 Azure Active Directory 到应用程序的用户和组自动预配](active-directory-scim-provisioning.md)

- - -
**问：用户如何使用 Azure AD 来登录应用程序？**

**答：**Azure AD 提供多种方式供用户查看和访问其应用程序，例如：

* Azure AD 访问面板
* Office 365 应用程序启动器
* 直接登录联合应用
* 联合、基于密码或现有的应用的深层链接

有关详细信息，请参阅 [为用户部署 Azure AD 集成的应用程序](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)。

- - -
**问：Azure AD 可通过哪些不同的方式来启用对应用程序的身份验证和单一登录？**

**答：** Azure AD 支持使用多种标准化协议（例如 SAML 2.0、OpenID Connect、OAuth 2.0 和 WS-Federation）进行身份验证和授权。 对于仅支持基于窗体的身份验证的应用程序，Azure AD 还支持密码保管和自动化登录功能。  

有关详细信息，请参阅：

* [Azure AD 的身份验证方案](active-directory-authentication-scenarios.md)
* [Active Directory 身份验证协议](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Azure Active Directory 中单一登录的工作原理是什么？](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

- - -
**问：是否可以添加本地运行的应用程序？**

**答：** Azure AD 应用程序代理可让你轻松安全地访问所选的本地 Web 应用程序。 可以像访问 Azure AD 中的软件即服务 (SaaS) 应用一样访问这些应用程序。 不需要设置 VPN 或更改网络基础结构。  

有关详细信息，请参阅[如何提供对本地应用程序的安全远程访问](active-directory-application-proxy-get-started.md)。

- - -
**问：如何要求访问特定应用程序的用户进行多重身份验证？**

**答：** 使用 Azure AD 条件访问，可以针对每个应用程序分配独特的访问策略。 可以在策略中要求用户始终进行多重身份验证，或者在未连接到本地网络时才进行。  

有关详细信息，请参阅[保护对 Office 365 和其他连接到 Azure Active Directory 的应用的访问](active-directory-conditional-access.md)。

- - -
**问：SaaS 应用的自动用户预配是什么？**

**答：**使用 Azure AD 可以在许多流行的云 (SaaS) 应用中自动创建、维护和删除用户标识。

有关详细信息，请参阅[使用 Azure Active Directory 自动预配和取消预配 SaaS 应用程序的用户](active-directory-saas-app-provisioning.md)。

- - -
**问：是否可以通过 Azure AD 设置安全的 LDAP 连接？**

**答：**否。  Azure AD 不支持 LDAP 协议。

