---
title: Azure AD Connect：直通身份验证 - 当前限制 | Microsoft Docs
description: 本文介绍 Azure Active Directory (Azure AD) 直通身份验证的当前限制
services: active-directory
keywords: Azure AD Connect 传递身份验证, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: billmath
ms.openlocfilehash: 3e533b8b23c095a3de845d9b26a96aea9d8ee086
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory 直通身份验证：当前限制

>[!IMPORTANT]
>Azure Active Directory (Azure AD) 直通身份验证是一项免费功能，不需要拥有任何付费版本的 Azure AD 即可使用。 直通身份验证仅适用于 Azure AD 全球实例，[Microsoft Azure 德国云](http://www.microsoft.de/cloud-deutschland)或 [Microsoft Azure 政府云](https://azure.microsoft.com/features/gov/)中不可用。

## <a name="supported-scenarios"></a>支持的方案

完全支持以下方案：

- 用户登录到所有基于 Web 浏览器的应用程序。
- 用户登录到支持[新式身份验证](https://aka.ms/modernauthga)的 Office 应用程序：_带有_新式身份验证的 Office 2016 和 Office 2013。
- 用户使用旧版协议（例如 Exchange ActiveSync、SMTP、POP 和 IMAP）登录 Outlook 客户端。
- 用户登录到支持新式身份验证的 Skype for Business，包括联机和混合拓扑。 在[此处](https://technet.microsoft.com/library/mt803262.aspx)详细了解受支持的拓扑。
- 适用于 Windows 10 设备的 Azure AD 域加入。
- 进行多重身份验证的应用密码。

## <a name="unsupported-scenarios"></a>不支持的方案

_不_支持以下方案：

- 用户登录到旧版 Office 客户端应用程序（不包括 Outlook，请参阅上面的**支持的方案**）：不带新式身份验证的 Office 2010 和 Office 2013。 我们建议组织在可能的情况下改用新式身份验证。 新式身份验证允许直通身份验证支持。 此外，它还通过使用[条件访问](../active-directory-conditional-access-azure-portal.md)功能（如 Azure 多重身份验证）来帮助保护你的用户帐户的安全。
- 仅在 Office 2010 上可以访问 Exchange 混合环境中的日历共享功能和闲/忙信息。
- 用户登录到_不带_新式身份验证的 Skype for Business 客户端应用程序。
- 用户登录到 PowerShell 版本 1.0。 建议使用 PowerShell 版本 2.0。
- 检测[凭据泄露](../active-directory-reporting-risk-events.md#leaked-credentials)的用户。
- Azure AD 域服务需要在租户上启用密码哈希同步。 因此，_仅_使用传递身份验证的租户不适用于需要 Azure AD 域服务的方案。
- 直通身份验证未与 [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md) 集成。
- 使用 iOS 设置助手的 Apple 设备注册计划 (Apple DEP) 不支持新式身份验证。 此计划将无法使用传递身份验证将 Apple DEP 设备注册到托管域的 Intune。 请考虑使用[公司门户应用](https://blogs.technet.microsoft.com/intunesupport/2018/02/08/support-for-multi-token-dep-and-authentication-with-company-portal/)作为替代方法。

>[!IMPORTANT]
>_仅_作为不支持方案的变通方法，可在 Azure AD Connect 向导中的[可选功能](active-directory-aadconnect-get-started-custom.md#optional-features)页上启用“密码哈希同步”。

>[!NOTE]
此外，启用“密码哈希同步”还可以选择在本地基础结构被破坏的情况下故障转移身份验证。 这种从直通身份验证到 Active Directory 密码哈希同步的故障转移不是自动进行的。 需要使用 Azure AD Connect 手动切换登录方法。 如果运行 Azure AD Connect 的服务器出现故障，则需要 Microsoft 支持部门的帮助以关闭传递身份验证。

## <a name="next-steps"></a>后续步骤
- [快速入门](active-directory-aadconnect-pass-through-authentication-quick-start.md)：快速了解 Azure AD 直通身份验证。
- [智能锁定](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)：了解如何在租户中配置智能锁定功能以保护用户帐户。
- [技术深入了解](active-directory-aadconnect-pass-through-authentication-how-it-works.md)：了解直通身份验证功能的工作原理。
- [常见问题解答](active-directory-aadconnect-pass-through-authentication-faq.md)：查找有关直通身份验证功能的常见问题的解答。
- [故障诊断](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)：了解如何解决直通身份验证功能的常见问题。
- [深入了解安全性](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md)：深入了解有关直通身份验证功能的技术信息。
- [Azure AD 无缝 SSO](active-directory-aadconnect-sso.md)：深入了解此补充功能。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)：使用 Azure Active Directory 论坛来提交新的功能请求。
