---
title: "登录到访问面板网站时出现问题 | Microsoft Docs"
description: "指导解决尝试登录以使用访问面板时可能会遇到的问题"
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
ms.date: 07/11/2017
ms.author: asteen
ms.reviwer: japere
ms.openlocfilehash: 0f50a705d4453970d039c2219aef8e1a411fd533
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>登录到访问面板网站时出现问题

访问面板是一个基于 Web 的门户，在 Azure Active Directory (Azure AD) 中拥有工作或学校帐户的用户可以使用它来查看和启动 Azure AD 管理员已授权他们访问的基于云的应用程序。 拥有 Azure AD 版本的用户还可以通过访问面板使用自助服务组和应用管理功能。 访问面板不同于 Azure 门户，它不要求用户拥有 Azure 订阅。

如果用户在 Azure AD 中拥有工作或学校帐户，则可以登录到访问面板。

-   用户可以直接通过 Azure AD 进行身份验证。

-   用户可以使用 Active Directory  联合身份验证服务 (AD FS) 进行身份验证。

-   用户可以通过 Windows Server Active Directory 进行身份验证。

如果用户具有 Azure 或 Office 365 订阅并且已在使用 Azure 门户或 Office 365 应用程序，则能够在无需再次登录的情况下无缝地使用访问面板。 系统会提示未进行身份验证的用户使用其在 Azure AD 中的帐户的用户名和密码进行登录。 如果组织已配置联合，只需键入用户名即可。

## <a name="general-issues-to-check-first"></a>首先要检查的常规问题 

-   确保用户尝试登录到**正确的 URL**：<https://myapps.microsoft.com>

-   确保用户的浏览器已将该 URL 添加到其**受信任的站点**

-   确保用户的帐户**已启用**登录功能。

-   确保用户的帐户**未被锁定**。

-   确保用户的**密码未过期或遗忘**。

-   确保**多重身份验证**未阻止用户访问。

-   确保**条件访问策略**或 **Identity Protection** 策略不会阻止用户访问。

-   确保用户的**身份验证联系信息**是最新的以允许强制执行多重身份验证或条件访问策略。

-   同时还务必尝试清除浏览器 Cookie，并尝试重新登录。

## <a name="meeting-browser-requirements-for-the-access-panel"></a>满足访问面板的浏览器要求

访问面板要求浏览器支持 JavaScript 并且已启用 CSS。 若要在访问面板中使用基于密码的单一登录 (SSO)，必须在用户的浏览器中安装访问面板扩展。 当用户选择某个已配置基于密码的 SSO 的应用程序时，会自动下载此扩展。

对于基于密码的 SSO，最终用户的浏览器可以是：

-   Internet Explorer 8、9、10、11 - 在 Windows 7 或更高版本上

-   Windows 10 周年纪念版或更高版本上的 Edge 

-   Chrome -- 在 Windows 7 或更高版本上，以及在 MacOS X 或更高版本上

-   Firefox 26.0 或更高版本 -- 在 Windows XP SP2 或更高版本上，以及在 Mac OS X 10.6 或更高版本上


## <a name="problems-with-the-users-account"></a>用户帐户存在的问题

对访问面板的访问可能会由于用户帐户存在的问题而被阻止。 下面是可以用来排除和解决用户及其帐户设置存在的问题的一些方法：

-   [检查在 Azure Active Directory 中是否存在用户帐户](#check-if-a-user-account-exists-in-azure-active-directory)

-   [检查用户帐户的状态](#check-a-users-account-status)

-   [重置用户的密码](#reset-a-users-password)

-   [启用自助服务密码重置](#enable-self-service-password-reset)

-   [检查用户的多重身份验证状态](#check-a-users-multi-factor-authentication-status)

-   [检查用户的身份验证联系信息](#check-a-users-authentication-contact-info)

-   [检查用户的组成员身份](#check-a-users-group-memberships)

-   [检查用户的已分配许可证](#check-a-users-assigned-licenses)

-   [为用户分配许可证](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>检查在 Azure Active Directory 中是否存在用户帐户

若要检查是否存在用户帐户，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  检查用户对象的属性，确保它们看上去与预期一致并且未丢失任何数据。

### <a name="check-a-users-account-status"></a>检查用户帐户的状态

若要检查用户的帐户状态，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  单击“配置文件”。

8.  在“设置”下，确保“阻止登录”设置为“否”。

### <a name="reset-a-users-password"></a>重置用户的密码

若要重置用户的密码，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  单击用户边栏选项卡顶部的“重置密码”按钮。

8.  在出现的“重置密码”边栏选项卡上，单击“重置密码”按钮。

9.  为用户复制**临时密码**或**输入新密码**。

10. 告知用户此新密码，在他们下一次登录到 Azure Active Directory 时，需要更改此密码。

### <a name="enable-self-service-password-reset"></a>启用自助服务密码重置

若要启用自助服务密码重置，请执行以下部署步骤：

-   [使用户能够重置其 Azure Active Directory 密码](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-their-azure-ad-passwords)

-   [使用户能够重置或更改其 Active Directory 本地密码](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords)

### <a name="check-a-users-multi-factor-authentication-status"></a>检查用户的多重身份验证状态

若要检查用户的多重身份验证状态，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  单击边栏选项卡顶部的“多重身份验证”按钮。

7.  在**多重身份验证管理门户**加载后，确保位于“用户”选项卡上。

8.  通过搜索、筛选或排序在用户列表中找到用户。

9.  从用户列表中选择用户，并根据需要**启用**、**禁用**或**强制实施**多重身份验证。

   >[!NOTE]
   >如果用户处于**已强制实施**状态，可暂时将其设置为**已禁用**以允许用户重新登录到其帐户。 一旦他们重新登录到其帐户，便可以再次将其状态更改为**已启用**来要求他们在下次登录期间重新注册其联系信息。 此外，也可以按照[检查用户的身份验证联系信息](#check-a-users-authentication-contact-info)中的步骤为其验证或设置此数据。
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>检查用户的身份验证联系信息

若要检查用于多重身份验证、条件访问、Identity Protection 和密码重置的用户身份验证联系信息，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  单击“配置文件”。

8.  向下滚动到“身份验证联系信息”。

9.  **查看**为用户注册的数据并根据需要进行更新。

### <a name="check-a-users-group-memberships"></a>检查用户的组成员身份

若要检查用户的组成员身份，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  单击“组”查看用户所属的组。

### <a name="check-a-users-assigned-licenses"></a>检查用户的已分配许可证

若要检查用户的已分配许可证，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  单击“许可证”查看当前已分配给用户的许可证。

### <a name="assign-a-user-a-license"></a>为用户分配许可证 

要将许可证分配给用户，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  单击“许可证”查看当前已分配给用户的许可证。

8.  单击“分配”按钮。

9.  从可用产品列表中选择**一个或多个产品**。

10. **可选**单击“分配选项”项精确分配产品。 完成此操作后，单击“确定”。

11. 单击“分配”按钮，将这些许可证分配给此用户。

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>如果这些故障排除步骤未解决此问题

打开支持票证，并提供以下信息（如有）：

-   相关错误 ID

-   UPN（用户电子邮件地址）

-   租户 ID

-   浏览器类型

-   错误发生的时区和时间/时间段

-   Fiddler 跟踪

## <a name="next-steps"></a>后续步骤
[使用应用程序代理为应用提供单一登录](active-directory-application-proxy-sso-using-kcd.md)
