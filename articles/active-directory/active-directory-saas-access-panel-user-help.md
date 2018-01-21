---
title: "使用 Azure Active Directory 中的“我的应用”门户时是否需要帮助 | Microsoft Docs"
description: "获取使用访问面板执行常见任务的说明。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: 9bec51e1d49308baecc76143ec80902d2da418e8
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
---
# <a name="do-you-need-help-with-the-my-apps-portal"></a>使用“我的应用”门户时是否需要帮助？

用户访问此页面可能是因为在使用“我的应用”门户时遇到了问题。 虽然有些情况需要联系支持人员或管理员才能解决问题，但在此之前，可以参考以下故障排除主题获得帮助。

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>登录到“我的应用”门户时遇到问题

要检查的常规问题：

- 查看是否登录到正确的 URL：[https://myapps.microsoft.com](https://myapps.microsoft.com)

- 尝试将 URL 添加到浏览器的受信任站点。

- 确保密码未过期或遗忘。 有关如何更新密码的更多详细信息，请查看[此处](active-directory-passwords-update-your-own-password.md)。

- 查看身份验证联系信息是否为最新且不会阻止用户访问门户。 有关设置身份验证信息的更多详细信息，请查看[此处](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user)。

- 尝试清除浏览器 Cookie，然后重新登录。

如果尝试登录时仍然遇到问题，请联系管理员获得更多帮助。


## <a name="how-do-i-update-my-password"></a>如何更新密码？

如果忘记了密码、从来没有从 IT 人员处收到过密码、帐户被锁定，或者需要更改密码，请参阅[请为我提供帮助，我忘记了 Azure AD 密码](active-directory-passwords-update-your-own-password.md)获取更多详细信息。

## <a name="how-do-i-register-for-password-reset"></a>如何注册密码重置？

作为最终用户，可以使用自助密码重置 (SSPR) 来重置密码或解锁帐户，而不需求助他人。 在使用此功能之前，必须注册身份验证方法或确认管理员填充的预定义身份验证方法。 有关更多详细信息，请参阅[注册自助密码重置](active-directory-passwords-reset-register.md)。


## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>安装“我的应用安全登录扩展”时遇到问题

查看是否满足浏览器要求：

- 门户要求浏览器支持 JavaScript 并且已启用 CSS。 如果使用基于密码的单一登录应用，还必须安装随附的扩展。 启动为基于密码的单一登录应用配置的应用程序时，会自动下载此扩展。

- 扩展的浏览器要求如下：
    - Windows 10 周年纪念版或更高版本上的 Edge
    - Windows 7 或更高版本以及 MacOS X 或更高版本上的 Chrome
    - Windows XP SP2 或更高版本以及 Mac OS X 10.6 或更高版本上的 Firefox 26.0 或更高版本
    - Windows 7 或更高版本上的 Internet Explorer 8、9、10、11（支持受限）

也可以通过下面的直接链接下载适用于 Chrome 和 Edge 的扩展：

- [Chrome 扩展](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

- [Edge 扩展](https://www.microsoft.com/store/apps/9pc9sckkzk84)

安装后如果遇到问题，请尝试执行以下步骤：

- 查看浏览器扩展设置，确保已启用扩展。

- 重新启动浏览器，登录到“我的应用”门户。

- 清除浏览器 Cookie，登录到“我的应用”门户。
- 若要访问诊断工具以及获得为 IE 配置扩展的分步说明，请按照[对 Internet Explorer 的访问面板扩展进行故障排除](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)指南进行操作。

## <a name="how-do-i-use-the-my-apps-secure-sign-in-extension"></a>如何使用我的应用安全登录扩展？
更改该扩展的我的应用默认 URL

如果使用的我的应用 URL 不是 https://myapps.microsoft.com，则必须通过以下步骤配置默认 URL：
1. 未登录到该扩展时，请**右键单击**该扩展图标。
2. 从菜单中单击“选择我的应用 URL”。
3. **选择**默认 URL。
4. 单击该扩展图标。
5. 通过选择“登录以开始使用”登录到该扩展。

从浏览器直接登录到应用中
1. 安装该扩展后，通过选择“登录以开始使用”登录到该扩展。
2. 导航到要登录到的应用的**登录 URL**，这通常是显示登录表单的应用的 URL。
3. 该扩展应更改状态并让你知道密码可用，请单击**扩展图标**以登录

从扩展启动应用
1. 安装该扩展后，通过选择“登录以开始使用”登录到该扩展。
2. 单击扩展图标以打开其菜单。
3. 在“我的应用”门户中**搜索**可用的应用。
4. 从“搜索结果”中单击该应用以启动它。
5. 最近启动的三个应用也会显示在“最近使用”快捷方式列表中

> [!NOTE]
> 这些选项仅适用于 Edge、Chrome、Firefox。

## <a name="how-do-i-add-a-new-app"></a>如何添加新应用？

1.  在“应用”页上，单击“添加应用”。

2.  搜索要添加的应用，然后单击“添加”。

**备注：**

- 仅当管理员为用户的帐户启用此选项访问权限的情况下，用户才能进行相应的访问。

- 如果应用需要权限，可能要等管理员批准。


## <a name="how-do-i-manage-my-group-memberships"></a>如何管理组成员身份？

1. 单击“组”磁贴。 
2. 若要创建组，请在“我拥有的组”下，单击“创建组”，然后按照说明操作。
3. 若要加入组，请在“我加入的组”下，单击“加入组”，然后按照说明操作。

**备注：**

- 仅当管理员为用户的帐户启用此选项访问权限的情况下，用户才能进行相应的访问。

- 在所属的组中，可查看详细信息并退出组。

- 在拥有的组中，可查看详细信息、添加或删除成员以及退出组。


## <a name="next-steps"></a>后续步骤

有关信息方面的疑难解答，请参阅[使用应用程序访问面板网站或移动应用程序时出现问题](active-directory-application-access-panel-content-map.md)

