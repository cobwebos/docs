---
title: 登录到为 SSO 配置 Azure AD 库应用时出现问题 |Microsoft Docs
description: 如何排查为密码单一登录配置的 Azure AD 库应用程序的问题。
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9fd17d9e066be6a1abff5165436a09b8921184e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381309"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>为 SSO 配置的 Azure AD 库应用的登录问题

访问面板是一个基于 web 的门户。 它使具有 Azure Active Directory (Azure AD) 工作或学校帐户的用户可以访问他们有权访问的基于云的应用程序。 具有 Azure AD 版本的用户还可以通过访问面板使用自助服务组和应用管理功能。

访问面板与 Azure 门户分离。 用户无需 Azure 订阅即可使用访问面板。

若要在访问面板中使用基于密码的单一登录 (SSO), 必须在浏览器中安装访问面板扩展。 当你选择配置为基于密码的 SSO 的应用时, 会自动下载此扩展。

## <a name="browser-requirements-for-access-panel"></a>访问面板的浏览器要求

访问面板需要支持 JavaScript 且已启用 CSS 的浏览器。

以下浏览器支持基于密码的 SSO:

- Windows 7 或更高版本上的 Internet Explorer 8、9、10和11

- Windows 7 或更高版本上或 MacOS X 或更高版本上的 Chrome

- Firefox 26.0 或更高版本的 Windows XP SP2 或更高版本或 Mac OS X 10.6 或更高版本

>[!NOTE]
>当向 Microsoft Edge 添加对浏览器扩展的支持时, 基于密码的 SSO 扩展可供 Windows 10 中的 Microsoft Edge 使用。

## <a name="install-the-access-panel-browser-extension"></a>安装访问面板浏览器扩展

请执行以下步骤：

