---
title: Azure Active Directory B2C | Microsoft Docs
description: "介绍如何使用 Azure Active Directory B2C 构建 Windows 桌面应用程序，其中包括登录、注册和配置文件管理。"
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 9da14362-8216-4485-960e-af17cd5ba3bd
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/22/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: a977cb509fb64d7c986e2e0f7e2b5e4e3e45dec0
ms.openlocfilehash: 9a78daac2269c9d44558e92b80c869603e014fb6


---
# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Azure AD B2C：构建 Windows 桌面应用
通过使用 Azure Active Directory (Azure AD) B2C，只需几个简短的步骤即可将强大的自助服务标识管理功能添加到桌面应用。 本文将说明如何创建 .NET Windows Presentation Foundation (WPF)“待办事项列表”应用，其中包括用户注册、登录和配置文件管理。 该应用将支持使用用户名或电子邮件进行注册和登录。 该应用还将支持使用社交帐户（如 Facebook 和 Google）进行注册和登录。

## <a name="get-an-azure-ad-b2c-directory"></a>获取 Azure AD B2C 目录
只有在创建目录或租户之后，才可使用 Azure AD B2C。  目录是所有用户、应用、组等对象的容器。 如果没有容器，请先 [创建 B2C 目录](active-directory-b2c-get-started.md) ，然后继续执行本指南中的步骤。

## <a name="create-an-application"></a>创建应用程序
接下来，需要在 B2C 目录中创建应用。 此应用为 Azure AD 提供所需的 Azure AD 信息，使之能够与应用安全通信。 若要创建应用，请遵循 [这些说明](active-directory-b2c-app-registration.md)。  请务必：

* 在应用程序中包括**本机客户端**。
* 复制**重定向 URI** `urn:ietf:wg:oauth:2.0:oob`。 它是此代码示例的默认 URL。
* 复制分配给应用的 **应用程序 ID** 。 稍后需要用到此值。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>创建策略
在 Azure AD B2C 中，每个用户体验由 [策略](active-directory-b2c-reference-policies.md)定义。 本代码示例包含三个标识体验：注册、登录和编辑配置文件。 需要按照[策略参考文章](active-directory-b2c-reference-policies.md#create-a-sign-up-policy)中所述，为每个类型创建一个策略。 创建三个策略时，请务必：

* 在标识提供者边栏选项卡中，选择“用户 ID 注册”或“电子邮件注册”。
* 在注册策略中，选择“显示名称”和其他注册属性。
* 针对每个策略选择“显示名称”和“对象 ID”声明作为应用程序声明。 也可以选择其他声明。
* 创建每个策略后，请复制策略的 **名称** 。 其前缀应为 `b2c_1_`。  稍后需要用到这些策略名称。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

成功创建三个策略后，可以开始构建应用。

## <a name="download-the-code"></a>下载代码
本教程的代码 [保留在 GitHub 上](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet)。 若要根据说明构建示例，请 [下载 .zip 文件格式的骨干项目](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip)。 也可以克隆骨干项目：

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

完成的应用也[作为 .zip 文件提供](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip)，或者放在同一存储库的 `complete` 分支中。

下载示例代码后，打开 Visual Studio .sln 文件开始处理。 `TaskClient` 项目是用户与之交互的 WPF 桌面应用程序。 出于本教程的目的，它将调用 Azure 中托管的后端任务 Web API，用于存储用户的每个待办事项列表。  无需构建 Web API，我们已为你运行它。

若要了解 Web API 如何使用 Azure AD B2C 安全验证请求，请查看 [Web API 快速入门文章](active-directory-b2c-devquickstarts-api-dotnet.md)。

## <a name="execute-policies"></a>执行策略
应用通过发送身份验证消息与 Azure AD B2C 进行通信，该消息指定要作为 HTTP 请求的一部分进行执行的策略。 对于 .NET 桌面应用程序，可以使用预览版 Microsoft 身份验证库 (MSAL) 发送 OAuth 2.0 身份验证消息、执行策略并获取调用 Web API 的令牌。

### <a name="install-msal"></a>安装 MSAL
使用 Visual Studio 程序包管理器控制台将 MSAL 添加到 `TaskClient` 项目。

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>输入 B2C 详细信息
打开文件 `Globals.cs` 并将每个属性值替换为自己的值。 此类用于在整个 `TaskClient` 中引用常用值。

```C#
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="create-the-publicclientapplication"></a>创建 PublicClientApplication
MSAL 的主类是 `PublicClientApplication`。 此类用于表示 Azure AD B2C 系统中的应用程序。 初始化应用程序时，在 `MainWindow.xaml.cs` 中创建 `PublicClientApplication` 实例。 这可以在整个窗口中使用。

```C#
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app,
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };

    ...
