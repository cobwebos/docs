---
title: 从 .NET 桌面 (WPF) 应用将用户登录并调用 Microsoft Graph API | Microsoft Docs
description: 了解如何生成一个 .NET Windows 桌面应用程序，使其与 Azure AD 集成以方便登录，并使用 OAuth 2.0 调用 Azure AD 保护的 API。
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b06ceb86e8406657766be1375889fcfe1b4673d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204536"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>快速入门：从 .NET 桌面 (WPF) 应用将用户登录并调用 Microsoft Graph API

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

对于需要访问受保护资源的 .NET 本机客户端，Azure Active Directory (Azure AD) 提供了 Active Directory 身份验证库 (ADAL)。 使用 ADAL，应用可以轻松获取访问令牌。 

在本快速入门中，你将学习如何生成具有以下功能的 .NET WPF 待办事项列表应用程序：

* 使用 OAuth 2.0 身份验证协议获取用于调用 Azure AD Graph API 的访问令牌。
* 在目录中搜索具有给定别名的用户。
* 将用户注销。

要构建完整的工作应用程序，需要：

1. 将应用程序注册到 Azure AD。
2. 安装并配置 ADAL。
3. 使用 ADAL 从 Azure AD 获取令牌。

## <a name="prerequisites"></a>先决条件

开始前，请完成这些先决条件：

* [下载应用框架](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip)或[下载已完成的示例](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip)
* 拥有你可在其中创建用户和注册应用程序的 Azure AD 租户。 如果还没有租户，请[了解如何获取租户](quickstart-create-new-tenant.md)。

## <a name="step-1-register-the-directorysearcher-application"></a>步骤 1：注册 DirectorySearcher 应用程序

为了使应用能够获取令牌，请在 Azure AD 租户中注册该应用，并授予它访问 Azure AD Graph API 的权限：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在顶栏上选择你的帐户，并在“目录”列表下选择要注册应用程序的 Active Directory 租户。
3. 选择左侧导航栏中的“所有服务”，并选择“Azure Active Directory”。
4. 在“应用注册”上，选择“添加”。
5. 根据提示创建一个新的“本机”客户端应用程序。
    * 应用程序的“名称”将向最终用户描述应用程序
    * “重定向 URI”是 Azure AD 要用来返回令牌响应的方案与字符串组合。 输入特定于你的应用程序的值，例如 `http://DirectorySearcher`。

6. 完成注册后，AAD 将为应用分配唯一的应用程序 ID。 在后面的部分中会用到此值，因此，请从应用程序页复制此值。
7. 在“设置”页上，依次选择“所需权限”和“添加”。 选择“Microsoft Graph”作为 API，并在“委派的权限”下添加“读取目录数据”权限。 设置此权限后，应用程序便可以在图形 API 中查询用户。

## <a name="step-2-install-and-configure-adal"></a>步骤 2：安装并配置 ADAL

将应用程序注册到 Azure AD 后，可以安装 ADAL 并编写标识相关的代码。 为了使 ADAL 能够与 Azure AD 进行通信，需要为 ADAL 提供一些有关应用注册的信息。

1. 首先，使用程序包管理器控制台将 ADAL 添加到 `DirectorySearcher` 项目。

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

1. 在 `DirectorySearcher` 项目中，打开 `app.config`。
1. 替换 `<appSettings>` 节中的元素值，以反映你在 Azure 门户中输入的值。 只要使用 ADAL，代码就会引用这些值。
  * `ida:Tenant` 是你的 Azure AD 租户的域，例如 contoso.onmicrosoft.com
  * `ida:ClientId` 是你从门户中复制的应用程序的客户端 ID。
  * `ida:RedirectUri` 是你在门户中注册的 URL。

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>步骤 3：使用 ADAL 从 Azure AD 获取令牌

ADAL 遵守的基本原理是，每当应用需要访问令牌时，它只需调用 `authContext.AcquireTokenAsync(...)`，ADAL 会负责其余的工作。

1. 在 `DirectorySearcher` 项目中，打开 `MainWindow.xaml.cs`。
1. 找到 `MainWindow()` 方法。 
1. 初始化你的应用的 `AuthenticationContext` - ADAL 的主类。 你将在 `AuthenticationContext` 中传递 ADAL 与 Azure AD 通信时所需的坐标，并告诉 ADAL 如何缓存令牌。

    ```csharp
    public MainWindow()
    {
        InitializeComponent();

        authContext = new AuthenticationContext(authority, new FileCache());

        CheckForCachedToken();
    }
    ```

