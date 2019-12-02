---
title: 调用受 Azure AD 保护的 ASP.NET Web API - Microsoft 标识平台
description: 本快速入门介绍如何从 Windows 桌面 (WPF) 应用程序调用受 Azure Active Directory 保护的 ASP.NET Web API。 WPF 客户端对用户进行身份验证、请求访问令牌并调用 Web API。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: c558d45702498e6c1164d7ee1731e80ff195349e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328546"
---
# <a name="build-a-multi-tenant-daemon-with-the-microsoft-identity-platform-endpoint"></a>使用 Microsoft 标识平台终结点构建多租户守护程序

在本教程中，你将了解如何使用 Microsoft 标识平台在长时间运行的非交互式过程中访问 Microsoft 企业客户的数据。 示例守护程序使用 [OAuth2 客户端凭据授权](v2-oauth2-client-creds-grant-flow.md)获取访问令牌，然后使用该令牌调用 [Microsoft Graph](https://graph.microsoft.io) 并访问组织数据。

此应用构建为 ASP.NET MVC 应用程序，并使用 OWIN OpenID Connect 中间件，以便于用户登录。  本示例中其“守护程序”组件为 API 控制器，在调用该控制器时，将从 Microsoft Graph 中提取客户的 Azure AD 租户中的用户列表。  此 `SyncController.cs` 由 Web 应用程序中的 AJAX 调用触发，并使用[适用于 .NET 的 Microsoft 身份验证库 (MSAL)](msal-overview.md) 获取 Microsoft Graph 的访问令牌。

对于更简单的控制台守护程序应用程序，请查看 [.NET Core 守护程序快速入门](quickstart-v2-netcore-daemon.md)。

## <a name="scenario"></a>场景

由于该应用是一个供任何 Microsoft 企业客户使用的多租户应用，因此它必须为客户提供一种“注册”或将应用程序“连接”到公司数据的方法。  在连接流期间，公司管理员首先将应用程序权限直接授予应用，以便它能够以非交互方式访问公司数据，而无需用户登录  。  本示例中的大部分逻辑介绍了如何使用标识平台[管理员同意](v2-permissions-and-consent.md#using-the-admin-consent-endpoint)终结点来实现此连接流。

![拓扑](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

有关此示例中使用的概念的详细信息，请务必阅读[标识平台终结点客户端凭据协议文档](v2-oauth2-client-creds-grant-flow.md)。

## <a name="prerequisites"></a>先决条件

若要运行本快速入门中的示例，你将需要：

- [Visual Studio 2017 或 2019](https://visualstudio.microsoft.com/downloads/)
- 一个 Azure Active Directory (Azure AD) 租户。 有关如何获取 Azure AD 租户的详细信息，请参阅[如何获取 Azure AD 租户](quickstart-create-new-tenant.md)
- Azure AD 租户中的一个或多个用户帐户。 此示例不适用于 Microsoft 帐户（以前称为 Windows Live 帐户）。 因此，如果使用 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)并且之前从未在目录中创建用户帐户，则现在需要执行此操作。

## <a name="clone-or-download-this-repository"></a>克隆或下载此存储库

在 shell 或命令行中：

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

或[下载 ZIP 文件格式的示例](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip)。

## <a name="register-the-sample-application-with-your-azure-ad-tenant"></a>将示例应用程序注册到 Azure AD 租户

此示例中有一个项目。 若要注册该项目，可以：

- 按照步骤[将示例注册到 Azure Active Directory 租户](#register-the-sample-application-with-your-azure-ad-tenant)并[将示例配置为使用 Azure AD 租户](#choose-the-azure-ad-tenant-for-the-applications)
- 或使用具有以下特征的 PowerShell 脚本：
  - 自动为你创建 Azure AD 应用程序和相关对象（密码、权限、依赖项） 
  - 修改 Visual Studio 项目的配置文件。

如果要使用此自动化：

1. 请在 Windows 上运行 PowerShell，并导航到克隆目录的根目录
1. 在 PowerShell 中运行：

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. 运行脚本以创建 Azure AD 应用程序，并相应地配置示例应用程序的代码。
1. 在 PowerShell 中运行：

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   > [应用创建脚本](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md)中介绍了运行脚本的其他方式

1. 请打开 Visual Studio 解决方案，并单击“启动”以运行代码。

如果不想使用此自动化，请遵循以下步骤。

### <a name="choose-the-azure-ad-tenant-for-the-applications"></a>为应用程序选择 Azure AD 租户

首先，你将需要：

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 如果帐户存在于多个 Azure AD 租户中，请在页面顶部菜单的右上角选择配置文件，然后“切换目录”  。
   将门户会话更改为所需的 Azure AD 租户。

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>注册客户端应用 (dotnet-web-daemon-v2)

1. 导航到面向开发人员的 Microsoft 标识平台的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页。
1. 选择“新注册”。 
1. 出现“注册应用程序”页后，请输入应用程序的注册信息： 
   - 在“名称”  部分输入一个会显示给应用用户的有意义的应用程序名称，例如 `dotnet-web-daemon-v2`。
   - 在“支持的帐户类型”  部分，选择“任何组织目录中的帐户”  。
   - 在重定向 URI（可选）部分的组合框中选择“Web”，然后输入以下重定向 URI  ：
       - `https://localhost:44316/`
       - `https://localhost:44316/Account/GrantPermissions` 如果有多个重定向 URI，将需要在成功创建应用后，从“身份验证”选项卡中添加这些 URI  。
1. 选择“注册”  以创建应用程序。
1. 在应用的“概述”页上，找到“应用程序(客户端) ID”值，并记下该值以供后续使用   。 你将需要使用该值为此项目配置 Visual Studio 配置文件。
1. 在应用的页面列表中，选择“身份验证”。 
   - 在“高级设置”  部分，将“注销 URL”  设置为 `https://localhost:44316/Account/EndSession`
   - 在“高级设置” | “隐式授权”  **部分中，选中“访问令牌”和“ID 令牌”**   ，因为该示例需要启用[隐式授权流](v2-oauth2-implicit-grant-flow.md)才能登录用户并调用 API。
1. 选择“保存”。 
1. 在“证书和密码”页上的“客户端密码”部分选择“新建客户端密码”    ：

   - 键入（实例 `app secret` 的）密钥说明，
   - 选择密钥持续时间（“1 年”、“2 年”或“永不过期”）    。
   - 按“添加”按钮时，会显示密钥值，将其复制然后保存在安全位置  。
   - 稍后将需要此密钥来配置 Visual Studio 中的项目。 该密钥值将不会再次显示，也无法通过其他任何方式检索，因此，该值在 Azure 门户中显示时，请立即记下来。
1. 在应用的页面列表中，选择“API 权限” 
   - 单击“添加权限”  按钮，然后
   - 确保已选中“Microsoft API”选项卡 
   - 在“常用 Microsoft API”部分中，单击“Microsoft Graph”  
   - 在“应用程序权限”部分，确保已勾选正确的权限  ：User.Read.All 
   - 选择“添加权限”按钮 

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>将示例配置为使用 Azure AD 租户

在下面的步骤中，“ClientID”等同于“应用程序 ID”或“AppId”。

在 Visual Studio 中打开解决方案以配置项目

### <a name="configure-the-client-project"></a>配置客户端项目

如果使用了安装脚本，将应用以下更改。

1. 打开 `UserSync\Web.Config` 文件
1. 查找应用密钥 `ida:ClientId`，并将现有值替换为从 Azure 门户复制的 `dotnet-web-daemon-v2` 应用程序的应用程序 ID (clientId)。
1. 在 Azure 门户中查找应用密钥 `ida:ClientSecret`，并将现有值替换为在创建 `dotnet-web-daemon-v2` 应用期间保存的密钥。

## <a name="run-the-sample"></a>运行示例

清理解决方案，重新生成解决方案，并运行 UserSync 应用程序，并以管理员身份登录 Azure AD 租户开始操作。  如果没有用于测试的 Azure AD 租户，可[按照这些说明](quickstart-create-new-tenant.md)获取租户。

登录时，应用将首先询问让你登录并阅读你的用户配置文件的权限。  通过此同意，应用程序可确保你是企业用户。

![用户同意](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

然后，应用程序将尝试通过 Microsoft Graph 同步 Azure AD 租户中的用户列表。  如果无法执行此操作，它将要求你（租户管理员）将租户连接到应用程序。

然后，应用程序将请求读取租户中用户列表的权限。

![管理员同意](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

授予权限后，你将从应用中注销  。 该操作用于确保从令牌缓存中删除 Graph 的任何现有访问令牌。 再次登录后，获得的新令牌将具有调用 MS Graph 所需的权限。
授予权限后，应用程序将能够随时查询用户。  可通过单击用户页上的“同步用户”按钮刷新用户列表来确认这一点  。  尝试添加或删除用户并重新同步列表（但请注意，它只同步第一页用户！）。

## <a name="about-the-code"></a>关于代码

此示例的相关代码在以下文件中：

- 首次登录：`App_Start\Startup.Auth.cs`、`Controllers\AccountController.cs`。  具体而言，控制器上的操作具有 Authorize 属性，该属性将强制用户登录。 应用程序使用[授权代码流](v2-oauth2-auth-code-flow.md)，以便于用户登录。
- 将用户列表同步到本地内存中存储：`Controllers\SyncController.cs`
- 显示本地内存中存储中的用户列表：`Controllers\UserController.cs`
- 使用管理员同意终结点从租户管理员获取权限：`Controllers\AccountController.cs`

## <a name="recreate-this-sample-app"></a>重新创建此示例应用

1. 在 Visual Studio 中，创建新的 `Visual C#` `ASP.NET Web Application (.NET Framework)` 项目。 在下一个屏幕中，选择 `MVC` 项目模板。 还可为 `Web API` 添加文件夹和核心引用，因为稍后将添加 Web API 控制器。  将项目所选的身份验证模式保留为默认模式，即 `No Authentication`。
2. 在“解决方案资源管理器”窗口中选择项目，然后按“F4”键显示项目属性   。 在项目属性中将“已启用 SSL”  设置为 `True`。 请注意“SSL URL”  。 在 Azure 门户中配置此应用程序的注册时需要用到该 URL。
3. 请添加以下 ASP.Net OWIN 中间件 NuGet：`Microsoft.Owin.Security.ActiveDirectory`、`Microsoft.Owin.Security.Cookies` 和 `Microsoft.Owin.Host.SystemWeb`、`Microsoft.IdentityModel.Protocol.Extensions`、`Microsoft.Owin.Security.OpenIdConnect` 和 `Microsoft.Identity.Client`。 
4. 在 `App_Start` 文件夹中，创建 `Startup.Auth.cs` 类。 从命名空间名称中删除 `.App_Start`。  用示例应用的同一文件中的代码替换 `Startup` 类的代码。  请务必使用整个类定义！  定义从 `public class Startup` 更改为 `public partial class Startup`
5. 在 `Startup.Auth.cs` 通过添加 Visual Studio intellisense 建议的 `using` 语句来解析缺少的引用。
6. 右键单击该项目，依次选择“添加”和“类”，并在搜索框中输入“OWIN”。  “OWIN Startup 类”将显示为选项；请选择该选项并将类命名为 `Startup.cs`。
7. 在 `Startup.cs` 中，用示例应用的同一文件中的代码替换 `Startup` 类的代码。  请再次注意定义从 `public class Startup` 更改为 `public partial class Startup`。
8. 在该文件夹中，添加一个名为 `MsGraphUser.cs` 的新类。  将实现替换为示例中同名文件的内容。
9. 添加名为 `AccountController` 的新“MVC 5 控制器 - 空”  。 将实现替换为示例中同名文件的内容。
10. 添加名为 `UserController` 的新“MVC 5 控制器 - 空”  。 将实现替换为示例中同名文件的内容。
11. 添加名为 `SyncController` 的新“Web API 2 控制器 - 空”  。 将实现替换为示例中同名文件的内容。
12. 对于用户界面，请在 `Views\Account` 文件夹中添加名为 `GrantPermissions`、`Index` 和 `UserMismatch` 的三个“空(无模型)视图”，并在 `Views\User` 文件夹中添加一个名为 `Index` 的视图  。 将实现替换为示例中同名文件的内容。
13. 更新 `Shared\_Layout.cshtml` 和 `Home\Index.cshtml`，以正确链接各种视图。

## <a name="deploy-this-sample-to-azure"></a>将此示例部署到 Azure

此项目具有 Web 应用/Web API 项目。 若要将其部署到 Azure 网站，将需要为每个网站执行以下操作：

- 创建 Azure 网站
- 将 Web 应用/Web API 发布到网站，然后
- 更新其客户端以调用网站而不是 IIS Express。

### <a name="create-and-publish-the-dotnet-web-daemon-v2-to-an-azure-web-site"></a>创建 `dotnet-web-daemon-v2` 并将其发布到 Azure 网站

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 单击左上角的 `Create a resource`，选择“Web” --> Web 应用”，然后为网站命名，例如 `dotnet-web-daemon-v2-contoso.azurewebsites.net`   。
1. 然后选择 `Subscription`、`Resource Group`、`App service plan and Location`。 `OS` 将为“Windows”且 `Publish` 将为“代码”   。
1. 单击 `Create` 并等待创建应用服务。
1. 获取 `Deployment succeeded` 通知后，单击 `Go to resource` 导航到新创建的应用服务。
1. 创建网站后，请在“仪表板”中查找网站并单击以打开“应用服务”>“概述”屏幕    。
1. 在“应用服务”的“概述”选项卡中，单击“获取发布配置文件”链接下载该配置文件并保存   。  还可以使用其他部署机制，如源代码管理。
1. 切换到 Visual Studio 并转到 dotnet-web-daemon-v2 项目。  在“解决方案资源管理器”中右键单击该项目，然后选择“发布”  。  在底部栏上单击“导入配置文件”，然后导入先前下载的发布配置文件  。
1. 单击“配置”  ，然后在 `Connection tab` 中更新“目标 URL”，使其成为主页 URL 中的 `https`，例如 [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)。 单击“下一步”。 
1. 在“设置”选项卡上，确保未选择 `Enable Organizational Authentication`。  单击“ **保存**”。 在主屏幕上单击“发布”  。
1. Visual Studio 将发布项目，并自动打开浏览器以访问该项目的 URL。  如果看到该项目的默认网页，则发布成功。

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>更新 `dotnet-web-daemon-v2` 的 Azure AD 租户应用程序注册

1. 导航回到 [Azure 门户](https://portal.azure.com)。
在左侧导航窗格中，选择“Azure Active Directory”服务  ，然后选择“应用注册”  。
1. 在结果屏幕中，选择 `dotnet-web-daemon-v2` 应用程序。
1. 在应用程序的“身份验证”  | 页上，使用服务地址更新“注销 URL”字段，例如 [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)
1. 在“品牌”  菜单中，将“主页 URL”  更新为服务地址，例如 [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)。 保存配置。
1. 在“身份验证”->“重定向 URI”菜单的值列表中添加相同的 URL  。 如果有多个重定向 URL，请确保每个重定向 URL 都有一个使用应用服务的 URI 的新条目。

## <a name="community-help-and-support"></a>社区帮助和支持

使用 [Stack Overflow](http://stackoverflow.com/questions/tagged/msal) 获取社区的支持。
先在 Stack Overflow 上提问并浏览现有问题，查看是否已有人提出过相同疑问。
请务必用 [`adal` `msal` `dotnet`] 标记你的提问和评论。

如果发现示例中有错误，请在 [GitHub 问题](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues)上提出问题。

如果发现 MSAL.NET 中有错误，请在 [MSAL.NET GitHub 问题](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues)上提出问题。

若要提供建议，请访问以下[用户之声页](https://feedback.azure.com/forums/169401-azure-active-directory)。

## <a name="next-steps"></a>后续步骤
了解有关 Microsoft 标识平台支持的不同[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)的详细信息。

有关详细信息，请参阅以下概念文档：

- [Azure Active Directory 中的租赁](single-and-multi-tenant-apps.md)
- [了解 Azure AD 应用程序许可体验](application-consent-experience.md)
- [如何：使用多租户应用程序模式让任何 Azure Active Directory 用户登录](howto-convert-app-to-be-multi-tenant.md)
- [了解用户同意和管理员同意](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Azure Active Directory 中的应用程序对象和服务主体对象](app-objects-and-service-principals.md)
- [快速入门：将应用程序注册到 Microsoft 标识平台](quickstart-register-app.md)
- [快速入门：配置客户端应用程序以访问 Web API](quickstart-configure-app-access-web-apis.md)
- [为具有客户端凭据流的应用程序获取令牌](msal-client-applications.md)

对于更简单的多租户控制台守护程序应用程序，请查看 [.NET Core 守护程序快速入门](quickstart-v2-netcore-daemon.md)。
