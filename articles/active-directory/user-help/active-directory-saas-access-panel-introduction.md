---
title: Azure Active Directory 中的访问面板是什么？ | Microsoft Docs
description: 了解如何使用各种形式的访问面板（Web 浏览器、Android 应用、iPhone 和 iPad 应用）来访问 SaaS 应用。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/18
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 31b6741163c9808c32d0f1ae3da8f6f1ffe794eb
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39058419"
---
# <a name="what-is-the-access-panel"></a>访问面板是什么？

访问面板是一个基于 Web 的门户。 如果在Azure Active Directory (Azure AD) 中拥有工作或学校帐户，可以使用访问面板来查看和启动 Azure AD 管理员已授权你访问的基于云的应用程序。 还可以通过访问面板使用自助服务组和应用管理功能。

访问面板与 Azure 门户不同， 它不需要你拥有 Azure 订阅。

![访问面板][1]使用访问面板可以编辑一些配置文件设置，并执行以下操作：

- 更改工作或学校帐户关联的密码。

- 编辑密码重置设置。

- 编辑与多重身份验证相关的联系人和首选项设置（适用于管理员要求使用访问面板执行此类操作的帐户）。

- 查看帐户详细信息，例如，用户 ID、备用电子邮件地址、移动电话号码、办公电话号码和设备。

- 查看和启动 Azure AD 管理员已授权访问的基于云的应用程序。 

- 自我管理组。 管理员可以在 Azure AD 中创建和管理安全组，并请求安全组成员身份。 有关详细信息，请参阅 [Self-service group management for users in Azure AD](../users-groups-roles/groups-self-service-management.md)（面向 Azure AD 中用户的自助组管理）和 [Manage your groups](../fundamentals/active-directory-manage-groups.md)（管理组）。




## <a name="access-the-access-panel"></a>访问访问面板

可以转到 `http://myapps.microsoft.com` 来访问访问面板。

如果为登录页配置了自定义商标，则可以将组织的域追加到 URL 来加载此商标（例如 `http://myapps.microsoft.com/<your domain>.com`）。

可以使用已在 Azure 门户中配置的任何活动域名或已验证域名，如下所示：![Wingtip Toys 域名][2]  

将此 URL 分发给要登录到与 Azure AD 集成的应用程序的所有用户。

## <a name="authentication"></a>身份验证

若要访问访问面板，必须通过 Azure AD 中的工作或学校帐户进行身份验证。 可以直接在 Azure AD 中进行身份验证。 或者，如果组织已使用 Active Directory 联合身份验证服务 (AD FS) 或其他技术配置联合，则可以通过 Windows Server Active Directory 进行身份验证。

如果拥有 Azure 或 Office 365 订阅并一直在使用 Azure 门户或 Office 365 应用程序，则可以看到应用程序列表，而无需再次登录。 如果未执行身份验证，系统会提示使用 Azure AD 中的帐户用户名和密码登录。 如果组织已配置联合，只需键入用户名即可。

执行身份验证后，可与管理员已集成到目录的应用程序交互。 要了解如何将应用程序与 Azure AD 集成，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="web-browser-requirements"></a>Web 浏览器要求

访问面板的最低要求是使用支持 JavaScript 且已启用 CSS 的浏览器。 为了让用户通过基于密码的单一登录 (SSO) 登录到应用程序，必须在浏览器中安装“访问面板”扩展。 选择某个已配置基于密码的 SSO 的应用程序时，会自动下载此扩展。

安装程序特定于体系结构。 如果单击下载链接，则仅获取适用于当前正在运行的 OS 体系结构的安装程序。 如果你是应用程序部署管理员，请确保从 64 位和 32 位设备访问下载链接以同时获取这两个安装程序。


访问面板扩展插件当前适用于：
- **Edge**：Windows 10 周年纪念版或更高版本上。 
- **Chrome**：在 Windows 7 或更高版本上，以及在 MacOS X 或更高版本上。
- **Firefox 26.0 或更高版本**：在 Windows XP SP2 或更高版本上，以及在 Mac OS X 10.6 或更高版本上。
- **Internet Explorer 11**：在 Windows 7 或更高版本上（支持受限）。

## <a name="my-apps-secure-sign-in-extension"></a>我的应用安全登录扩展插件
此扩展插件要求用户使用密码进行单一登录。 安装后，用户还可通过单击“登录以开始使用”登录该扩展插件，启用附加功能。 

