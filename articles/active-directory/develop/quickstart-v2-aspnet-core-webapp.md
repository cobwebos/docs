---
title: Azure AD v2.0 ASP.NET Core Web 应用快速入门 | Microsoft Docs
description: 了解如何使用 OpenID Connect 在 ASP.NET Core Web 应用上实现 Microsoft 登录
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: f57dc1707a9e25e4ea308142e804cdbc80d4308a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984936"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>向 ASP.NET Core Web 应用添加 Microsoft 登录功能

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

本快速入门包含代码示例，演示如何从任何 Azure Active Directory 实例中登录个人（hotmail.com、live.com 等等）工作和学校帐户。

![本快速入门生成的示例应用的工作原理](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.png)


> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>注册应用程序并下载快速入门应用
>
> ### <a name="register-and-configure-your-application-and-code-sample"></a>注册并配置应用程序和代码示例
> #### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序
> 
> 1. 转到 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app)。
> 1. 输入应用程序的名称，确保未选中“引导式设置”选项，然后单击“创建”。
> 1. 单击 `Add Platform`，并选择 `Web`。
> 1. 确保“允许隐式流”保持选中状态。
> 1. 在“重定向 URL”中输入 `https://localhost:3110/`。
> 1. 滚动到页面底部，单击“保存”。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>步骤 1：在 Azure 门户中配置应用程序
> 要使此快速入门中的代码示例正常运行，需要将答复 URL 添加为 `http://localhost:3110/`。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [执行此更改]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](media/quickstart-v2-aspnet-core-webapp/green-check.png) 应用程序已使用此属性进行配置

#### <a name="step-2-download-your-aspnet-core-project"></a>步骤 2：下载 ASP.NET Core 项目

- [下载 ASP.NET Core 项目](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>步骤 3：配置项目

1. 将 zip 文件提取到本地文件夹（例如，C:\Azure-Samples）
1. 如果使用 Visual Studio 2017，请在 Visual Studio 中打开项目（可选）
1. 编辑 appsettings.json 并将 `ClientId` 的值替换为刚刚注册的应用程序的应用程序 ID：

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "common"
    ```
1. 如果你的应用程序为单租户应用程序（仅限当前目录中的账户），则在 appsettings.json 文件中，查找 `TenantId` 的值，并将 `common` 替换为租户 ID 和租户名称（例如，contoso.microsoft.com）。 可在“概述页”中获取租户名称。

## <a name="more-information"></a>详细信息

本部分概述了登录用户所需的代码。 这有助于了解代码的工作原理、主要参数，并且可用于向现有 ASP.NET Core 应用程序添加登录。

### <a name="startup-class"></a>Startup 类

Microsoft.AspNetCore.Authentication 中间件使用主机进程初始化时执行的 Startup 类：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(sharedOptions =>
    {
        sharedOptions.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddAzureAd(options => Configuration.Bind("AzureAd", options))
    .AddCookie();

    services.AddMvc();
}
```

`AddAuthentication` 方法可配置服务，以添加基于 Cookie 的身份验证 - 用于浏览器方案，以及对 OpenIdConnect 设置质询。 

包含 `.AddAzureAd` 的行可向应用程序添加 Azure Active Directory 身份验证。

此外，AzureAdAuthenticationBuilderExtensions.cs 文件还可向 AzureAd 身份验证管道添加扩展方法。 此扩展方法可配置 Azure AD 身份验证所需的属性。 `IConfigureNamedOptions` 接口中的 `Configure` 方法包含如下内容：

```csharp
public void Configure(string name, OpenIdConnectOptions options)
{
    options.ClientId = _azureOptions.ClientId;
    options.Authority = $"{_azureOptions.Instance}common/v2.0";   // V2 specific
    options.UseTokenLifetime = true;
    options.RequireHttpsMetadata = false;
    options.TokenValidationParameters.ValidateIssuer = false;     // accept several tenants (here simplified)
}
```
> |其中  |  |
> |---------|---------|
> |ClientId     |Azure 门户中注册的应用程序的应用程序 ID|
> |颁发机构 | 用户要进行身份验证的 STS 终结点。 对于公有云，通常为 https://login.microsoftonline.com/{tenant}/v2.0，其中 {tenant} 是租户名称、租户 ID 或者引用常用终结点（用于多租户应用程序）的 common|
> |UseTokenLifetime |表示身份验证 cookie 应匹配身份验证令牌的 cookie|
> |RequireHttpsMetadata     |元数据地址或颁发机构需要 HPPS。 不建议将此值更改为 `True`|
> |TokenValidationParameters     | 用于令牌验证的参数列表。 在这种情况下，`ValidateIssuer` 设置为 `false`，以指示它可以接受来自任何个人或工作或学校帐户的登录|

### <a name="initiate-an-authentication-challenge"></a>启动身份验证质询

可以通过在控制器中请求身份验证质询，强制用户登录，就像在 AccountController.cs 中一样：

```csharp
public IActionResult SignIn()
{
    var redirectUrl = Url.Action(nameof(HomeController.Index), "Home");
    return Challenge(
        new AuthenticationProperties { RedirectUri = redirectUrl },
        OpenIdConnectDefaults.AuthenticationScheme);
}
```

> [!TIP]
> 如果希望视图同时适用于经身份验证和未经身份验证的用户，使用上述方法请求身份验证质询是可选也是常用选择。 可以使用下一节中所述的方法来保护控制器。

### <a name="protect-a-controller-or-a-controllers-method"></a>保护控制器或控制器的方法

可以使用 `[Authorize]` 属性保护控制器或控制器方法。 此属性通过仅允许经身份验证的用户，限制对控制器或方法的访问 - 这意味着如果用户未经身份验证，可启动身份验证质询，以访问控制器。

## <a name="next-steps"></a>后续步骤

请参阅此 ASP.NET Core 快速入门的 GitHub 存储库，了解有关详细信息，包括有关如何添加对全新 ASP.NET Core Web 应用程序的身份验证的说明：

> [!div class="nextstepaction"]
> [ASP.NET Core Web 代码示例](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]