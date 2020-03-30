---
title: 获取有关"我的应用"门户的帮助 - Azure 活动目录*微软文档
description: 获取有关在"我的应用"门户中登录和执行常见任务的帮助。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: f72dd5595d67ae989cec5681d22def9a2f929adf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253151"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>解决"我的应用"门户的问题

如果您在登录或使用 **"我的应用"** 门户时遇到问题，请尝试这些疑难解答提示，然后再联系帮助台或管理员寻求帮助。

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>安装"我的应用安全登录扩展"时遇到问题

如果您在安装"我的应用安全登录扩展"时遇到问题：

- 确保您使用的是受支持的浏览器，包括：

    - **微软边缘。** 在 Windows 10 周年版或更高版本上运行。

    - **谷歌浏览器。** 在 Windows 7 或更高版本上运行，在 Mac OS X 或更高版本中运行。

    - **莫齐拉火狐26.0或更高版本。** 在 Windows XP SP2 或更高版本上运行，在 Mac OS X 10.6 或更高版本中运行。

    - **互联网浏览器11。** 在 Windows 7 或更高版本上运行（有限支持）。

- 确保浏览器扩展设置已打开。

- 请尝试重新启动浏览器，然后再次登录到 **"我的应用"** 门户。

- 请尝试清除浏览器的 Cookie，然后重新启动并再次登录 **"我的应用"** 门户。

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>无法登录到 **"我的应用"** 门户

如果您在登录 **"我的应用"** 门户时遇到问题，可以尝试以下操作：

- 确保您使用的是正确的 URL。 它应该是https://myapps.microsoft.com或组织的自定义页面，例如https://myapps.microsoft.com/contoso.com。

- 请确保您的密码正确且未过期。 有关详细信息，请参阅[重置工作或学校密码](active-directory-passwords-update-your-own-password.md)。

- 确保您的验证信息是最新且准确的。 有关详细信息，请参阅[Azure 多重身份验证对我意味着什么？](multi-factor-authentication-end-user.md)或[更改安全信息方法和信息](security-info-add-update-methods-overview.md)。

- 将 **"我的应用门户**URL"添加到**Internet 属性>安全>受信任的网站**设置。

- 清除浏览器的缓存并尝试重新登录。

## <a name="my-password-isnt-working"></a>我的密码不起作用

如果您忘记了密码、从未从组织收到密码、帐户被锁定或想要更改密码，请参阅["帮助"，我忘记了 Azure AD 密码](active-directory-passwords-update-your-own-password.md)。

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>我希望能够重置自己的密码

为了能够重置自己的密码，管理员必须首先打开组织的功能，然后必须更新和验证所需的验证方法。 有关如何更新验证方法的详细信息，请参阅[注册自助服务密码重置](active-directory-passwords-reset-register.md)。

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>启动应用时，我收到"访问被拒绝"消息

如果您在从 **"我的应用"** 门户启动应用后收到 **"访问被拒绝**"消息，则可以尝试以下操作：

- 请确保您已安装["我的应用安全登录扩展"，](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)并且您使用的是[受支持的浏览器](my-apps-portal-end-user-access.md#supported-browsers)。

- 请确保您对应用使用正确的 URL，并且该 URL 位于**Internet 属性>安全>可信网站**列表中。

- 请确保您的密码正确且未过期。 有关详细信息，请参阅[重置工作或学校密码](active-directory-passwords-update-your-own-password.md)。

- 确保您的验证信息是最新且准确的。 有关详细信息，请参阅[Azure 多重身份验证对我意味着什么？](multi-factor-authentication-end-user.md)或[更改安全信息方法和信息](security-info-add-update-methods-overview.md)。

- 清除浏览器的缓存并尝试重新登录。

如果在尝试了这些操作后仍无法访问你的应用，则必须与组织的帮助台联系以获得帮助。

## <a name="next-steps"></a>后续步骤

登录 **"我的应用"** 门户后，您还可以更新个人资料和帐户信息、群组信息和访问审阅信息（如果您具有权限）。

- [访问和使用"我的应用"门户上的应用](my-apps-portal-end-user-access.md)。

- [更改您的个人资料信息](my-apps-portal-end-user-update-profile.md)。

- [查看和更新与组相关的信息](my-apps-portal-end-user-groups.md)。

- [执行您自己的访问评论](my-apps-portal-end-user-access-reviews.md)。