1. 找到 `Search(...)` 方法，当用户在应用的 UI 中单击“搜索”按钮时，将调用该方法。 此方法将向 Azure AD Graph API 发出 GET 请求，以查询其 UPN 以给定搜索词开头的用户。
1. 若要查询图形 API，请在请求的 `Authorization` 标头中包含 access_token - 这是 ADAL 的进入位置。

    ```csharp
    private async void Search(object sender, RoutedEventArgs e)
    {
        // Validate the Input String
        if (string.IsNullOrEmpty(SearchText.Text))
        {
            MessageBox.Show("Please enter a value for the To Do item name");
            return;
        }

        // Get an Access Token for the Graph API
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
            UserNameLabel.Content = result.UserInfo.DisplayableId;
            SignOutButton.Visibility = Visibility.Visible;
        }
        catch (AdalException ex)
        {
            // An unexpected error occurred, or user canceled the sign in.
            if (ex.ErrorCode != "access_denied")
                MessageBox.Show(ex.Message);

            return;
        }

        ...
    }
    ```

    当应用程序通过调用 `AcquireTokenAsync(...)` 请求令牌时，ADAL 将尝试返回一个令牌，而不要求用户输入凭据。
    * 如果 ADAL 确定用户需要登录以获取令牌，会显示登录对话框，收集用户的凭据，并在身份验证成功后返回令牌。 
    * 如果 ADAL 出于任何原因无法返回令牌，则会引发 `AdalException`。

1. 请注意，`AuthenticationResult` 对象包含 `UserInfo` 对象，后者可用于收集应用程序可能需要的信息。 在 DirectorySearcher 中，`UserInfo` 用于使用用户 ID 自定义应用的 UI。
1. 当用户选择“注销”按钮时，请确保 `AcquireTokenAsync(...)` 的后续调用将要求用户登录。 你可以通过清除令牌缓存轻松使用 ADAL 执行此操作：

    ```csharp
    private void SignOut(object sender = null, RoutedEventArgs args = null)
    {
        // Clear the token cache
        authContext.TokenCache.Clear();

        ...
    }
    ```

    如果用户未单击“注销”按钮，则需要保留用户的会话，以在他们下次运行 DirectorySearcher 时使用。 当应用程序启动时，可以检查现有令牌的 ADAL 令牌缓存，并相应地更新 UI。

1. 在 `CheckForCachedToken()` 方法中，再次调用 `AcquireTokenAsync(...)`，此次传入 `PromptBehavior.Never` 参数。 `PromptBehavior.Never` 将告知 ADAL 不应提示用户登录；如果 ADAL 无法返回令牌，则应引发异常。

    ```csharp
    public async void CheckForCachedToken() 
    {
        // As the application starts, try to get an access token without prompting the user. If one exists, show the user as signed in.
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "user_interaction_required")
            {
                // An unexpected error occurred.
                MessageBox.Show(ex.Message);
            }

            // If user interaction is required, proceed to main page without singing the user in.
            return;
        }

        // A valid token is in the cache
        SignOutButton.Visibility = Visibility.Visible;
        UserNameLabel.Content = result.UserInfo.DisplayableId;
    }
    ```

祝贺你！ 现在，你已有了一个可正常工作的 .NET WPF 应用程序，它可以对用户进行身份验证，使用 OAuth 2.0 安全地调用 Web API，并获取有关用户的基本信息。 如果尚未这样做，可以在租户中填充一些用户。 运行 DirectorySearcher 应用程序，并使用这些用户之一进行登录。 根据用户的 UPN 搜索其他用户。 关闭应用，并重新运行应用。 请注意，用户的会话将保持不变。 注销，并以其他用户身份重新登录。

使用 ADAL 可以方便地将这些常见标识功能整合到应用程序中。 它会负责所有的繁琐工作 - 包括缓存管理、OAuth 协议支持、向户显示登录名 UI、刷新已过期的令牌，等等。 只需要真正了解一个 API 调用，即 `authContext.AcquireTokenAsync(...)`。

有关参考，请参阅 [GitHub 上的](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip)已完成示例（不含你的配置值）。

## <a name="next-steps"></a>后续步骤

了解如何使用 OAuth 2.0 持有者访问令牌保护 Web API。
> [!div class="nextstepaction"]
> [使用 Azure AD 保护 .NET Web API >>](quickstart-v1-dotnet-webapi.md)
