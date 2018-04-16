---
title: 在 Azure Active Directory 中访问和使用“我的应用”门户时获得帮助 | Microsoft Docs
description: 获得有关在访问面板中登录和执行常见任务的帮助。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: 285ee0c4e7f963c372c4f95face73864ba2a4f68
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="troubleshoot-issues-with-accessing-and-using-the-my-apps-portal"></a>排查有关访问和使用“我的应用”门户的问题

如果在登录或使用“我的应用”门户时遇到问题，请尝试运用本文所述的故障排除提示，然后再向支持人员或管理员求助。

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>登录到“我的应用”门户时遇到问题

请尝试运用以下一般提示：

- 首先，检查是否使用了正确的 URL：[https://myapps.microsoft.com](https://myapps.microsoft.com)。
- 尝试将 URL 添加到浏览器的受信任站点。
- 确保密码正确且尚未过期。 有关详细信息，请参阅[重置工作或学校帐户密码](active-directory-passwords-update-your-own-password.md)。
- 确保身份验证联系人信息是最新的，不会妨碍访问。 有关详细信息，请参阅 [Azure 多重身份验证对我而言有什么用途？](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user)
- 尝试清除浏览器 Cookie，然后重新登录。

如果尝试登录时仍然遇到问题，请联系管理员。


## <a name="i-seem-to-be-having-password-issues"></a>我似乎遇到了密码问题

如果忘记了密码、从来没有从 IT 人员处收到过密码、帐户被锁定，或者需要更改密码，请参阅[请为我提供帮助，我忘记了 Azure AD 密码](active-directory-passwords-update-your-own-password.md)。

## <a name="i-need-to-register-for-password-reset"></a>我需要注册密码重置

可以使用自助密码重置 (SSPR) 来重置密码或解锁帐户，而无需求助他人。 在使用此功能之前，必须注册身份验证方法或确认管理员要求的预定义身份验证方法。 有关详细信息，请参阅[注册自助密码重置](active-directory-passwords-reset-register.md)。


## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>安装“我的应用安全登录扩展”时遇到问题

“我的应用”门户要求浏览器支持 JavaScript 并且已启用 CSS。 如果使用基于密码的单一登录应用，还必须安装随附的扩展。 启动为基于密码的单一登录应用配置的应用程序时，会自动下载此扩展。

确保符合以下浏览器要求：
- **Edge**：Windows 10 周年纪念版或更高版本上。
- **Chrome**：在 Windows 7 或更高版本上，以及在 Mac OS X 或更高版本上。
- **Firefox 26.0 或更高版本**：在 Windows XP SP2 或更高版本上，以及在 Mac OS X 10.6 或更高版本上。
- **Internet Explorer 8、9、10、11**：在 Windows 7 或更高版本上（支持受限）。

也可以直接从以下站点下载适用于 Chrome 和 Microsoft Edge 的扩展：

- [Chrome 扩展](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Edge 扩展](https://www.microsoft.com/store/apps/9pc9sckkzk84)

如果已安装扩展但仍遇到问题，请尝试以下解决方法：

- 检查浏览器扩展设置，确保已启用扩展。
- 重启浏览器，并登录到“我的应用”门户。
- 清除浏览器 Cookie，并登录到“我的应用”门户。
- 若要访问诊断工具并获取有关为 Internet Explorer 配置扩展的分步说明，请参阅[排查 Internet Explorer 的访问面板扩展问题](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)。

## <a name="use-the-my-apps-secure-sign-in-extension"></a>使用“我的应用”安全登录扩展
* 如果使用的我的应用 URL 不是 `https://myapps.microsoft.com`，请通过执行以下操作配置默认 URL：
   1. 未登录到该扩展时，请右键单击该扩展图标。
   2. 在菜单上，选择“我的应用 URL”。
   3. 选择默认 URL。
   4. 单击该扩展图标。
   5. 若要登录到该扩展，请选择“登录以开始使用”。

* 若要从浏览器登录直接到应用，请执行以下操作：
   1. 安装该扩展后，通过选择“登录以开始使用”登录到该扩展。
   2. 使用登录 URL 登录到应用。  
       登录 URL 通常是显示登录表单的应用的 URL。
      该扩展的状态应会更改，并告知密码可用。
   3. 若要登录，请选择扩展图标。

* 若要从扩展启动应用，请执行以下操作：
   1. 安装该扩展后，通过选择“登录以开始使用”登录到该扩展。
   2. 选择扩展图标打开其菜单。
   3. 在“我的应用”门户中搜索可用的应用。
   4. 在搜索结果列表中，选择应用。  
       最近使用的三个应用会显示在“最近使用”快捷方式列表中。

> [!NOTE]
> 这些选项仅适用于 Microsoft Edge、Chrome 和 Firefox。

## <a name="how-do-i-add-a-new-app"></a>如何添加新应用？

1.  在“应用”页上，选择“添加应用”。
2.  搜索要添加的应用，然后选择“添加”。

   > [!NOTE]
   > * 仅当管理员为你的帐户启用了此选项的访问权限时，你才可以访问此选项。
   > * 如果应用需要权限，可能要等管理员批准。
   > 

## <a name="how-do-i-manage-my-group-memberships"></a>如何管理组成员身份？

选择“组”磁贴，然后执行以下操作之一： 
* 若要创建组，请在“我拥有的组”下，选择“创建组”，然后遵照说明操作。
* 若要加入组，请在“我加入的组”下，选择“加入组”，然后遵照说明操作。

   > [!NOTE]
   > * 仅当管理员为你的帐户启用了此选项的访问权限时，你才可以访问此选项。
   > * 在所属的组中，可以查看详细信息和退出该组。
   > * 在拥有的组中，可以查看详细信息、添加或删除成员以及退出该组。
   >


## <a name="next-steps"></a>后续步骤

有关更多故障排除信息，请参阅[使用应用程序访问面板网站或移动应用程序时出现问题](active-directory-application-access-panel-content-map.md)。

