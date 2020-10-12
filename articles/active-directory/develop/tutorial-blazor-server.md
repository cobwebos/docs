---
title: 教程 - 创建使用 Microsoft 标识平台进行身份验证的 Blazor Server 应用 | Azure
titleSuffix: Microsoft identity platform
description: 在本教程中，你将在 Blazor Server 应用中设置使用 Microsoft 标识平台的身份验证。
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: c696d8834c24a792432469bf7b1adffc87f718ba
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372960"
---
# <a name="tutorial-create-a-blazor-server-app-that-uses-the-microsoft-identity-platform-for-authentication"></a>教程：创建使用 Microsoft 标识平台进行身份验证的 Blazor Server 应用

Blazor 服务器在 ASP.NET Core 应用中添加了对在服务器上托管 Razor 组件的支持。 在本教程中，你将了解如何使用 Microsoft 标识平台在 Blazor Server 应用中实施身份验证以及通过 Microsoft Graph 检索数据。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建配置为使用 Azure Active Directory (Azure AD) 进行身份验证的新 Blazor Server 应用。
> * 使用 Microsoft.Identity.Web 处理身份验证和授权
> * 通过受保护的 Web API Microsoft Graph 检索数据

## <a name="prerequisites"></a>先决条件

- [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
- 可在其中注册应用的 Azure AD 租户。 如果你无权访问 Azure AD 租户，可以通过注册到 [Microsoft 365 开发人员计划](https://developer.microsoft.com/microsoft-365/dev-program)或创建 [Azure 免费帐户](https://azure.microsoft.com/free)来获取一个租户。

## <a name="register-the-app-in-the-azure-portal"></a>在 Azure 门户中注册应用

使用 Azure Active Directory (Azure AD) 进行身份验证的每个应用都必须注册到 Azure AD。 按照[注册应用程序](quickstart-register-app.md)中的说明以及以下附加说明进行操作：

- 对于“支持的帐户类型”设置，请选择“仅限此组织目录中的帐户”。 
- 将“重定向 URI”下拉框的设置保留为“Web”并输入 `https://localhost:5001/signin-oidc`。 在 Kestrel 上运行的应用的默认端口为 5001。 如果应用通过一个不同的端口提供，请指定该端口号而非 `5001`。

在“身份验证” > “隐式授权”中，选中“访问令牌”和“ID 令牌”的复选框，然后选择“保存”按钮。

最后，因为应用调用了一个受保护的 API（在本例中为 Microsoft Graph），因此在请求访问令牌来调用该 API 时，它需要一个客户端机密来验证其身份。 

1. 在同一应用注册中，在“管理”下选择“证书和机密”。
2. 创建一个永不过期的**新客户端机密**。
3. 记下该机密的**值**，你在下一步中将使用它。 离开此窗格后，将无法再访问它。 但是，你可以根据需要重新创建它。

## <a name="create-the-app-using-the-net-cli"></a>使用 .NET CLI 创建应用

运行以下命令来下载适用于 Microsoft.Identity.Web 的模板，在本教程中将使用这些模板。 

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::0.4.0-preview
```

然后，运行以下命令来创建应用程序。 将命令中的占位符替换为你的应用的概览页面中的正确信息，然后在命令行界面中执行该命令。 使用 `-o|--output` 选项指定的输出位置将创建一个项目文件夹（如果该文件夹不存在）并成为应用程序名称的一部分。

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| 占位符   | Azure 门户中的名称       | 示例                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | 应用程序（客户端）ID | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | 目录（租户）ID   | `e86c78e2-0000-0000-0000-918e0565a45e` |

现在，在编辑器中导航到你的新 Blazor 应用，并在 appsettings.json 文件中添加客户端机密，替换文本“secret-from-app-registration”。

```json
"ClientSecret": "xkAlNiG70000000_UI~d.OS4Dl.-Cy-1m3",
```

## <a name="test-the-app"></a>测试应用

现在可以生成并运行应用了。 运行此模板应用时，必须使用 --framework 指定要运行的框架。 本教程使用 .NET Core 3.1 SDK。 

```dotnetcli
dotnet run --framework netcoreapp3.1
```

在浏览器中导航到 `https://localhost:5001`，使用 Azure AD 用户帐户登录，以查看运行的应用。 

## <a name="retrieving-data-from-microsoft-graph"></a>通过 Microsoft Graph 检索数据

[Microsoft Graph](/graph/overview) 提供了一系列 API，可用于访问用户的 Microsoft 365 数据。 通过使用 Microsoft 标识平台作为你的应用的标识提供者，你可以更轻松地访问此信息，因为 Microsoft Graph 直接支持 Microsoft 标识平台颁发的令牌。 在本部分中，你将添加代码，以便在应用程序的“提取数据”页上显示已登录用户的电子邮件。

在开始之前，请注销你的应用，因为你将对所需权限进行更改，并且你的当前令牌将不起作用。 如果你尚未这样做，请再次运行应用，并在更新以下代码之前选择“注销”。 

现在，你将更新应用的注册和代码，以便拉取用户的电子邮件并在应用中显示这些消息。 若要实现此目的，请先在 Azure AD 中扩展应用注册权限，以启用对电子邮件数据的访问权限。 然后，向 Blazor 应用添加代码来检索此数据并将其显示在其中一个页面上。

1. 在 Azure 门户的“应用注册”中选择你的应用。
1. 在“管理”下选择“API 权限”。
1. 选择“添加权限” > “Microsoft Graph” 。
1. 选择“委托的权限”，然后搜索并选择“Mail.Read”权限。
1. 选择“添加权限”。

在 appsettings.json 文件中，更新你的代码，以便提取具有恰当权限的合适令牌。 在“DownstreamAPI”下，在“user.read”作用域后添加“mail.read”。 这指定应用将请求对哪些作用域（或权限）进行访问。

```json
"Scopes": "user.read mail.read"
```

接下来，更新 FetchData.razor 文件中的代码以检索电子邮件数据，而不是检索默认的（随机的）天气详细信息。 将该文件中的代码替换为下面的代码：

```csharp
@page "/fetchdata"

@inject IHttpClientFactory HttpClientFactory
@inject Microsoft.Identity.Web.ITokenAcquisition TokenAcquisitionService

<p>This component demonstrates fetching data from a service.</p>

@if (messages == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h1>Hello @userDisplayName !!!!</h1>
    <table class="table">
        <thead>
            <tr>
                <th>Subject</th>
                <th>Sender</th>
                <th>Received Time</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var mail in messages)
            {
                <tr>
                    <td>@mail.Subject</td>
                    <td>@mail.Sender</td>
                    <td>@mail.ReceivedTime</td>
                </tr>
            }
        </tbody>
    </table>
}

@code {

    private string userDisplayName;
    private List<MailMessage> messages = new List<MailMessage>();

    private HttpClient _httpClient;

    protected override async Task OnInitializedAsync()
    {
        _httpClient = HttpClientFactory.CreateClient();


        // get a token
        var token = await TokenAcquisitionService.GetAccessTokenForUserAsync(new string[] { "User.Read", "Mail.Read" });

        // make API call
        _httpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        var dataRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me");

        if (dataRequest.IsSuccessStatusCode)
        {
            var userData = System.Text.Json.JsonDocument.Parse(await dataRequest.Content.ReadAsStreamAsync());
            userDisplayName = userData.RootElement.GetProperty("displayName").GetString();
        }

        var mailRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me/messages?$select=subject,receivedDateTime,sender&$top=10");

        if (mailRequest.IsSuccessStatusCode)
        {
            var mailData = System.Text.Json.JsonDocument.Parse(await mailRequest.Content.ReadAsStreamAsync());
            var messagesArray = mailData.RootElement.GetProperty("value").EnumerateArray();

            foreach (var m in messagesArray)
            {
                var message = new MailMessage();
                message.Subject = m.GetProperty("subject").GetString();
                message.Sender = m.GetProperty("sender").GetProperty("emailAddress").GetProperty("address").GetString();
                message.ReceivedTime = m.GetProperty("receivedDateTime").GetDateTime();
                messages.Add(message);
            }
        }
    }

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}

```

启动应用。 你会注意到，系统将提示你输入新添加的权限，指示一切按预期方式工作。 现在，除了基本的用户配置文件数据之外，应用还请求访问电子邮件数据。

在授权同意后，导航到“提取数据”页来阅读某封电子邮件。

:::image type="content" source="./media/tutorial-blazor-server/final-app-2.png" alt-text="最终应用的屏幕截图。它有一个显示为“Hello Nicholas”的标题，它显示了属于 Nicholas 的电子邮件列表。":::

## <a name="next-steps"></a>后续步骤

- [Microsoft 标识平台最佳做法和建议](./identity-platform-integration-checklist.md)
- [Microsoft 标识 Web 基础知识](https://github.com/AzureAD/microsoft-identity-web/wiki/Microsoft-Identity-Web-basics)
