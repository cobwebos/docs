---
title: Azure AD Connect：直通身份验证 | Microsoft Docs
description: 本文介绍 Azure Active Directory (Azure AD) 传递身份验证，以及它如何通过针对本地 Active Directory 验证用户密码来实现 Azure AD 登录。
services: active-directory
keywords: 什么是 Azure AD Connect 直通身份验证, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 899aeeb994ca5c9bf30dfca876dff61c1ccf3ea7
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2018
ms.locfileid: "49637573"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>使用 Azure Active Directory 传递身份验证的用户登录

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>什么是 Azure Active Directory 传递身份验证？

借助 Azure Active Directory (Azure AD) 传递身份验证证，用户可使用同一密码登录到本地应用程序和基于云的应用程序。 此功能为用户提供更好的体验 - 少记一个密码，并且能减少 IT 支持人员成本，因为用户不太可能忘记如何登录。 如果用户使用 Azure AD 进行登录，此功能可直接针对本地 Active Directory 验证用户的密码。

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

此功能是 [Azure AD 密码哈希同步](how-to-connect-password-hash-synchronization.md)的一种替代方法，可为组织提供同样的云身份验证权益。 但如果某些组织希望强制执行其本地 Active Directory 安全和密码策略，则可以选择性地使用传递身份验证。 请查看[本指南](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)，对比各种 Azure AD 登录方法并了解如何为组织选择正确的登录方法。

![Azure AD 直通身份验证](./media/how-to-connect-pta/pta1.png)

可将传递身份验证与[无缝单一登录](how-to-connect-sso.md)功能结合使用。 这样一来，如果用户在企业网络中的企业计算机上访问应用程序，他们不需要键入密码便可登录。

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>使用 Azure AD 传递身份验证的主要优势

- 出色的用户体验
  - 用户使用同样的密码登录本地和基于云的应用程序。
  - 用户花费在联系 IT 支持人员解决密码相关问题上的时间更少。
  - 用户可以完成云中的[自助服务密码管理](../authentication/active-directory-passwords-overview.md)任务。
- 易于部署和管理
  - 无需复杂的本地部署或网络配置。
  - 仅需在本地安装一个轻型代理。
  - 无管理开销。 代理会自动获得改进和 bug 修复。
- *安全*
  - 本地密码永远不会以任何形式存储在云中。
  - 可通过与 [Azure AD 条件访问策略](../active-directory-conditional-access-azure-portal.md)（包括多重身份验证 (MFA)、[阻止旧式身份验证](../conditional-access/conditions.md)）无缝协作，也可通过[筛选暴力破解密码攻击](../authentication/howto-password-smart-lockout.md)来保护用户帐户。
  - 代理只从网络内部建立出站连接。 因此，无需在外围网络（也称为 DMZ）中安装代理。
  - 使用基于证书的身份验证保护代理与 Azure AD 之间的通信。 这些证书每隔几个月由 Azure AD 自动续订。
- 高可用性
  - 可在多台本地服务器上安装其他代理，提供登录请求的高可用性。

## <a name="feature-highlights"></a>功能特点

- 支持用户登录到所有基于 Web 浏览器的应用程序和使用[新式身份验证](https://aka.ms/modernauthga)的 Microsoft Office 客户端应用程序。
- 登录用户名可以是本地默认用户名 (`userPrincipalName`)，也可以是 Azure AD Connect 中配置的另一个属性（称为 `Alternate ID`）。
- 该功能可与[条件性访问](../active-directory-conditional-access-azure-portal.md)功能（例如多重身份验证 (MFA)）进行无缝配合使用，帮助保护用户安全。
- 与基于云的[自助密码管理](../authentication/active-directory-passwords-overview.md)集成，包括本地 Active Directory 的密码写回和通过禁止常用密码的密码保护。
- 如果 AD 林之间存在信任关系并且正确配置了名称后缀路由，则支持多林环境。
- 这是一项免费功能，不需要拥有任何付费版本的 Azure AD 即可使用此功能。
- 可通过 [Azure AD Connect](whatis-hybrid-identity.md) 启用它。
- 该功能使用轻型本地代理侦听和响应密码验证请求。
- 安装多个代理可提供登录请求的高可用性。
- 该功能[保护](../authentication/howto-password-smart-lockout.md)云中的本地帐户免受密码搜索攻击。

## <a name="next-steps"></a>后续步骤

- [快速入门](how-to-connect-pta-quick-start.md) - 快速了解并运行 Azure AD 传递身份验证。
- [从 AD FS 迁移到传递身份验证](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) - 从 AD FS（或其他联合技术）迁移到传递身份验证的详细指南。
- [智能锁定](../authentication/howto-password-smart-lockout.md) - 在租户中配置智能锁定功能以保护用户帐户。
- [当前限制](how-to-connect-pta-current-limitations.md) - 了解支持和不支持的方案。
- [深入技术探究](how-to-connect-pta-how-it-works.md) - 了解此功能如何运作。
- [常见问题](how-to-connect-pta-faq.md) - 常见问题解答。
- [故障排除](tshoot-connect-pass-through-authentication.md) - 了解如何解决使用此功能时遇到的常见问题。
- [深入了解安全性](how-to-connect-pta-security-deep-dive.md) - 有关该功能的其他深入技术信息。
- [Azure AD 无缝 SSO](how-to-connect-sso.md) - 深入了解此补充功能。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用于填写新功能请求。