1. 在支持的浏览器中打开[访问面板](https://myapps.microsoft.com), 并以用户身份登录 Azure AD。

2. 在访问面板中选择启用了密码 SSO 的应用。

3. 出现提示时, 选择 "**立即安装**"。

4. 系统会根据浏览器定向到下载链接。 选择 "**添加**" 以安装浏览器扩展。

5. 如果系统提示, 请选择 "**启用**" 或 "**允许**"。

6. 安装完成后, 重新启动浏览器。

7.  登录到访问面板, 并查看是否可以启动启用了密码 SSO 的应用。

还可以通过以下链接直接下载 Chrome 和 Firefox 的扩展:

-   [Chrome 访问面板扩展](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 访问面板扩展](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>设置 Internet Explorer 的组策略

可以设置组策略, 以便在用户的计算机上远程安装 Internet Explorer 的访问面板扩展。

以下是先决条件:

-   必须设置[Active Directory 域服务](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), 并且用户的计算机必须加入到你的域中。

-   你具有编辑组策略对象 (GPO) 的 "编辑设置" 权限。 默认情况下，以下安全组的成员具有此权限：域管理员、企业管理员和组策略创建者所有者。 [了解详细信息](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)。

若要配置组策略并将其部署到用户, 请参阅[如何使用组策略部署 Internet Explorer 的访问面板扩展](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy)。

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Internet Explorer 中的访问面板疑难解答

若要访问诊断工具和配置扩展的说明, 请参阅对[Internet Explorer 的访问面板扩展进行故障排除](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)。

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>为 Azure AD 库应用配置密码 SSO

若要从 Azure AD 库配置应用, 需要执行以下操作:

-   从 Azure AD 库添加应用程序
-   [将应用程序配置为密码单一登录](#configure-the-app-for-password-sso)
-   [将用户分配到应用](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>从 Azure AD 库添加应用程序

请执行以下步骤：

1. 打开[Azure 门户](https://portal.azure.com), 并以 "全局管理员" 或 "共同管理员" 身份登录。

2. 选择左侧导航窗格顶部的 "**所有服务**" 以打开 Azure AD 扩展。

3. 在筛选器搜索框中键入**Azure Active Directory** , 然后选择 " **Azure Active Directory**"。

4. 从 Azure AD 导航窗格中选择 "**企业应用程序**"。

5. 选择 "**企业应用程序**" 窗格右上角的 "**添加**"。

6. 在 "**从库中添加**" 部分的 "**输入名称**" 框中, 键入应用程序的名称。

7. 选择要为 SSO 配置的应用。

8. *可选：* 添加应用之前, 可以在 "**名称**" 框中更改其名称。

9. 单击 "**添加**" 以添加该应用。

   短暂延迟后, 你将可以看到应用程序的配置窗格。

### <a name="configure-the-app-for-password-sso"></a>为密码 SSO 配置应用

请执行以下步骤：

1. 打开[Azure 门户](https://portal.azure.com/), 并以 "全局管理员" 或 "共同管理员" 身份登录。

2. 选择左侧导航窗格顶部的 "**所有服务**" 以打开 Azure AD 扩展。

3. 在筛选器搜索框中键入**Azure Active Directory** , 然后选择 " **Azure Active Directory**"。

4. 在 Azure AD 导航窗格中选择 "**企业应用程序**"。

5. 选择 "**所有应用程序**" 以查看应用列表。

   > [!NOTE]
   > 如果看不到所需的应用, 请使用 "**所有应用程序" 列表**顶部的 "**筛选器**" 控件。 将 "**显示**" 选项设置为 "所有应用程序"。

6. 选择要为 SSO 配置的应用。

7. 在应用程序加载后, 在应用程序的左侧窗格中选择 "**单一登录**"。

8. 选择 **"基于密码的登录模式"** 。

9. 将用户分配到应用。

10. 你还可以为用户提供凭据。 (否则, 系统会提示用户在应用启动时输入凭据。)为此, 请选择用户的行。 然后选择 "**更新凭据**", 并输入其用户名和密码。

### <a name="assign-users-to-the-app"></a>将用户分配到应用

若要直接将用户分配到应用, 请执行以下步骤:

1. 打开[Azure 门户](https://portal.azure.com/)并以全局管理员身份登录。

2. 在左侧的导航难点中选择 "**所有服务**", 以打开 Azure AD 扩展。

3. 在筛选器搜索框中键入**Azure Active Directory** , 然后选择 " **Azure Active Directory**"。

4. 在 Azure AD 导航窗格中选择 "**企业应用程序**"。

5. 选择 "**所有应用程序**" 以查看应用程序的列表。

   > [!NOTE]
   > 如果看不到所需的应用, 请使用 "**所有应用程序" 列表**顶部的 "**筛选器**" 控件。 将 "**显示**" 选项设置为 "所有应用程序"。

6. 从列表中, 选择要向其分配用户的应用。

7. 加载应用程序后, 在左侧的应用导航窗格中选择 "**用户和组**"。

8. 选择 "**用户和组**" 列表顶部的 "**添加**", 打开 "**添加分配**" 窗格。

9. 在 "**添加分配**" 窗格中选择 "**用户和组**"。

10. 在 "**按名称或电子邮件地址搜索**" 框中, 键入要分配的用户的完整名称或电子邮件地址。

11. 将鼠标悬停在用户的列表中。 选中用户的个人资料照片或徽标旁边的复选框, 以将该用户添加到 "**选定**" 列表。

12. *可选：* 若要添加其他用户, 请在 "**按名称或电子邮件地址搜索**" 框中键入另一个姓名或电子邮件地址, 然后选中该复选框以将该用户添加到 "**选定**" 列表。

13. 选择完用户后, 请单击 "**选择**" 将其添加到分配给该应用的用户和组的列表中。

14. *可选：* 单击 "**添加分配**" 窗格中的 "**选择角色**", 选择要分配给所选用户的角色。

15. 选择 "**分配**", 将应用分配给所选用户。

    短暂延迟后, 用户将能够从访问面板访问这些应用。

## <a name="request-support"></a>请求支持 
如果在设置 SSO 并分配用户时收到错误消息, 请打开支持票证。 尽可能多地包含以下信息:

-   相关错误 ID
-   UPN（用户电子邮件地址）
-   TenantID
-   浏览器类型
-   发生错误时的时区和时间/时间范围
-   Fiddler 跟踪

## <a name="next-steps"></a>后续步骤
[使用应用程序代理为应用提供单一登录](application-proxy-configure-single-sign-on-with-kcd.md)
