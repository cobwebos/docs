---
title: 适用于混合组织的 B2B 协作 - Azure Active Directory | Microsoft Docs
description: 使用 Azure AD B2B 协作授予合作伙伴对本地资源和云资源的访问权限。
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/26/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 943ccadbc87cd8d2345078405e2a27930634668e
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33928090"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>适用于混合组织的 Azure Active Directory B2B 协作

使用 Azure Active Directory (Azure AD) B2B 协作，可以轻松向外部合作伙伴授予对组织中应用和资源的访问权限。 即使是在同时包含本地资源和基于云的资源的混合配置中，也能做到这一点。 当前是在本地标识系统中管理外部合作伙伴帐户，还是在云中以 Azure AD B2B 用户的形式管理外部帐户，都不重要。 现在，可以对这两种环境使用相同的登录凭据，向这些用户授予对任一位置中的资源的访问权限。

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>向 Azure AD 中的 B2B 用户授予对本地应用的访问权限

如果组织使用 Azure AD B2B 协作功能邀请合作伙伴组织中的来宾用户加入 Azure AD，则现在可以向这些 B2B 用户提供本地应用的访问权限。

对于使用基于 SAML 的身份验证的应用，可以通过 Azure 门户，使用 Azure AD 应用程序代理向 B2B 用户提供这些应用以进行身份验证。

对于结合 Kerberos 约束委派 (KCD) 使用 Windows 集成身份验证 (IWA) 的应用，还可以使用 Azure AD 代理进行身份验证。 但是，要进行授权，需要在本地 Windows Server Active Directory 中创建一个用户对象。 可以使用两种方法创建表示 B2B 来宾用户的本地用户对象。

- 可以使用 Microsoft Identity Manager (MIM) 2016 SP1 和 Microsoft Graph 的 MIM 管理代理。
- 可以使用 PowerShell 脚本。 （此解决方案不需要 MIM。）

有关如何实施这些解决方案的详细信息，请参阅[向 Azure AD 中的 B2B 用户授予对本地应用程序的访问权限](active-directory-b2b-hybrid-cloud-to-on-premises.md)。

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>向本地托管的合作伙伴帐户授予对云资源的访问权限

在 Azure AD 推出之前，使用本地标识系统的组织一贯是在其本地目录中管理合作伙伴帐户。 如果你就在这样的一家组织，则需要确保在将应用和其他资源转移到云时，合作伙伴仍旧拥有访问权限。 最好是让这些用户使用相同的凭据集来访问云资源和本地资源。 

我们现在提供了相应的方法，让你使用 Azure AD Connect 将这些本地帐户作为“来宾用户”同步到云，在云中，这些帐户的行为与 Azure AD B2B 用户相同。

为帮助保护公司数据，可将访问范围控制为适当的资源，并配置授权策略，以区分对待这些来宾用户和本公司的员工。

有关实施详细信息，请参阅[使用 Azure AD B2B 协作向本地托管的合作伙伴帐户授予对云资源的访问权限](active-directory-b2b-hybrid-on-premises-to-cloud.md)。
 
## <a name="next-steps"></a>后续步骤

- [向 Azure AD 中的 B2B 用户授予对本地应用程序的访问权限](active-directory-b2b-hybrid-cloud-to-on-premises.md)
- [使用 Azure AD B2B 协作向本地托管的合作伙伴帐户授予对云资源的访问权限](active-directory-b2b-hybrid-on-premises-to-cloud.md)


