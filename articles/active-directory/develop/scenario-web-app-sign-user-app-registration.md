---
title: 注册用于登录用户的 Web 应用 - Microsoft 标识平台 | Azure
description: 了解如何注册用于登录用户的 Web 应用
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1adff446e6d41e30db109d0871811dc651f1f4f5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026233"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>用于登录用户的 Web 应用：应用注册

本文介绍可将用户登录的 Web 应用的应用注册具体信息。

若要注册应用程序，可以使用：

- [Web 应用快速入门](#register-an-app-by-using-the-quickstarts)。 除了提供创建应用程序的第一手体验，Azure 门户中的快速入门还包含名为“为我进行此更改”的按钮。  可以使用此按钮设置所需属性，对现有应用也可以这样做。 需根据自己的情况调整这些属性的值。 具体而言，应用的 Web API URL 可能会不同于建议的默认值，后者还会影响注销 URI。
- 用于[手动注册应用程序](#register-an-app-by-using-the-azure-portal)的 Azure 门户。
- PowerShell 和命令行工具。

## <a name="register-an-app-by-using-the-quickstarts"></a>按照快速入门注册应用

可使用以下链接启动 Web 应用程序的创建：

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>使用 Azure 门户注册应用程序

> [!NOTE]
> 要使用的门户不同，具体取决于你的应用程序是在 Microsoft Azure 公有云还是在全国或主权云中运行。 有关详细信息，请参阅[国家云](./authentication-national-cloud.md#app-registration-endpoints)。


1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。 或者，登录到所选的适用于全国云的[Azure 门户](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#app-registration-endpoints)。
2. 如果你的帐户有权限访问多个租户，请在右上角选择该帐户。 然后，将门户会话设置为所需的 Azure Active Directory (Azure AD) 租户。
3. 在左侧窗格中选择“Azure Active Directory”服务，然后选择“应用注册” > “新建注册”。   

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. “注册应用程序”页出现后，请输入应用程序的注册信息： 
   1. 为应用程序选择支持的帐户类型。 （请参阅[支持的帐户类型](./v2-supported-account-types.md)。）
   1. 在“名称”部分输入一个会显示给应用用户的有意义的应用程序名称。  例如，输入 **AspNetCore-WebApp**。
   1. 在“重定向 URI”中，添加将在成功进行身份验证后接受返回的令牌响应的应用程序类型和 URI 目标。  例如，输入 https://localhost:44321  。  然后，选择“注册”。
   ![registration](media/scenario-webapp/scenario-webapp-app-registration-1.png)
1. 选择“身份验证”菜单，然后添加以下信息：
   1. 对于“回复 URL”，请添加“Web”类型的 **https://localhost:44321/signin-oidc**。********
   1. 在“高级设置”部分，将“注销 URL”设置为 **https://localhost:44321/signout-oidc** 。 
   1. 选择“隐式授权”下的“ID 令牌”。
   1. 选择“保存”。
  ![registration](media/scenario-webapp/scenario-webapp-app-registration-2.png)
 
# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. 出现“注册应用程序”页后，请输入应用程序的注册信息：
   1. 为应用程序选择支持的帐户类型。 （请参阅[支持的帐户类型](./v2-supported-account-types.md)。）
   1. 在“名称”部分输入一个会显示给应用用户的有意义的应用程序名称。 例如，输入 **MailApp-openidconnect-v2**。
   1. 在 "**重定向 URI （可选）** " 部分中，在组合框中选择 " **Web** "，然后输入以下重定向 URI： **https://localhost:44326/** 。
1. 选择“注册”以创建应用程序。
1. 选择“身份验证”菜单。****
1. 在 "**高级设置**" "  |  **隐式授予**" 部分中，选择 " **ID 令牌**"。 本示例需要启用[隐式授权流](v2-oauth2-implicit-grant-flow.md)才能将用户登录。
1. 选择“保存” 。

# <a name="java"></a>[Java](#tab/java)

1. “注册应用程序”页显示后，请输入应用程序的显示名称。**** 例如，输入 **java-webapp**。
1. 选择**任何组织目录和个人 Microsoft 帐户中的帐户（例如 Skype、Xbox、Outlook.com）**，然后选择 " **WEB 应用/API** " 作为**应用程序类型**。
1. 选择“注册”**** 以注册应用程序。
1. 在左侧菜单中选择“身份验证”。**** 在“重定向 URI”下选择“Web”。********

1. 输入两个重定向 URI：一个用于登录页，另一个用于图形页。 对于这个 URI，请使用相同的主机和端口号，后接 **/msal4jsample/secure/aad**（登录页）和 **msal4jsample/graph/me**（用户信息页）。

   默认情况下，该示例使用：

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  然后选择“保存”。

1. 从菜单中选择“证书和机密”。****
1. 在 "**客户端密码**" 部分中，选择 "**新建客户端密钥**"，然后：

   1. 输入密钥说明。
   1. 选择密钥持续时间“1 年”。****
   1. 选择“添加”  。
   1. 显示密钥值后，请将其复制供稍后使用。 此值不会再次显示，也无法通过任何其他方式检索。

# <a name="python"></a>[Python](#tab/python)

1. 出现“注册应用程序”页后，请输入应用程序的注册信息：
   1. 在“名称”部分输入一个会显示给应用用户的有意义的应用程序名称。 例如，输入 **python-webapp**。
   1. 将**支持的帐户类型**更改为**组织目录和个人 Microsoft 帐户中的帐户（例如 Skype、Xbox、Outlook.com）**。
   1. 在“重定向 URI (可选)”部分，选择组合框中的“Web”并输入以下重定向 URI：**http://localhost:5000/getAToken**。********
1. 选择“注册”以创建应用程序。
1. 在应用的“概述”页上，找到“应用程序(客户端) ID”值，并记下该值供稍后使用。  稍后需要使用它为此项目配置 Visual Studio 配置文件。
1. 在左侧菜单中，选择“证书和机密”。****
1. 在“客户端机密”部分，选择“新建客户端机密”，然后：********

   1. 输入密钥说明。
   1. 选择密钥持续时间“1 年”。
   1. 选择“添加”  。
   1. 显示密钥值后，请将其复制。 稍后需要用到此信息。
---

## <a name="register-an-app-by-using-powershell"></a>使用 PowerShell 注册应用

> [!NOTE]
> 目前，Azure AD PowerShell 只能使用下述受支持帐户类型创建应用程序：
>
> - MyOrg（仅限此组织目录中的帐户）
> - AnyOrg（任何组织目录中的帐户）
>
> 你可以创建一个应用程序，该应用程序使用其 Microsoft 个人帐户（例如 Skype、Xbox 或 Outlook.com）来登录用户。 首先，创建一个多租户应用程序。 支持的帐户类型是任何组织目录中的帐户。 然后， [`accessTokenAcceptedVersion`](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#accesstokenacceptedversion-attribute) **2** [`signInAudience`](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#signinaudience-attribute) `AzureADandPersonalMicrosoftAccount` 在 Azure 门户的[应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)中，将属性更改为2，并将属性更改为。 有关详细信息，请参阅 ASP.NET Core 教程中的[步骤 1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) 。 可以采用任何语言将此步骤通用化到 web 应用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用的代码配置](scenario-web-app-sign-user-app-configuration.md)
