
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>使用 Microsoft 身份验证库 (MSAL) 获取用于 Microsoft Graph API 的令牌

本部分介绍了如何使用 MSAL 获取 Microsoft 图形 API 令牌。

1.  在 `MainWindow.xaml.cs` 中，将 MSAL 库的引用添加到类：

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
将 <code>MainWindow</code> 类代码替换为：
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
### <a name="more-information"></a>更多信息
#### <a name="getting-a-user-token-interactive"></a>以交互方式获取用户令牌
调用 `AcquireTokenAsync` 方法将出现提示用户登录的窗口。 用户首次访问受保护的资源或获取令牌的静默操作失败（如用户密码过期）时，应用程序通常会要求用户以交互方式登录。

#### <a name="getting-a-user-token-silently"></a>以静默方式获取用户令牌
`AcquireTokenSilentAsync` 处理令牌获取和续订，无需任何用户交互。 首次执行 `AcquireTokenAsync` 后，通常使用 `AcquireTokenSilentAsync` 方法获得用于访问受保护资源的令牌，以便进行后续调用 - 因为调用请求或续订令牌都以静默方式进行。
`AcquireTokenSilentAsync` 最终会失败（例如，用户已注销，或已在另一台设备上更改了密码）。 MSAL 检测到可以通过请求交互式操作解决问题时，它将引发 `MsalUiRequiredException`。 应用程序可以通过两种方式处理此异常：

1.  立即调用 `AcquireTokenAsync`，随后出现用户登录提示。 此模式通常用于联机应用程序，此时应用程序中没有可供用户使用的脱机内容。 此指导式设置生成的示例使用此模式：首次执行示例时可以在操作中看到此模式：由于没有用户曾使用过此应用程序，因此 `PublicClientApp.Users.FirstOrDefault()` 将包含一个 null 值，并且将引发 `MsalUiRequiredException` 异常。 此示例中的代码随后会处理此异常，方法是通过调用 `AcquireTokenAsync` 使其显示用户登录提示。

2.  应用程序还可以直观地提示用户以交互方式登录，用户可以选择在合适的时间登录，或者应用程序可以稍后重试 `AcquireTokenSilentAsync`。 如果用户可以在不中断应用程序的情况下（例如，应用程序中有可用的脱机内容）使用应用程序的其他功能，则通常会使用此方法。 在这种情况下，用户可以决定何时登录并访问受保护的资源，或何时刷新过期信息，或在网络暂时不可用得到还原后，应用程序可以决定重试 `AcquireTokenSilentAsync`。
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>使用刚获得的令牌调用 Microsoft Graph API

1. 将以下新方法添加到 `MainWindow.xaml.cs`。 该方法用于使用授权标头对图形 API 执行 `GET` 请求：

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
### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>对受保护 API 进行 REST 调用的详细信息

在此示例应用程序中，使用 `GetHttpContentWithToken` 方法对需要令牌的受保护资源发出 HTTP `GET` 请求，并将内容返回给调用方。 此方法可在 HTTP 授权标头中添加获取的令牌。 此示例中的资源是 Microsoft Graph API me 终结点 - 显示用户个人资料信息。
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>添加方法以注销用户

1. 将以下方法添加到 `MainWindow.xaml.cs` 可注销用户：

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
### <a name="more-info-on-sign-out"></a>注销详细信息

`SignOutButton_Click` 删除 MSAL 用户缓存中的用户 - 这会有效地告知 MSAL 忘记当前用户，以便以后成功执行获取令牌的交互式请求。
此示例中的应用程序支持单个用户，但 MSAL 也支持可同时注册多个帐户的方案（例如，用户可以在一个电子邮件应用程序包含多个帐户）。
<!--end-collapse-->

## <a name="display-basic-token-information"></a>显示基本令牌信息

1. 将以下方法添加到 `MainWindow.xaml.cs`，显示有关令牌的基本信息：

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
### <a name="more-information"></a>更多信息

通过 OpenID 连接获取的令牌还包含与用户相关的一小部分信息。 `DisplayBasicTokenInfo` 显示令牌中包含的基本信息：例如，用户的显示名称和 ID，以及令牌到期日期和表示访问令牌本身的字符串。 显示此信息的目的是便于查看。 多次单击“调用 Microsoft 图形 API”按钮，用户便会发现后续请求使用了同一令牌。 而且还会注意到，在 MSAL 决定续订令牌时，到期日期也延长了。
<!--end-collapse-->

