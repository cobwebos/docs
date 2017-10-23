---
title: "Azure AD Connect：传递身份验证 - 常见问题 | Microsoft Docs"
description: "有关 Azure Active Directory 传递身份验证的常见问题的解答。"
services: active-directory
keywords: "Azure AD Connect 传递身份验证, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: billmath
ms.openlocfilehash: e0c93cefb135d3ea1c5f0a3797602124aed85c5c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory 传递身份验证：常见问题

本文解决了有关 Azure Active Directory (Azure AD) 传递身份验证的常见问题。 请随时返回查看新内容。

## <a name="which-of-the-azure-ad-sign-in-methods---pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs---should-i-choose"></a>我应选择哪种 Azure AD 登录方法（传递身份验证、密码哈希同步和 Active Directory 联合身份验证服务 (AD FS)）？

这取决于本地环境和组织需求。 请阅读本文来[对比各种 Azure AD 登录方法](active-directory-aadconnect-user-signin.md)。

## <a name="is-pass-through-authentication-a-free-feature"></a>传递身份验证是免费功能吗？

传递身份验证是免费功能，不需要拥有任何付费版本的 Azure AD 即可使用此功能。 功能正式发布时仍然可以免费使用。

## <a name="is-pass-through-authentication-available-in-microsoft-cloud-germanyhttpwwwmicrosoftdecloud-deutschland-and-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>能否在 [Microsoft 云德国](http://www.microsoft.de/cloud-deutschland)和 [Microsoft Azure 政府云](https://azure.microsoft.com/features/gov/)中使用传递身份验证？

否。传递身份验证只能在全球范围内的 Azure AD 实例中使用。

## <a name="does-conditional-accessactive-directory-conditional-accessmd-work-with-pass-through-authentication"></a>是否可以在传递身份验证中使用[条件访问](../active-directory-conditional-access.md)？

可以。包括 Azure 多重身份验证的所有条件访问功能都能在传递身份验证中使用。

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>传递身份验证是否支持使用“备用 ID”（而非“userPrincipalName”）作为其用户名？

是的。 传递身份验证在 Azure AD Connect 中配置时支持作为用户名的 `Alternate ID`，如[此处](active-directory-aadconnect-get-started-custom.md)所示。 并非所有 Office 365 应用程序都支持 `Alternate ID`。 有关支持声明，请参阅特定应用程序文档。

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>密码哈希同步是否可以充当传递身份验证的回滚？

不可以。密码哈希同步并非传递身份验证的通用回滚。 它仅可充当[传递身份验证尚不支持的方案](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios)的回滚。 为避免用户登录失败，应配置传递身份验证以实现[高可用性](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)。

## <a name="can-i-install-an-azure-ad-application-proxyactive-directory-application-proxy-get-startedmd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>能否在传递身份验证代理所在的同一台服务器上安装 [Azure AD 应用程序代理](../active-directory-application-proxy-get-started.md)连接器？

是的，直通身份验证代理的更名版本（版本 1.5.193.0 或更高版本）支持此配置。

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>需要哪些版本的 Azure AD Connect 和传递身份验证代理？

使用该功能需要 Azure AD Connect 1.1.486.0 或更高版本、传递身份验证 1.5.58.0 或更高版本。 所有软件都应安装在运行 Windows Server 2012 R2 或更高版本的服务器上。

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-using-pass-through-authentication"></a>如果用户的密码已过期，但仍尝试使用传递身份验证登录，会发生什么情况？

如果已针对特定的用户配置[密码写回](../active-directory-passwords-update-your-own-password.md)，则当用户使用传递身份验证进行登录时，可更改或重置其密码。 密码会按预期写回到本地 Active Directory。

但是，若没有为特定用户配置密码写回，或者没有为用户分配有效的 Azure AD 许可证，则用户不能在云中更新其密码。 即使密码过期也不能更新。 用户会看到此消息：“组织不允许更新此站点上的密码。 请根据组织建议的方法更新密码，或者请求管理员提供帮助。” 用户或管理员必须在你本地 Active Directory 中重置其密码。

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>传递身份验证如何防止不受密码搜索攻击？

有关详细信息，请阅读[此文](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)。

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>传递身份验证通过端口 80 和 443 传递什么内容？

- 身份验证代理通过端口 443 为所有功能操作发出 HTTPS 请求。
- 传递身份验证通过端口 80 发出 HTTP 请求以下载 SSL 证书吊销列表。

     >[!NOTE]
     >在最近的更新中，我们减少了功能所需的端口数。 如果有较旧版 Azure AD Connect 或身份验证代理，请也打开以下端口：5671、8080、9090、9091、9350、9352 和 10100-10120。

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>传递身份验证代理能否通过出站 Web 代理服务器进行通信？

