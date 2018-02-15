---
title: "Azure AD Connect：直通身份验证 - 工作原理 | Microsoft 文档"
description: "本文介绍了 Azure Active Directory 直通身份验证的工作原理"
services: active-directory
keywords: "Azure AD Connect 传递身份验证, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: billmath
ms.openlocfilehash: eaa9995430833c0c087ed0d4044f6c41d254e3ff
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2018
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory 直通身份验证：技术深入研究
本文简要介绍了 Azure Active Directory (Azure AD) 直通身份验证的工作原理。 有关深入的技术和安全信息，请参阅[深入了解安全性](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md)一文。

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Azure Active Directory 直通身份验证的工作原理是什么？

当用户尝试登录到由 Azure AD 保护的应用程序时，如果已在租户中启用直通身份验证，会发生以下情况：

1. 用户尝试访问某个应用程序，例如 [Outlook Web 应用](https://outlook.office365.com/owa/)。
2. 如果用户尚未登录，该用户将被重定向到 Azure AD **用户登录**页面。
3. 用户在 Azure AD 登录页中输入其用户名和密码，然后选择“登录”按钮。
4. 收到登录请求后，Azure AD 将该用户名和密码（已使用公钥加密）排入队列。
5. 本地身份验证代理从队列中检索用户名和已加密的密码。 请注意，代理不会频繁地从队列中轮询请求，但会通过预先建立的持久性连接检索请求。
6. 代理使用其私钥解密密码。
7. 代理使用标准 Windows API（类似于 Active Directory 联合身份验证服务 (AD FS) 使用的机制）针对 Active Directory 验证该用户名和密码。 用户名可以是本地默认用户名（通常为 `userPrincipalName`），也可以是在 Azure AD Connect 中配置的另一个属性（称为 `Alternate ID`）。
8. 本地 Active Directory 域控制器 (DC) 将评估请求并向代理返回适当的响应（成功、失败、密码过期或用户被锁定）。
9. 身份验证代理转而将此响应返回给 Azure AD。
10. Azure AD 评估响应，并相应地向用户提供响应。 例如，Azure AD 立即让用户登录或请求 Azure 多重身份验证。
11. 如果用户登录成功，则该用户可以访问应用程序。

下图说明了所涉及的所有组件和步骤：

![直通身份验证](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>后续步骤
- [当前限制](active-directory-aadconnect-pass-through-authentication-current-limitations.md)：了解支持和不支持的方案。
- [快速入门](active-directory-aadconnect-pass-through-authentication-quick-start.md)：快速了解 Azure AD 直通身份验证。
- [智能锁定](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)：在租户中配置智能锁定功能以保护用户帐户。
- [常见问题](active-directory-aadconnect-pass-through-authentication-faq.md)：查找常见问题的解答。
- [故障诊断](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)：了解如何解决直通身份验证功能的常见问题。
- [深入了解安全性](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md)：深入了解有关直通身份验证功能的技术信息。
- [Azure AD 无缝 SSO](active-directory-aadconnect-sso.md)：深入了解此补充功能。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)：使用 Azure Active Directory 论坛来提交新的功能请求。

