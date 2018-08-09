---
title: Azure AD v2 UWP 入门 | Microsoft Docs
description: 通用 Windows 平台应用程序 (UWP) 如何通过 Azure Active Directory v2 终结点调用需要访问令牌的 API
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
ms.date: 04/20/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 48d03b590d718cf82d692dc177a69f6d54de43d1
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495911"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>从通用 Windows 平台应用程序 (XAML) 调用 Microsoft 图形 API

本指南介绍本机通用 Windows 平台 (UWP) 应用程序如何请求访问令牌，然后调用 Microsoft 图形 API。 本指南也适用于其他需要从 Azure Active Directory v2 终结点请求访问令牌的 API。

在本指南结束时，应用程序将使用个人帐户调用受保护的 API。 示例包括 outlook.com、live.com 等等。 应用程序还将调用任何使用 Azure Active Directory 的公司或组织提供的工作和学校帐户。

>[!NOTE]
> 本指南需要安装了通用 Windows 平台开发的 Visual Studio 2017。 请参阅[设置](https://docs.microsoft.com/windows/uwp/get-started/get-set-up)，获取有关如何下载和配置 Visual Studio 以开发通用 Windows 平台应用的说明。

### <a name="how-this-guide-works"></a>本指南的工作原理

![本指南工作原理的示意图](./media/tutorial-v2-windows-uwp/uwp-intro.png)

本指南创建的示例 UWP 应用程序查询从 Azure Active Directory v2 终结点接受令牌的 Microsoft 图形 API 或 Web API。 在此方案中，通过 Authorization 标头向 HTTP 请求添加了令牌。 Microsoft 身份验证库 (MSAL) 处理令牌获取和续订。

### <a name="nuget-packages"></a>NuGet 包

本指南使用以下 NuGet 包：

|库|Description|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft 身份验证库|


## <a name="set-up-your-project"></a>设置项目

本部分逐步说明如何将 Windows 桌面 .NET 应用程序 (XAML) 与“登录 Microsoft”集成。 然后，该应用程序可以查询需要令牌的 Web API（例如 Microsoft 图形 API）。

本指南创建的应用程序显示用来查询图形 API 的按钮、注销按钮和显示调用结果的文本框。

>[!NOTE]
> 想要改为下载此示例的 Visual Studio 项目？ [下载项目](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip)并跳到[应用程序注册](#register-your-application "应用程序注册步骤")步骤，在运行代码示例前对其进行配置。


### <a name="create-your-application"></a>创建应用程序
1. 在 Visual Studio 中，选择“文件” > “新建” > “项目”。
2. 在“模板”下，选择“Visual C#”。
3. 选择“空白应用(通用 Windows)”。
4. 为应用命名，然后选择“确定”。
5. 如果出现提示，请选择任意版本作为“目标”版本和“最低”版本，然后选择“确定”。

    >![最低版本和目标版本](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>向项目添加 Microsoft 身份验证库
1. 在 Visual Studio 中，选择“工具” > “NuGet 包管理器” > “包管理器控制台”。
2. 在“包管理器控制台”窗口中复制并粘贴以下命令：

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

> [!NOTE]
> 此命令将安装 [Microsoft 身份验证库](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)。 MSAL 获取、缓存和刷新用于访问受 Azure Active Directory v2 保护的 API 的用户令牌。

## <a name="initialize-msal"></a>初始化 MSAL
此步骤帮助创建用于处理与 MSAL 的交互（例如处理令牌）的类。

1. 打开 **App.xaml.cs** 文件，将 MSAL 的引用添加到该类：

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. 将以下两行添加到应用的类（在 <code>sealed partial class App : Application</code> 块内）：

    ```csharp
    // Below is the clientId of your app registration. 
    // You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

## <a name="create-your-applications-ui"></a>创建应用程序 UI

系统会自动创建 **MainPage.xaml** 文件作为项目模板的一部分。 打开此文件，并遵照说明操作：

* 将应用程序的 **Grid** 节点替换为以下代码：

    ```xml
    <Grid>
        <StackPanel Background="Azure">
            <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
                <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
                <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
            </StackPanel>
            <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
            <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
        </StackPanel>
    </Grid>
    ```
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>使用 MSAL 获取 Microsoft 图形 API 的令牌

本部分介绍如何使用 MSAL 获取 Microsoft 图形 API 的令牌。

1.  在 **MainPage.xaml.cs** 中，将 MSAL 的引用添加到该类：

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. 将 <code>MainPage</code> 类的代码替换为以下代码：

    ```csharp
    public sealed partial class MainPage : Page
    {
        // Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";
    
        // Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };
    
        public MainPage()
        {
            this.InitializeComponent();
        }
    
        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;
    
            try
            {
                authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, App.PublicClientApp.Users.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
    
                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
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
    }
    ```

### <a name="more-information"></a>详细信息
#### <a name="get-a-user-token-interactively"></a>以交互方式获取用户令牌
调用 `AcquireTokenAsync` 方法将出现提示用户进行登录的窗口。 当用户首次需要访问受保护的资源时，应用程序通常会要求用户进行交互式登录。 当用于获取令牌的无提示操作失败时，用户也可能需要登录。 例如，当用户的密码过期时。

#### <a name="get-a-user-token-silently"></a>以无提示方式获取用户令牌
`AcquireTokenSilentAsync` 方法处理令牌获取和续订，无需进行任何用户交互。 首次执行 `AcquireTokenAsync` 并提示用户输入凭据后，应使用 `AcquireTokenSilentAsync` 方法请求后续调用的令牌，因为此方法以无提示方式获取令牌。 MSAL 将处理令牌缓存和续订。

最终，`AcquireTokenSilentAsync` 方法会失败。 失败可能是因为用户已注销，或者在另一设备上更改了密码。 MSAL 检测到可以通过请求交互式操作解决问题时，它将引发 `MsalUiRequiredException` 异常。 应用程序可以通过两种方式处理此异常：

* 它可以立即调用 `AcquireTokenAsync`。 此调用会导致系统提示用户进行登录。 此模式通常用于联机应用程序，这类应用程序中没有可供用户使用的脱机内容。 此引导式设置生成的示例遵循此模式。 首次运行示例时可以看到其正在运行。 
    * 由于没有用户使用过该应用程序，因此 `PublicClientApp.Users.FirstOrDefault()` 包含一个 null 值，并且引发 `MsalUiRequiredException` 异常。
    * 此示例中的代码随后通过调用 `AcquireTokenAsync` 来处理此异常。 此调用会导致系统提示用户进行登录。

* 或者，它会向用户呈现视觉指示，要求用户进行交互式登录。 然后，用户可以选择适当的时间进行登录。 也可以让应用程序稍后重试 `AcquireTokenSilentAsync`。 当用户可以使用其他应用程序功能而不导致中断时，会频繁使用此模式。 例如，当脱机内容在应用程序中可用时。 在这种情况下，用户可以决定何时需要登录来访问受保护的资源或刷新过期信息。 或者，可让应用程序决定在网络临时不可用又还原后，是否重试 `AcquireTokenSilentAsync`。

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>使用刚获得的令牌调用 Microsoft 图形 API

* 将以下新方法添加到 **MainPage.xaml.cs**。 此方法用于通过 [Authorize] 标头对图形 API 执行 `GET` 请求：

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
            // Add the token in Authorization header
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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>对受保护 API 进行 REST 调用的详细信息

在此示例应用程序中，使用 `GetHttpContentWithToken` 方法对需要令牌的受保护资源发出 HTTP `GET` 请求。 然后，此方法将内容返回给调用方。 此方法在 **HTTP Authorization** 标头中添加获取的令牌。 此示例中的资源是 Microsoft Graph API **me** 终结点，用于显示用户的个人资料信息。
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>添加方法以注销用户

* 若要注销用户，请将以下方法添加到 **MainPage.xaml.cs**：

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

### <a name="more-information-on-sign-out"></a>有关注销的详细信息

`SignOutButton_Click` 方法从 MSAL 用户缓存中删除用户。 此方法有效告知 MSAL 要忘记当前用户。 然后，仅当获取令牌的未来请求设置为交互式时，该请求才会成功。
此示例中的应用程序支持单个用户。 但是，MSAL 也支持可同时登录多个帐户的方案。 用户在其中具有多个帐户的电子邮件应用程序就是一个示例。

## <a name="display-basic-token-information"></a>显示基本令牌信息

* 将以下方法添加到 **MainPage.xaml.cs**，以显示有关令牌的基本信息：

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

### <a name="more-information"></a>详细信息

通过 OpenID 连接获取的 ID 令牌还包含与用户相关的一小部分信息。 `DisplayBasicTokenInfo` 显示令牌中包含的基本信息。 例如，显示用户的显示名称和 ID、令牌过期日期，以及表示访问令牌本身的字符串。 如果多次选择“调用 Microsoft 图形 API”按钮，将会发现后续请求重复使用了同一令牌。 而且还会发现，在 MSAL 决定续订令牌时，过期日期也延长了。

## <a name="register-your-application"></a>注册应用程序

现在需要在 Microsoft 应用程序注册门户中注册应用程序：
1. 转到 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app)注册应用程序。
2. 输入应用程序的名称。
3. 确保未选中“引导式设置”选项。
4. 依次选择“添加平台”、“本机应用程序”和“保存”。
5. 复制“应用程序 ID”中的 GUID，返回 Visual Studio，打开 **App.xaml.cs**，并将 `your_client_id_here` 替换为刚刚注册的应用程序 ID：

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>在联盟域中启用集成身份验证（可选）

与 Azure Active Directory 联合域一起使用时，若要启用 Windows 集成身份验证，应用程序清单必须启用其他功能：

1. 双击“Package.appxmanifest”。
2. 选择“功能”选项卡并确保启用以下设置：

    - 企业身份验证
    - 专用网络(客户端和服务器)
    - 共享用户证书

3. 打开 **App.xaml.cs**，并在应用构造函数中添加以下行：

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> 默认情况下，未为此示例配置 Windows 集成身份验证。 请求“企业身份验证”或“共享用户证书”功能的应用程序需要由 Windows 应用商店进行的更高级别的验证。 此外，并非所有开发人员都希望执行更高级别的验证。 仅当需要使用 Azure Active Directory 联合域进行 Windows 集成身份验证时，才启用此设置。


## <a name="test-your-code"></a>测试代码

若要测试应用程序，请按 F5 在 Visual Studio 中运行项目。 将显示主窗口：

![应用程序的用户界面](./media/tutorial-v2-windows-uwp/testapp-ui.png)

准备好测试后，选择“调用 Microsoft Graph API”。 然后使用 Microsoft Azure Active Directory 组织帐户或 Microsoft 帐户（live.com 或 outlook.com）登录。 如果是首次测试，会看到要求用户登录的窗口：

![登录页](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>同意
首次登录应用程序时，会看到如下所示的许可屏幕。 选择“是”显式许可访问：

![访问许可屏幕](./media/tutorial-v2-windows-uwp/consentscreen.png)
### <a name="expected-results"></a>预期结果
“API 调用结果”屏幕上会显示 Microsoft 图形 API 调用返回的用户个人资料信息：

![“API 调用结果”屏幕](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

“令牌信息”框中还会显示通过 `AcquireTokenAsync` 或 `AcquireTokenSilentAsync` 获得的令牌的相关基本信息：

|属性  |格式  |Description |
|---------|---------|---------|
|**Name** |用户全名|用户的名字和姓氏。|
|**用户名** |<span>user@domain.com</span> |用于标识用户的用户名。|
|**令牌到期** |DateTime |令牌的过期时间。 MSAL 根据需要通过续订令牌来延长到期日期。|
|**访问令牌** |String |发送到需要授权标头的 HTTP 请求的令牌字符串。|

#### <a name="see-whats-in-the-access-token-optional"></a>查看访问令牌中的信息（可选）
（可选）复制“访问令牌”中的值，并将其粘贴到 https://jwt.ms 中以对其进行解码并查看声明列表。

### <a name="more-information-about-scopes-and-delegated-permissions"></a>有关作用域和委派权限的详细信息

Microsoft 图形 API 需要 *user.read* 作用域来读取用户的个人资料。 默认情况下，在应用程序注册门户中注册的每个应用程序中，都会自动添加此作用域。 Microsoft Graph 的其他 API 以及后端服务器的自定义 API 可能需要其他作用域。 Microsoft 图形 API 需要 *Calendars.Read* 作用域来列出用户的日历。

若要在应用程序上下文中访问用户的日历，请将 *Calendars.Read* 委派权限添加到应用程序注册信息。 然后，将 *Calendars.Read* 作用域添加到 `acquireTokenSilent` 调用。 

> [!NOTE]
> 增加作用域数量时，用户可能会收到接受其他许可的提示。

## <a name="known-issues"></a>已知问题

### <a name="issue-1"></a>问题 1
在 Azure Active Directory 联合域上登录应用程序时，可能会收到以下错误消息之一：
 - 在请求中未找到有效的客户端证书。
 - 在用户的证书存储中未找到有效的证书。
 - 请重试选择不同的身份验证方法。

**原因：** 未启用企业功能和证书功能。

**解决方法：** 遵循[联合域中的集成身份验证](#enable-integrated-authentication-on-federated-domains-optional)中的步骤。

### <a name="issue-2"></a>问题 2
[在联合域中启用集成身份验证](#enable-integrated-authentication-on-federated-domains-optional)，并尝试在 Windows 10 计算机上使用 Windows Hello 登录到配置了多重身份验证的环境。 此时会显示证书列表。 但如果选择使用 PIN，则永远不会显示 PIN 窗口。

**原因：** 在 Windows 10 桌面版上运行的 UWP 应用程序中的 Web 身份验证代理存在已知限制。 该代理在 Windows 10 手机版上可正常工作。

**解决方法：** 选择“使用其他选项登录”。 然后选择“使用用户名和密码登录”。 选择“提供密码”。 然后完成手机身份验证过程。
