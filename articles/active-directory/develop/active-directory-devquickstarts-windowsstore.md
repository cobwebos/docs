---
title: "Azure AD Windows 应用商店入门 | Microsoft Docs"
description: "生成与 Azure AD 集成以方便登录，并使用 OAuth 调用 Azure AD 保护 API 的 Windows 应用商店应用。"
services: active-directory
documentationcenter: windows
author: jmprieur
manager: mtillman
editor: 
ms.assetid: 3b96a6d1-270b-4ac1-b9b5-58070c896a68
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 09/16/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f76324e4415dcc300a0c2e21a89d30301c17ebab
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="integrate-azure-ad-with-windows-store-apps"></a>将 Azure AD 与 Windows 应用商店应用集成
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

> [!NOTE]
> Visual Studio 2017 中不支持 Windows 应用商店 8.1 和更低版本的项目。  有关详细信息，请参阅 [Visual Studio 2017 平台目标以及兼容性](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs)。

如果要开发 Windows 应用商店应用，通过 Azure Active Directory (Azure AD) 可简单直接地使用用户的 Active Directory 帐户对其进行身份验证。 通过与 Azure AD 集成，应用可安全地使用 Azure AD 保护的任何 Web API，例如 Office 365 API 或 Azure API。

对于需要访问受保护资源的 Windows 应用商店桌面应用，Azure AD 提供 Active Directory 身份验证库 (ADAL)。 ADAL 的唯一用途就是方便应用获取访问令牌。 为了演示操作的简单性，本文介绍了如何生成 DirectorySearcher Windows 应用商店应用，该应用可以：

