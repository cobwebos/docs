---
title: 获取我的应用门户-Azure Active Directory 的帮助 |Microsoft Docs
description: 获取与登录到我的应用门户中执行常见任务的帮助。
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e87ae0a8adf28176d9a97cbf1b78943179884a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340104"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>我的应用门户的疑难解答
如果遇到登录或使用的问题**我的应用**门户中，请尝试以下故障排除提示之前联系支持人员或管理员获取帮助。

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>我遇到安装我的应用安全登录扩展
如果遇到安装我的应用安全登录扩展时出现问题：

- 请确保使用支持的浏览器，其中包括：

    - **Microsoft Edge.** 运行 Windows 10 周年纪念版或更高版本。
    - **Google Chrome。** 运行 Windows 7 或更高版本，并在 Mac OS X 或更高版本。
    - **Mozilla Firefox 26.0 或更高版本。** 运行 Windows XP SP2 或更高版本，并在 Mac OS X 10.6 或更高版本。
    - **Internet Explorer 11。** Windows 7 或更高版本 （有限的支持） 上运行。

- 请确保打开浏览器扩展设置。

- 请尝试重新启动你的浏览器和登录到**我的应用**再次门户。

- 请尝试清除浏览器 cookie，然后重新启动并登录到**我的应用**再次门户。

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>我无法登录到**我的应用**门户
如果你遇到登录问题**我的应用**门户中，可以尝试以下：

- 请确保使用正确的 URL。 它应该 https://myapps.microsoft.com或自定义的页面为你的组织，例如 https://myapps.microsoft.com/contoso.com。

- 请确保你的密码正确无误并且尚未过期。 有关详细信息，请参阅[重置工作或学校密码](active-directory-passwords-update-your-own-password.md)。

- 请确保你的验证信息是最新且准确。 有关详细信息，请参阅[什么 Azure 多重身份验证对我而言用途？](multi-factor-authentication-end-user.md)或[更改您的安全信息的方法和信息](security-info-add-update-methods-overview.md)。

- 添加**My App**门户 URL**互联 > 安全 > 受信任的站点**设置。

- 清除浏览器的缓存，然后尝试再次登录。

## <a name="my-password-isnt-working"></a>我的密码不能正常工作
如果你忘记了密码，永远不会收到一个来自你的组织、 锁定你的帐户，或者想要更改你的密码，请参阅[帮助，我忘记了 Azure AD 密码](active-directory-passwords-update-your-own-password.md)。

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>我想要能够重置我自己的密码
要能够重置自己的密码，你的管理员必须首先打开功能为你的组织，，然后你必须更新并验证所需的验证方法。 有关如何更新您的验证方法的详细信息，请参阅[注册自助服务密码重置](active-directory-passwords-reset-register.md)。

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>我收到启动应用时拒绝访问消息
如果您收到了**访问被拒绝**消息后启动应用程序从**My App**门户中，可以尝试以下：

- 请确保已安装[我的应用安全登录扩展](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)和你正在使用[支持的浏览器](my-apps-portal-end-user-access.md#supported-browsers)。

- 请确保为应用程序中，使用正确的 URL 和 URL 位于你**互联 > 安全 > 受信任的站点**列表。

- 请确保你的密码正确无误并且尚未过期。 有关详细信息，请参阅[重置工作或学校密码](active-directory-passwords-update-your-own-password.md)。

- 请确保你的验证信息是最新且准确。 有关详细信息，请参阅[什么 Azure 多重身份验证对我而言用途？](multi-factor-authentication-end-user.md)或[更改您的安全信息的方法和信息](security-info-add-update-methods-overview.md)。

- 清除浏览器的缓存，然后尝试再次登录。

如果尝试这些操作后仍无法访问您的应用程序，必须与你组织的技术支持联系以获得帮助。

## <a name="next-steps"></a>后续步骤
登录到后**我的应用**门户中，您还可以更新你的配置文件和帐户信息、 组信息和访问查看的信息 （如果您具有权限）。

- [访问和我的应用门户上使用应用](my-apps-portal-end-user-access.md)。

- [更改个人资料信息](my-apps-portal-end-user-update-profile.md)。

- [查看和更新你与组相关的信息](my-apps-portal-end-user-groups.md)。

- [执行你自己的访问评审](my-apps-portal-end-user-access-reviews.md)。