- 可通过访问应用的“登录 URL”直接登录应用。 导航到应用的登录 URL 时，该扩展插件会检测操作并提供从该扩展插件进行登录的选项。
- 可使用该扩展插件的“快速搜索”功能，从访问面板启动其任何应用。 
- 该扩展插件还会在“最近使用的应用”部分显示最近启动的三个应用程序。
- 通过[应用程序代理](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-get-started)进行远程操作时，可以使用内部公司 URL

> [!NOTE]
> 附加功能仅适用于 Microsoft Edge、Chrome 和 Firefox。
>
可以直接从以下站点下载扩展：
- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

如果使用的我的应用 URL 不是 `https://myapps.microsoft.com`，请通过执行以下操作配置默认 URL：
1. 未登录到该扩展时，请右键单击该扩展图标。
2. 在菜单上，选择“我的应用 URL”。
3. 选择默认 URL。
4. 单击该扩展图标。
5. 选择“登录以开始使用”。

若要在使用扩展进行远程操作时使用内部公司 URL，请执行以下操作：
1. 在租户上[配置应用程序代理](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-enable)。
2. 通过应用程序代理[发布应用程序](https://docs.microsoft.com/en-us/azure/active-directory/application-proxy-publish-azure-portal)和 URL。
3. 安装该扩展，并通过选择“登录以开始使用”登录到该扩展。
4. 现在，即使在执行远程操作时也可以浏览内部公司 URL。

> [!NOTE]
> 还可选择主菜单上的设置齿轮，然后在“公司内部 URL 重定向”选项处选择“关闭”，以此关闭自动重定向到公司 URL。


## <a name="mobile-app-support"></a>移动应用支持

Azure Active Directory 团队发布了“我的应用”移动应用。 安装此应用后，可以在 iOS 和 Android 设备上登录到基于密码的 SSO 应用程序。

> [!NOTE]
> 几乎可以在任何设备上的任何 Web 浏览器中登录到支持与 Azure AD 联合的应用程序（包括 Salesforce、Google Apps、Dropbox、Box、Concur、Workday、Office 365 和其他 70 种以上的应用程序），无需安装任何插件或移动应用。 此外，无需在移动设备上使用“我的应用”移动应用，就能[体验访问面板](https://myapps.microsoft.com/)的所有其他功能。
>
>

### <a name="my-apps-for-android"></a>我的应用 Android 版

运行 Android 4.1 和更高版本的任何 Android 设备都支持“我的应用 Android 版”。  

[Google Play 商店](https://play.google.com/store/apps/details?id=com.microsoft.myapps)中已提供此应用。

![我的应用 Android 版][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>我的应用 iPhone 和 iPad 版

运行 iOS 7 或更高版本的任何 iPhone 或 iPad 设备都支持“我的应用 iOS 版”。  

[Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8) 中已提供此应用。

![我的应用 iOS 版][4]    


## <a name="managed-browser-for-my-apps"></a>Managed Browser 中的“我的应用”

“我的应用”还已集成到 Intune Managed Browser 中。 适用于 iOS 和 Android 设备的 Intune Managed Browser 在确保移动设备上的数据安全性方面发挥着重要的作用。 该浏览器可让我们安全查看和导航可能包含公司信息的网页，并提供安全的 Web 浏览体验。  

Managed Browser 主页和书签中提供了“我的应用”的快速访问链接，只需单击几下鼠标即可进入任何想要访问的应用程序。

[Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) 和 [Google Play 商店](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser&hl=en)中已提供 Intune Managed Browser。

![Managed Browser 中的“我的应用”][5]    


## <a name="tips-for-testing-the-user-experience"></a>有关测试用户体验的提示

如果你是 Azure 管理员并已使用目录中的帐户登录到 Azure 门户，则会以当前帐户的身份自动登录到访问面板。 此视图显示所有已分配给你的应用程序。

若要以不同的用户帐户测试，请执行以下操作：

1. 在 Azure 门户或访问面板的右上角，选择“注销”。 
2. 转到[访问面板](http://myapps.microsoft.com)。
3. 在登录页中，键入目录中想要测试的帐户的用户名和密码。


## <a name="starting-applications"></a>启动应用程序

本部分介绍访问面板上可能会出现有多种类型的应用程序。

### <a name="office-365-applications"></a>Office 365 应用程序

如果组织正在使用 Office 365 应用程序并且拥有相应的许可证，Office 365 应用程序会显示在访问面板上。

选择 Office 365 应用程序的应用程序磁贴时，会重定向到该应用程序并自动登录。

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>使用基于联合的 SSO 配置的 Microsoft 应用程序与第三方应用程序

管理员可以在 SSO 模式设置为“Azure AD 单一登录”的情况下，将应用程序添加到 Azure 门户的“Active Directory”部分中。 仅当管理员已向你显式授予这些应用程序的访问权限时，你才能看到这些应用程序。

选择某个应用程序的磁贴时，会重定向并自动登录到该应用程序。

### <a name="password-based-sso-without-identity-provisioning"></a>没有标识预配的基于密码的 SSO

管理员可以在 SSO 模式设置为“基于密码的单一登录”的情况下，将应用程序添加到 Azure 门户的“Active Directory”部分中。 目录中的所有用户都可看到在此模式下配置的所有应用程序。

首次选择其中一个应用程序的磁贴时，系统会提示安装适用于 Internet Explorer 或 Chrome 的密码 SSO 插件。 安装后可能需要重启 Web 浏览器。 返回访问面板并再次选择应用程序磁贴时，系统会提示输入应用程序的用户名和密码。 输入用户名和密码后，这些凭据会安全存储在 Azure AD 中并链接到 Azure AD 中的帐户。

下次选择应用程序磁贴时，会自动登录到该应用程序。  

无需再次输入凭据，也无需安装密码 SSO 插件。

如果凭据在第三方目标应用程序中已更改，则也必须更新 Azure AD 中存储的相应凭据。 

若要更新凭据，请执行以下操作：

1. 选择应用程序磁贴上的图标。
2. 选择“更新凭据”以重新输入应用程序的用户名和密码。


### <a name="password-based-sso-with-identity-provisioning"></a>有标识预配的基于密码的 SSO

管理员可以在 SSO 模式设置为“基于密码的单一登录”的情况下，将应用程序连同标识预配一起添加到 Azure 门户的“Active Directory”部分中。

首次选择其中一个应用程序的磁贴时，系统会提示安装适用于 Internet Explorer 或 Chrome 的密码 SSO 插件。 安装后可能需要重启 Web 浏览器。  

返回访问面板并选择应用程序磁贴时，会自动登录到应用程序。

某些应用程序可能要求在首次登录时更改密码。 如果凭据在第三方目标应用程序中已更改，则也必须更新 Azure AD 中存储的相应凭据。 

若要更新凭据，请执行以下操作：

1. 选择应用程序磁贴上的图标。
2. 选择“更新凭据”以重新输入应用程序的用户名和密码。


### <a name="application-with-existing-sso-solutions"></a>包含现有 SSO 解决方案的应用程序

Azure 门户提供了第三个选项（名为“现有的单一登录”）用于配置应用程序的 SSO。 管理员可以使用此选项创建应用程序的链接，并将其放置在所选用户的访问面板上。

例如，如果某个应用程序配置为使用 AD FS 2.0 验证用户身份，则管理员可以使用“现有的单一登录”选项在访问面板上创建该应用程序的链接。 访问该链接时，会通过 AD FS 2.0 或者由该应用程序提供的任何现有 SSO 解决方案进行身份验证。


## <a name="next-steps"></a>后续步骤

- 若要查看与应用程序管理相关的所有主题列表，请参阅[有关 Azure Active Directory 中应用程序管理的文章索引](../active-directory-apps-index.md)。
 
- 若要了解如何将 SaaS 应用集成到 Azure AD，请参阅[有关如何集成 SaaS 应用的教程列表](../saas-apps/tutorial-list.md)。
 
- 若要详细了解如何使用 Azure AD 管理应用，请参阅[使用 Azure Active Directory 进行单一登录和应用访问权限管理简介](../manage-apps/what-is-single-sign-on.md)。
 
- 有关详细了解用户预配，请参阅[在 SaaS 应用程序中自动预配和取消预配用户](../active-directory-saas-app-provisioning.md)。

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[3]: ./media/active-directory-saas-access-panel-introduction/03.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