* 使用 [OAuth 2.0 身份验证协议](https://msdn.microsoft.com/library/azure/dn645545.aspx)获取调用 Azure AD 图形 API 的访问令牌。
* 在目录中搜索具有给定用户主体名称 (UPN) 的用户。
* 将用户注销。

## <a name="before-you-get-started"></a>准备工作
* 下载[框架项目](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/skeleton.zip)，或下载[已完成的示例](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip)。 每个下载内容都是 Visual Studio 2015 解决方案。
* 还需要一个可在其中创建用户和注册应用的 Azure AD 租户。 如果还没有租户，请[了解如何获取租户](active-directory-howto-tenant.md)。

准备好后，请按照以下 3 个部分中的步骤操作。

## <a name="step-1-register-the-directorysearcher-app"></a>步骤 1：注册 DirectorySearcher 应用
若要让应用获取令牌，首先需要在 Azure AD 租户中注册该应用，并授予其访问 Azure AD 图形 API 的权限。 方法如下：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在顶部栏上，单击帐户。 然后，在“目录”列表下选择要注册应用的 Active Directory 租户。
3. 在左窗格中，单击“更多服务”，然后选择“Azure Active Directory”。
4. 单击“应用注册”，并选择“添加”。
5. 根据提示创建**本机客户端应用程序**。
  * **名称** - 向用户描述应用。
  * **重定向 URI** 是 Azure AD 用来返回令牌响应的方案与字符串组合。 现在，输入一个占位符值（例如，**http://DirectorySearcher**）。 稍后将替换此值。
6. 完成注册后，Azure AD 将向应用分配唯一的应用程序 ID。 复制“应用程序”选项卡上的值，因为稍后需用到此值。
7. 在“设置”页上，选择“所需权限”，并选择“添加”。
8. 对于 **Azure Active Directory** 应用，选择“Microsoft Graph”作为 API。
9. 在“委派的权限”下，添加“以已登录用户的身份访问目录”权限。 如此，使应用能够查询用户的图形 API。

## <a name="step-2-install-and-configure-adal"></a>步骤 2：安装和配置 ADAL
将应用注册到 Azure AD 后，可以安装 ADAL 并编写标识相关的代码。 若要允许 ADAL 与 Azure AD 通信，请向其提供一些有关应用注册的信息。

1. 使用包管理器控制台将 ADAL 添加到 DirectorySearcher 项目。

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

2. 在 DirectorySearcher 项目中，打开 MainPage.xaml.cs。
3. 将“配置值”区域中的值替换为在 Azure 门户中输入的值。 只要使用 ADAL，代码就会引用这些值。
  * *tenant* 是 Azure AD 租户的域（例如 contoso.onmicrosoft.com）。
  * *clientId* 是从门户中复制的应用的客户端 ID。
4. 现在需要发现 Windows 应用商店应用的回调 URI。 在 `MainPage` 方法中的此行上设置一个断点：
    ```
    redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
    ```
5. 生成解决方案，确保还原所有包引用。 如果缺少任何包，请打开 Nuget 包管理器并还原这些包。
6. 运行应用，并在到达断点时，复制 `redirectUri` 的值。 该值应如下所示：

    ```
    ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
    ```

7. 返回 Azure 门户中应用的“设置”选项卡，添加具有上述值的 **RedirectUri**。  

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>步骤 3：使用 ADAL 从 Azure AD 获取令牌
ADAL 遵守的基本原理是，每当应用需要访问令牌时，只需调用 `authContext.AcquireToken(…)`，ADAL 就会负责其余的工作。  

1. 初始化应用的 `AuthenticationContext`，这是 ADAL 的主类。 此操作将向 ADAL 传递它与 Azure AD 通信所需的坐标，并告知 ADAL 如何缓存令牌。

    ```C#
    public MainPage()
    {
        ...

        authContext = new AuthenticationContext(authority);
    }
    ```

2. 找到 `Search(...)` 方法，用户在应用 UI 上单击“搜索”按钮时会调用此方法。 此方法将向 Azure AD 图形 API 发出 get 请求，以查询其 UPN 以给定搜索词开头的用户。 要查询图形 API，请将访问令牌包括在请求的 **Authorization** 标头中。 然后便是 ADAL 发挥作用的时候。

    ```C#
    private async void Search(object sender, RoutedEventArgs e)
    {
        ...
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectURI, new PlatformParameters(PromptBehavior.Auto, false));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "authentication_canceled")
            {
                ShowAuthError(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", ex.ErrorCode, ex.Message));
            }
            return;
        }
        ...
    }
    ```
    应用通过调用 `AcquireTokenAsync(...)` 请求令牌时，ADAL 会尝试返回一个令牌，而不要求用户输入凭据。 如果 ADAL 确定用户需要登录以获取令牌，则会显示登录对话框、收集用户的凭据，并在身份验证成功后返回令牌。 如果 ADAL 出于任何原因无法返回令牌，则 AuthenticationResult 将处于错误状态。
3. 现在，就可以使用刚刚获得的访问令牌。 同时，在 `Search(...)` 方法中，将令牌附加到 **Authorization** 标头的图形 API get 请求中：

    ```C#
    // Add the access token to the Authorization header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new HttpCredentialsHeaderValue("Bearer", result.AccessToken);

    ```
4. 还可使用 `AuthenticationResult` 对象在应用中显示有关用户的信息，例如，用户的 ID：

    ```C#
    // Update the page UI to represent the signed-in user
    ActiveUser.Text = result.UserInfo.DisplayableId;
    ```
5. 还可使用 ADAL 将用户从应用中注销。 用户单击“注销”按钮时，需确保 `AcquireTokenAsync(...)` 的后续调用显示登录视图。 使用 ADAL 时，此操作与清除令牌缓存一样简单：

    ```C#
    private void SignOut()
    {
        // Clear session state from the token cache.
        authContext.TokenCache.Clear();

        ...
    }
    ```

## <a name="whats-next"></a>后续步骤
现在，便拥有了一个有效的 Windows 应用商店应用，可对用户进行身份验证，使用 OAuth 2.0 安全地调用 Web API，并获取有关用户的基本信息。

如果尚未在租户中填充用户，现在便可执行此操作。
1. 运行 DirectorySearcher 应用，并使用其中一个用户进行登录。
2. 根据用户的 UPN 搜索其他用户。
3. 关闭应用，并重新运行应用。 请注意，用户的会话将保持不变。
4. 单击右键显示底部栏进行注销，并以另一用户的身份重新登录。

使用 ADAL 可以方便地将所有这些常见标识功能合并到应用中。 它会负责所有的繁琐工作，例如缓存管理、OAuth 协议支持、向用户显示登录 UI，以及刷新过期令牌。 只需知道一个 API 调用，即 `authContext.AcquireToken*(…)`。

有关参考，请下载[已完成的示例](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip)（无配置值）。

现在，可以转到其他标识方案。 例如，尝试[使用 Azure AD 保护 .NET Web API](active-directory-devquickstarts-webapi-dotnet.md)。

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
