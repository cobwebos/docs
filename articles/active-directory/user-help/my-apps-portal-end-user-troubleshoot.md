---
title: 获取“我的应用”门户的相关帮助 - Azure Active Directory | Microsoft Docs
description: 获得有关在“我的应用”门户中登录和执行常见任务的帮助。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: ed1f06e4747492c43d19a00c1091b2b4d7f7050f
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83741886"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>排查“我的应用”门户的相关问题

如果在登录或使用“我的应用”门户时遇到问题，请尝试运用以下故障排除提示，然后再向支持人员或管理员求助。

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>安装“我的应用安全登录扩展”时遇到问题

如果在安装“我的应用安全登录扩展”时遇到问题：

- 确保使用的是支持的浏览器，其中包括：

    - Microsoft Edge： 在 Windows 10 周年纪念版或更高版本上运行。

    - Google Chrome： 在 Windows 7 或更高版本，以及 Mac OS X 或更高版本上运行。

    - Mozilla Firefox 26.0 或更高版本： 在 Windows XP SP2 或更高版本，以及 Mac OS X 10.6 或更高版本上运行。

    - Internet Explorer 11： 在 Windows 7 或更高版本（支持受限）上运行。

- 确保已打开浏览器扩展设置。

- 尝试重启浏览器，然后重新登录到“我的应用”门户。

- 尝试清除浏览器 Cookie，然后重新登录到“我的应用”门户。

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>我无法登录到“我的应用”门户

如果在登录“我的应用”门户时遇到问题，可以尝试以下操作：

- 确保使用正确的 URL。 它应该为 https://myapps.microsoft.com 或组织的自定义页面，如 https://myapps.microsoft.com/contoso.com 。

- 确保密码正确且尚未过期。 有关详细信息，请参阅[重置工作或学校密码](active-directory-passwords-update-your-own-password.md)。

- 确保验证信息是最新的，且准确无误。 有关详细信息，请参阅 [Azure 多重身份验证对我而言有什么用途？](multi-factor-authentication-end-user.md)或[更改安全信息方法和信息](security-info-add-update-methods-overview.md)。

- 将“我的应用”门户 URL 添加“Internet 属性”>“安全”>“受信任的站点”设置 。

- 清除浏览器缓存，然后重新尝试登录。

## <a name="my-password-isnt-working"></a>我的密码无法使用

如果忘记了密码、从来没有从组织处收到过密码、帐户被锁定，或者需要更改密码，请参阅[请为我提供帮助，我忘记了 Azure AD 密码](active-directory-passwords-update-your-own-password.md)。

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>我想能够重置自己的密码

为了能够重置你自己的密码，管理员必须先为你的组织启用此功能，然后你自己必须更新并验证所需的验证方法。 要详细了解如何更新验证方法，请参阅[注册自助式密码重置](active-directory-passwords-reset-register.md)。

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>启动应用时，我收到“拒绝访问”消息

如果是在从“我的应用”门户启动应用后收到“拒绝访问”消息，则可以尝试以下操作 ：

- 确保已安装[我的应用安全登录扩展](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)并使用[支持的浏览器](my-apps-portal-end-user-access.md#supported-browsers)。

- 确保使用的是正确的应用 URL，且 URL 在“Internet 属性”>“安全”>“受信任的站点”列表中。

- 确保密码正确且尚未过期。 有关详细信息，请参阅[重置工作或学校密码](active-directory-passwords-update-your-own-password.md)。

- 确保验证信息是最新的，且准确无误。 有关详细信息，请参阅 [Azure 多重身份验证对我而言有什么用途？](multi-factor-authentication-end-user.md)或[更改安全信息方法和信息](security-info-add-update-methods-overview.md)。

- 清除浏览器缓存，然后重新尝试登录。

如果尝试这些操作后仍无法访问应用，则必须与组织的支持人员联系以获得帮助。

## <a name="next-steps"></a>后续步骤

登录到“我的应用”门户后，还可以更新配置文件和帐户信息、组信息以及访问评审信息（如有权限）。

- [访问并使用“我的应用”门户上的应用](my-apps-portal-end-user-access.md)。

- [更改个人资料信息](my-apps-portal-end-user-update-profile.md)。

- [查看和更新与组相关的信息](my-apps-portal-end-user-groups.md)。

- [执行自己的访问评审](my-apps-portal-end-user-access-reviews.md)。