```

### <a name="initiate-a-sign-up-flow"></a>启动注册流程
用户选择注册时，建议启动一个注册流程，该流程使用所创建的注册策略。 通过使用 MSAL，只需调用 `pca.AcquireTokenAsync(...)`。 传递给 `AcquireTokenAsync(...)` 的参数可确定接收的令牌类型、身份验证请求中所用的策略等。

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }

            MessageBox.Show(message);
        }

        return;
    }
}
```

### <a name="initiate-a-sign-in-flow"></a>启动登录流程
可以使用与启动注册流程相同的方式启动登录流程。 用户登录时，这一次是使用登录策略对 MSAL 执行相同调用：

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>启动编辑配置文件流程
同样，可以相同方式执行编辑配置文件策略：

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

在所有这些情况下，MSAL 将返回 `AuthenticationResult` 中的令牌或引发异常。 每次从 MSAL 获取令牌时，都可以使用 `AuthenticationResult.User` 对象更新应用中的用户数据，如 UI。 ADAL 还可缓存令牌，以便在应用程序的其他部件中使用。

### <a name="check-for-tokens-on-app-start"></a>应用启动时检查令牌
MSAL 还可用于跟踪用户的登录状态。  在此应用中，我们希望用户即使关闭并重新打开它，也能保持登录状态。  回到 `OnInitialized` 替代中，请使用 MSAL 的 `AcquireTokenSilent` 方法检查缓存的令牌：

```C#
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
    return;
}
```

## <a name="call-the-task-api"></a>调用任务 API
现在，你已使用 MSAL 执行策略并获取令牌。  如果想要使用以下令牌之一来调用任务 API，可以再次使用 MSAL 的 `AcquireTokenSilent` 方法检查缓存的令牌：

```C#
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
        }
        else
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }
            MessageBox.Show(message);
        }

        return;
    }
    ...
```

成功调用 `AcquireTokenSilentAsync(...)` 并在缓存中找到令牌时后，可以将该令牌添加到 HTTP 请求的 `Authorization` 标头。 任务 Web API 将使用此标头对读取用户的待办事项列表的请求进行身份验证：

```C#
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>注销用户
最后，用户选择“注销”时，可以使用 MSAL 结束用户与该应用的会话。  使用 MSAL 时，清除令牌缓存中的所有令牌即可完成此操作：

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>运行示例应用
最后，构建并运行示例。  使用电子邮件地址或用户名注册应用。 注销，然后以同一用户身份重新登录。 编辑该用户的配置文件。 注销并使用其他用户身份注册。

## <a name="add-social-idps"></a>添加社交 IDP
目前，该应用仅支持用户使用**本地帐户**进行注册和登录。 这些帐户是存储在 B2C 目录中使用用户名和密码的帐户。 通过使用 Azure AD B2C，无需更改任何代码即可添加对其他标识提供者 (IDP) 的支持。

要向应用添加社交 IDP，请按照这些文章中的详细说明开始操作。 对于要支持的每个 IDP，需要在该系统中注册一个应用程序并获取一个客户端 ID。

* [将 Facebook 设置为 IDP](active-directory-b2c-setup-fb-app.md)
* [将 Google 设置为 IDP](active-directory-b2c-setup-goog-app.md)
* [将 Amazon 设置为 IDP](active-directory-b2c-setup-amzn-app.md)
* [将 LinkedIn 设置为 IDP](active-directory-b2c-setup-li-app.md)

将身份提供者添加到 B2C 目录后，需要编辑你的三个策略以包含新的 IDP，如[策略参考文章](active-directory-b2c-reference-policies.md)中所述。 保存策略后，再次运行应用。 应该会在每个标识体验中看到新增的 IDP 作为登录和注册选项。

可以试验该策略并观察其对示例应用的影响。 添加或删除 IDP、操纵应用程序声明或更改注册属性。 试验持续进行，直到能够了解策略、身份验证请求和 MSAL 如何联系在一起时为止。

为方便参考，已完成的示例[以 .zip 文件形式提供](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip)。 也可以从 GitHub 克隆它：

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```



<!--HONumber=Nov16_HO3-->


