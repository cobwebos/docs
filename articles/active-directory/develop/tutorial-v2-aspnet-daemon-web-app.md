---
title: 生成使用 Microsoft 标识平台终结点的多租户守护程序
description: 本教程介绍如何从 Windows 桌面 (WPF) 应用程序调用受 Azure Active Directory 保护的 ASP.NET Web API。 WPF 客户端对用户进行身份验证、请求访问令牌并调用 Web API。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: b63aa2b2d98a12246d0dc2c35e015da872caff28
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83641105"
---
# <a name="tutorial-build-a-multitenant-daemon-that-uses-the-microsoft-identity-platform-endpoint"></a>教程：生成使用 Microsoft 标识平台终结点的多租户守护程序

在本教程中，你将了解如何使用 Microsoft 标识平台在长时间运行的非交互式过程中访问 Microsoft 企业客户的数据。 示例守护程序使用 [OAuth2 客户端凭据授予](v2-oauth2-client-creds-grant-flow.md)获取访问令牌。 然后，该守护程序使用该令牌调用 [Microsoft Graph](https://graph.microsoft.io) 并访问组织数据。

> [!div class="checklist"]
> * 将守护程序应用与 Microsoft 标识平台集成
> * 由管理员直接向应用授予应用程序权限
> * 获取用于调用 Microsoft Graph API 的访问令牌
> * 调用 Microsoft Graph API。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

该应用是作为 ASP.NET MVC 应用程序生成的。 它使用 OWIN OpenID Connect 中间件将用户登录。

本示例中的“守护程序”组件是 API 控制器 `SyncController.cs`。 调用该控制器时，它会从 Microsoft Graph 提取客户的 Azure Active Directory (Azure AD) 租户中的用户列表。 `SyncController.cs` 由 Web 应用程序中的 AJAX 调用触发。 它使用[适用于 .NET 的 Microsoft 身份验证库 (MSAL)](msal-overview.md) 获取 Microsoft Graph 的访问令牌。

>[!NOTE]
> 如果你不熟悉 Microsoft 标识平台，我们建议你从 [.NET Core 守护程序快速入门](quickstart-v2-netcore-daemon.md)开始。

## <a name="scenario"></a>场景

由于该应用是面向 Microsoft 企业客户的多租户应用，因此它必须为客户提供一种“注册”应用程序或将应用程序“连接”到其公司数据的方法。 在连接流期间，公司管理员首先将应用程序权限直接授予应用，使该应用能够以非交互方式访问公司数据，而无需用户登录。 本示例中的大部分逻辑介绍了如何使用标识平台[管理员许可](v2-permissions-and-consent.md#using-the-admin-consent-endpoint)终结点来实现此连接流。

![拓扑](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

有关此示例中使用的概念的详细信息，请阅读[标识平台终结点的客户端凭据协议文档](v2-oauth2-client-creds-grant-flow.md)。

## <a name="prerequisites"></a>先决条件

若要运行本快速入门中的示例，你将需要：

- [Visual Studio 2017 或 2019](https://visualstudio.microsoft.com/downloads/)。
- Azure AD 租户。 有关详细信息，请参阅[如何获取 Azure AD 租户](quickstart-create-new-tenant.md)。
- Azure AD 租户中的一个或多个用户帐户。 本示例不适用于 Microsoft 帐户（前称为 Windows Live 帐户）。 如果已使用 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)并且从未在目录中创建过用户帐户，则现在需要执行此操作。

## <a name="clone-or-download-this-repository"></a>克隆或下载此存储库

在 shell 或命令行中输入以下命令：

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

或[下载 zip 文件格式的示例](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip)。

## <a name="register-your-application"></a>注册应用程序

本示例包含一个项目。 若要向 Azure AD 租户注册应用程序，可以：

- 遵循[将示例注册到 Azure Active Directory 租户](#register-your-application)和[将示例配置为使用 Azure AD 租户](#choose-the-azure-ad-tenant)中的步骤。
- 使用可执行以下操作的 PowerShell 脚本：
  - 自动创建 Azure AD 应用程序和相关对象（密码、权限、依赖项）。
  - 修改 Visual Studio 项目的配置文件。

若要使用自动化：

1. 请在 Windows 上运行 PowerShell，并转到克隆的目录的根目录。
1. 运行以下命令：

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. 运行脚本以创建 Azure AD 应用程序，并相应地配置示例应用程序的代码：

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   [应用创建脚本](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md)中介绍了运行脚本的其他方式。

1. 打开 Visual Studio 解决方案，选择“启动”以运行代码。

如果不想要使用自动化，请使用以下部分中的步骤。

### <a name="choose-the-azure-ad-tenant"></a>选择 Azure AD 租户

1. 使用工作或学校帐户或者个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 如果帐户位于多个 Azure AD 租户中，请在页面顶部的菜单中选择你的个人资料，然后选择“切换目录”。
1. 将门户会话更改为所需的 Azure AD 租户。

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>注册客户端应用 (dotnet-web-daemon-v2)

1. 转到面向开发人员的 Microsoft 标识平台中的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页。
1. 选择“新注册”。
1. “注册应用程序”页出现后，请输入应用程序的注册信息：
   - 在“名称”部分输入一个会显示给应用用户的有意义的应用程序名称。 例如，输入 **dotnet-web-daemon-v2**。
   - 在“支持的帐户类型”部分，选择“任何组织目录中的帐户”。
   - 在“重定向 URI (可选)”部分的组合框中选择“Web”，然后输入以下重定向 URI： 
       - **https://localhost:44316/**
       - **https://localhost:44316/Account/GrantPermissions**

     如果有两个以上的重定向 URI，则需要在成功创建应用后，从“身份验证”选项卡中添加这些 URI。
1. 选择“注册”以创建应用程序。
1. 在应用的“概述”页上，找到“应用程序(客户端) ID”值，并记下该值以供后续使用 。 你将需要使用该值为此项目配置 Visual Studio 配置文件。
1. 在应用的页面列表中，选择“身份验证”。 然后：
   - 在“高级设置”部分，将“注销 URL”设置为 **https://localhost:44316/Account/EndSession** 。 
   - 在“高级设置” > “隐式授权”部分，选择“访问令牌”和“ID 令牌”。    本示例需要启用[隐式授权流](v2-oauth2-implicit-grant-flow.md)，使用户能够登录并调用 API。
1. 选择“保存”。
1. 在“证书和机密”页上的“客户端机密”部分选择“新建客户端机密”。   然后：

   1. 输入密钥说明（例如“应用机密”）。
   1. 选择密钥持续时间（“1 年”、“2 年”或“永不过期”）  。
   1. 选择“添加”按钮。
   1. 显示密钥值后，请将其复制并保存到安全位置。 稍后将需要此密钥来配置 Visual Studio 中的项目。 此密钥以后不再显示，也无法通过任何其他方式检索它。
1. 在应用的页面列表中，选择“API 权限”。 然后：
   1. 选择“添加权限”按钮。
   1. 确保已选择“Microsoft API”选项卡。
   1. 在“常用 Microsoft API”部分，选择“Microsoft Graph”。 
   1. 在“应用程序权限”部分，确保已选择适当的权限： **User.Read.All**。
   1. 选择“添加权限”按钮。

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>将示例配置为使用 Azure AD 租户

在以下步骤中，**ClientID** 等同于“应用程序 ID”或 **AppId**。

在 Visual Studio 中打开解决方案以配置项目。

### <a name="configure-the-client-project"></a>配置客户端项目

如果使用了安装脚本，将应用以下更改。

1. 打开 **UserSync\Web.Config** 文件。
1. 找到应用密钥 **ida:ClientId**。 将现有值替换为 **dotnet-web-daemon-v2** 应用程序的、从 Azure 门户复制的应用程序 ID。
1. 找到应用密钥 **ida:ClientSecret**。 将现有值替换为在 Azure 门户中创建 **dotnet-web-daemon-v2** 应用期间保存的密钥。

## <a name="run-the-sample"></a>运行示例

清理解决方案，重新生成解决方案，运行 UserSync 应用程序，然后以管理员身份登录到 Azure AD 租户中。 如果没有用于测试的 Azure AD 租户，可[按照这些说明](quickstart-create-new-tenant.md)获取租户。

登录时，应用首先会请求提供所需的权限，以便能够将你登录并读取你的用户配置文件。 应用通过这种许可来确保你是企业用户。

![用户同意](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

然后，应用会尝试通过 Microsoft Graph 从 Azure AD 租户同步用户列表。 如果无法同步，它会请求你（租户管理员）将租户连接到应用。

然后，应用将请求读取租户中用户列表的权限。

![管理员同意](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

授予权限后，你将从应用注销。 这种注销可确保从令牌缓存中删除 Microsoft Graph 的所有现有访问令牌。 再次登录时，获取的新令牌将拥有调用 Microsoft Graph 所需的权限。


在你授予权限后，应用随时可以查询用户。 可以通过选择“同步用户”按钮并刷新用户列表来确认这一点。 尝试添加或删除用户，并重新同步列表。 （但请注意，应用只会同步第一页中的用户。）

## <a name="about-the-code"></a>关于代码

此示例的相关代码在以下文件中：

- **App_Start\Startup.Auth.cs**、**Controllers\AccountController.cs**：初始登录 具体而言，控制器上的操作具有 **Authorize** 属性，该属性将强制用户登录。 应用程序使用[授权代码流](v2-oauth2-auth-code-flow.md)，以便于用户登录。
- **Controllers\SyncController.cs**：将用户列表同步到本地内存中存储。
- **Controllers\UserController.cs**：显示本地内存中存储中的用户列表。
- **Controllers\AccountController.cs**：使用管理员许可终结点从租户管理员获取权限。

## <a name="re-create-the-sample-app"></a>重新创建示例应用

1. 在 Visual Studio 中，创建新的 **Visual C#** **ASP.NET Web 应用程序(.NET Framework)** 项目。
1. 在下一屏幕中选择“MVC”项目模板。 另外，请为 **Web API** 添加文件夹和核心引用，因为稍后要添加 Web API 控制器。 将项目的所选身份验证模式保留为默认设置：“无身份验证”。
1. 在“解决方案资源管理器”窗口中选择该项目，然后按 **F4** 键。
1. 在项目属性中，将“已启用 SSL”设置为“True”。  记下“SSL URL”中的信息。 在 Azure 门户中配置此应用程序的注册时需要用到这些信息。
1. 添加以下 ASP.NET OWIN 中间件 NuGet 包：
   - Microsoft.Owin.Security.ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft.IdentityModel.Protocol.Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft.Identity.Client
1. 在 **App_Start** 文件夹中：
   1. 创建名为 **Startup.Auth.cs** 的类。
   1. 从命名空间名称中删除 **.App_Start**。
   1. 将 **Startup** 类的代码替换为示例应用的同一文件中的代码。
   请务必使用整个类定义。 定义将从 **public class Startup** 更改为 **public partial class Startup**。
1. 在 **Startup.Auth.cs**中，通过添加 Visual Studio IntelliSense 建议的 **using** 语句来解决缺少引用的问题。
1. 右键单击该项目，然后依次选择“添加”、“类”。 
1. 在搜索框中输入 **OWIN**。 “OWIN Startup 类”将作为一个选项显示。 选择该选项，将类命名为 **Startup.cs**。
1. 在 **Startup.cs** 中，将 **Startup** 类的代码替换为示例应用的同一文件中的代码。 同样请注意，定义将从 **public class Startup** 更改为 **public partial class Startup**。
1. 在 **Models** 文件夹中，添加名为 **MsGraphUser.cs** 的新类。 将实现替换为示例中同名文件的内容。
1. 添加名为 **AccountController** 的“MVC 5 控制器 - 空”新实例。 将实现替换为示例中同名文件的内容。
1. 添加名为 **UserController** 的“MVC 5 控制器 - 空”新实例。 将实现替换为示例中同名文件的内容。
1. 添加名为 **SyncController** 的“Web API 2 控制器 - 空”新实例。 将实现替换为示例中同名文件的内容。
1. 对于用户界面，请在 **Views\Account** 文件夹中，添加名为 **GrantPermissions**、**Index** 和 **UserMismatch** 的三个“空(无模型)视图”实例。 在 **Views\User** 文件夹中添加一个名为 **Index** 的实例。 将实现替换为示例中同名文件的内容。
1. 更新 **Shared\_Layout.cshtml** 和 **Home\Index.cshtml**，以正确地将各个视图链接到一起。

## <a name="deploy-the-sample-to-azure"></a>将示例部署到 Azure

此项目包含 Web 应用和 Web API 项目。 若要将这些项目部署到 Azure 网站，请对每个项目执行以下步骤：

1. 创建一个 Azure 网站。
1. 将 Web 应用和 Web API 发布到该网站。
1. 更新客户端以调用该网站而不是调用 IIS Express。

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>创建 dotnet-web-daemon-v2 并将其发布到 Azure 网站

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左上角，选择“创建资源”。
1. 选择“Web” > “Web 应用”，然后为网站命名。  例如，将它命名为 **dotnet-web-daemon-v2-contoso.azurewebsites.net**。
1. 选择“订阅”、“资源组”和“应用服务计划和位置”的信息。   为“OS”选择“Windows”，为“发布”选择“代码”。   
1. 选择“创建”，等待创建完应用服务。
1. 出现“部署成功”通知后，选择“转到资源”以转到新建的应用服务。 
1. 创建网站后，在“仪表板”中找到它，并将其选中以打开应用服务的“概述”屏幕。 
1. 在应用服务的“概述”选项卡中，选择“获取发布配置文件”链接下载该配置文件并保存。  可以使用其他部署机制，例如，从源代码管理进行部署。
1. 切换到 Visual Studio，然后：
   1. 转到 **dotnet-web-daemon-v2** 项目。
   1. 在解决方案资源管理器中右键单击该项目，然后选择“发布”。
   1. 在底部栏上选择“导入配置文件”，然后导入先前下载的发布配置文件。
1. 选择“配置”。
1. 在“连接”选项卡上更新目标 URL，使其使用“https”。 例如，使用 `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`。 选择“**下一页**”。
1. 在“设置”选项卡上，确保已清除“启用组织身份验证”。 
1. 选择“保存”。 在主屏幕上选择“发布”。

Visual Studio 将发布项目，同时自动打开浏览器并加载该项目的 URL。 如果看到该项目的默认网页，则表示发布成功。

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>更新 dotnet-web-daemon-v2 的 Azure AD 租户应用程序注册

1. 返回到 [Azure 门户](https://portal.azure.com)。
1. 在左侧窗格中选择“Azure Active Directory”服务，然后选择“应用注册”。 
1. 选择 **dotnet-web-daemon-v2** 应用程序。
1. 在应用程序的“身份验证”页上，使用服务地址更新“注销 URL”字段。  例如，使用 `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`。
1. 在“品牌”菜单中，将“主页 URL”更新为服务地址。  例如，使用 `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`。
1. 保存配置。
1. 在“身份验证” > “重定向 URI”菜单的值列表中添加相同的 URL。  如果有多个重定向 URL，请确保每个重定向 URL 都有一个使用应用服务的 URI 的新条目。

## <a name="clean-up-resources"></a>清理资源
如果不再需要，请删除[注册应用程序](#register-your-application) 步骤中创建的应用对象。  若要删除应用程序，请按照[删除你或你的组织编写的应用程序](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization)中的说明进行操作。

## <a name="get-help"></a>获取帮助

使用 [Stack Overflow](http://stackoverflow.com/questions/tagged/msal) 获取社区的支持。
先在 Stack Overflow 上提问并浏览现有问题，查看是否已有人提出过相同的问题。
请务必在提问或评论中加上“adal”、“msal”和“dotnet”标记。

如果发现示例中有错误，请在 [GitHub 问题](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues)上提出问题。

如果发现 MSAL.NET 中有错误，请在 [MSAL.NET GitHub 问题](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues)上提出问题。

若要提供建议，请转到 [User Voice 页](https://feedback.azure.com/forums/169401-azure-active-directory)。

## <a name="next-steps"></a>后续步骤
了解有关 Microsoft 标识平台支持的不同[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)的详细信息。

有关详细信息，请参阅以下概念文档：

- [Azure Active Directory 中的租赁](single-and-multi-tenant-apps.md)
- [了解 Azure AD 应用程序许可体验](application-consent-experience.md)
- [使用多租户应用程序模式让任何 Azure Active Directory 用户登录](howto-convert-app-to-be-multi-tenant.md)
- [了解用户同意和管理员同意](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Azure Active Directory 中的应用程序对象和服务主体对象](app-objects-and-service-principals.md)
- [快速入门：将应用程序注册到 Microsoft 标识平台](quickstart-register-app.md)
- [快速入门：配置客户端应用程序以访问 Web API](quickstart-configure-app-access-web-apis.md)
- [为具有客户端凭据流的应用程序获取令牌](msal-client-applications.md)

对于更简单的多租户控制台守护程序应用程序，请参阅 [.NET Core 守护程序快速入门](quickstart-v2-netcore-daemon.md)。
