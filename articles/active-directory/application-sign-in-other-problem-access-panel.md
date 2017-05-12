---
title: "从访问面板登录应用程序时出现的问题 | Microsoft Docs"
description: "如何对从位于 myapps.microsoft.com 的 Microsoft Azure AD 访问面板中访问应用程序时出现的问题进行故障排除"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: b89569b818b0e2c79934a842fdc428011674c000
ms.contentlocale: zh-cn
ms.lasthandoff: 04/18/2017


---

# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>从访问面板登录应用程序时出现的问题

访问面板是一个基于 Web 的门户，在 Azure Active Directory (Azure AD) 中拥有工作或学校帐户的用户可以使用它来查看和启动 Azure AD 管理员已授予他们访问权限的基于云的应用程序。 

这些应用程序代表用户在 Azure AD 门户中进行配置。 应用程序必须正确配置并分配到用户或用户所属的组，才能在访问面板中看到应用程序。

用户看到的应用类型分为以下几类：

-   Office 365 应用程序

-   使用基于联合的 SSO 配置的 Microsoft 应用程序与第三方应用程序

-   基于密码的 SSO 应用程序

-   使用现有 SSO 解决方案的应用程序

## <a name="general-issues-to-check-first"></a>首先要检查的常规问题

-   确保使用的**浏览器**能满足访问面板的最低要求。

-   确保用户的浏览器已将应用程序的 URL 添加到其**受信任的站点**。

-   务必确认应用程序**配置**正确。

-   确保用户帐户**已启用**进行登录。

-   确保用户帐户**未锁定**。

-   确保用户的**密码未过期或遗忘**。

-   确保**多重身份验证**不会阻止用户访问。

-   确保**条件访问策略**或 **Identity Protection** 策略不会阻止用户访问。

-   确保用户的**身份验证联系信息**是最新信息以允许强制执行多重身份验证或条件访问策略。

-   确保同时尝试清除浏览器 Cookie，并尝试重新登录。

## <a name="meeting-browser-requirements-for-the-access-panel"></a>满足访问面板的浏览器要求

访问面板要求浏览器支持 JavaScript 并且已启用 CSS。 若要在访问面板中使用基于密码的单一登录 (SSO)，必须在用户的浏览器中安装访问面板扩展。 当用户选择某个已配置基于密码的 SSO 的应用程序时，会自动下载此扩展。

对于基于密码的 SSO，最终用户的浏览器可以是：

-   Internet Explorer 8、9、10、11 - 在 Windows 7 或更高版本上

-   Chrome -- 在 Windows 7 或更高版本上，以及在 MacOS X 或更高版本上

-   Firefox 26.0 或更高版本 -- 在 Windows XP SP2 或更高版本上，以及在 Mac OS X 10.6 或更高版本上

**注意**：如果浏览器扩展支持 Edge，则基于密码的 SSO 扩展可供 Windows 10 中的 Edge 使用。

## <a name="how-to-install-the-access-panel-browser-extension"></a>如何安装访问面板浏览器扩展

若要安装访问面板浏览器扩展，请按照以下步骤操作：

