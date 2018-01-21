---
title: "安装应用程序访问面板浏览器扩展时遇到问题 | Microsoft Docs"
description: "如何修复安装访问面板浏览器扩展时遇到的常见错误"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: 26dc5d5ffce84206450123132c0633c2aa323e9f
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2018
---
# <a name="problem-installing-the-application-access-panel-browser-extension"></a>安装应用程序访问面板浏览器扩展时遇到问题

访问面板是一个基于 Web 的门户，在 Azure Active Directory (Azure AD) 中拥有工作或学校帐户的用户可以使用它来查看和启动 Azure AD 管理员已授权他们访问的基于云的应用程序。 拥有 Azure AD 版本的用户还可以通过访问面板使用自助服务组和应用管理功能。 访问面板不同于 Azure 门户，它不要求用户拥有 Azure 订阅。

若要在访问面板中使用基于密码的单一登录 (SSO)，必须在用户的浏览器中安装访问面板扩展。 当用户选择某个已配置基于密码的 SSO 的应用程序时，会自动下载此扩展。

## <a name="meeting-browser-requirements-for-the-access-panel"></a>满足访问面板的浏览器要求

访问面板要求浏览器支持 JavaScript 并且已启用 CSS。 若要在访问面板中使用基于密码的单一登录 (SSO)，必须在用户的浏览器中安装访问面板扩展。 当用户选择某个已配置基于密码的 SSO 的应用程序时，会自动下载此扩展。

对于基于密码的 SSO，最终用户的浏览器可以是：

-   Windows 10 周年纪念版或更高版本上的 Edge 

-   Chrome -- 在 Windows 7 或更高版本上，以及在 MacOS X 或更高版本上

-   Firefox 26.0 或更高版本 -- 在 Windows XP SP2 或更高版本上，以及在 Mac OS X 10.6 或更高版本上

-   Internet Explorer 8、9、10、11 - 在 Windows 7 或更高版本上（支持受限）
## <a name="how-to-install-the-access-panel-browser-extension"></a>如何安装访问面板浏览器扩展

若要安装访问面板浏览器扩展，请按照以下步骤操作：

1.  在某个支持的浏览器中打开[访问面板](https://myapps.microsoft.com)，并在 Azure AD 中以“用户”身份登录。

2.  在访问面板中单击“密码-SSO 应用程序”。

3.  在出现询问是否安装该软件的提示时，选择“立即安装”。

4.  根据浏览器的情况，用户将定向到下载链接。 将扩展“添加”到浏览器中。

5.  如果浏览器出现提示，选择“启用”或“允许”扩展。

6.  安装完成后，“重启”浏览器会话。

7.  登录到访问面板，并查看是否可以**启动**密码 - SSO 应用程序

也可通过下面的直接链接下载适用于 Chrome 和 Edge 的扩展：

-   [Chrome 访问面板扩展](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Edge 访问面板扩展](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

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
3. 该扩展应更改状态并让你知道密码可用，请单击**扩展图标**以登录。

从扩展启动应用
1. 安装该扩展后，通过选择“登录以开始使用”登录到该扩展。
2. 单击扩展图标以打开其**菜单**。
3. 在“我的应用”门户中**搜索**可用的应用。
4. 从“搜索结果”中单击该应用以启动它。
5. 最近启动的三个应用也会显示在“最近使用”快捷方式列表中

> [!NOTE]
> 这些选项仅适用于 Edge、Chrome、Firefox。

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>设置 Internet Explorer 的组策略

可以设置组策略，以便在用户的计算机上远程安装 Internet Explorer 的访问面板扩展。

先决条件包括：

-   已设置 [Active Directory 域服务](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)，并且已将用户的计算机加入域。

-   必须拥有“编辑设置”权限才能编辑组策略对象 (GPO)。 默认情况下，以下安全组的成员拥有此权限：域管理员、企业管理员和组策略创建者和所有者。 [了解详细信息](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)。

有关如何配置组策略并将其部署到用户的分步说明，请按照教程[如何使用组策略部署 Internet Explorer 的访问面板扩展](active-directory-saas-ie-group-policy.md)操作。

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>对 Internet Explorer 中的访问面板扩展进行故障排除

若要访问诊断工具以及获得为 IE 配置扩展的分步说明，请按照[对 Internet Explorer 的访问面板进行故障排除](active-directory-saas-ie-troubleshooting.md)指南进行操作。

> [!NOTE]
> IE 仅受到有限支持，且不再接收新的软件更新。 Edge 是建议使用的浏览器。

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>如果这些故障排除步骤未解决此问题

打开支持票证，并提供以下信息（如有）：

-   相关错误 ID

-   UPN（用户电子邮件地址）

-   TenantID

-   浏览器类型

-   错误发生的时区和时间/时间段

-   Fiddler 跟踪

## <a name="next-steps"></a>后续步骤
[什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)
