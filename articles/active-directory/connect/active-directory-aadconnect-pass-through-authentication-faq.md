---
title: Azure AD Connect：传递身份验证 - 常见问题 | Microsoft Docs
description: 有关 Azure Active Directory 直通身份验证的常见问题的解答
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
ms.date: 01/04/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 8363c49c4a52785fb5deacb3ac4998d38aca1430
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593872"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory 传递身份验证：常见问题

本文解决了有关 Azure Active Directory (Azure AD) 直通身份验证的常见问题。 请随时返回查看更新内容。

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>我应选择哪种 Azure AD 登录方法（直通身份验证、密码哈希同步和 Active Directory 联合身份验证服务 (AD FS)）？

这取决于本地环境和组织需求。 请阅读 [Azure AD Connect 用户登录选项](active-directory-aadconnect-user-signin.md)文章来对比各种 Azure AD 登录方法。

## <a name="is-pass-through-authentication-a-free-feature"></a>传递身份验证是免费功能吗？

直通身份验证是一项免费功能。 不需要拥有任何付费版本的 Azure AD 即可使用。

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>能否在 [Microsoft Azure 德国云](http://www.microsoft.de/cloud-deutschland)和 [Microsoft Azure 政府云](https://azure.microsoft.com/features/gov/)中使用直通身份验证？

不会。 直通身份验证只能在全球范围内的 Azure AD 实例中使用。

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>是否可以在直通身份验证中使用[条件访问](../active-directory-conditional-access-azure-portal.md)？

是的。 包括 Azure 多重身份验证的所有条件访问功能都能在直通身份验证中使用。

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>传递身份验证是否支持使用“备用 ID”（而非“userPrincipalName”）作为其用户名？

是的。 直通身份验证在 Azure AD Connect 中配置时支持作为用户名的 `Alternate ID`。 有关详细信息，请参阅 [Azure AD Connect 的自定义安装](active-directory-aadconnect-get-started-custom.md)。 并非所有 Office 365 应用程序都支持 `Alternate ID`。 请参阅特定应用程序的文档支持声明。

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>密码哈希同步是否可以充当直通身份验证的回退？

不会。 直通身份验证不自动故障转移到密码哈希同步。 它仅可充当[传递身份验证尚不支持的方案](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios)的回滚。 为避免用户登录失败，应配置传递身份验证以实现[高可用性](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)。

## <a name="can-i-install-an-azure-ad-application-proxymanage-appsapplication-proxymd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>能否在传递身份验证代理所在的同一台服务器上安装 [Azure AD 应用程序代理](../manage-apps/application-proxy.md)连接器？

是的。 直通身份验证代理的更名版本（版本 1.5.193.0 或更高版本）支持此配置。

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>需要哪些版本的 Azure AD Connect 和传递身份验证代理？

使用该功能需要 Azure AD Connect 1.1.486.0 或更高版本、直通身份验证 1.5.58.0 或更高版本。 所有软件都应安装在 Windows Server 2012 R2 或更高版本的服务器上。

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>如果用户的密码已过期，但仍尝试使用直通身份验证登录，会发生什么情况？

如果已针对特定的用户配置[密码写回](../active-directory-passwords-update-your-own-password.md)，则当用户使用直通身份验证进行登录时，可更改或重置其密码。 密码会按预期写回到本地 Active Directory。

若没有为特定用户配置密码写回，或者没有为用户分配有效的 Azure AD 许可证，则用户不能在云中更新其密码。 即使密码过期也不能更新。 用户会看到此消息：“组织不允许更新此站点上的密码。 请根据组织建议的方法更新密码，或者请求管理员提供帮助。” 用户或管理员必须在本地 Active Directory 中重置其密码。

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>直通身份验证如何防止不受密码搜索攻击？

请参阅 [Azure Active Directory 直通身份验证：智能锁定](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)以了解详细信息。

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>传递身份验证通过端口 80 和 443 传递什么内容？

- 身份验证代理通过端口 443 为所有功能操作发出 HTTPS 请求。
- 直通身份验证通过端口 80 发出 HTTP 请求，以下载 SSL 证书吊销列表 (CRL)。

     >[!NOTE]
     >最近的更新减少了功能所需的端口数。 如果有较旧版 Azure AD Connect 或身份验证代理，也请打开以下端口：5671、8080、9090、9091、9350、9352 和 10100-10120。

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>传递身份验证代理能否通过出站 Web 代理服务器进行通信？

是的。 如果在本地环境中启用 Web 代理自动发现 (WPAD)，身份验证代理将自动尝试查找并使用网络中的 Web 代理服务器。

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>能否在同一台服务器上安装两个或更多传递身份验证代理？

否。在一台服务器上只能安装一个传递身份验证代理。 若要配置直通身份验证实现高可用性，请遵循 [Azure Active Directory 直通身份验证：快速入门](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)中的说明。

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>如何删除直通身份验证代理？

只要直通身份验证正在运行，它就会保持活动状态，并不断处理用户登录请求。 如果要卸载身份验证代理，请转到“控制面板”->“程序”->“程序和功能”，卸载“Microsoft Azure AD Connect 身份验证代理”和“Microsoft Azure AD Connect 代理更新程序”程序。

如果在完成上述步骤后查看 [Azure Active Directory 管理中心](https://aad.portal.azure.com)的“直通身份验证”边栏选项卡，将看到显示为“非活动”状态的身份验证代理。 这是正常情况。 该身份验证代理在几天后将自动从列表中删除。

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>我已使用 AD FS 登录到 Azure AD。 如何将它切换为传递身份验证？

若已使用 Azure AD Connect 向导配置 AD FS 作为登录方法，请将用户登录所使用的方法更改为直通身份验证。 此更改将在租户中启用直通身份验证，并将所有联合域转换为托管域。 直通身份验证将处理所有登录到租户的后续请求。 目前，在 Azure AD Connect 中无法同时使用不同域中的 AD FS 和传递身份验证。

若已将 AD FS 配置为在 Azure AD Connect 向导外部登录的方法，请将用户登录方法更改为直通身份验证。 可以从“请勿配置”选项中进行此更改。 此更改将在租户中启用直通身份验证，但所有联合域将继续使用 AD FS 进行登录。 使用 PowerShell 将其中的一些或全部联合域手动转换为托管域。 进行更改后，只有直通身份验证处理登录到托管域的所有请求。

>[!IMPORTANT]
>直通身份验证不会处理仅限云的 Azure AD 用户的登录。

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>是否能在多林 Active Directory 环境中使用直通身份验证？

是的。 如果 Active Directory 林之间存在林信任关系并且正确配置了名称后缀路由，则支持多林环境。

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>需要安装多少个直通身份验证代理？

安装多个直通身份验证代理能保证[高可用性](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)。 但它不提供身份验证代理之间的确定性负载均衡。

请考虑希望在租户上看到的登录请求的峰值和平均负载。 作为基准，单一身份验证代理可在标准的 4 核 CPU、16 GB RAM 服务器上每秒处理 300 到 400 个身份验证。

若要估算网络流量，请使用以下大小调整指南：
- 每个请求的有效负载大小为 (0.5K + 1K * num_of_agents) 个字节，即 Azure AD 至身份验证代理间的数据。 此处，“num_of_agents”表示在租户上注册的身份验证代理的数量。
- 每个响应的有效负载大小为 1K 字节，即身份验证代理至 Azure AD 间的数据。

对于大多数客户而言，总共两个或三个身份验证代理足以满足高可用性和大容量需求。 应在域控制器附近安装身份验证代理以改善登录延迟。

>[!NOTE]
>系统限制每位租户最多安装 12 个身份验证代理。

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>能否在未运行 Azure AD Connect 的服务器上安装第一个传递身份验证代理？

否。不支持该方案。

## <a name="how-can-i-disable-pass-through-authentication"></a>如何禁用传递身份验证？

返回 Azure AD Connect 向导，将用户登录方法从传递身份验证更改为另一种方法。 此更改将禁用租户上的传递身份验证并从该服务器上卸载身份验证代理。 必须从其他服务器手动卸载身份验证代理。

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>如果卸载传递身份验证代理，会发生什么情况？

如果从服务器上卸载直通身份验证代理，会导致服务器停止接受登录请求。 若要避免中断租户上的用户登录功能，请确保在卸载直通身份验证代理前有其他正在运行的身份验证代理。

## <a name="next-steps"></a>后续步骤
- [当前限制](active-directory-aadconnect-pass-through-authentication-current-limitations.md)：了解支持和不支持的方案。
- [快速入门](active-directory-aadconnect-pass-through-authentication-quick-start.md)：快速了解 Azure AD 直通身份验证。
- [智能锁定](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)：了解如何在租户中配置智能锁定功能以保护用户帐户。
- [技术深入了解](active-directory-aadconnect-pass-through-authentication-how-it-works.md)：了解直通身份验证功能的工作原理。
- [故障诊断](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)：了解如何解决直通身份验证功能的常见问题。
- [深入了解安全性](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md)：深入了解有关直通身份验证功能的技术信息。
- [Azure AD 无缝 SSO](active-directory-aadconnect-sso.md)：深入了解此补充功能。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)：使用 Azure Active Directory 论坛来提交新的功能请求。

