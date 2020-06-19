---
title: 发布本机客户端应用 - Azure AD | Microsoft Docs
description: 介绍如何使本机客户端应用与 Azure AD 应用程序代理连接器通信，以提供本地应用的安全远程访问。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 442e1515159afc1df79bb6f5f1f747ce0800fef7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647234"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>如何让本机客户端应用程序与代理应用程序交互

使用 Azure Active Directory (Azure AD) 应用程序代理不仅可以发布 Web 应用，而且还能发布已使用 Microsoft 身份验证库 (MSAL) 配置的本机客户端应用程序。 本机客户端应用程序不同于 Web 应用，因为前者安装在设备上，而后者需通过浏览器进行访问。

为了支持本机客户端应用程序，应用程序代理接受标头中发送的、由 Azure AD 颁发的令牌。 应用程序代理服务会对用户执行身份验证。 此解决方案不使用应用程序令牌进行身份验证。

![最终用户、Azure AD 与已发布应用程序之间的关系](./media/application-proxy-configure-native-client-application/richclientflow.png)

若要发布本机应用程序，请使用 Microsoft 身份验证库（负责处理身份验证并支持许多客户端环境）。 应用程序代理适用于[代表已登录用户调用 Web API 的桌面应用](https://docs.microsoft.com/azure/active-directory/develop/authentication-flows-app-scenarios#desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user)方案。

本文引导读者完成使用应用程序代理和 Azure AD 身份验证库发布本机应用程序的四个步骤。

## <a name="step-1-publish-your-proxy-application"></a>步骤 1：发布代理应用程序

像发布任何其他应用程序一样发布代理应用程序，并分配可访问该应用程序的用户。 有关详细信息，请参阅[使用应用程序代理发布应用程序](application-proxy-add-on-premises-application.md)。

## <a name="step-2-register-your-native-application"></a>步骤 2：注册本机应用程序

现在需要在 Azure AD 中注册应用程序，如下所示：

1. 登录到 [Azure Active Directory 门户](https://aad.portal.azure.com/)。 会显示 Azure Active Directory 管理中心的仪表板。
1. 在边栏中，选择“Azure Active Directory”。 会显示 Azure Active Directory 概述页。
1. 在 Azure AD 概述边栏中，选择“应用注册”。 会显示所有应用注册的列表。
1. 选择“新注册”。 会显示“注册应用程序”页。

   ![在 Azure 门户中创建新的应用注册](./media/application-proxy-configure-native-client-application/create.png)

1. 在“名称”标题中，为应用程序指定面向用户的显示名称。
1. 在“受支持的帐户类型”标题下，使用以下指导原则选择访问级别：

   - 若要仅面向组织内部的帐户，请选择“仅此组织目录中的帐户”。
   - 若要仅面向企业或教育客户，请选择“任何组织目录中的帐户”。
   - 若要面向最广泛的 Microsoft 标识集，请选择“任何组织目录中的帐户和个人 Microsoft 帐户”。
1. 在“重定向 URI”下，选择“公共客户端(移动和桌面)”，然后键入应用程序的重定向 URI `https://login.microsoftonline.com/common/oauth2/nativeclient`。
1. 选择并阅读“Microsoft 平台策略”，然后选择“注册”。 会创建并显示新应用程序注册的概述页。

有关创建新应用程序注册的更多详细信息，请参阅[将应用程序与 Azure Active Directory 集成](../develop/quickstart-register-app.md)。

## <a name="step-3-grant-access-to-your-proxy-application"></a>步骤 3：授予对代理应用程序的访问权限

现在已注册了本机应用程序，便可以向它授予对目录中其他应用程序的访问权限，在此例中是访问代理应用程序。 若要使本机应用程序可以向代理应用程序公开，请执行以下操作：

1. 在新应用程序注册页的边栏中，选择“API 权限”。 会显示新应用程序注册的“API 权限”页。
1. 选择“添加权限”。 会显示“请求获取 API 权限”页。
1. 在“选择 API”设置下，选择“我的组织使用的 API”。 将显示一个列表，其中包含目录中公开 API 的应用程序。
1. 在搜索框中键入或滚动查找在[步骤 1：发布代理应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-native-client-application#step-1-publish-your-proxy-application)中发布的代理应用程序，然后选择该代理应用程序。
1. 在“应用程序需要哪种类型的权限?”标题中，选择权限类型。 如果本机应用程序需要以已登录用户的身份访问代理应用程序 API，请选择“委托的权限”。
1. 在“选择权限”标题中，选择所需权限，然后选择“添加权限”。 本机应用程序的“API 权限”页现在会显示你添加的代理应用程序和权限 API。

## <a name="step-4-add-the-microsoft-authentication-library-to-your-code-net-c-sample"></a>步骤 4：向代码添加 Microsoft 身份验证库（.NET C# 示例）

在 Microsoft 身份验证库 (MSAL) 的身份验证上下文中编辑本机应用程序代码，以包含以下文本： 

```         
// Acquire Access Token from AAD for Proxy Application
IPublicClientApplication clientApp = PublicClientApplicationBuilder
.Create(<App ID of the Native app>)
.WithDefaultRedirectUri() // will automatically use the default Uri for native app
.WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
.Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
 {
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
 }
    catch (MsalUiRequiredException ex)
 {
     authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();                
 }

if (authResult != null)
 {
  //Use the Access Token to access the Proxy Application

  HttpClient httpClient = new HttpClient();
  HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
  HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
 }
```

示例代码中的所需信息可在 Azure AD 门户中找到，如下所示：

| 所需信息 | 如何在 Azure AD 门户中找到它 |
| --- | --- |
| \<租户 ID> | “Azure Active Directory” > “属性” > “目录 ID” |
| \<本机应用的应用 ID> | “应用程序注册” > 你的本机应用程序 > “概述” > “应用程序 ID” |
| \<范围> | “应用程序注册” > 你的本机应用程序 > “API 权限” > 单击权限 API (user_impersonation) > 一个标题为“user_impersonation”的面板会出现在右侧。 > 范围是编辑框中的 URL。
| \<代理应用 Url> | API 的外部 Url 和路径

使用这些参数编辑 MSAL 之后，用户可以向本机客户端应用程序进行身份验证，即使他们在企业网络之外也是如此。

## <a name="next-steps"></a>后续步骤

有关本机应用程序流的详细信息，请参阅 [Azure Active Directory 中的本机应用](../azuread-dev/native-app.md)。

了解如何[在 Azure Active Directory 中设置应用程序的单一登录](what-is-single-sign-on.md#choosing-a-single-sign-on-method)。