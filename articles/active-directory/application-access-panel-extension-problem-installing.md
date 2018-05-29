---
title: 安装应用程序访问面板浏览器扩展 - Azure | Microsoft Docs
description: 修复安装访问面板浏览器扩展时遇到的常见错误。
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/4/18
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: 8db0c28b0ea6b6f78ef86c773ff24f9d22fe3ecd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34353868"
---
# <a name="install-the-access-panel-browser-extension"></a>安装访问面板浏览器扩展

访问面板是一个基于 Web 的门户。 如果在Azure Active Directory (Azure AD) 中拥有工作或学校帐户，可以使用访问面板来查看和启动 Azure AD 管理员已授权你访问的基于云的应用程序。 

如果使用 Azure AD 版本，还可以通过访问面板使用自助服务组和应用管理功能。 

访问面板与 Azure 门户不同， 它不需要你拥有 Azure 订阅。

## <a name="web-browser-requirements"></a>Web 浏览器要求

访问面板的最低要求是使用支持 JavaScript 且已启用 CSS 的浏览器。 若要在访问面板中通过基于密码的 SSO 登录到应用程序，必须已在浏览器中安装访问面板扩展。 选择某个已配置基于密码的 SSO 的应用程序时，会自动下载此扩展。

对于基于密码的 SSO，可以使用以下任一浏览器：

- **Edge**：在 Windows 10 周年纪念版或更高版本上。 
- **Chrome**：在 Windows 7 或更高版本上，以及在 MacOS X 或更高版本上。
- **Firefox 26.0 或更高版本**：在 Windows XP SP2 或更高版本上，以及在 Mac OS X 10.6 或更高版本上。

## <a name="install-the-access-panel-browser-extension"></a>安装访问面板浏览器扩展

若要安装访问面板浏览器扩展，请执行以下操作：

1.  在某个支持的浏览器中打开[访问面板](https://myapps.microsoft.com)，然后以 Azure AD 帐户中某个用户身份登录。

2.  选择基于密码的 SSO 应用程序。

3.  出现提示时，选择“立即安装”。  
    你将被定向到所选浏览器的下载链接。 
    
4.  选择 **添加** 。

5.  如果出现提示，请**启用**或**允许**扩展。

6.  安装完毕后，重新启动浏览器。

7.  登录到访问面板，并检查是否可以启动基于密码的 SSO 应用程序。

也可以直接从以下站点下载适用于 Chrome 和 Microsoft Edge 的扩展：

- [Chrome 扩展](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Edge 扩展](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

## <a name="use-the-my-apps-secure-sign-in-extension"></a>使用我的应用安全登录扩展
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
       
* 若要在进行远程操作时使用内部公司 URL，请执行以下操作：
    1. 在租户上[配置应用程序代理](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-enable)
    2. 通过应用程序代理[发布应用程序](https://docs.microsoft.com/en-us/azure/active-directory/application-proxy-publish-azure-portal)和 URL
    3. 安装该扩展，并通过选择“登录以开始使用”登录到该扩展
    4. 现在，即使在执行远程操作时也可以浏览内部公司 URL

> [!NOTE]
> 上述选项仅适用于 Microsoft Edge、 Chrome 和 Firefox。

## <a name="set-up-a-group-policy-for-internet-explorer"></a>设置 Internet Explorer 的组策略

可以设置组策略，以便在用户的计算机上远程安装 Internet Explorer 的访问面板扩展。

设置组策略之前，请确保：

-   已设置 [Active Directory 域服务](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)，并且已将用户的计算机加入域。

-   若要编辑组策略对象 (GPO)，必须具有“编辑设置”权限。 默认情况下，向以下安全组的成员授予此权限：域管理员、企业管理员和组策略创建者和所有者。

有关如何配置组策略并将其部署到用户的分步说明，请参阅[使用组策略部署 Internet Explorer 的访问面板扩展](active-directory-saas-ie-group-policy.md)。

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>对 Internet Explorer 中的访问面板扩展进行故障排除

若要访问诊断工具以及获得有关配置 Internet Explorer 扩展的信息，请参阅[对 Internet Explorer 的访问面板扩展进行故障排除](active-directory-saas-ie-troubleshooting.md)。

> [!NOTE]
> Internet Explorer 仅受到有限支持，且不再接收新的软件更新。 Microsoft Edge 是建议使用的浏览器。

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>如果上述步骤无法解决问题，

请使用以下信息（如果有）开具支持票证：

-   相关错误 ID
-   UPN（用户电子邮件地址）
-   TenantID
-   浏览器类型
-   发生错误的时区以及时间或时间范围
-   Fiddler 跟踪

## <a name="next-steps"></a>后续步骤
[什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)
