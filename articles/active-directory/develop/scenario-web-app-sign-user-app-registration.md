---
title: 登录用户的 Web 应用（应用注册）-Microsoft 标识平台
description: 了解如何构建用于登录用户的 web 应用（应用注册）
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
ms.openlocfilehash: 641f71f6111930b54d0a2bd548f16d3cb0c07189
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175255"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>登录用户的 Web 应用-应用注册

本页说明登录用户的 web 应用的应用注册细节。

若要注册应用程序，你可以使用：

- [Web 应用快速入门](#register-an-app-using-the-quickstarts)-除了成为创建应用程序的极佳入门体验外，Azure 门户中的快速入门还包含一个名为 "**为我进行此更改**" 的按钮。 您可以使用此按钮设置所需的属性，即使对于现有应用程序也是如此。 你需要将这些属性的值调整为你自己的事例。 具体而言，应用程序的 web API URL 可能与建议的默认值不同，这也会影响注销 URI。
- 用于[手动注册应用程序](#register-an-app-using-azure-portal)的 Azure 门户
- PowerShell 和命令行工具

## <a name="register-an-app-using-the-quickstarts"></a>使用快速入门注册应用程序

如果导航到此链接，可以创建 web 应用程序的启动：

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-using-azure-portal"></a>使用 Azure 门户注册应用

> [!NOTE]
> 要使用的门户不同，具体取决于你的应用程序是在 Microsoft Azure 公有云还是在全国或主权云中运行。 有关详细信息，请参阅[国家云](./authentication-national-cloud.md#app-registration-endpoints)


1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。 或者，登录到所选的国家/地区云 Azure 门户。
1. 如果你的帐户有权访问多个租户，请在右上角选择该帐户，并将门户会话设置为所需的 Azure AD 租户。
1. 在左侧导航窗格中，选择 " **Azure Active Directory** " 服务，然后选择 "**应用注册**" > "**新建注册**"。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

4. “注册应用程序”页出现后，请输入应用程序的注册信息：
   1. 为应用程序选择支持的帐户类型（请参阅[支持的帐户类型](./v2-supported-account-types.md)）
   1. 在“名称”部分输入一个会显示给应用用户的有意义的应用程序名称，例如 `AspNetCore-WebApp`。
   1. 在 "**重定向 URI**" 中，添加应用程序的类型，以及在成功进行身份验证后将接受返回的令牌响应的 URI 目标。 例如，`https://localhost:44321/` 。  选择“注册”。
1. 选择“身份验证”菜单，然后添加以下信息：
   1. 在 "**答复 URL**" 中，添加类型为 "Web" 的 `https://localhost:44321/signin-oidc`。
   1. 在“高级设置”部分，将“注销 URL”设置为 `https://localhost:44321/signout-oidc`。
   1. 在“隐式授权”下，勾选“ID 令牌”。
   1. 选择“保存”。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

4. 出现“注册应用程序”页后，请输入应用程序的注册信息：
   1. 为应用程序选择支持的帐户类型（请参阅[支持的帐户类型](./v2-supported-account-types.md)）
   - 在“名称”部分输入一个会显示给应用用户的有意义的应用程序名称，例如 `MailApp-openidconnect-v2`。
   - 在 "重定向 URI （可选）" 部分中，在组合框中选择 " **Web** "，然后输入以下重定向 uri： "`https://localhost:44326/`"。
1. 选择“注册”以创建应用程序。
1. 选择“身份验证”菜单，然后添加以下信息：
   - 在 "**高级设置**" | "**隐式授予**" 部分中，检查**ID 令牌**，因为此示例需要启用[隐式授权流](v2-oauth2-implicit-grant-flow.md)才能登录用户。
1. 选择“保存”。

# <a name="javatabjava"></a>[Java](#tab/java)

4. 当 "**注册应用程序" 页**出现时，请输入应用程序的友好名称（例如 "webapp"），选择 "任何组织目录和个人 Microsoft 帐户中的帐户（例如 Skype，Xbox，Outlook.com）"，然后选择 "Web 应用"。/API "作为*应用程序类型*。
1. 单击 "**注册**" 以注册应用程序。
1. 在左侧菜单中，单击 "**身份验证**"，然后在 "*重定向 uri*" 下选择 "Web"。 需要输入两个不同的重定向 Uri：一个用于登录页面，另一个用于图形页面。 对于这两种情况，应使用相同的主机和端口号，然后使用 "/msal4jsample/secure/aad" 作为登录页，并在 "msal4jsample/graph/me" 中键入 "用户信息" 页。
 默认情况下，该示例使用：

    - `http://localhost:8080/msal4jsample/secure/aad`。
    - `http://localhost:8080/msal4jsample/graph/me`

    在“高级设置”部分，将“注销 URL”设置为 `http://localhost:8080/msal4jsample/sign_out`。

     单击 "**保存**"。

1. 从菜单中选择 "**证书" & 机密**，然后在 "**客户端密码**" 部分中，单击 "**新建客户端密钥**"：

    - 键入键说明
    - 选择一个密钥持续时间（ **1 年**）。
    - 选择 "**添加**" 时，将显示密钥值。
    - 复制密钥的值。 此键值不会再次显示，也不能通过任何其他方式进行检索，因此，在 Azure 门户中显示它时，会立即记录此值。

# <a name="pythontabpython"></a>[Python](#tab/python)

4. 出现“注册应用程序”页后，请输入应用程序的注册信息：
   - 在“名称”部分输入一个会显示给应用用户的有意义的应用程序名称，例如 `python-webapp`。
   - 将**支持的帐户类型**更改为**组织目录和个人 Microsoft 帐户中的帐户（例如 Skype、Xbox、Outlook.com）** 。
   - 在 "重定向 URI （可选）" 部分中，在组合框中选择 " **Web** "，然后输入以下重定向 uri： "`http://localhost:5000/getAToken`"。
1. 选择“注册”以创建应用程序。
1. 在应用的“概述”页上，找到“应用程序(客户端) ID”值，并记下该值以供后续使用。 你需要它来为此项目配置 Visual Studio 配置文件。
1. 在应用的 "概述" 页上，选择 "**身份验证**" 部分。
   - 在 "**高级设置**" 部分中，将**注销 URL**设置为 `http://localhost:5000/logout`

  选择“保存”。
1. 在左侧菜单中选择“证书和机密”，然后在“客户端机密”部分单击“新建客户端机密”：

      - 键入键说明
      - 选择密钥持续时间“1 年”。
      - 单击“添加”时，将显示密钥值。
      - 复制密钥的值。 稍后需要用到此值。
---

## <a name="register-an-app-using-powershell"></a>使用 PowerShell 注册应用程序

> [!NOTE]
> 目前 Azure AD PowerShell 仅创建具有以下受支持的帐户类型的应用程序：
>
> - MyOrg （仅限此组织目录中的帐户）
> - AnyOrg （任何组织目录中的帐户）。
>
> 如果你想要创建一个使用个人 Microsoft 帐户（例如 Skype、XBox、Outlook.com）登录用户的应用程序，你可以首先创建一个多租户应用程序（受支持的帐户类型 = 任何组织目录中的帐户），然后更改Azure 门户的应用程序清单中的 `signInAudience` 属性。 ASP.NET Core 教程的步骤[1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant)中详细说明了这一点（并且可以采用任何语言通用化到 web 应用）。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用程序的代码配置](scenario-web-app-sign-user-app-configuration.md)
