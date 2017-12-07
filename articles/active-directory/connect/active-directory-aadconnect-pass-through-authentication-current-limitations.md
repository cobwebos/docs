---
title: "Azure AD Connect：直通身份验证 - 当前限制 | Microsoft Docs"
description: "本文介绍 Azure Active Directory (Azure AD) 直通身份验证的当前限制"
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
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 978ad8f14d70fe60cb220136e87ce4a064672b8a
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory 直通身份验证：当前限制

>[!IMPORTANT]
>Azure Active Directory (Azure AD) 直通身份验证是一项免费功能，不需要拥有任何付费版本的 Azure AD 即可使用。 直通身份验证仅适用于 Azure AD 全球实例，[Microsoft Azure 德国云](http://www.microsoft.de/cloud-deutschland)或 [Microsoft Azure 政府云](https://azure.microsoft.com/features/gov/)中不可用。

## <a name="supported-scenarios"></a>支持的方案

完全支持以下方案：

- 用户登录到所有基于 Web 浏览器的应用程序
- 用户登录到支持[新式身份验证](https://aka.ms/modernauthga)的 Office 365 客户端应用程序
- 带有新式身份验证的 Office 2016 和 Office 2013
- 适用于 Windows 10 设备的 Azure AD 域加入
- Exchange ActiveSync 支持

## <a name="unsupported-scenarios"></a>不支持的方案

_不_支持以下方案：

- 用户登录到旧版 Office 客户端应用程序：不带新式身份验证的 Office 2010 和 Office 2013。 我们建议组织在可能的情况下改用新式身份验证。 新式身份验证允许直通身份验证支持。 此外，它还通过使用[条件访问](../active-directory-conditional-access-azure-portal.md)功能（如 Azure 多重身份验证）来帮助保护你的用户帐户的安全。
- 用户登录到 Skype for Business 客户端应用程序，包括 Skype for Business 2016。
- 用户登录到 PowerShell 版本 1.0。 建议使用 PowerShell 版本 2.0。
- Azure Active Directory 域服务。
- 进行多重身份验证的应用密码。
- 检测[凭据泄露](../active-directory-reporting-risk-events.md#leaked-credentials)的用户。

>[!IMPORTANT]
>仅作为不支持方案的变通方法，可在 Azure AD Connect 向导中的[可选功能](active-directory-aadconnect-get-started-custom.md#optional-features)页上启用“密码哈希同步”。 此外，启用“密码哈希同步”还可以选择在本地基础结构被破坏的情况下故障转移身份验证。 这种从直通身份验证到 Active Directory 密码哈希同步的故障转移不是自动进行的。 而是需要在 Microsoft 支持部门的帮助下完成。

## <a name="next-steps"></a>后续步骤
- [快速入门](active-directory-aadconnect-pass-through-authentication-quick-start.md)：快速了解 Azure AD 直通身份验证。
- [智能锁定](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)：了解如何在租户中配置智能锁定功能以保护用户帐户。
- [技术深入了解](active-directory-aadconnect-pass-through-authentication-how-it-works.md)：了解直通身份验证功能的工作原理。
- [常见问题解答](active-directory-aadconnect-pass-through-authentication-faq.md)：查找有关直通身份验证功能的常见问题的解答。
- [故障诊断](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)：了解如何解决直通身份验证功能的常见问题。
- [深入了解安全性](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md)：深入了解有关直通身份验证功能的技术信息。
- [Azure AD 无缝 SSO](active-directory-aadconnect-sso.md)：深入了解此补充功能。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)：使用 Azure Active Directory 论坛来提交新的功能请求。

