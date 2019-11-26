---
title: 用于登录用户的 Web 应用（应用注册）- Microsoft 标识平台
description: 了解如何构建用于登录用户的 Web 应用（应用注册）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50e4778693e448b9a989d70d3ca8cf91c76ce380
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482059"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>登录用户的 Web 应用：应用注册

本文介绍了用于登录用户的 web 应用的应用注册细节。

若要注册应用程序，可以使用：

- [Web 应用快速入门](#register-an-app-by-using-the-quickstarts)。 除了成为创建应用程序的极佳首次体验外，Azure 门户中的快速入门还包含一个名为 "**为我做出此更改**" 的按钮。 可以使用此按钮设置所需属性，对现有应用也可以这样做。 需根据自己的情况调整这些属性的值。 具体而言，应用程序的 web API URL 可能与建议的默认值不同，这也会影响注销 URI。
- 用于[手动注册应用程序](#register-an-app-by-using-the-azure-portal)的 Azure 门户。
- PowerShell 和命令行工具。

## <a name="register-an-app-by-using-the-quickstarts"></a>使用快速入门注册应用程序

你可以使用以下链接启动 web 应用程序的创建：

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>使用 Azure 门户注册应用程序

> [!NOTE]
> 要使用的门户不同，具体取决于你的应用程序是在 Microsoft Azure 公有云还是在全国或主权云中运行。 有关详细信息，请参阅[国内云](./authentication-national-cloud.md#app-registration-endpoints)。


1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。 或者，登录到所选的适用于全国云的 Azure 门户。
1. 如果你的帐户允许你访问多个租户，请在右上角选择你的帐户。 然后，将门户会话设置为所需的 Azure Active Directory （Azure AD）租户。
1. 在左窗格中，选择 " **Azure Active Directory**服务"，然后选择 "**应用注册**" > "**新注册**"。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

1. 出现“注册应用程序”页后，请输入应用程序的注册信息：
   1. 为应用程序选择支持的帐户类型。 （请参阅[支持的帐户类型](./v2-supported-account-types.md)。）
   1. 在 "**名称**" 部分中，输入将向应用用户显示的有意义的应用程序名称。 例如，输入**AspNetCore-WebApp**。
   1. 对于 "**重定向 URI**"，请在身份验证成功后添加应用程序类型和将接受返回的令牌响应的 URI 目标。 例如，输入 **https://localhost:44321** 。 然后，选择“注册”。
1. 选择“身份验证”菜单，然后添加以下信息：
   1. 对于 "**答复 URL**"，添加类型为 " **Web**" 的 **https://localhost:44321/signin-oidc** 。
   1. 在 "**高级设置**" 部分中，将 "**注销 URL** " 设置为 **https://localhost:44321/signout-oidc** 。
   1. 在 "**隐式授予**" 下，选择 " **ID 令牌**"。
   1. 选择“保存”。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

1. 出现“注册应用程序”页后，请输入应用程序的注册信息：
   1. 为应用程序选择支持的帐户类型。 （请参阅[支持的帐户类型](./v2-supported-account-types.md)。）
   1. 在 "**名称**" 部分中，输入将向应用用户显示的有意义的应用程序名称。 例如，输入**MailApp-openidconnect-v2**。
   1. 在 "**重定向 URI （可选）** " 部分中，在组合框中选择 " **Web** "，然后输入以下重定向 URI： " **https://localhost:44326/** "。
1. 选择“注册”以创建应用程序。
1. 选择 "**身份验证**" 菜单。 
1. 在 "**高级设置**" | **隐式授权**"部分中，选择" **ID 令牌**"。 此示例需要启用[隐式授权流](v2-oauth2-implicit-grant-flow.md)才能登录用户。
1. 选择“保存”。

# <a name="javatabjava"></a>[Java](#tab/java)

1. 当 "**注册应用程序" 页**出现时，请输入应用程序的显示名称。 例如，输入**webapp**。 
1. 选择**任何组织目录和个人 Microsoft 帐户中的帐户（例如 Skype、Xbox、Outlook.com）** ，然后选择 " **WEB 应用/API** " 作为**应用程序类型**。
1. 选择 "**注册**" 以注册应用程序。
1. 在左侧菜单中，选择 "**身份验证**"。 在 "**重定向 uri**" 下，选择**Web**。 

1. 输入两个重定向 Uri：一个用于登录页面，另一个用于图形页面。 对于这两种情况，请使用相同的主机和端口号，后跟 **/msal4jsample/secure/aad**的登录页和**msal4jsample/graph/me**的 "用户信息" 页。
 
   默认情况下，该示例使用：

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

1. 在 "**高级设置**" 部分中，将 "**注销 URL** " 设置为 **http://localhost:8080/msal4jsample/sign_out** 。 然后选择“保存”。

1. 从菜单中选择 "**证书" & "机密**"。 
1. 在 "**客户端密码**" 部分中，选择 "**新建客户端密钥**"，然后：

   1. 输入键说明。
   1. 选择密钥持续时间**为1年**。
   1. 选择“添加”。
   1. 显示密钥值时，请将其复制到后面。 此值不会再次显示，也不会被任何其他方法检索。

# <a name="pythontabpython"></a>[Python](#tab/python)

1. 出现“注册应用程序”页后，请输入应用程序的注册信息：
   1. 在 "**名称**" 部分中，输入将向应用用户显示的有意义的应用程序名称。 例如，输入**python-webapp**。
   1. 将**支持的帐户类型**更改为**组织目录和个人 Microsoft 帐户中的帐户（例如 Skype、Xbox、Outlook.com）** 。
   1. 在 "**重定向 URI （可选）** " 部分中，在组合框中选择 " **Web** "，然后输入以下重定向 URI： " **http://localhost:5000/getAToken** "。
1. 选择“注册”以创建应用程序。
1. 在应用的“概述”页上，找到“应用程序(客户端) ID”值，并记下该值以供后续使用。 稍后需要使用它为此项目配置 Visual Studio 配置文件。
1. 选择 "**身份验证**" 部分。 在 "**高级设置**" 中，将 "**注销 URL** " 设置为 **http://localhost:5000/logout** 。 然后选择“保存”。
1. 在左侧菜单中，选择 "**证书" & "机密**"。
1. 在 "**客户端密码**" 部分中，选择 "**新建客户端密钥**"，然后：

   1. 输入键说明。
   1. 选择密钥持续时间“1 年”。
   1. 选择“添加”。
   1. 显示密钥值时，请复制它。 稍后需要用到此信息。
---

## <a name="register-an-app-by-using-powershell"></a>使用 PowerShell 注册应用

> [!NOTE]
> 目前，Azure AD PowerShell 仅创建以下受支持的帐户类型的应用程序：
>
> - MyOrg （仅限此组织目录中的帐户）
> - AnyOrg （任何组织目录中的帐户）
>
> 你可以创建一个应用程序，该应用程序使用其 Microsoft 个人帐户（例如 Skype、Xbox 或 Outlook.com）来登录用户。 首先，创建一个多租户应用程序。 支持的帐户类型是任何组织目录中的帐户。 然后，从 Azure 门户更改应用程序清单中的 `signInAudience` 属性。 有关详细信息，请参阅 ASP.NET Core 教程中的[步骤 1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) 。 可以采用任何语言将此步骤通用化到 web 应用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用的代码配置](scenario-web-app-sign-user-app-configuration.md)