是的。 如果在本地环境中启用 WPAD (Web Proxy Auto-Discovery)，身份验证代理将自动尝试查找并使用网络中的 Web 代理服务器。

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>能否在同一台服务器上安装两个或更多传递身份验证代理？

否。在一台服务器上只能安装一个传递身份验证代理。 若要配置传递身份验证实现高可用性，请遵循这篇[文章](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)中的说明。

## <a name="i-already-use-active-directory-federation-services-ad-fs-for-azure-ad-sign-in-how-do-i-switch-it-to-pass-through-authentication"></a>已使用 Active Directory 联合身份验证服务 (AD FS) 登录 Azure AD。 如何将它切换为传递身份验证？

若已使用 Azure AD Connect 向导配置 AD FS 作为登录方法，请将用户登录方法更改为传递身份验证。 此更改将在租户中启用传递身份验证，并将“所有”联合域转换为托管域。 传递身份验证将处理租户中所有的后续登录请求。 目前，在 Azure AD Connect 中无法同时使用不同域中的 AD FS 和传递身份验证。

如果在 Azure AD Connect 向导“之外”配置了 AD FS 作为登录方法，请将用户登录方法更改为传递身份验证（从“请勿配置”选项）。 此更改将在租户中启用传递身份验证。 但所有联合域将继续使用 AD FS 进行登录。 使用 PowerShell 将这些联合中的一些或全部转换为托管域。 之后所有托管域上的登录请求都将（“只”）由传递身份验证处理。

>[!IMPORTANT]
>传递身份验证不会处理仅限云的 Azure AD 用户的登录。

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-ad-environment"></a>是否能在多林方案 AD 环境中使用传递身份验证？

是的。 如果 AD 林之间存在信任关系并且正确配置了名称后缀路由，则支持多林环境。

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>需要安装多少个直通身份验证代理？

安装多个直通身份验证代理能保证[高可用性](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)。 但不提供负载均衡。 一个或两个身份验证代理会处理这批登录请求。

请考虑希望在租户上看到的登录请求的峰值和平均负载。 作为基准，单一身份验证代理可在标准的 4 核 CPU、16 GB RAM 服务器上每秒处理 300,000 到 400,000 个身份验证。 对于大多数客户而言，总共两个或三个身份验证代理足以满足高可用性和大容量需求。

推荐在域控制器附近安装身份验证代理以改善延迟。

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>能否在未运行 Azure AD Connect 的服务器上安装第一个传递身份验证代理？

否。不支持该方案。

## <a name="how-many-pass-through-authentication-agents-should-i-install"></a>应安装多少传递身份验证代理？

我们建议：

- 共安装两个或三个身份验证代理。 此配置足够满足大多数客户的需求。
- 可在域控制器上（或尽量靠近它们）安装身份验证代理以改善登录延迟。
- 请确保将服务器（已安装身份验证代理）添加到需要验证密码的用户的 AD 林中。

>[!NOTE]
>系统限制每位租户最多安装 12 个身份验证代理。

## <a name="how-can-i-disable-pass-through-authentication"></a>如何禁用传递身份验证？

返回 Azure AD Connect 向导，将用户登录方法从传递身份验证更改为另一种方法。 此更改将禁用租户上的传递身份验证并从该服务器上卸载身份验证代理。 对于其他服务器中的身份验证代理，必须手动进行卸载。

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>如果卸载传递身份验证代理，会发生什么情况？

从服务器上卸载传递身份验证代理会导致其停止接收登录请求。 执行此操作之前请确保正在运行另一个身份验证代理，以避免租户上的用户登录断开。

## <a name="next-steps"></a>后续步骤
- [当前限制](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - 了解支持和不支持的方案。
- [快速入门](active-directory-aadconnect-pass-through-authentication-quick-start.md) - 快速了解 Azure AD 直通身份验证。
- [**智能锁定**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) - 在租户中配置智能锁定功能以保护用户帐户。
- [深入技术探究](active-directory-aadconnect-pass-through-authentication-how-it-works.md) - 了解此功能如何运作。
- [故障排除](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 了解如何解决使用此功能时遇到的常见问题。
- [**深入了解安全性**](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) - 有关该功能的其他深入技术信息。
- [**Azure AD 无缝 SSO**](active-directory-aadconnect-sso.md) - 深入了解此补充功能。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用于填写新功能请求。
