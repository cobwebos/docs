---
title: 使用深层链接登录应用程序时出现的问题 | Microsoft Docs
description: 如何对使用 Azure AD 从深层链接 URL 访问应用程序时出现的问题进行故障排除
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
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 3bf357fef2aad85c45abb1fa8e06ff4420a6f14a
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34069904"
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>使用深层链接登录应用程序时出现的问题

访问面板是一个基于 Web 的门户，在 Azure Active Directory (Azure AD) 中拥有工作或学校帐户的用户可以使用它来查看和启动 Azure AD 管理员已授予他们访问权限的基于云的应用程序。 

这些应用程序代表用户在 Azure AD 门户中进行配置。 应用程序必须正确配置并分配到用户或用户所属的组，才能在访问面板中看到应用程序。

深层链接或用户访问 URL 是用户可用来直接从其浏览器 URL 栏访问其密码 - SSO 应用程序的链接。 通过导航到此链接，用户会自动登录到应用程序，而不必先转到访问面板。 此链接与用户从 Office 365 应用程序启动器访问这些应用程序时使用的链接相同。

## <a name="general-issues-to-check-first"></a>首先要检查的常规问题

-   确保使用的**浏览器**能满足访问面板的最低要求。

-   确保用户的浏览器已将应用程序的 URL 添加到其**受信任的站点**。

-   务必确认应用程序**配置**正确。

-   确保用户的帐户**已启用**登录功能。

-   确保用户的帐户**未被锁定**。

-   确保用户的**密码未过期或遗忘**。

-   确保**多重身份验证**未阻止用户访问。

-   确保**条件访问策略**或 **Identity Protection** 策略不会阻止用户访问。

-   确保用户的**身份验证联系信息**是最新的以允许强制执行多重身份验证或条件访问策略。

-   确保同时尝试清除浏览器 Cookie，并尝试重新登录。

## <a name="checking-the-deeplink"></a>检查深层链接

若要检查是否具有正确的深层链接，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

7.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

8.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

9.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

10. 单击“所有应用程序”，查看所有应用程序的列表。

   * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

11. 选择要检查其深层链接的应用程序。

12. 找到“用户访问 URL”标签。 深层链接应与此 URL 匹配。

## <a name="how-to-install-the-access-panel-browser-extension"></a>如何安装访问面板浏览器扩展

若要安装访问面板浏览器扩展，请执行以下步骤：

1.  在某个支持的浏览器中打开[访问面板](https://myapps.microsoft.com)，并在 Azure AD 中以“用户”身份登录。

2.  在访问面板中单击“密码-SSO 应用程序”。

3.  在出现询问是否安装该软件的提示时，选择“立即安装”。

4.  将根据你的浏览器将你定向到下载链接。 将扩展“添加”到浏览器中。

5.  如果浏览器出现提示，选择“启用”或“允许”扩展。

6.  安装完成后，“重启”浏览器会话。

7.  登录到访问面板，并查看是否可以**启动**密码 - SSO 应用程序

也可以通过下面的直接链接下载适用于 Chrome 和 Firefox 的扩展：

-   [Chrome 访问面板扩展](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 访问面板扩展](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>如何配置 Azure AD 库应用程序的密码单一登录

若要从 Azure AD 库配置应用程序，必须：

-   [从 Azure AD 库添加应用程序](#add-an-application-from-the-Azure-AD-gallery)

-   [将应用程序配置为密码单一登录](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>从 Azure AD 库添加应用程序

若要从 Azure AD 库添加应用程序，请执行以下步骤：

1.  打开 [Azure 门户](https://portal.azure.com)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  在“企业应用程序”窗格的右上角，单击“添加”按钮。

6.  在“从库中添加”部分的“输入名称”文本框中，键入应用程序的名称。

7.  选择要配置为单一登录的应用程序。

8.  在添加应用程序前，可以在“名称”文本框中更改名称。

9.  若要添加应用程序，请单击“添加”。

稍等片刻，便可看到应用程序的配置窗格。

### <a name="configure-the-application-for-password-single-sign-on"></a>将应用程序配置为密码单一登录

若要为应用程序配置单一登录，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。

6.  选择要配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  选择“基于密码的登录”模式。

9.  [将用户分配到应用程序](#how-to-assign-a-user-to-an-application-directly)。

10. 此外，还可以通过下列步骤代表用户提供凭据：选择用户对应的行，单击“更新凭据”，并代表用户输入用户名和密码。 否则，会在启动时提示用户输入凭据。

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>如何配置非库应用程序的密码单一登录

若要从 Azure AD 库配置应用程序，必须：

-   [添加非库应用程序](#add-a-non-gallery-application)

-   [将应用程序配置为密码单一登录](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>添加非库应用程序

若要从 Azure AD 库添加应用程序，请执行以下步骤：

1.  打开 [Azure 门户](https://portal.azure.com)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  在“企业应用程序”窗格的右上角，单击“添加”按钮。

6.  单击“非库应用程序”。

7.  在“名称”文本框中输入应用程序的名称。 选择“添加”。

稍等片刻，便可看到应用程序的配置窗格。

### <a name="configure-the-application-for-password-single-sign-on"></a>将应用程序配置为密码单一登录

若要为应用程序配置单一登录，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

    1.  如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。

6.  选择要配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  选择“基于密码的登录”模式。

9.  输入**登录 URL**，这是用户将在其中输入用户名和密码以进行登录的 URL。 确保登录字段在 URL 中可见。

10. 将用户分配到应用程序。

11. 此外，还可以通过下列步骤代表用户提供凭据：选择用户对应的行，单击“更新凭据”，并代表用户输入用户名和密码。 否则，会在启动时提示用户输入凭据。

## <a name="how-to-assign-a-user-to-an-application-directly"></a>如何直接将用户分配到应用程序

若要直接将一个或多个用户分配到应用程序，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  从列表中选择要向其分配用户的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“用户和组”。

8.  单击“用户和组”列表顶部的“添加”按钮，以打开“添加分配”窗格。

9.  在“添加分配”窗格中，单击“用户和组”选择器。

10. 在“按名称或电子邮件地址搜索”搜索框中，键入要分配的用户的**全名**或**电子邮件地址**。

11. 将鼠标悬停在列表中的“用户”上方以显示“复选框”。 若要将用户添加到“已选择”列表，请单击用户个人资料照片或徽标旁边的复选框。

12. **可选：** 如果想要**添加多个用户**，请在“按名称或电子邮件地址搜索”搜索框中，键入其他**全名**或**电子邮件地址**，然后单击复选框以将此用户添加到“已选择”列表。

13. 在完成用户的选择后，单击“选择”按钮将他们添加到要分配给应用程序的用户和组列表。

14. **可选：** 单击“添加分配”窗格中的“选择角色”选择器，选择一个角色来分配给所选用户。

15. 单击“分配”按钮，将应用程序分配给选定用户。

稍等片刻，所选用户就能使用解决方案描述部分所述的方法来启动这些应用程序了。

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>如果这些故障排除步骤未解决此问题。 

打开支持票证，并提供以下信息（如有）：

-   相关错误 ID

-   UPN（用户电子邮件地址）

-   TenantID

-   浏览器类型

-   错误发生的时区和时间/时间段

-   Fiddler 跟踪

## <a name="next-steps"></a>后续步骤
[使用应用程序代理为应用提供单一登录](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
