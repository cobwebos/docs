---
title: 登录到配置为密码 SSO 的 Azure AD 库应用出现问题 |Microsoft Docs
description: 如何为密码单一登录配置的 Azure AD 库应用程序的问题进行疑难解答。
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
ms.openlocfilehash: 742df882fb64e09ff63ef2eceb5514ca070dc227
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190328"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>使用 Azure AD 库应用程序配置 sso 登录问题

访问面板是一个基于 web 的门户。 它使用户获得了 Azure Active Directory (Azure AD) 工作或学校帐户来访问他们具有权限的基于云的应用程序。 自助服务组和应用管理功能，通过访问面板，也可以使用具有 Azure AD 版本的用户。

访问面板是独立于 Azure 门户。 用户不需要 Azure 订阅才能使用访问面板。

若要在访问面板中使用基于密码的单一登录 (SSO)，必须在浏览器中安装访问面板扩展。 当你选择的基于密码的 SSO 配置的应用程序时，扩展将自动下载。

## <a name="browser-requirements-for-access-panel"></a>访问面板浏览器要求

访问面板要求浏览器支持 JavaScript 且已启用 CSS。

以下浏览器支持基于密码的 SSO:

- Internet Explorer 8、 9、 10 和 11 在 Windows 7 或更高版本

- 在 Windows 7 或更高版本或在 MacOS X 或更高版本的 chrome

- Firefox 26.0 或更高版本在 Windows XP SP2 或更高版本或在 Mac OS X 10.6 或更高版本

>[!NOTE]
>基于密码的 SSO 扩展可适用于 Windows 10 中的 Microsoft Edge 时对浏览器扩展已添加到 Microsoft Edge 的支持。

## <a name="install-the-access-panel-browser-extension"></a>安装访问面板浏览器扩展

执行以下步骤:

