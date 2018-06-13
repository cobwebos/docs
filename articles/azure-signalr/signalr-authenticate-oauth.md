---
title: Azure SignalR 服务客户端身份验证教程 | Microsoft Docs
description: 本教程介绍如何对 Azure SignalR 服务客户端进行身份验证
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/17/2018
ms.author: wesmc
ms.openlocfilehash: 748e5839233b9d71b9ed072d0cfe45f018471c52
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33869693"
---
# <a name="tutorial-azure-signalr-service-authentication"></a>教程：Azure SignalR 服务身份验证

Microsoft Azure SignalR 服务目前提供[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本教程建立在快速入门中介绍的聊天室应用程序基础上。 如果尚未完成[使用 SignalR 服务创建聊天室](signalr-quickstart-dotnet-core.md)，请先完成该练习。 

本教程介绍如何实现自己的身份验证和将其与 Microsoft Azure SignalR 服务集成。 

对于实际方案而言，最初在快速入门聊天室应用程序中使用的身份验证太过简单。 应用程序允许每个客户端声明自己的身份，服务器只需接受即可。 此方法在实际应用程序中用处不大，因为恶意用户可通过模拟其他人访问敏感数据。 

[GitHub](https://github.com/) 提供基于常用行业标准协议（名为 [OAuth](https://oauth.net/)）的身份验证 API。 这些 API 允许第三方应用程序对 GitHub 帐户进行身份验证。 在本教程中，将使用这些 API 通过 Github 帐户实现身份验证后，才允许客户端登录到聊天室应用程序。 对 GitHub 帐户进行身份验证后，帐户信息将添加为 Web 客户端用来进行身份验证的 cookie。

若要深入了解通过 GitHub 提供的 OAuth 身份验证 API，请参阅 [Basics of Authentication](https://developer.github.com/v3/guides/basics-of-authentication/)（身份验证基础知识）。

可使用任何代码编辑器来完成本快速入门中的步骤。 但是，[Visual Studio Code](https://code.visualstudio.com/) 是一个很好的选项，可用于 Windows、macOS 和 Linux 平台。

本教程所用代码可在 [AzureSignalR-samples GitHub 存储库](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/GitHubChat)下载。


![托管在 Azure 中的 OAuth 完成](media/signalr-authenticate-oauth/signalr-oauth-complete-azure.png)


本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 GitHub 帐户注册新的 OAuth 应用
> * 添加身份验证控制器以支持 GitHub 身份验证
> * 将 ASP.NET Core Web 应用部署到 Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

若要完成本教程，必须具备以下先决条件：

* 在 [GitHub](https://github.com/) 上创建的帐户
* [Git](https://git-scm.com/)
* [.NET Core SDK](https://www.microsoft.com/net/download/windows) 
* [已配置的 Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* 下载或克隆 [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) GitHub 存储库。


## <a name="create-an-oauth-app"></a>创建 OAuth 应用

1. 打开 web 浏览器，导航到 `https://github.com` 并登录帐户。

2. 对于帐户，导航到“设置” > “开发人员设置”，然后单击“注册新应用程序”或“OAuth 应用”下的“新建 OAuth 应用”。

3. 为新 OAuth 应用使用以下设置，然后单击“注册应用程序”：

    | 设置名称 | 建议的值 | 说明 |
    | ------------ | --------------- | ----------- |
    | 应用程序名称 | Azure SignalR 聊天 | Github 用户应能识别并信任他们要进行身份验证的应用。   |
    | 主页 URL | *http://localhost:5000/home* | |
    | 应用程序说明 | 配合使用 Azure SignalR 服务和 Github 身份验证的聊天室示例 | 有效的应用程序说明可帮助应用程序用户理解所用的身份验证上下文。 |
    | 授权回调 URL | *http://localhost:5000/signin-github* | 这是 OAuth 应用程序最重要的设置。 它是身份验证成功后 GitHub 返回用户的回调 URL。 在本教程中，必须使用 AspNet.Security.OAuth.GitHub 包的默认回调 URL“/signin-github”。  |

4. 新的 OAuth 应用注册完成后，使用以下命令将客户端 ID 和客户端密码添加到机密管理器。 将 Your_GitHub_Client_Id 和 Your_GitHub_Client_Secret 替换为 OAuth 应用的值。

        dotnet user-secrets set GitHubClientId Your_GitHub_Client_Id
        dotnet user-secrets set GitHubClientSecret Your_GitHub_Client_Secret


## <a name="implement-the-oauth-flow"></a>实现 OAuth 流

### <a name="update-the-startup-class-to-support-github-authentication"></a>更新启动类以支持 GitHub 身份验证

1. 将引用添加到最新的 Microsoft.AspNetCore.Authentication.Cookies 包并还原所有包。

        dotnet add package Microsoft.AspNetCore.Authentication.Cookies -v 2.1.0-rc1-30656
        dotnet restore

1. 打开 Startup.cs，并为以下命名空间添加 `using` 语句：

    ```csharp
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Security.Claims;
    using Microsoft.AspNetCore.Authentication.Cookies;
    using Microsoft.AspNetCore.Authentication.OAuth;
    using Newtonsoft.Json.Linq;
    ```

2. 在 `Startup` 类的顶部，添加保存 GitHub OAuth 应用密码的机密管理器密钥的常数。

    ```csharp
    private const string GitHubClientId = "GitHubClientId";
    private const string GitHubClientSecret = "GitHubClientSecret";
    ```

3. 将以下代码添加到 `ConfigureServices` 方法，支持使用 GitHub OAuth 应用的身份验证：

    ```csharp
    services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
        .AddCookie()
        .AddGitHub(options =>
        {
            options.ClientId = Configuration[GitHubClientId];
            options.ClientSecret = Configuration[GitHubClientSecret];
            options.Scope.Add("user:email");
            options.Events = new OAuthEvents
            {
                OnCreatingTicket = GetUserCompanyInfoAsync
            };
        });
    ```

4. 将 `GetUserCompanyInfoAsync` 帮助程序方法添加到 `Startup` 类。    

    ```csharp
    private static async Task GetUserCompanyInfoAsync(OAuthCreatingTicketContext context)
    {
        var request = new HttpRequestMessage(HttpMethod.Get, context.Options.UserInformationEndpoint);
        request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", context.AccessToken);

        var response = await context.Backchannel.SendAsync(request,
            HttpCompletionOption.ResponseHeadersRead, context.HttpContext.RequestAborted);

        var user = JObject.Parse(await response.Content.ReadAsStringAsync());
        if (user.ContainsKey("company"))
        {
            var company = user["company"].ToString();
            var companyIdentity = new ClaimsIdentity(new[]
            {
                new Claim("Company", company)
            });
            context.Principal.AddIdentity(companyIdentity);
        }
    }        
    ```

5. 使用以下代码行更新启动类的 `Configure` 方法，然后保存文件。

        app.UseAuthentication();



### <a name="add-an-authentication-controller"></a>添加身份验证控制器

在本部分中，将实现 `Login` API，它使用 GitHub OAuth 应用对客户端进行身份验证。 身份验证后，API 会在将客户端重定向回聊天应用前向 Web 客户端响应添加 cookie。 该 cookie 稍后将用于标识客户端。

1. 将新的控制器代码文件添加到 chattest\Controllers 目录。 将文件命名为 AuthController.cs。

2. 为身份验证控制器添加以下代码。 如果项目目录不是 chattest，请务必更新命名空间：

    ```csharp
    using AspNet.Security.OAuth.GitHub;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.AspNetCore.Mvc;

    namespace chattest.Controllers
    {
        [Route("/")]
        public class AuthController : Controller
        {
            [HttpGet("login")]
            public IActionResult Login()
            {
                if (!User.Identity.IsAuthenticated)
                {
                    return Challenge(GitHubAuthenticationDefaults.AuthenticationScheme);
                }

                HttpContext.Response.Cookies.Append("githubchat_username", User.Identity.Name);
                HttpContext.SignInAsync(User);
                return Redirect("/");
            }
        }
    }    
    ```

3. 保存所做更改。    

### <a name="update-the-hub-class"></a>更新集线器类

默认情况下，当 Web 客户端尝试连接到 SignalR 服务时，会基于内部提供的访问令牌授予连接。 此访问令牌不与经身份验证的标识相关联。 此访问实际上是匿名访问。 

在本部分中，通过向集线器类添加 `Authorize` 属性，并将集线器方法更新为从经身份验证的用户声明中读取用户名，启动真实身份验证。

1. 打开 Hub\Chat.cs 并添加对以下命名空间的引用：

    ```csharp
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    ```

2. 更新集线器代码，如下所示。 此代码将 `Authorize` 属性添加到 `Chat` 类，并使用集线器方法中用户经身份验证的标识。 此外，还添加了 `OnConnectedAsync` 方法，这会在每次出现新客户端连接时将系统消息记录到聊天室。

    ```csharp
    [Authorize]
    public class Chat : Hub
    {
        public override Task OnConnectedAsync()
        {
            return Clients.All.SendAsync("broadcastMessage", "_SYSTEM_", $"{Context.User.Identity.Name} JOINED");
        }

        // Uncomment this line to only allow user in Microsoft to send message
        //[Authorize(Policy = "Microsoft_Only")]
        public void BroadcastMessage(string message)
        {
            Clients.All.SendAsync("broadcastMessage", Context.User.Identity.Name, message);
        }

        public void Echo(string message)
        {
            var echoMessage = $"{message} (echo from server)";
            Clients.Client(Context.ConnectionId).SendAsync("echo", Context.User.Identity.Name, echoMessage);
        }
    }
    ```

3. 保存所做更改。

### <a name="update-the-web-client-code"></a>更新 Web 客户端代码

1. 打开 wwwroot\index.html，将提示用户名的代码替换为使用身份验证控制器返回的 cookie 的代码。

    从 index.html 中删除以下代码：

    ```javascript
    // Get the user name and store it to prepend to messages.
    var username = generateRandomName();
    var promptMessage = 'Enter your name:';
    do {
        username = prompt(promptMessage, username);
        if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
            username = '';
            promptMessage = 'Invalid input. Enter your name:';
        }
    } while(!username)
    ```

    添加以下代码替换上述代码，从而使用 cookie：

    ```javascript
    // Get the user name cookie.
    function getCookie(key) {
        var cookies = document.cookie.split(';').map(c => c.trim());
        for (var i = 0; i < cookies.length; i++) {
            if (cookies[i].startsWith(key + '=')) return unescape(cookies[i].slice(key.length + 1));
        }
        return '';
    }
    var username = getCookie('githubchat_username');    
    ```

2. 在添加的用来使用 cookie 的代码行下，为 `appendMessage` 函数添加以下定义：

    ```javascript
    function appendMessage(encodedName, encodedMsg) {
        var messageEntry = createMessageEntry(encodedName, encodedMsg);
        var messageBox = document.getElementById('messages');
        messageBox.appendChild(messageEntry);
        messageBox.scrollTop = messageBox.scrollHeight;
    }
    ```

3. 使用以下代码更新 `bindConnectionMessage` 和 `onConnected` 函数，以便使用 `appendMessage`。

    ```javascript
    function bindConnectionMessage(connection) {
        var messageCallback = function(name, message) {
            if (!message) return;
            // Html encode display name and message.
            var encodedName = name;
            var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
            appendMessage(encodedName, encodedMsg);
        };
        // Create a function that the hub can call to broadcast messages.
        connection.on('broadcastMessage', messageCallback);
        connection.on('echo', messageCallback);
        connection.onclose(onConnectionError);
    }

    function onConnected(connection) {
        console.log('connection started');
        document.getElementById('sendmessage').addEventListener('click', function (event) {
            // Call the broadcastMessage method on the hub.
            if (messageInput.value) {
                connection
                    .invoke('broadcastMessage', messageInput.value)
                    .catch(e => appendMessage('_BROADCAST_', e.message));
            }

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
        document.getElementById('message').addEventListener('keypress', function (event) {
            if (event.keyCode === 13) {
                event.preventDefault();
                document.getElementById('sendmessage').click();
                return false;
            }
        });
        document.getElementById('echo').addEventListener('click', function (event) {
            // Call the echo method on the hub.
            connection.send('echo', messageInput.value);

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
    }    
    ```    

4. 在 index.html 底部，更新 `connection.start()` 的错误处理程序（如下所示），提示用户进行登录。

    ```javascript
    connection.start()
        .then(function () {
            onConnected(connection);
        })
        .catch(function (error) {
            if (error) {
                if (error.message) {
                    console.error(error.message);
                }
                if (error.statusCode && error.statusCode === 401) {
                    appendMessage('_BROADCAST_', 'You\'re not logged in. Click <a href="/login">here</a> to login with GitHub.');
                }
            }
        });
    ```

5. 保存所做更改。    



## <a name="build-and-run-the-app-locally"></a>在本地生成并运行应用

1. 保存对所有文件的更改。 

2. 使用 .NET Core CLI 生成应用，在命令行界面中执行以下命令：

        dotnet build

3. 成功完成应用生成后，执行以下命令，在本地运行 Web 应用：

        dotnet run

    默认情况下，应用将本地托管在端口 5000 上：

        E:\Testing\chattest>dotnet run
        Hosting environment: Production
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

4. 启动浏览器窗口并导航到 `http://localhost:5000`。 单击顶部的“此处”链接，使用 GitHub 进行登录。 

    ![托管在 Azure 中的 OAuth 完成](media/signalr-authenticate-oauth/signalr-oauth-complete-azure.png)

    系统将提示授予聊天应用访问 GitHub 帐户的权限。 单击“授权”按钮。 
    
    ![授权 OAuth 应用](media/signalr-authenticate-oauth/signalr-authorize-oauth-app.png)
    
    将重定向回聊天应用程序并使用 GitHub 帐户名称进行登录。 Web 应用程序使用添加的新身份验证进行身份验证，从而确定帐户名称。

    ![标识的帐户](media/signalr-authenticate-oauth/signalr-oauth-account-identified.png)

    由于聊天应用通过 GitHub 执行身份验证并将身份验证信息存储为 cookie，应将其部署到 Azure，以便其他用户可使用他们的帐户进行身份验证并从其他工作站进行通信。 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-the-app-to-azure"></a>将应用部署到 Azure

在本部分中，将使用 Azure Cloud Shell 的 Azure 命令行接口 (CLI) 创建新的 [Azure Web 应用](https://docs.microsoft.com/azure/app-service/)，在 Azure 中托管 ASP.NET 应用程序。 Web 应用将配置为使用本地 Git 部署。 还将使用 SignalR 连接字符串、GitHub OAuth 应用密码和部署用户配置 Web 应用。

本部分中的步骤使用 Azure CLI 的 signalr 扩展。 执行以下命令，安装 Azure CLI 2.0 的 signalr 扩展：

```azurecli-interactive
az extension add -n signalr
```

创建以下资源时，请确保使用的资源组与 SignalR 服务资源驻留的资源组相同。 通过此方法，稍后若要删除所有资源，可更轻松地进行清理。 给定示例假定使用之前教程中建议的组名称 SignalRTestResources。


### <a name="create-the-web-app-and-plan"></a>创建 Web 应用和计划

复制下面命令的文本并更新参数。 将更新的脚本粘贴到 Azure Cloud Shell，然后按 Enter 创建新的应用服务计划和 Web 应用。

```azurecli-interactive
#========================================================================
#=== Update these variable for your resource group name.              ===
#========================================================================
ResourceGroupName=SignalRTestResources

#========================================================================
#=== Update these variable for your web app.                          ===
#========================================================================
WebAppName=myWebAppName
WebAppPlan=myAppServicePlanName

# Create an App Service plan.
az appservice plan create --name $WebAppPlan --resource-group $ResourceGroupName \
    --sku FREE

# Create the new Web App
az webapp create --name $WebAppName --resource-group $ResourceGroupName \
    --plan $WebAppPlan


```


| 参数 | 说明 |
| -------------------- | --------------- |
| ResourceGroupName | 这是之前教程中建议的资源组名称。 将所有教程资源聚集在一起是一个好办法。 使用在之前教程中使用的相同资源组。 | 
| WebAppPlan | 输入一个新的、唯一的应用服务计划名称。 | 
| WebAppName | 这将是新 Web 应用的名称，也是 URL 的一部分。 使用唯一名称。 例如，signalrtestwebapp22665120。   | 



### <a name="add-app-settings-to-the-web-app"></a>将应用设置添加到 Web 应用

在本部分中，将添加以下组件的应用设置：

* SignalR 服务资源连接字符串
* GitHub OAuth 应用客户端 ID
* GitHub OAuth 应用客户端密码

复制下面命令的文本并更新参数。 将更新的脚本粘贴到 Azure Cloud Shell，然后按 Enter 添加应用设置：

```azurecli-interactive
#========================================================================
#=== Update these variables for your GitHub OAuth App.                ===
#========================================================================
GitHubClientId=1234567890
GitHubClientSecret=1234567890

#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
SignalRServiceResource=mySignalRresourcename
WebAppName=myWebAppName

# Get the SignalR Service resource hostName
signalRhostname=$(az signalr show --name $SignalRServiceResource \
    --resource-group $ResourceGroupName --query hostName -o tsv)

# Get the SignalR primary key 
signalRprimarykey=$(az signalr key list --name $SignalRServiceResource \
    --resource-group $ResourceGroupName --query primaryKey -o tsv)

# Form the connection string to the service resource
connstring="Endpoint=https://$signalRhostname;AccessKey=$signalRprimarykey;"

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "Azure:SignalR:ConnectionString=$connstring" 

#Add the app settings to use with GitHub authentication
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientId=$GitHubClientId" 
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientSecret=$GitHubClientSecret" 

```

| 参数 | 说明 |
| -------------------- | --------------- |
| GitHubClientId | 为此变量分配 GitHub OAuth 应用的机密客户端 ID。 |
| GitHubClientSecret | 为此变量分配 GitHub OAuth 应用的密码。 |
| ResourceGroupName | 将此变量更新为在上一部分中使用的相同资源组名称。 | 
| SignalRServiceResource | 使用快速入门中创建的 SignalR 服务资源名称更新此变量。 例如，signalrtestsvc48778624。 | 
| WebAppName | 使用上一部分中创建的新 Web 应用名称更新此变量。 | 



### <a name="configure-the-web-app-for-local-git-deployment"></a>为本地 Git 部署配置 Web 应用

在 Azure Cloud Shell 中，粘贴以下脚本。 此脚本创建新的部署用户名和密码，使用 Git 将代码部署到 Web 应用时会使用该用户名和密码。 该脚本还使用本地 Git 存储库配置用于部署的 Web 应用，并返回 Git 部署 URL。

```azurecli-interactive
#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
WebAppName=myWebAppName

#========================================================================
#=== Update these variables for your deployment user.                 ===
#========================================================================
DeploymentUserName=myUserName
DeploymentUserPassword=myPassword

# Add the desired deployment user name and password
az webapp deployment user set --user-name $DeploymentUserName \
    --password $DeploymentUserPassword

# Configure Git deployment and note the deployment URL in the output
az webapp deployment source config-local-git --name $WebAppName \
    --resource-group $ResourceGroupName \
    --query [url] -o tsv

```

| 参数 | 说明 |
| -------------------- | --------------- |
| DeploymentUserName | 选择新的部署用户名。 |
| DeploymentUserPassword | 为新的部署用户选择密码。 |
| ResourceGroupName | 使用上一部分中使用的相同资源组名称。 | 
| WebAppName | 这将是之前创建的新 Web 应用的名称。 | 


记下此命令返回的 Git 部署 URL。 稍后将使用此 URL。


### <a name="deploy-your-code-to-the-azure-web-app"></a>将代码部署到 Azure Web 应用

若要部署代码，请在 Git shell 中执行以下命令。

1. 导航到项目目录的根目录。 如果未使用 Git 存储库初始化该项目，请执行以下命令：

        git init

2. 为前面记下的 Git 部署 URL 添加远程：

        git remote add Azure <your git deployment url>

3. 将所有文件暂存在初始化存储库中并添加提交。

        git add -A
        git commit -m "init commit"

4. 将代码部署到 Azure 中的 Web 应用。        

        git push Azure master

    系统将提示进行身份验证以便将代码部署到 Azure。 输入上面创建的部署用户的用户名和密码。

### <a name="update-the-github-oauth-app"></a>更新 GitHub OAuth 应用 

需要执行的最后一步是更新 GitHub OAuth 应用的“主页 URL”和“授权回调 URL”，指向新的托管应用。

1. 在浏览器中打开 [http://github.com](http://github.com) 并导航到帐户的“设置” > “开发人员设置” > “Oauth 应用”。

2. 单击身份验证应用并更新“主页 URL”和“授权回调 URL”，如下所示：

    | 设置 | 示例 |
    | ------- | ------- |
    | 主页 URL | https://signalrtestwebapp22665120.azurewebsites.net/home |
    | 授权回调 URL | https://signalrtestwebapp22665120.azurewebsites.net/signin-github |


3. 导航到 Web 应用 URL，并测试应用程序。

    ![托管在 Azure 中的 OAuth 完成](media/signalr-authenticate-oauth/signalr-oauth-complete-azure.png)


## <a name="clean-up-resources"></a>清理资源

如果还将继续下一教程，可保留此快速入门中创建的资源，并在下一教程中重复使用。

否则，如果已完成快速入门示例应用程序，则可删除此快速入门中创建的 Azure 资源，避免产生费用。 

> [!IMPORTANT]
> 删除资源组这一操作是不可逆的，资源组以及其中的所有资源会被永久删除。 请确保不会意外删除错误的资源组或资源。 如果是在现有资源组（其中包含要保留的资源）中为托管此示例而创建了相关资源，则可从各自的边栏选项卡逐个删除这些资源，而不是删除资源组。
> 
> 

登录到 [Azure 门户](https://portal.azure.com)，并单击“资源组”。

在“按名称筛选...”文本框中键入资源组的名称。 本文的说明使用名为“SignalRTestResources”的资源组。 在结果列表中的资源组上，单击“...”，然后单击“删除资源组”。

   
![删除](./media/signalr-authenticate-oauth/signalr-delete-resource-group.png)


系统会要求确认是否删除资源组。 键入资源组的名称进行确认，然后单击“删除”。
   
几分钟后，将删除该资源组及其包含的所有资源。

## <a name="next-steps"></a>后续步骤

在本教程中，借助 OAuth 添加身份验证，为使用 Azure SignalR 服务进行身份验证提供了更好的方法。 若要了解有关使用 Azure SignalR 服务器的详细信息，请继续了解用于 SignalR 服务的 Azure CLI 示例。

> [!div class="nextstepaction"]
> [Azure SignalR CLI 示例](./signalr-cli-samples.md)