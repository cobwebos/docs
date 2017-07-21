---
title: "访问面板简介 | Microsoft 文档"
description: "了解如何使用各种形式的访问面板（Web 浏览器、Android 应用、iPhone 和 iPad 应用）来访问 SaaS 应用。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: c5a0f3f958d42d514a95477c1e39f02c78307d4d
ms.contentlocale: zh-cn
ms.lasthandoff: 04/06/2017

---
# <a name="what-is-the-access-panel"></a>访问面板是什么？
访问面板是一个基于 Web 的门户。 在Azure Active Directory (Azure AD) 中拥有工作或学校帐户的用户可以使用访问面板来查看和启动 Azure AD 管理员已授权他们访问的基于云的应用程序。 拥有 Azure AD 版本的用户还可以通过访问面板使用自助组管理功能。

访问面板不同于 Azure 门户，它不要求用户拥有 Azure 订阅。

![访问面板][1]

访问面板可让用户编辑一些配置文件设置，包括：

* 更改工作或学校帐户关联的密码。
* 编辑密码重置设置。
* 编辑与多重身份验证相关的联系人和首选项设置（适用于管理员要求使用访问面板执行此类操作的帐户）。
* 查看帐户详细信息，例如，用户 ID、备用电子邮件地址、移动电话和办公电话号码。
* 查看和启动 Azure AD 管理员已授权访问的基于云的应用程序。 有关从用户角度阐述的访问面板详细信息，请参阅 [Using the Access Panel](https://msdn.microsoft.com/library/azure/dn756411.aspx)（使用访问面板）。
* 自我管理组。 更具体地说，管理员可以在 Azure AD 中创建和管理安全组，并请求安全组成员身份。 有关详细信息，请参阅 [Self-service group management for users in Azure AD](active-directory-accessmanagement-self-service-group-management.md)（面向 Azure AD 中用户的自助组管理）和 [Manage your groups](active-directory-manage-groups.md)（管理组）。

## <a name="accessing-the-access-panel"></a>访问访问面板
用户可以通过在 Web 浏览器中浏览以下 URL 来访问访问面板： <br>
**http://myapps.microsoft.com**

如果为登录页配置了自定义商标，则默认情况下，可以将组织的域追加到 URL 来尾来加载此商标： <br>
**http://myapps.microsoft.com/contosobuild.com**

在此情况下，可以使用已在 Azure 门户中目录的“域”选项卡下面配置的任何活动域名或已验证域名，如以下屏幕截图中所示：

![Wingtip Toys 域名][2]  

必须将此 URL 分发给要登录到与 Azure AD 集成的应用程序的所有用户。

## <a name="authentication"></a>身份验证
若要访问访问面板，用户必须通过 Azure AD 中的工作或学校帐户进行身份验证。 用户可以直接在 Azure AD 中进行身份验证。 或者，如果组织已使用 Active Directory 联合身份验证服务 (AD FS) 或其他技术配置联合，则用户可以通过 Windows Server Active Directory 进行身份验证。

如果用户具有 Azure 或 Office 365 订阅并一直在使用 Azure 门户或 Office 365 应用程序，则会看到应用程序列表，而无需再次登录。 系统将提示未经身份验证的用户在 Azure AD 中使用其帐户的用户名和密码登录。 如果组织已配置联合，只需键入用户名即可。

身份验证后，用户可与管理员已集成到目录的应用程序交互。 若要了解如何将应用程序与 Azure AD 集成，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

## <a name="web-browser-requirements"></a>Web 浏览器要求
访问面板的最低要求是使用支持 JavaScript 且已启用 CSS 的浏览器。 为了让用户通过基于密码的单一登录 (SSO) 登录到应用程序，必须在用户的浏览器中安装“访问面板”扩展。 当用户选择某个已配置基于密码的 SSO 的应用程序时，会自动下载此扩展。

“访问面板”扩展目前适用于 Internet Explorer 8 和更高版本、Microsoft Edge、Chrome 及 Firefox 浏览器。

## <a name="mobile-app-support"></a>移动应用支持
Azure Active Directory 团队发布了“我的应用”移动应用。 用户安装此应用后，可以在 iOS 和 Android 设备上登录到基于密码的 SSO 应用程序。

> [!NOTE]
> 用户几乎可以在任何设备上的任何 Web 浏览器中登录到支持与 Azure AD 联合的应用程序（包括 Salesforce、Google Apps、Dropbox、Box、Concur、Workday、Office 365 和其他 70 种以上的应用程序），无需安装任何插件或移动应用。 此外，无需在移动设备上使用“我的应用”移动应用，就能[体验访问面板](https://myapps.microsoft.com/)的其他功能。
>
>

### <a name="my-apps-for-android"></a>我的应用 Android 版
运行 Android 4.1 和更高版本的任何 Android 设备都支持“我的应用 Android 版”。 [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.myapps) 中现已提供此应用。

![我的应用 Android 版屏幕][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>我的应用 iPhone 和 iPad 版
运行 iOS 7 和更高版本的任何 iPhone 或 iPad 设备都支持“我的应用 iOS 版”。 Apple App Store 中现已提供此应用。

![我的应用 iOS 版屏幕][4]    

## <a name="tips-for-testing-the-user-experience"></a>有关测试用户体验的提示
如果你是 Azure 管理员并已使用目录中的帐户登录到 Azure 门户，将以当前管理员帐户自动登录到访问面板。 在此情况下，可以看到已分配给此帐户的所有应用程序。

以*不同的*用户帐户测试：

1. 单击 Azure 门户或访问面板右上角的用户菜单，然后选择“注销”。 随后你将从 Azure AD 注销。
2. 转到[访问面板](http://myapps.microsoft.com)。
3. 在登录页中，输入目录中想要测试的帐户的用户名和密码。

## <a name="starting-applications"></a>启动应用程序
访问面板中可以显示多种类型的应用程序。

### <a name="office-365-applications"></a>Office 365 应用程序
如果组织正在使用 Office 365 应用程序并且用户拥有相应的许可证，Office 365 应用程序将显示在用户的访问面板上。

当用户单击 Office 365 应用程序的应用程序磁贴时，将重定向到该应用程序并自动登录。

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>使用基于联合的 SSO 配置的 Microsoft 应用程序与第三方应用程序
管理员可以在 SSO 模式设置为“Azure AD 单一登录”的情况下，将应用程序添加到 Azure 门户的“Active Directory”部分中。 仅当管理员已向用户显式授予这些应用程序的访问权限时，该用户才能看到这些应用程序。

当用户单击其中一个应用程序的磁贴时，将重定向到该应用程序并自动登录。

### <a name="password-based-sso-without-identity-provisioning"></a>没有标识预配的基于密码的 SSO
管理员可以在 SSO 模式设置为“基于密码的单一登录”的情况下，将应用程序添加到 Azure 门户的“Active Directory”部分中。 目录中的所有用户都可看到在此模式下配置的所有应用程序。

当用户首次单击其中一个应用程序的磁贴时，系统会提示他们安装适用于 Internet Explorer 或 Chrome 的密码 SSO 插件。 安装后可能需要用户重新启动其 Web 浏览器。 当用户返回访问面板并再次单击应用程序磁贴时，系统会提示他们输入应用程序的用户名和密码。 用户输入用户名和密码后，这些凭据将安全存储在 Azure AD 中并链接到 Azure AD 中的用户帐户。

下次用户单击应用程序磁贴时，将自动登录到该应用程序。 用户无需再次输入凭据，也无需再次安装密码 SSO 插件。

如果用户的凭据在第三方目标应用程序中已更改，则用户也必须更新 Azure AD 中存储的相应凭据。 若要更新凭据，用户需要选择应用程序磁贴中的图标，然后选择“更新凭据”来重新输入该应用程序的用户名和密码。

### <a name="password-based-sso-with-identity-provisioning"></a>有标识预配的基于密码的 SSO
管理员可以在 SSO 模式设置为“基于密码的单一登录”的情况下，将应用程序连同标记预配一起添加到 Azure 门户的“Active Directory”部分中。

当用户首次单击其中一个应用程序的应用程序磁贴时，系统会提示他们安装适用于 Internet Explorer 或 Chrome 的密码 SSO 插件。 安装后可能需要用户重新启动其 Web 浏览器。 当用户返回访问面板并单击应用程序磁贴时，将会自动登录到应用程序。

某些应用程序可能要求用户在首次登录时更改密码。 如果用户的凭据在第三方目标应用程序中已更改，则用户也必须更新 Azure AD 中存储的相应凭据。 若要更新凭据，用户需要选择应用程序磁贴中的图标，然后选择“更新凭据”来重新输入该应用程序的用户名和密码。

### <a name="application-with-existing-sso-solutions"></a>包含现有 SSO 解决方案的应用程序
Azure 门户提供了第三个选项（“现有的单一登录”）用于配置应用程序的 SSO。 管理员可以使用此选项创建应用程序的链接，并将其放置在所选用户的访问面板上。

例如，如果某个应用程序配置为使用 AD FS 2.0 验证用户身份，则管理员可以使用“现有的单一登录”选项在访问面板上创建该应用程序的链接。 当用户访问该链接时，将通过 AD FS 2.0 或者由该应用程序提供的任何现有 SSO 解决方案进行身份验证。

## <a name="related-articles"></a>相关文章
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [有关如何集成 SaaS 应用的教程列表](active-directory-saas-tutorial-list.md)
* [使用 Azure Active Directory 进行单一登录和应用访问权限管理简介](active-directory-appssoaccess-whatis.md)
* [在 SaaS 应用程序中自动预配和取消预配用户](active-directory-saas-app-provisioning.md)

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/ic767166.png
[2]: ./media/active-directory-saas-access-panel-introduction/ic767167.png
[3]: ./media/active-directory-saas-access-panel-introduction/ic767168.png
[4]: ./media/active-directory-saas-access-panel-introduction/ic767169.png

