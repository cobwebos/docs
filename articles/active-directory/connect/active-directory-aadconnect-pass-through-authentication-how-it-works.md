---
title: "Azure AD Connect：直通身份验证 - 工作原理 | Microsoft Docs"
description: "本文介绍了 Azure Active Directory 直通身份验证的工作原理。"
services: active-directory
keywords: "Azure AD Connect 直通身份验证, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 3c648054666a1771187b3862b7c4972831b85f2c
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory 直通身份验证：技术深入研究

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Azure Active Directory 直通身份验证的工作原理是什么？

当用户尝试登录到由 Azure Active Directory (Azure AD) 保护的应用程序时，如果已在租户中启用直通身份验证，会发生以下情况：

1. 用户尝试访问应用程序（例如 Outlook Web App - https://outlook.office365.com/owa/）。
2. 如果用户尚未登录，该用户将被重定向到 Azure AD 登录页面。
3. 用户在 Azure AD 登录页中输入其用户名和密码，并单击“登录”按钮。
4. 收到登录请求后，Azure AD 将该用户名和密码（已使用公钥加密）排入队列。
5. 本地直通身份验证代理对队列发出出站调用，并检索用户名及加密密码。
6. 代理使用其私钥解密密码。
7. 然后，代理使用标准 Windows API（类似于 Active Directory 联合身份验证服务使用的机制）针对 Active Directory 验证该用户名和密码。 用户名可以是本地默认用户名（通常为 `userPrincipalName`），也可以是在 Azure AD Connect 中配置的另一个属性（称为 `Alternate ID`）。
8. 然后，本地 Active Directory 域控制器 (DC) 评估请求并向代理返回适当的响应（成功、失败、密码过期或用户被锁定）。
9. 代理随后将此响应返回给 Azure AD。
10. Azure AD 评估响应并适当地响应用户，例如，立刻让用户登录或请求进行多重身份验证 (MFA)。
11. 如果用户登录成功，则该用户可以访问应用程序。

下图说明了所涉及的所有组件和步骤。

![直通身份验证](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>后续步骤
- [**当前限制**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - 了解支持和不支持的方案。
- [**快速入门**](active-directory-aadconnect-pass-through-authentication-quick-start.md) - 启动并运行 Azure AD 直通身份验证。
- [**常见问题**](active-directory-aadconnect-pass-through-authentication-faq.md) - 常见问题解答。
- [故障排除](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 了解如何解决使用此功能时遇到的常见问题。
- [**Azure AD 无缝 SSO**](active-directory-aadconnect-sso.md) - 深入了解此补充功能。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用于填写新功能请求。