1. 打开[访问面板](https://myapps.microsoft.com)中支持的浏览器，并为 Azure AD 中的用户登录。

2. 在访问面板中选择启用密码 SSO 应用程序。

3. 系统提示时选择**立即安装**。

4. 你将定向到基于浏览器的下载链接。 选择**添加**安装浏览器扩展。

5. 如果系统提示，请选择**启用**或**允许**。

6. 安装完成后，重新启动你的浏览器。

7.  登录到访问面板和查看是否能够启动你的密码 SSO 已启用的应用。

通过这些链接，可以直接于 Chrome 和 Firefox 下载扩展：

-   [Chrome 访问面板扩展](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 访问面板扩展](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>设置 Internet Explorer 的组策略

您可以设置允许你远程安装访问面板扩展 Internet Explorer 的用户的计算机上的组策略。

以下是系统必备组件：

-   [Active Directory 域服务](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)必须设置，并在用户的计算机必须加入到你的域。

-   具有"编辑设置"权限才能编辑组策略对象 (GPO)。 默认情况下，以下安全组的成员具有此权限：域管理员、企业管理员和组策略创建者所有者。 [了解详细信息](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)。

若要配置组策略并将其部署到用户，请参阅[如何部署使用组策略的 Internet Explorer 访问面板扩展](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy)。

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>对 Internet 资源管理器中的访问面板进行故障排除

若要访问诊断工具和说明来配置扩展，请参阅[Internet explorer 访问面板扩展进行故障排除](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)。

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>配置 Azure AD 库应用程序的密码 SSO

若要配置 Azure AD 库中的应用，需要执行以下操作：

-   从 Azure AD 库中添加应用
-   [配置适用于密码单一登录的应用](#configure-the-app-for-password-sso)
-   [将用户分配到应用程序](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>从 Azure AD 库中添加应用

执行以下步骤:

1. 打开[Azure 门户](https://portal.azure.com)并以全局管理员或共同管理员。

2. 选择**所有服务**左侧和右侧以打开 Azure AD 扩展导航窗格的顶部。

3. 类型**Azure Active Directory**在筛选器搜索框，然后选择**Azure Active Directory**。

4. 选择**企业应用程序**从 Azure AD 的导航窗格。

5. 选择**外**右上角的**企业应用程序**窗格。

6. 在中**从库中的添加**部分中，键入名称中的应用程序**输入一个名称**框。

7. 选择你想要为 SSO 配置的应用。

8. *可选：* 在添加该应用程序之前，您可以更改其名称中的**名称**框。

9. 单击**添加**以添加该应用。

   在短暂延迟后您将能够看到应用的配置窗格。

### <a name="configure-the-app-for-password-sso"></a>配置适用于密码 SSO 的应用

执行以下步骤:

1. 打开[Azure 门户](https://portal.azure.com/)并以全局管理员或共同管理员。

2. 选择**所有服务**左侧和右侧以打开 Azure AD 扩展导航窗格的顶部。

3. 类型**Azure Active Directory**在筛选器搜索框，然后选择**Azure Active Directory**。

4. 选择**企业应用程序**在 Azure AD 的导航窗格中。

5. 选择**所有应用程序**若要查看您的应用程序的列表。

   > [!NOTE]
   > 如果未看到所需的应用，使用**筛选器**顶部的控件**应用程序列表中所有**。 设置**显示**选项为"所有应用程序。"

6. 选择你想要为 SSO 配置的应用。

7. 应用程序加载后，选择**单一登录**在应用程序的左侧窗格中。

8. 选择**基于密码的单一登录**模式。

9. 将用户分配到应用程序。

10. 此外可以为用户提供凭据。 （否则，用户将会提示您在应用启动时输入凭据。）若要执行此操作，选择用户的行。 然后选择**更新凭据**并输入其用户名和密码。

### <a name="assign-users-to-the-app"></a>将用户分配到应用

若要直接将用户分配到应用程序，请执行以下步骤：

1. 打开[Azure 门户](https://portal.azure.com/)并以全局管理员身份登录

2. 选择**所有服务**中左侧和右侧以打开 Azure AD 扩展的导航痛苦。

3. 类型**Azure Active Directory**在筛选器搜索框，然后选择**Azure Active Directory**。

4. 选择**企业应用程序**在 Azure AD 的导航窗格中。

5. 选择**所有应用程序**若要查看应用程序的列表。

   > [!NOTE]
   > 如果未看到所需的应用，使用**筛选器**顶部的控件**应用程序列表中所有**。 设置**显示**选项为"所有应用程序。"

6. 从列表中，选择你想要向其分配用户的应用。

7. 应用程序加载后，选择**用户和组**从左侧和右侧的应用程序的导航窗格。

8. 选择**外**顶部**用户和组**列表以打开**添加分配**窗格。

9. 选择**用户和组**中**添加分配**窗格。

10. 在中**按名称或电子邮件地址搜索**框中，键入完整的名称或你想要分配的用户的电子邮件地址。

11. 鼠标悬停在列表中的用户。 选择用户的个人资料照片或徽标添加到该用户旁边的复选框**选定**列表。

12. *可选：* 若要添加另一个用户，请键入其他名称或电子邮件地址中的**按名称或电子邮件地址搜索**框，并选择复选框以添加到该用户**选定**列表。

13. 完成选择用户后，单击**选择**将它们添加到用户和组分配到应用的列表。

14. *可选：* 单击**选择角色**中**添加分配**窗格选择要分配给所选的用户角色。

15. 选择**分配**要将应用分配到所选用户。

    在短暂延迟后用户将能够从访问面板访问这些应用程序。

## <a name="request-support"></a>请求支持 
如果您设置 SSO，并将用户分配时获取一条错误消息，请打开支持票证。 包括尽可能多的可能与以下信息：

-   相关错误 ID
-   UPN（用户电子邮件地址）
-   TenantID
-   浏览器类型
-   时区和时间/时间范围中出现错误
-   Fiddler 跟踪

## <a name="next-steps"></a>后续步骤
[使用应用程序代理为应用提供单一登录](application-proxy-configure-single-sign-on-with-kcd.md)
