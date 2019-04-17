---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 3f72f6a5097221c904faff633b5a4ee5a6e023c1
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59532654"
---
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>使用 MSAL 获取 Microsoft Graph API 的令牌

本部分使用 MSAL 获取 Microsoft Graph API 的令牌。

1. 在 *MainWindow.xaml.cs* 文件中，将 MSAL 的引用添加到类：

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. 将 `MainWindow` 类代码替换为以下内容：

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };


        public MainWindow()
        {
            InitializeComponent();
        }

      /// <summary>
        /// Call AcquireToken - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();
            var firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await app.AcquireTokenSilent(scopes, firstAccount)
                    .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilent.
                // This indicates you need to call AcquireTokenInteractive to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await app.AcquireTokenInteractive(scopes)
                        .WithAccount(accounts.FirstOrDefault())
                        .WithPrompt(Prompt.SelectAccount)
                        .ExecuteAsync();
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
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    ```

<!--start-collapse-->
### <a name="more-information"></a>详细信息

#### <a name="get-a-user-token-interactively"></a>以交互方式获取用户令牌

调用 `AcquireTokenInteractive` 方法将出现提示用户登录的窗口。 当用户首次需要访问受保护的资源时，应用程序通常会要求用户进行交互式登录。 当用于获取令牌的无提示操作失败时（例如，当用户的密码过期时），用户也可能需要登录。

#### <a name="get-a-user-token-silently"></a>以无提示方式获取用户令牌

`AcquireTokenSilent` 方法处理令牌获取和续订，无需进行任何用户交互。 首次执行 `AcquireTokenInteractive` 后，通常使用 `AcquireTokenSilent` 方法获得用于访问受保护资源的令牌，以便进行后续调用，因为调用请求或续订令牌都以无提示方式进行。

最终，`AcquireTokenSilent` 方法会失败。 失败可能是因为用户已注销，或者在另一设备上更改了密码。 MSAL 检测到可以通过请求交互式操作解决问题时，它将引发 `MsalUiRequiredException` 异常。 应用程序可以通过两种方式处理此异常：

* 它可以立即调用 `AcquireTokenInteractive`。 此调用会导致系统提示用户进行登录。 此模式通常用于联机应用程序，这类应用程序中没有可供用户使用的脱机内容。 此指导式设置生成的示例遵循此模式，第一次执行示例时可以看到其正在运行。 

* 由于没有用户使用过该应用程序，因此 `PublicClientApp.Users.FirstOrDefault()` 包含一个 null 值，并且引发 `MsalUiRequiredException` 异常。 

* 此示例中的代码随后处理此异常，方法是通过调用 `AcquireTokenInteractive` 使其显示用户登录提示。

* 它可以改为向用户呈现视觉指示，要求用户进行交互式登录，以便他们可以选择适当的时间进行登录。 也可以让应用程序稍后重试 `AcquireTokenSilent`。 当用户可以使用其他应用程序功能而不导致中断时（例如，当脱机内容在应用程序中可用时），会频繁使用此模式。 在这种情况下，用户可以决定何时需要登录来访问受保护的资源或刷新过期信息。 也可让应用程序决定在网络临时不可用又还原后，是否重试 `AcquireTokenSilent`。
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>使用刚获得的令牌调用 Microsoft Graph API

将以下新方法添加到 `MainWindow.xaml.cs`。 该方法用于通过 Authorize 标头对图形 API 执行 `GET` 请求：

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
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>对受保护 API 进行 REST 调用的详细信息

在此示例应用程序中，请使用 `GetHttpContentWithToken` 方法对需要令牌的受保护资源发出 HTTP `GET` 请求，然后将内容返回给调用方。 此方法可在 HTTP 授权标头中添加获取的令牌。 此示例中的资源是 Microsoft Graph API me 终结点，可显示用户个人资料信息。
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>添加注销用户的方法

将以下方法添加到 `MainWindow.xaml.cs` 文件可注销用户：

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    var accounts = await App.PublicClientApp.GetAccountsAsync(); 

    if (accounts.Any())
    {
        try
        {
            await App.PublicClientApp.RemoveAsync(accounts.FirstOrDefault());
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
### <a name="more-information-about-user-sign-out"></a>有关用户注销的详细信息

`SignOutButton_Click` 方法删除 MSAL 用户缓存中的用户，这样可有效地告知 MSAL 忘记当前用户，使将来获取令牌的请求只能在交互模式下取得成功。

此示例中的应用程序支持单个用户，但 MSAL 也支持同时注册多个帐户的情况。 例如，用户可在电子邮件应用程序中使用多个帐户。
<!--end-collapse-->

## <a name="display-basic-token-information"></a>显示基本令牌信息

若要显示有关令牌的基本信息，请将以下方法添加到 *MainWindow.xaml.cs* 文件：

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Username: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>详细信息

除了用于调用 Microsoft Graph API 的访问令牌，MSAL 还可以在用户登录后获取 ID 令牌。 此令牌包含一小部分与用户相关的信息。 `DisplayBasicTokenInfo` 方法显示包含在令牌中的基本信息。 例如，它显示用户的显示名称和 ID，以及令牌到期日期和表示访问令牌本身的字符串。 多次选择“调用 Microsoft Graph API”按钮，便会发现后续请求使用了同一令牌。 而且还会注意到，在 MSAL 决定续订令牌时，到期日期也延长了。
<!--end-collapse-->
