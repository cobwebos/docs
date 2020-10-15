---
title: 教程：构建 Blazor Server 聊天应用 - Azure SignalR
description: 本教程将介绍如何使用 Azure SignalR 服务构建和修改 Blazor Server 应用
author: JialinXin
ms.service: signalr
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jixin
ms.openlocfilehash: 1a75c083015d1f10a3ed3dba15480430747756eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90525010"
---
# <a name="tutorial-build-a-blazor-server-chat-app"></a>教程：构建 Blazor Server 聊天应用

本教程演示如何构建和修改 Blazor Server 应用。 将了解如何执行以下操作：

> [!div class="checklist"]
> * 使用 Blazor Server 应用构建简单的聊天室。
> * 修改 Razor 组件。
> * 在组件中使用事件处理和数据绑定。
> * 在 Visual Studio 中快速部署到 Azure 应用服务。
> * 将本地 SignalR 迁移到 Azure SignalR 服务。

## <a name="prerequisites"></a>必备条件
* 安装 [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)（版本 >= 3.0.100）
* 安装 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)（版本 >= 16.3）
> Visual Studio 2019 预览版也可以正常使用，该版本与最新 Blazor Server 应用模板一起发布，后者面向更新的 .Net Core 版本。

[存在问题？请告诉我们。](https://aka.ms/asrs/qsblazor)

## <a name="build-a-local-chat-room-in-blazor-server-app"></a>在 Blazor Server 应用中构建本地聊天室

从 Visual Studio 2019 16.2.0 版开始，Azure SignalR 服务是内置的 Web 应用发布进程，这样管理 Web 应用与 SignalR 服务之间的依赖项将更加方便。 无需更改任何代码，即可体验在开发本地环境中使用本地 SignalR，同时体验针对 Azure 应用服务使用 Azure SignalR 服务。

1. 创建聊天 Blazor 应用
   
   在 Visual Studio 中，选择“新建项目”->“Blazor 应用”->（命名应用并选择一个文件夹）->“Blazor Server 应用”。 确保已安装 .NET Core SDK 3.0+，使 Visual Studio 可正确识别目标框架。

   [ ![blazor-chat-create](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png#lightbox)
   
   或者，运行 cmd
   ```dotnetcli
   dotnet new blazorserver -o BlazorChat
   ```
   
1. 添加 `BlazorChatSampleHub.cs` 文件以实现 `Hub` 进行聊天。
   
   ```cs
   using System;
   using System.Threading.Tasks;
   using Microsoft.AspNetCore.SignalR;
   
   namespace BlazorChat
   {
       public class BlazorChatSampleHub : Hub
       {
           public const string HubUrl = "/chat";
   
           public async Task Broadcast(string username, string message)
           {
               await Clients.All.SendAsync("Broadcast", username, message);
           }
   
           public override Task OnConnectedAsync()
           {
               Console.WriteLine($"{Context.ConnectionId} connected");
               return base.OnConnectedAsync();
           }
   
           public override async Task OnDisconnectedAsync(Exception e)
           {
               Console.WriteLine($"Disconnected {e?.Message} {Context.ConnectionId}");
               await base.OnDisconnectedAsync(e);
           }
       }
   }
   ```
   
1. 在 `Startup.Configure()` 中为中心添加一个终结点。
   
   ```cs
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapBlazorHub();
       endpoints.MapFallbackToPage("/_Host");
       endpoints.MapHub<BlazorChatSampleHub>(BlazorChatSampleHub.HubUrl);
   });
   ```
   
1. 安装 `Microsoft.AspNetCore.SignalR.Client` 包以使用 SignalR 客户端。

   ```dotnetcli
   dotnet add package Microsoft.AspNetCore.SignalR.Client --version 3.1.7
   ```

1. 在 `Pages` 文件夹下创建 `ChartRoom.razor` 以实现 SignalR 客户端。 按以下步骤操作，或直接复制 [ChatRoom.razor](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BlazorChat/Pages/ChatRoom.razor)。

   1. 添加页面链接和引用。
      
      ```razor
      @page "/chatroom"
      @inject NavigationManager navigationManager
      @using Microsoft.AspNetCore.SignalR.Client;
      ```

   1. 将代码添加到新的 SignalR 客户端以发送和接收消息。
      
      ```razor
      @code {
          // flag to indicate chat status
          private bool _isChatting = false;
          
          // name of the user who will be chatting
          private string _username;
      
          // on-screen message
          private string _message;
          
          // new message input
          private string _newMessage;
          
          // list of messages in chat
          private List<Message> _messages = new List<Message>();
          
          private string _hubUrl;
          private HubConnection _hubConnection;
      
          public async Task Chat()
          {
              // check username is valid
              if (string.IsNullOrWhiteSpace(_username))
              {
                  _message = "Please enter a name";
                  return;
              };
      
              try
              {
                  // Start chatting and force refresh UI.
                  _isChatting = true;
                  await Task.Delay(1);

                  // remove old messages if any
                  _messages.Clear();
         
                  // Create the chat client
                  string baseUrl = navigationManager.BaseUri;
      
                  _hubUrl = baseUrl.TrimEnd('/') + BlazorChatSampleHub.HubUrl;
      
                  _hubConnection = new HubConnectionBuilder()
                      .WithUrl(_hubUrl)
                      .Build();
      
                  _hubConnection.On<string, string>("Broadcast", BroadcastMessage);
      
                  await _hubConnection.StartAsync();
      
                  await SendAsync($"[Notice] {_username} joined chat room.");
              }
              catch (Exception e)
              {
                  _message = $"ERROR: Failed to start chat client: {e.Message}";
                  _isChatting = false;
              }
          }
      
          private void BroadcastMessage(string name, string message)
          {
              bool isMine = name.Equals(_username, StringComparison.OrdinalIgnoreCase);
      
              _messages.Add(new Message(name, message, isMine));
      
              // Inform blazor the UI needs updating
              StateHasChanged();
          }
      
          private async Task DisconnectAsync()
          {
              if (_isChatting)
              {
                  await SendAsync($"[Notice] {_username} left chat room.");
      
                  await _hubConnection.StopAsync();
                  await _hubConnection.DisposeAsync();
      
                  _hubConnection = null;
                  _isChatting = false;
              }
          }
      
          private async Task SendAsync(string message)
          {
              if (_isChatting && !string.IsNullOrWhiteSpace(message))
              {
                  await _hubConnection.SendAsync("Broadcast", _username, message);
      
                  _newMessage = string.Empty;
              }
          }
      
          private class Message
          {
              public Message(string username, string body, bool mine)
              {
                  Username = username;
                  Body = body;
                  Mine = mine;
              }
      
              public string Username { get; set; }
              public string Body { get; set; }
              public bool Mine { get; set; }
      
              public bool IsNotice => Body.StartsWith("[Notice]");
      
              public string CSS => Mine ? "sent" : "received";
          }
      }
      ```

   1. 在 `@code` 之前添加渲染部分，以便 UI 与 SignalR 客户端交互。
      
      ```razor
      <h1>Blazor SignalR Chat Sample</h1>
      <hr />
      
      @if (!_isChatting)
      {
          <p>
              Enter your name to start chatting:
          </p>
      
          <input type="text" maxlength="32" @bind="@_username" />
          <button type="button" @onclick="@Chat"><span class="oi oi-chat" aria-hidden="true"></span> Chat!</button>
      
          // Error messages
          @if (_message != null)
          {
              <div class="invalid-feedback">@_message</div>
              <small id="emailHelp" class="form-text text-muted">@_message</small>
          }
      }
      else
      {
          // banner to show current user
          <div class="alert alert-secondary mt-4" role="alert">
              <span class="oi oi-person mr-2" aria-hidden="true"></span>
              <span>You are connected as <b>@_username</b></span>
              <button class="btn btn-sm btn-warning ml-md-auto" @onclick="@DisconnectAsync">Disconnect</button>
          </div>
          // display messages
          <div id="scrollbox">
              @foreach (var item in _messages)
              {
                  @if (item.IsNotice)
                  {
                      <div class="alert alert-info">@item.Body</div>
                  }
                  else
                  {
                      <div class="@item.CSS">
                          <div class="user">@item.Username</div>
                          <div class="msg">@item.Body</div>
                      </div>
                  }
              }
              <hr />
              <textarea class="input-lg" placeholder="enter your comment" @bind="@_newMessage"></textarea>
              <button class="btn btn-default" @onclick="@(() => SendAsync(_newMessage))">Send</button>
          </div>
      }
      ```

1. 更新 `NavMenu.razor`，在 `NavMenuCssClass` 下插入聊天室的输入菜单（如 REST）。

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="chatroom">
           <span class="oi oi-chat" aria-hidden="true"></span> Chat room
       </NavLink>
   </li>
   ```
   
1. 更新 `site.css` 以优化图表区域气泡视图。 将代码追加到末尾。

   ```css
   /* improved for chat text box */
   textarea {
       border: 1px dashed #888;
       border-radius: 5px;
       width: 80%;
       overflow: auto;
       background: #f7f7f7
   }
   
   /* improved for speech bubbles */
   .received, .sent {
       position: relative;
       font-family: arial;
       font-size: 1.1em;
       border-radius: 10px;
       padding: 20px;
       margin-bottom: 20px;
   }
   
   .received:after, .sent:after {
       content: '';
       border: 20px solid transparent;
       position: absolute;
       margin-top: -30px;
   }
   
   .sent {
       background: #03a9f4;
       color: #fff;
       margin-left: 10%;
       top: 50%;
       text-align: right;
   }
   
   .received {
       background: #4CAF50;
       color: #fff;
       margin-left: 10px;
       margin-right: 10%;
   }
   
   .sent:after {
       border-left-color: #03a9f4;
       border-right: 0;
       right: -20px;
   }
   
   .received:after {
       border-right-color: #4CAF50;
       border-left: 0;
       left: -20px;
   }
   
   /* div within bubble for name */
   .user {
       font-size: 0.8em;
       font-weight: bold;
       color: #000;
   }
   
   .msg {
       /*display: inline;*/
   }
   ```

1. 单击 <kbd>F5</kbd> 运行应用。 你将可以如下所示进行聊天。

   [ ![blazor-chat](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif#lightbox)
   
[存在问题？请告诉我们。](https://aka.ms/asrs/qsblazor)

## <a name="publish-to-azure"></a>发布到 Azure

   目前为止，Blazor 应用正在使用本地 SignalR，在部署到 Azure 应用服务时，建议使用 [Azure SignalR 服务](https://docs.microsoft.com/aspnet/core/signalr/scale?view=aspnetcore-3.1#azure-signalr-service)，该服务允许将 Blazor Server 应用扩展为大量的并发 SignalR 连接。 此外，SignalR 服务的全球覆盖和高性能数据中心可帮助显著减少由于地理位置造成的延迟。

> [!IMPORTANT]
> 在 Blazor Server 应用中，UI 状态是在服务器端进行维护，这意味着在这种情况下需要确保服务器粘性。 如果只有一个应用服务器，则服务器粘性可得到确保，这是设计使然。 但如果有多个应用服务器，则客户端协商和连接可能会转到不同服务器，并导致 Blazor 应用出现 UI 错误。 因此，需要在 `appsettings.json` 中实现服务器粘性，如下所示：
> ```json
> "Azure:SignalR:ServerStickyMode": "Required"
> ```

1. 右键单击该项目，然后导航到 `Publish`。

   * 目标：Azure
   * 特定目标：支持所有类型的 Azure 应用服务。
   * 应用服务：新建应用服务或选择现有的应用服务。

   [ ![blazor-chat-profile](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif#lightbox)

1. 添加 Azure SignalR 服务依赖项

   创建发布配置文件后，可以在“服务依赖项”下看到一条推荐消息。 单击“配置”以在面板中新建或选择现有的 Azure SignalR 服务。

   [ ![blazor-chat-dependency](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png#lightbox)

   服务依赖项将执行以下操作，确保应用在 Azure 上时自动切换到 Azure SignalR 服务。

   * 更新 [`HostingStartupAssembly`](https://docs.microsoft.com/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) 以使用 Azure SignalR 服务。
   * 添加 Azure SignalR 服务 NuGet 包引用。
   * 更新配置文件属性以保存依赖项设置。
   * 根据你的选择，配置机密存储。
   * 添加 `appsettings` 配置，确保应用面向所选的 Azure SignalR 服务。

   [ ![blazor-chat-dependency-summary](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png#lightbox)

1. 发布应用

   现已准备好发布。 发布完成后，应用将自动浏览页面。 
   > [!NOTE]
   > 由于 Azure 应用服务部署启动延迟，因此应用首次访问页面时可能无法立即正常工作，请尝试刷新页面，稍等一段时间。
   > 此外，你还可以使用浏览器调试器模式与 <kbd>F12</kbd>，以验证流量是否已重定向到 Azure SignalR 服务。

   [ ![blazor-chat-azure](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png#lightbox)
   
[存在问题？请告诉我们。](https://aka.ms/asrs/qsblazor)

## <a name="further-topic-enable-azure-signalr-service-in-local-development"></a>延伸主题：在本地开发中启用 Azure SignalR 服务

1. 添加对 Azure SignalR SDK 的引用

   ```dotnetcli
   dotnet add package Microsoft.Azure.SignalR --version 1.5.1
   ```

1. 在 `Startup.ConfigureServices()` 中添加对 Azure SignalR 服务的调用。

   ```cs
   public void ConfigureServices(IServiceCollection services)
   {
       ...
       services.AddSignalR().AddAzureSignalR();
       ...
   }
   ```

1. 在 `appsetting.json` 中或使用[机密管理器](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=visual-studio#secret-manager)工具配置 Azure SignalR 服务 `ConnectionString`

> [!NOTE]
> 可以对 SignalR SDK 使用 [`HostingStartupAssembly`](https://docs.microsoft.com/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) 来替换步骤 2。
> 
> 1. 添加配置以启用 `appsetting.json` 中的 Azure SignalR 服务
>    ```js
>    "Azure": {
>      "SignalR": {
>        "Enabled": true,
>        "ServerStickyMode": "Required",
>        "ConnectionString": <your-connection-string>
>      }
>    }
>    ```
> 
> 1. 分配托管的启动程序集以使用 Azure SignalR SDK。 编辑 `launchSettings.json` 并在 `environmentVariables` 内添加如下所示的配置。
>    ```js
>    "environmentVariables": {
>        ...,
>        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.Azure.SignalR"
>      }
>    ```

[存在问题？请告诉我们。](https://aka.ms/asrs/qsblazor)

## <a name="clean-up-resources"></a>清理资源

若要清理本教程创建的资源，请使用 Azure 门户删除相应的资源组。

## <a name="next-steps"></a>后续步骤

本教程介绍以下操作：

> [!div class="checklist"]
> * 使用 Blazor Server 应用构建简单的聊天室。
> * 修改 Razor 组件。
> * 在组件中使用事件处理和数据绑定。
> * 在 Visual Studio 中快速部署到 Azure 应用服务。
> * 将本地 SignalR 迁移到 Azure SignalR 服务。

深入了解高可用性。
> [!div class="nextstepaction"]
> [复原能力和灾难恢复](signalr-concept-disaster-recovery.md)

## <a name="additional-resources"></a>其他资源

* [ASP.NET Core Blazor](https://docs.microsoft.com/aspnet/core/blazor)
