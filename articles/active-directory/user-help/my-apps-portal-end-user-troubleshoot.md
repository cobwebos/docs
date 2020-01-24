---
title: 获取有关 "我的应用" 门户的帮助-Azure Active Directory |Microsoft Docs
description: 获取有关在 "我的应用" 门户中登录和执行常见任务的帮助。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 2987183900ad3232d46b2152bd52808ae96c802b
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704827"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>"我的应用" 门户问题疑难解答

如果在登录或使用 "**我的应用**" 门户时遇到问题，请在联系支持人员或管理员寻求帮助之前，先尝试这些故障排除技巧。

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>安装 "我的应用" 安全登录扩展时遇到问题

如果在安装 "我的应用" 安全登录扩展时遇到问题：

- 请确保使用的是受支持的浏览器，包括：

    - **Microsoft Edge.** 在 Windows 10 周年纪念版或更高版本上运行。

    - **Google Chrome。** 在 Windows 7 或更高版本上运行，在 Mac OS X 或更高版本上运行。

    - **Mozilla Firefox 26.0 或更高版本。** 在 Windows XP SP2 或更高版本上运行，在 Mac OS X 10.6 或更高版本上运行。

    - **Internet Explorer 11。** 在 Windows 7 或更高版本上运行（支持受限）。

- 确保浏览器扩展设置已打开。

- 尝试重新启动浏览器，并再次登录到 "**我的应用**" 门户。

- 尝试清除浏览器的 cookie，并重新启动并再次登录到 "**我的应用**" 门户。

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>我无法登录到 "**我的应用**" 门户

如果在登录 "**我的应用**" 门户时遇到问题，可以尝试以下操作：

- 请确保使用正确的 URL。 它应 https://myapps.microsoft.com 或组织的自定义页面，如 https://myapps.microsoft.com/contoso.com 。

- 请确保密码正确且未过期。 有关详细信息，请参阅[重置工作或学校密码](active-directory-passwords-update-your-own-password.md)。

- 请确保您的验证信息是最新的，并且是准确的。 有关详细信息，请参阅[Azure 多重身份验证对我意味着什么？](multi-factor-authentication-end-user.md)或[更改安全信息方法和信息](security-info-add-update-methods-overview.md)。

- 将**我的应用**门户 URL 添加到**Internet 属性 > 安全性 > 受信任的站点**"设置。

- 清除浏览器的缓存，然后重试登录。

## <a name="my-password-isnt-working"></a>我的密码不起作用

如果你忘记了密码，从未收到过你的组织中的密码，或者需要更改密码，请参阅[帮助，我忘记了我的 Azure AD 密码](active-directory-passwords-update-your-own-password.md)。

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>我希望能够重置自己的密码

要重置你自己的密码，你的管理员必须首先为你的组织启用此功能，然后你必须更新并验证所需的验证方法。 有关如何更新验证方法的详细信息，请参阅[注册自助服务密码重置](active-directory-passwords-reset-register.md)。

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>启动应用时，收到 "拒绝访问" 消息

如果从 "**我的应用**" 门户启动应用后收到 "**拒绝访问**" 消息，则可以尝试以下操作：

- 请确保已安装 "[我的应用" 安全登录扩展](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)，并且你使用的是[受支持的浏览器](my-apps-portal-end-user-access.md#supported-browsers)。

- 请确保使用正确的应用 URL，并确保 URL 位于**Internet 属性上 > 安全 > 受信任的站点**列表。

- 请确保密码正确且未过期。 有关详细信息，请参阅[重置工作或学校密码](active-directory-passwords-update-your-own-password.md)。

- 请确保您的验证信息是最新的，并且是准确的。 有关详细信息，请参阅[Azure 多重身份验证对我意味着什么？](multi-factor-authentication-end-user.md)或[更改安全信息方法和信息](security-info-add-update-methods-overview.md)。

- 清除浏览器的缓存，然后重试登录。

如果尝试这些东西后仍无法访问你的应用，则必须与组织的技术支持人员联系以获得帮助。

## <a name="next-steps"></a>后续步骤

登录到 "**我的应用**" 门户之后，你还可以更新你的配置文件和帐户信息、你的组信息和访问评审信息（如果你有权限）。

- [访问和使用 "我的应用" 门户上的应用](my-apps-portal-end-user-access.md)。

- [更改你的配置文件信息](my-apps-portal-end-user-update-profile.md)。

- [查看和更新组相关的信息](my-apps-portal-end-user-groups.md)。

- [执行你自己的访问评审](my-apps-portal-end-user-access-reviews.md)。
