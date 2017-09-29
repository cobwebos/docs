---
title: "Azure AD Connect：直通身份验证 - 当前限制 | Microsoft Docs"
description: "本文介绍 Azure Active Directory (Azure AD) 直通身份验证的当前限制。"
services: active-directory
keywords: "Azure AD Connect 传递身份验证, 安装 Active Directory, Azure AD 所需组件, SSO, 单一登录"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 6e193efe391c614a17d2861d4ba7d7776a7d441c
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory 直通身份验证：当前限制

>[!IMPORTANT]
>Azure AD 直通身份验证是一项免费功能，不需要拥有任何付费版本的 Azure AD 即可使用此功能。 直通身份验证仅适用于 Azure AD 全球实例，[Microsoft 云德国](http://www.microsoft.de/cloud-deutschland)和 [Microsoft Azure 政府云](https://azure.microsoft.com/features/gov/)中不可用。

## <a name="supported-scenarios"></a>支持的方案

完全支持以下方案：

- 用户登录到所有基于 Web 浏览器的应用程序。
- 用户登录到支持[新式身份验证](https://aka.ms/modernauthga)的 Office 365 客户端应用程序。
- 适用于 Windows 10 设备的 Azure AD Join。
- Exchange ActiveSync 支持。

## <a name="unsupported-scenarios"></a>不支持的方案

_不_支持以下方案：

- 用户登录到旧版 Office 客户端应用程序（Office 2013 或更早版本）。 我们建议组织在可能的情况下改用新式身份验证。 新式身份验证允许传递身份验证支持，而且还有助于使用[条件访问](../active-directory-conditional-access.md)功能（例如多重身份验证 (MFA)）保护用户帐户。
- 用户登录到 Skype for Business 客户端应用程序，包括 Skype for Business 2016。
- 用户登录到 PowerShell v1.0。 建议改用 PowerShell v2.0。
- MFA 的应用密码。
- 检测[凭据泄露](../active-directory-reporting-risk-events.md#leaked-credentials)的用户。

>[!IMPORTANT]
>作为不支持方案的变通方法，可在 Azure AD Connect 向导中的[可选功能](active-directory-aadconnect-get-started-custom.md#optional-features)页面上启用“密码哈希同步”。 密码哈希同步只能作为前述方案的后备方法（而不能作为直通身份验证的通用后备方法）。 启用密码哈希同步还可提供在本地基础结构被破坏的情况下故障转移身份验证（通过 Microsoft 支持部门）的选项。

## <a name="next-steps"></a>后续步骤
- [**快速入门**](active-directory-aadconnect-pass-through-authentication-quick-start.md) - 启动并运行 Azure AD 直通身份验证。
- [深入技术探究](active-directory-aadconnect-pass-through-authentication-how-it-works.md) - 了解此功能如何运作。
- [**常见问题**](active-directory-aadconnect-pass-through-authentication-faq.md) - 常见问题解答。
- [故障排除](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 了解如何解决使用此功能时遇到的常见问题。
- [**Azure AD 无缝 SSO**](active-directory-aadconnect-sso.md) - 深入了解此补充功能。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用于填写新功能请求。

