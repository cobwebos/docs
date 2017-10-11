---
title: "Azure AD v2 Windows 桌面获取已启动 – 使用 |Microsoft 文档"
description: "如何 Windows 桌面.NET (XAML) 应用程序可以调用一个 API，由 Azure Active Directory v2 终结点需要访问令牌"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 826ba0a00b26993d4f37f0a8ce587d7bb77e7eb4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>使用 Microsoft 身份验证库 (MSAL) 来获取用于 Microsoft Graph API 的令牌

本部分说明如何使用 MSAL 获取 Microsoft Graph API 的令牌。

1.  在`MainWindow.xaml.cs`，将 MSAL 库的引用添加到类：

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
替换<code>MainWindow</code>类使用的代码：
</li>
</ol>

```csharp
public partial class MainWindow : Window
{
    //Set the API Endpoint to Graph 'me' endpoint
    string _graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

    //Set the scope for API call to user.read
    string[] _scopes = new string[] { "user.read" };


    public MainWindow()
    {
        InitializeComponent();
    }

    /// <summary>
    /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
    /// </summary>
    private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
    {
        AuthenticationResult authResult = null;

        try
        {
            authResult = await App.PublicClientApp.AcquireTokenSilentAsync(_scopes, App.PublicClientApp.Users.FirstOrDefault());
        }
        catch (MsalUiRequiredException ex)
        {
            // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
            System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

            try
            {
                authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
            }
            catch (MsalException msalex)
            {
                ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
            }
        }
        catch (Exception ex)
        {
            ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
            return;
        }

        if (authResult != null)
        {
            ResultText.Text = await GetHttpContentWithToken(_graphAPIEndpoint, authResult.AccessToken);
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>详细信息
#### <a name="getting-a-user-token-interactive"></a>获取用户的令牌交互式
调用`AcquireTokenAsync`方法都可产生在窗口中提示用户登录。 应用程序通常要求用户以交互方式登录他们需要访问受保护的资源的第一个时间或在无提示操作以获取令牌失败 （例如用户的密码已过期）。

#### <a name="getting-a-user-token-silently"></a>获取用户令牌以无提示方式
`AcquireTokenSilentAsync`处理令牌收购和无需任何用户交互的续订。 后`AcquireTokenAsync`首次执行`AcquireTokenSilentAsync`是用来获取用于访问受保护的资源，以便后续调用-以无提示方式进行调用来请求或续订令牌的令牌的常用方法。
最终，`AcquireTokenSilentAsync`将失败 – 例如，用户已注销，或已更改其密码在另一台设备上的。 当 MSAL 检测到可以通过要求交互式操作解决此问题，则激发`MsalUiRequiredException`。 你的应用程序可以处理此异常两种方式：

1.  针对调用`AcquireTokenAsync`立即，这将导致在提示用户进行登录。 在联机应用程序通常使用此模式中的任何脱机内容应用程序中可用的用户。 此指导安装程序生成的示例使用此模式： 你可以在第一个操作时间执行示例中看到： 因为没有用户曾经使用该应用程序，`PublicClientApp.Users.FirstOrDefault()`将包含空值，和`MsalUiRequiredException`将引发异常。 此示例中的代码然后通过调用处理异常`AcquireTokenAsync`导致提示用户进行登录。

2.  应用程序还可以对交互式登录是必需的因此用户可以选择适当的时间进行登录，或应用程序可以重试的用户发出的可视指示`AcquireTokenSilentAsync`在更高版本时。 通常不被中断的情况下，用户可以使用应用程序的其他功能-例如，脱机内容中提供了应用程序时使用。 在这种情况下，用户可以决定当他们想要登录来访问受保护的资源，或刷新过期的信息，或者你的应用程序可以决定重试`AcquireTokenSilentAsync`网络暂时变得不可用后的还原时。
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>调用 Microsoft Graph API 使用刚刚获得的令牌

1. 添加新方法下面你`MainWindow.xaml.cs`。 方法用于进行`GET`针对使用授权标头的 Graph API 请求：

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```
<!--start-collapse-->
### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>针对受保护 API REST 调用的详细信息

在此示例应用程序中，`GetHttpContentWithToken`方法用于发出 HTTP`GET`针对受保护资源需要使用令牌请求，然后返回到调用方的内容。 此方法将获取的令牌中添加*HTTP 授权标头*。 对于此示例中，资源是 Microsoft Graph API*我*终结点 – 显示用户的配置文件信息。
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>添加一个方法来注销用户

1. 添加以下方法你`MainWindow.xaml.cs`注销用户：

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    if (App.PublicClientApp.Users.Any())
    {
        try
        {
            App.PublicClientApp.Remove(App.PublicClientApp.Users.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>注销的详细信息

`SignOutButton_Click`用户从缓存中移除 MSAL 用户 – 这有效地将告知 MSAL 忘记当前用户，如果要使其为交互式的后续请求来获取令牌，将只会成功。
虽然此示例中的应用程序支持单个用户，但 MSAL 支持的方案，同时多个帐户可以登录的 – 一个示例是电子邮件应用程序，其中用户都具有多个帐户。
<!--end-collapse-->

## <a name="display-basic-token-information"></a>显示基本令牌信息

1. 添加以下方法向你`MainWindow.xaml.cs`以显示有关令牌的基本信息：

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Name: {authResult.User.Name}" + Environment.NewLine;
        TokenInfoText.Text += $"Username: {authResult.User.DisplayableId}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
    }
}
```
<!--start-collapse-->
### <a name="more-information"></a>详细信息

令牌获取通过*OpenID Connect*还包含与用户相关的信息的一小部分。 `DisplayBasicTokenInfo`显示包含在令牌中的基本信息： 例如，用户的显示名称和 ID，以及令牌的过期日期和表示的访问的字符串标记本身。 查看显示此信息。 可以命中*调用 Microsoft Graph API*按钮多次并看到的相同标记已的后续请求重用。 你还可以查看当 MSAL 决定它时要扩展是时间来续订令牌的到期日期。
<!--end-collapse-->