1.  在某个支持的浏览器中打开[访问面板](https://myapps.microsoft.com)，并在 Azure AD 中以“用户”身份登录。

2.  单击访问面板中的“密码 - SSO 应用程序”。

3.  在出现询问是否安装该软件的提示时，选择“立即安装”。

4.  根据浏览器的情况，用户将定向到下载链接。 将扩展“添加”到浏览器中。

5.  如果浏览器出现提示，选择“启用”或“允许”扩展。

6.  安装完成后，“重启”浏览器会话。

7.  登录到访问面板，并查看是否可以**启动**密码 - SSO 应用程序

也可以通过下面的直接链接下载适用于 Chrome 和 Firefox 的扩展：

-   [Chrome 访问面板扩展](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 访问面板扩展](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>如何配置 Azure AD 库应用程序的联合单一登录

使用企业单一登录功能启用的 Azure AD 库中的所有应用程序都具有可用的分步教程。 可以访问[有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)，了解分步指南的详细信息。

若要从 Azure AD 库配置应用程序，需要：

-   [从 Azure AD 库添加应用程序](#add-an-application)

-   [在 Azure AD 中配置应用程序的元数据值（登录 URL、标识符、回复 URL）](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [选择用户标识符，并添加要发送给应用程序的用户属性](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [检索 Azure AD 元数据和证书](#download-the-azure-ad-metadata-or-certificate)

-   [在应用程序中配置 Azure AD 元数据值（登录 URL、颁发者、注销 URL 和证书）](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [将用户分配到应用程序](#assign-users-to-the-application)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>从 Azure AD 库添加应用程序

若要从 Azure AD 库添加应用程序，请按照以下步骤操作：

1.  打开 [Azure 门户](https://portal.azure.com)，并以“全局管理员”或“共同管理员”身份登录

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“企业应用程序”边栏选项卡右上角的“添加”按钮

6.  在“从库中添加”部分的“输入名称”文本框中，键入应用程序的名称。

7.  选择要配置为单一登录的应用程序。

8.  在添加应用程序前，可以在“名称”文本框中更改名称。

9.  单击“添加”按钮，添加该应用程序。

稍等片刻，便能看到应用程序的配置边栏选项卡。

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>为 Azure AD 库中的应用程序配置单一登录

若要为应用程序配置单一登录，请按照以下步骤操作：

1.  <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  选择要配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  从“模式”下拉列表中选择“基于 SAML 的登录”。

9.  在“域和 URL”中输入所需的值。 应从应用程序供应商处获取这些值。

   1. 若要将应用程序配置为 SP 启动的 SSO，“登录 URL”是必需值。 对于某些应用程序，“标识符”也是必需值。

   2. 若要将应用程序配置为 IdP 启动的 SSO，“回复 URL”是必需值。 对于某些应用程序，“标识符”也是必需值。

10. **可选：**如果要查看非必需值，请单击“显示高级 URL 设置”。

11. 在“用户属性”的“用户标识符”下拉列表中，为用户选择唯一标识符。

12. **可选：**单击“查看和编辑其他所有用户属性”，以编辑在用户登录时要发送至应用程序（位于 SAML 令牌中）的属性。

   若要添加属性：

   1. 单击“添加属性”。 输入“名称”，并从下拉列表中选择“值”。

   2. 单击“保存”。 然后就能在表中看到新属性了。

13. 单击“配置 &lt;应用程序名称&gt;”以访问关于了解如何在应用程序中配置单一登录的文档。 此外，用户还拥有通过应用程序设置 SSO 所需的元数据 URL 和证书。

14. 单击“保存”以保存配置。

15. 将用户分配到应用程序。

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>选择用户标识符，并添加要发送给应用程序的用户属性

若要选择用户标识符或添加用户属性，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  选择已配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  在“用户属性”部分的“用户标识符”下拉列表中，为用户选择唯一标识符。 所选项需与应用程序中的期望值匹配，以便对用户进行身份验证。

    >[!NOTE]
    >Azure AD 会根据所选值或 SAML AuthRequest 中应用程序要求的格式，为 NameID 属性（用户标识符）选择格式。 有关详细信息，请参阅 NameIDPolicy 部分下的[单一登录 SAML 协议](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest)文章。
    >
    >

9.  若要添加用户属性，请单击“查看和编辑所有其他用户属性”，以编辑在用户登录时要发送至应用程序（位于 SAML 令牌中）的属性。

   若要添加属性：

   1. 单击“添加属性”。 输入“名称”，并从下拉列表中选择“值”。

   2. 单击“保存”。 然后就能在表中看到新属性了。

### <a name="download-the-azure-ad-metadata-or-certificate"></a>下载 Azure AD 元数据或证书

若要从 Azure AD 下载应用程序元数据或证书，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  选择已配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  转到“SAML 签名证书”部分，然后单击“下载”列值。 根据应用程序对于配置单一登录的要求，将看到下载元数据 XML 或下载证书选项。

    Azure AD 不提供用于获取元数据的 URL。 元数据只能作为 XML 文件进行检索。

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>如何配置非库应用程序的联合单一登录

若要配置非库应用程序，需要拥有 Azure AD Premium 和支持 SAML 2.0 的应用程序。 有关 Azure AD 版本的详细信息，请访问 [Azure AD 定价](https://azure.microsoft.com/pricing/details/active-directory/)。

-   [在 Azure AD 中配置应用程序的元数据值（登录 URL、标识符、回复 URL）](#configuring-single-sign-on)

-   [选择用户标识符，并添加要发送给应用程序的用户属性](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [检索 Azure AD 元数据和证书](#download-the-azure-ad-metadata-or-certificate)

-   [在应用程序中配置 Azure AD 元数据值（登录 URL、颁发者、注销 URL 和证书）](#configuring-single-sign-on)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>在 Azure AD 中配置应用程序的元数据值（登录 URL、标识符、回复 URL）

若要为不在 Azure AD 库中的应用程序配置单一登录，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“企业应用程序”边栏选项卡右上角的“添加”按钮

6.  在“添加自己的应用”部分中，单击“非库应用程序”

7.  在“名称”文本框中输入应用程序的名称。

8.  单击“添加”按钮，添加该应用程序。

9.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

10. 在“模式”下拉列表中选择“基于 SAML 的登录”

11. 在“域和 URL”中输入所需的值。 应从应用程序供应商处获取这些值。

  1. 若要将应用程序配置为 IdP 启动的 SSO，请输入“回复 URL”和“标识符”。

  2. **可选：**若要将应用程序配置为 SP 启动的 SSO，“登录 URL”是必需值。

12. 在“用户属性”的“用户标识符”下拉列表中，为用户选择唯一标识符。

13. **可选：**单击“查看和编辑其他所有用户属性”，以编辑在用户登录时要发送至应用程序（位于 SAML 令牌中）的属性。

   若要添加属性：

   1. 单击“添加属性”。 输入“名称”，并从下拉列表中选择“值”。

   2. 单击“保存”。 然后就能在表中看到新属性了。

14. 单击“配置 &lt;应用程序名称&gt;”以访问关于了解如何在应用程序中配置单一登录的文档。 此外，还具有应用程序要求的 Azure AD URL 和证书。

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>选择用户标识符，并添加要发送给应用程序的用户属性

若要选择用户标识符或添加用户属性，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  选择已配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  在“用户属性”部分的“用户标识符”下拉列表中，为用户选择唯一标识符。 所选项需与应用程序中的期望值匹配，以便对用户进行身份验证。

   >[!NOTE]
   >Azure AD 会根据所选值或 SAML AuthRequest 中应用程序要求的格式，为 NameID 属性（用户标识符）选择格式。 有关详细信息，请参阅 NameIDPolicy 部分下的[单一登录 SAML 协议](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest)文章。
   >
   >

9.  若要添加用户属性，请单击“查看和编辑所有其他用户属性”，以编辑在用户登录时要发送至应用程序（位于 SAML 令牌中）的属性。

   若要添加属性：

   1.单击“添加属性”。 输入“名称”，并从下拉列表中选择“值”。

   2 单击“保存”。 然后就能在表中看到新属性了。

### <a name="download-the-azure-ad-metadata-or-certificate"></a>下载 Azure AD 元数据或证书

若要从 Azure AD 下载应用程序元数据或证书，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

   * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  选择已配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  转到“SAML 签名证书”部分，然后单击“下载”列值。 根据应用程序对于配置单一登录的要求，将看到下载元数据 XML 或下载证书选项。

    Azure AD 不提供用于获取元数据的 URL。 元数据只能作为 XML 文件进行检索。

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>如何配置 Azure AD 库应用程序的密码单一登录

若要从 Azure AD 库配置应用程序，需要：

-   [从 Azure AD 库添加应用程序](#add-an-application)

-   [将应用程序配置为密码单一登录](#configure-the-application)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>从 Azure AD 库添加应用程序

若要从 Azure AD 库添加应用程序，请按照以下步骤操作：

1.  打开 [Azure 门户](https://portal.azure.com)，并以“全局管理员”或“共同管理员”身份登录

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“企业应用程序”边栏选项卡右上角的“添加”按钮

6.  在“从库中添加”部分的“输入名称”文本框中，键入应用程序的名称

7.  选择要配置为单一登录的应用程序

8.  在添加应用程序前，可以在“名称”文本框中更改名称。

9.  单击“添加”按钮，添加该应用程序。

稍等片刻，便能看到应用程序的配置边栏选项卡。

### <a name="configure-the-application-for-password-single-sign-on"></a>将应用程序配置为密码单一登录

若要为应用程序配置单一登录，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

 * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  选择要配置单一登录的应用程序

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  选择“基于密码的登录”模式。

9.  将用户分配到应用程序。

10. 此外，还可以通过以下方式代表用户提供凭据：选择用户对应的行，单击“更新凭据”，然后代表用户输入用户名和密码。 否则，会在启动时提示用户输入凭据。

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>如何配置非库应用程序的密码单一登录

若要从 Azure AD 库配置应用程序，需要：

-   [添加非库应用程序](#add-a-non-gallery-application)

-   [将应用程序配置为密码单一登录](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>添加非库应用程序

若要从 Azure AD 库添加应用程序，请按照以下步骤操作：

1.  打开 [Azure 门户](https://portal.azure.com)，并以“全局管理员”或“共同管理员”身份登录

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“企业应用程序”边栏选项卡右上角的“添加”按钮

6.  单击“非库应用程序”。

7.  在“名称”文本框中输入应用程序的名称。 选择“添加”。

稍等片刻，便能看到应用程序的配置边栏选项卡。

### <a name="configure-the-application-for-password-single-sign-on"></a>将应用程序配置为密码单一登录

若要为应用程序配置单一登录，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

 * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  选择要配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  选择“基于密码的登录”模式。

9.  输入“登录 URL”。 这就是用户在其中输入用户名和密码进行登录时的 URL。 确保登录字段在 URL 中可见。

10. 将用户分配到应用程序。

11. 此外，还可以通过以下方式代表用户提供凭据：选择用户对应的行，单击“更新凭据”，然后代表用户输入用户名和密码。 否则，会在启动时提示用户输入凭据。

## <a name="how-to-assign-a-user-to-an-application-directly"></a>如何直接将用户分配到应用程序

若要直接将一个或多个用户分配到应用程序，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  从列表中选择要向其分配用户的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“用户和组”。

8.  单击“用户和组”列表顶部的“添加”按钮，打开“添加分配”边栏选项卡。

9.  在“添加分配”边栏选项卡中，单击“用户和组”选择器。

10. 在“按名称或电子邮件地址搜索”搜索框中，键入要分配的用户的**全名**或**电子邮件地址**。

11. 将鼠标悬停在列表中的“用户”上方以显示“复选框”。 单击用户个人资料头像或徽标旁边的复选框，将用户添加到“已选择”列表。

12. **可选：**如果想要**添加多个用户**，请在“按名称或电子邮件地址搜索”搜索框中，键入其他**全名**或**电子邮件地址**，然后单击复选框以将此用户添加到“已选择”列表。

13. 在完成用户的选择后，单击“选择”按钮将他们添加到要分配给应用程序的用户和组列表。

14. **可选：**单击“添加分配”边栏选项卡中的“选择角色”选择器以选择要分配给所选用户的角色。

15. 单击“分配”按钮将应用程序分配给所选用户。

稍等片刻，所选用户就能使用解决方案描述部分所述的方法来启动这些应用程序了。

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>如果这些故障排除步骤未解决此问题

打开支持票证并提供以下信息（如果有）：

-   相关错误 ID

-   UPN（用户电子邮件地址）

-   TenantID

-   浏览器类型

-   错误发生时的时区和时间/时间范围

-   Fiddler 跟踪

## <a name="next-steps"></a>后续步骤
[使用应用程序代理为应用提供单一登录](active-directory-application-proxy-sso-using-kcd.md)


