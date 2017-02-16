---
title: "如何支持使用代理应用程序发布本机客户端应用 | Microsoft Docs"
description: "介绍如何使本机客户端应用与 Azure AD 应用程序代理连接器通信，以提供本地应用的安全远程访问权限。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: f0cae145-e346-4126-948f-3f699747b96e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: e65393c9582056f84530a32804e0d82fd451b688
ms.openlocfilehash: 34cacff4e8b13fa9d91387ca8762439908ed01fd


---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>如何使本地客户端应用与代理应用程序交互
Azure Active Directory 应用程序代理广泛用于发布浏览器应用程序，如 SharePoint、Outlook Web Access 和自定义业务线应用程序。 它还可用于发布本机客户端应用，这有别于 Web 应用，因为此类应用安装在设备上。 通过支持在标准 Authorize HTTP 标头中发送的 Azure AD 颁发令牌实现此操作。

![用户、Azure Active Directory 和已发布应用程序之间的关系](./media/active-directory-application-proxy-native-client/richclientflow.png)

发布此类应用程序的建议方法是使用 Azure AD 身份验证库，此库负责所有身份验证事宜，并且支持许多不同的客户端环境。 应用程序代理适合[本机应用程序到 Web API 方案](develop/active-directory-authentication-scenarios.md#native-application-to-web-api)。 完成此目的的过程如下所示：

## <a name="step-1-publish-your-application"></a>步骤 1：发布应用程序
像任何其他应用程序一样发布代理应用程序、配置用户并为他们提供高级或基本许可证。 有关详细信息，请参阅[使用应用程序代理发布应用程序](active-directory-application-proxy-publish.md)。

## <a name="step-2-configure-your-application"></a>步骤 2：配置应用程序
按如下方式配置本机应用程序：

1. 登录到 Azure 经典门户。
2. 选择左侧菜单上的 Active Directory 图标，然后选择你的目录。
3. 在顶部菜单中单击“应用程序”。 如果尚未将任何应用添加到你的目录，则此页只会显示“添加应用”链接。 单击该链接，或者单击命令栏上的“添加”按钮。
4. 在“要执行什么操作”页上，单击“添加我的组织正在开发的应用程序”链接。
5. 在“向我们说明你的应用程序”页上，指定应用程序名称，然后选择“本机客户端应用程序”。 单击箭头图标继续。
6. 在“应用程序信息”页上，为本机客户端应用程序提供**重定向 URI**，然后单击复选标记完成。

现已添加你的应用程序，此时你将转到应用程序的“快速启动”页。

## <a name="step-3-grant-access-to-other-applications"></a>步骤 3：向其他应用程序授予访问权限
使本机应用程序可以向目录中的其他应用程序公开：

1. 在顶部菜单中，单击“应用程序”、选择新的本机应用程序，然后单击“配置”。
2. 向下滚动到“对其他应用程序的权限”部分。 单击“添加应用程序”按钮并选择要授予本机应用程序访问权限的代理应用程序，然后单击右下角的复选标记。 从“委托的权限”下拉菜单中选择新的权限。

![针对其他应用程序的权限 - 添加应用程序](./media/active-directory-application-proxy-native-client/delegate_native_app.png)

## <a name="step-4-edit-the-active-directory-authentication-library"></a>步骤 4：编辑 Active Directory 身份验证库
在 Active Directory 身份验证库 (ADAL) 的身份验证上下文中编辑本机应用程序代码，以包含以下内容：

        // Acquire Access Token from AAD for Proxy Application
        AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
        AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

        //Use the Access Token to access the Proxy Application
        HttpClient httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
        HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

应替换变量，如下所示：

* **TenantId** 位于 应用程序“配置”页的 URL 中的 GIUD 中，紧跟在“/Directory/”之后。
* **前端 URL** 是在代理应用程序中输入的前端 URL，位于代理应用的“配置”页上。
* 本机应用的**客户端 Id** 位于本机应用程序的“配置”页上。
* **本机应用的重定向 URI** 位于本机应用程序的“配置”页上。

![新的本机应用程序配置页屏幕截图](./media/active-directory-application-proxy-native-client/new_native_app.png)

有关本机应用程序流的详细信息，请参阅[本机应用程序到 Web API](develop/active-directory-authentication-scenarios.md#native-application-to-web-api)。

## <a name="see-also"></a>另请参阅
* [使用你自己的域名发布应用程序](active-directory-application-proxy-custom-domains.md)
* [启用条件性访问](active-directory-application-proxy-conditional-access.md)
* [使用声明感知应用程序](active-directory-application-proxy-claims-aware-apps.md)
* [启用单一登录](active-directory-application-proxy-sso-using-kcd.md)

有关最新新闻和更新，请参阅 [应用程序代理博客](http://blogs.technet.com/b/applicationproxyblog/)




<!--HONumber=Jan17_HO3-->


