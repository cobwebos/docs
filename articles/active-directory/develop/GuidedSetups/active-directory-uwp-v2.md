---
title: Azure AD v2 UWP 入门 | Microsoft Docs
description: 通用 Windows 平台 (XAML) 应用程序如何通过 Azure Active Directory v2 终结点调用需要访问令牌的 API
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/20/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 390559922b3b8fb293d1c8b38f36dfd0a1df9ebd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763367"
---
# <a name="call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>从通用 Windows 平台 (UWP) 应用程序调用 Microsoft Graph API

本指南演示本机通用 Windows 平台 (XAML) 应用程序如何从 Azure Active Directory v2 终结点获取访问令牌，然后使用此访问令牌调用 Microsoft Graph API 或需要访问令牌的其他 API。

在本指南结束时，应用程序将能使用个人帐户（包括 outlook.com、live.com 和其他帐户）以及具有 Azure Active Directory 的任何公司或组织的工作和学校帐户调用受保护的 API。  

> 本指南需要安装了通用 Windows 平台开发的 Visual Studio 2017。 请查看[此文](https://docs.microsoft.com/windows/uwp/get-started/get-set-up "为 UWP 设置 Visual Studio")，获取有关如何下载和配置 Visual Studio 以开发通用 Windows 平台应用的说明。

### <a name="how-this-guide-works"></a>本指南的工作原理

![本指南的工作原理](media/active-directory-mobileanddesktopapp-windowsuniversalplatform-introduction/uwp-intro.png)

本指南创建的示例应用程序允许 UWP 应用查询从 Azure Active Directory v2 终结点接受令牌的 Microsoft Graph API 或 Web API。 在此方案中，通过 Authorization 标头向 HTTP 请求添加了令牌。 由 Microsoft 身份验证库 (MSAL) 处理令牌获取和续订。

### <a name="nuget-packages"></a>NuGet 包

本指南使用以下 NuGet 包：

|库|说明|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft 身份验证库 (MSAL)|


## <a name="set-up-your-project"></a>设置项目

本部分提供分步说明，介绍如何将 Windows 桌面 .NET 应用程序 (XAML) 与“登录 Microsoft”集成，使其能查询需要令牌的 Web API（例如 Microsoft Graph API）。

本指南所创建的应用程序显示用来查询图形 API 的按钮、注销按钮和显示调用结果的文本框。

> 想要改为下载此示例的 Visual Studio 项目？ [下载项目](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip)并跳到[应用程序注册](#register-your-application "应用程序注册步骤")步骤，在执行代码示例前对其进行配置。


### <a name="create-your-application"></a>创建应用程序
1. 在 Visual Studio 中：单击“文件” > “新建” > “项目”<br/>
2. 在“模板”下，选择“Visual C#”
3. 选择“空白应用(通用 Windows)”
4. 为该应用程序提供一个名称，并单击“确定”。
5. 如果出现提示，请随意选择任何版本作为*目标*版本和*最低*版本，并单击“确定”：<br/><br/>![最低版本和目标版本](media/active-directory-uwp-v2.md/vs-minimum-target.png)

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>向项目添加 Microsoft 身份验证库 (MSAL)
1. 在 Visual Studio 中：单击“工具” > “NuGet 包管理器” > “包管理器控制台”
2. 在“包管理器控制台”窗口中复制/粘贴以下命令：

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

> [!NOTE]
> 以上包将安装 [Microsoft 身份验证库 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)。 MSAL 负责获取、缓存和刷新用于访问受 Azure Active Directory v2 保护的 API 的用户令牌。

## <a name="initialize-msal"></a>初始化 MSAL
此步骤可帮助你创建用于处理与 MSAL 库的交互（例如处理令牌）的类。

1. 打开 **App.xaml.cs** 文件，将 MSAL 库的引用添加到类：

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

项目模板中应自动创建了 **MainPage.xaml** 文件。 打开此文件，并按照说明进行操作：

1.  将应用程序的 **<Grid>** 节点替换为：

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
    
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>使用 Microsoft 身份验证库 (MSAL) 获取用于 Microsoft Graph API 的令牌

本部分介绍如何使用 MSAL 获取 Microsoft Graph API 的令牌。

1.  在 **MainPage.xaml.cs** 中，将 MSAL 库的引用添加到类：

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. 将 <code>MainPage</code> 类的代码替换为：

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

### <a name="more-information"></a>更多信息
#### <a name="get-a-user-token-interactively"></a>以交互方式获取用户令牌
调用 `AcquireTokenAsync` 方法将出现提示用户登录的窗口。 当用户首次需要访问受保护的资源时，应用程序通常会要求用户进行交互式登录。 当用于获取令牌的无提示操作失败时（例如，当用户的密码过期时），用户也可能需要登录。

#### <a name="get-a-user-token-silently"></a>以无提示方式获取用户令牌
`AcquireTokenSilentAsync` 方法处理令牌获取和续订，无需进行任何用户交互。 首次执行 `AcquireTokenAsync` 后，通常使用 `AcquireTokenSilentAsync` 方法获得用于访问受保护资源的令牌，以便进行后续调用，因为调用请求或续订令牌都以无提示方式进行。

最终，`AcquireTokenSilentAsync` 方法会失败。 失败可能是因为用户已注销，或者在另一设备上更改了密码。 MSAL 检测到可以通过请求交互式操作解决问题时，它将引发 `MsalUiRequiredException` 异常。 应用程序可以通过两种方式处理此异常：

* 它可以立即调用 `AcquireTokenAsync`。 此调用会导致系统提示用户进行登录。 此模式通常用于联机应用程序，这类应用程序中没有可供用户使用的脱机内容。 此指导式设置生成的示例遵循此模式，第一次执行示例时可以看到其正在运行。 
    * 由于没有用户使用过该应用程序，因此 `PublicClientApp.Users.FirstOrDefault()` 包含一个 null 值，并且引发 `MsalUiRequiredException` 异常。 
    * 此示例中的代码随后处理此异常，方法是通过调用 `AcquireTokenAsync` 使其显示用户登录提示。

* 它可以改为向用户呈现视觉指示，要求用户进行交互式登录，以便他们可以选择适当的时间进行登录。 也可以让应用程序稍后重试 `AcquireTokenSilentAsync`。 当用户可以使用其他应用程序功能而不导致中断时（例如，当脱机内容在应用程序中可用时），会频繁使用此模式。 在这种情况下，用户可以决定何时需要登录来访问受保护的资源或刷新过期信息。 也可让应用程序决定在网络临时不可用又还原后，是否重试 `AcquireTokenSilentAsync`。

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>使用刚获得的令牌调用 Microsoft Graph API

1. 将以下新方法添加到 **MainPage.xaml.cs**。 该方法用于使用授权标头对图形 API 执行 `GET` 请求：

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

在此示例应用程序中，使用 `GetHttpContentWithToken` 方法对需要令牌的受保护资源发出 HTTP `GET` 请求，并将内容返回给调用方。 此方法可在 HTTP 授权标头中添加获取的令牌。 此示例中的资源是 Microsoft Graph API me 终结点 - 显示用户个人资料信息。
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>添加方法以注销用户

1. 若要注销用户，请将以下方法添加到 **MainPage.xaml.cs**：

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

### <a name="more-info-on-sign-out"></a>注销详细信息

方法 `SignOutButton_Click` 会从 MSAL 用户缓存中删除用户 - 这实际上告知 MSAL 忘记当前用户，以便以后成功执行获取令牌的交互式请求。
此示例中的应用程序支持单个用户，但 MSAL 也支持可同时注册多个帐户的方案（例如，用户可以在一个电子邮件应用程序包含多个帐户）。

## <a name="display-basic-token-information"></a>显示基本令牌信息

1. 将以下方法添加到 **MainPage.xaml.cs**，以便显示有关令牌的基本信息：

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

### <a name="more-information"></a>更多信息

通过 OpenID 连接获取的 ID 令牌还包含与用户相关的一小部分信息。 `DisplayBasicTokenInfo` 显示令牌中包含的基本信息：例如，用户的显示名称和 ID，以及令牌到期日期和表示访问令牌本身的字符串。 显示此信息的目的是便于查看。 多次单击“调用 Microsoft 图形 API”按钮，用户便会发现后续请求使用了同一令牌。 而且还会注意到，在 MSAL 决定续订令牌时，到期日期也延长了。

## <a name="register-your-application"></a>注册应用程序

现在需要在 Microsoft 应用程序注册门户中注册应用程序：
1. 转到 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app)注册应用程序
2. 输入应用程序的名称 
3. 确保取消选中“指导式设置”选项
4. 单击“添加平台”，然后选择“本机应用程序”并点击“保存”
5. 复制应用程序 ID 中的 GUID，返回 Visual Studio，打开 **App.xaml.cs**，并用刚才注册的应用程序 ID 替换 `your_client_id_here`：

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>在联盟域中启用集成身份验证（可选）

与 Azure Active Directory 联盟域一起使用时，若要启用 Windows 集成身份验证，应用程序清单必须启用其他功能：

1. 双击 **Package.appxmanifest**
2. 选择“功能”选项卡并确保启用以下设置：

    - 企业身份验证
    - 专用网络(客户端和服务器)
    - 共享用户证书 

3. 然后，打开 **App.xaml.cs**，并在应用构造函数中添加以下行：

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> 默认情况下，未为此示例配置 Windows 集成身份验证，因为请求*企业身份验证*或*共享用户证书*功能的应用程序需要由 Windows 应用商店进行的更高级别的验证，并非所有开发人员都希望执行更高级别的验证。 请仅当需要使用 Azure Active Directory 联盟域进行 Windows 集成身份验证时，才启用此设置。


## <a name="test-your-code"></a>测试代码

若要测试应用程序，请按 `F5` 在 Visual Studio 中运行项目。 主窗口应会出现：

![应用程序的用户界面](media/active-directory-uwp-v2.md/testapp-ui.png)

准备好进行测试时，单击“调用 Microsoft Graph API”，并使用 Microsoft Azure Active Directory（组织帐户）或 Microsoft Account（live.com、outlook.com）帐户登录。 如果是首次测试，会看到要求用户登录的窗口：

![登录页](media/active-directory-uwp-v2.md/sign-in-page.png)

### <a name="consent"></a>同意
用户首次登录应用程序时，会看到如下所示的许可屏幕，用户需要显式接受：

![许可屏幕](media/active-directory-uwp-v2.md/consentscreen.png)
### <a name="expected-results"></a>预期结果
API 调用结果屏幕上应显示 Microsoft Graph API 调用返回的用户配置文件信息：

![结果屏幕](media/active-directory-uwp-v2.md/uwp-results-screen.PNG)

“令牌信息”框中还应显示通过 `AcquireTokenAsync` 或 `AcquireTokenSilentAsync` 获得的令牌的相关基本信息：

|属性  |格式  |说明 |
|---------|---------|---------|
|**Name** |用户全名 |用户的名字和姓氏。|
|**用户名** |<span>user@domain.com</span> |用于标识用户的用户名。|
|**令牌到期** |DateTime |令牌到期的时间。 MSAL 根据需要通过续订令牌来延长到期日期。|
|**访问令牌** |String |发送到需要授权标头的 HTTP 请求的令牌字符串。|

#### <a name="see-what-is-in-the-access-token-optional"></a>查看访问令牌中的信息（可选）
（可选）可以复制访问令牌中的值，并将其粘贴到 https://jwt.ms 中以对其进行解码并查看声明列表。

### <a name="more-information-about-scopes-and-delegated-permissions"></a>有关作用域和委派权限的详细信息

Microsoft Graph API 需要 *user.read* 作用域来读取用户的个人资料。 默认情况下，在应用程序注册门户中注册的每个应用程序中，都会自动添加此作用域。 Microsoft Graph 的其他 API 以及后端服务器的自定义 API 可能需要其他作用域。 Microsoft Graph API 需要 *Calendars.Read* 作用域来列出用户的日历。

若要在应用程序上下文中访问用户的日历，请将 *Calendars.Read* 委派权限添加到应用程序注册信息。 然后，将 *Calendars.Read* 作用域添加到 `acquireTokenSilent` 调用。 

> [!NOTE]
> 增加作用域数量时，用户可能会收到接受其他许可的提示。

## <a name="known-issues"></a>已知问题

### <a name="issue-1"></a>问题 1：
在 Azure Active Directory 联盟域上登录应用程序时，可能会收到以下错误之一：
 - 在请求中未找到有效的客户端证书。
 - 在用户的证书存储中未找到有效的证书。
 - 请重试选择不同的身份验证方法。

**原因：** 未启用企业功能和证书功能

**解决方案：** 按照[联盟域中的集成身份验证](#enable-integrated-authentication-on-federated-domains-optional)中的步骤操作

### <a name="issue-2"></a>问题 2：
[在联盟域中启用集成身份验证](#enable-integrated-authentication-on-federated-domains-optional)后，如果尝试在 Windows 10 计算机上使用 Windows Hello 登录到配置了多重身份验证的环境，将显示证书列表，但是如果选择使用 PIN，则永远不会显示 PIN 窗口。

**原因：** 在 Windows 10 桌面版上运行的 UWP 应用程序中的 Web身份验证中转站存在已知限制（在 Windows 10 移动版上正常工作）

**解决方法：** 用户需要选择“使用其他选项登录”，然后改为选择“使用用户名和密码登录”，提供密码，然后完成手机身份验证。

