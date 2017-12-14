---
title: "使用 Azure 通知中心向 Chrome 应用发送推送通知 | Microsoft Docs"
description: "了解如何使用 Azure 通知中心将推送通知发送到 Chrome 应用。"
services: notification-hubs
keywords: "移动推送通知,推送通知,push notification,chrome 推送通知"
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 75d4ff59-d04a-455f-bd44-0130a68e641f
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-chrome
ms.devlang: JavaScript
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 107c001f1b02874adfdc53856f18e6bfcbcb0cf4
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2017
---
# <a name="send-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>使用 Azure 通知中心向 Chrome 应用发送推送通知
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

本主题说明如何使用 Azure 通知中心将推送通知发送到 Chrome 应用，然后，该通知会显示在 Google Chrome 浏览器的上下文中。 在本教程中，我们将创建一个 Chrome 应用，它使用 [Google Cloud Messaging (GCM)](https://developers.google.com/cloud-messaging/)接收推送通知。 

> [!NOTE]
> 要完成本教程，必须有一个有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F)。
> 
> 

本教程指导完成启用推送通知的以下基本步骤：

* [启用 Google Cloud Messaging](#register)
* [配置通知中心](#configure-hub)
* [将 Chrome 应用连接到通知中心](#connect-app)
* [向 Chrome 应用发送推送通知](#send)
* [其他功能](#next-steps)

> [!NOTE]
> Chrome 应用的推送通知不是常规的浏览器中通知，而是特定于浏览器扩展模型（有关详细信息，请参阅 [Chrome Apps Overview] （Chrome 应用概述））。 Chrome 应用除了在桌面浏览器中运行以外，还可通过 Apache Cordova 在移动设备（Android 和 iOS）上运行。 请参阅 [移动设备上的 Chrome 应用] ，了解详细信息。
> 
> 

配置 GCM 和 Azure 通知中心的方法与为 Android 配置相同，由于 [Google Cloud Messaging for Chrome] 已弃用，现在同一 GCM 同时支持 Android 设备和 Chrome 实例。

## <a id="register"></a>启用 Google Cloud Messaging
1. 导航到 [Google Cloud Console] 网站，使用 Google 帐户凭据登录，然后单击“创建项目”按钮。 提供相应的**项目名称**，并单击“创建”按钮。
   
       ![Google Cloud Console - Create Project][1]
2. 在刚才创建的项目的“项目”页上记下**项目编号**。 将使用此编号作为 Chrome 应用中的 **GCM 发送器 ID**，以在 GCM 中进行注册。
   
       ![Google Cloud Console - Project Number][2]
3. 在左侧窗格中，单击“API 和身份验证”，向下滚动并单击开关以启用 **Google Cloud Messaging for Android**。 **Google Cloud Messaging for Chrome**。
   
       ![Google Cloud Console - Server Key][3]
4. 在左侧窗格中，单击“凭据” > “新建密钥” > “服务器密钥” > “创建”。
   
       ![Google Cloud Console - Credentials][4]
5. 记下服务器 **API 密钥**。 将在通知中心中配置此密钥，然后进行启用以将推送通知发送到 GCM。
   
       ![Google Cloud Console - API Key][5]

## <a id="configure-hub"></a>配置通知中心
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6.   在“设置”边栏选项卡中选择“通知服务”，并选择“Google (GCM)”。 输入 API 密钥并保存。

&emsp;&emsp;![Azure 通知中心 - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

## <a id="connect-app"></a>将 Chrome 应用连接到通知中心
通知中心现在已配置为使用 GCM，并且你有连接字符串用于注册应用以接收和发送推送通知。 LK

### <a name="create-a-new-chrome-app"></a>新建 Chrome 应用
以下示例基于 [Chrome 应用 GCM 示例] ，使用推荐的方式来创建 Chrome 应用。 我们将重点介绍与 Azure 通知中心相关的步骤。 

> [!NOTE]
> 建议从 [Chrome 应用通知中心示例]中下载此 Chrome 应用的源代码。
> 
> 

此 Chrome 应用是通过 JavaScript 创建的，可以使用任何首选的文字编辑器来创建它。 下文是此 Chrome 应用的大致外观。

![Google Chrome 应用][15]

1. 创建一个文件夹并将其命名为 `ChromePushApp`。 当然，可以使用任意名称 - 但如果将其命名为其他名称，请务必替换所需代码段中的路径。
2. 在第二个步骤创建的文件夹中，下载 [crypto-js 库] 。 此库文件夹将包含两个子文件夹：`components` 和 `rollups`。
3. 创建 `manifest.json` 文件。 所有 Chrome 应用都受到包含应用程序元数据的信息列表文件，最重要的是，用户安装应用时授予应用的所有权限所支持。
   
        {
          "name": "NH-GCM Notifications",
          "description": "Chrome platform app.",
          "manifest_version": 2,
          "version": "0.1",
          "app": {
            "background": {
              "scripts": ["background.js"]
            }
          },
          "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
          "icons": { "128": "gcm_128.png" }
        }
   
    请注意 `permissions` 元素，该元素指定此 Chrome 应用可以从 GCM 中接收推送通知。 此外，它还必须指定 Azure 通知中心 URI，其中 Chrome 应用将进行 REST 调用以进行注册。
    本示例应用还使用了图标文件 `gcm_128.png`，该文件可在原始 GCM 示例中重复使用的源中找到。 可以使用此图标文件来替换任何符合 [图标条件](https://developer.chrome.com/apps/manifest/icons)的图像。
4. 使用以下代码创建名为 `background.js` 的文件：
   
        // Returns a new notification ID used in the notification.
        function getNotificationId() {
          var id = Math.floor(Math.random() * 9007199254740992) + 1;
          return id.toString();
        }
   
        function messageReceived(message) {
          // A message is an object with a data property that
          // consists of key-value pairs.
   
          // Concatenate all key-value pairs to form a display string.
          var messageString = "";
          for (var key in message.data) {
            if (messageString != "")
              messageString += ", "
            messageString += key + ":" + message.data[key];
          }
          console.log("Message received: " + messageString);
   
          // Pop up a notification to show the GCM message.
          chrome.notifications.create(getNotificationId(), {
            title: 'GCM Message',
            iconUrl: 'gcm_128.png',
            type: 'basic',
            message: messageString
          }, function() {});
        }
   
        var registerWindowCreated = false;
   
        function firstTimeRegistration() {
          chrome.storage.local.get("registered", function(result) {
   
            registerWindowCreated = true;
            chrome.app.window.create(
              "register.html",
              {  width: 520,
                 height: 500,
                 frame: 'chrome'
              },
              function(appWin) {}
            );
          });
        }
   
        // Set up a listener for GCM message event.
        chrome.gcm.onMessage.addListener(messageReceived);
   
        // Set up listeners to trigger the first-time registration.
        chrome.runtime.onInstalled.addListener(firstTimeRegistration);
        chrome.runtime.onStartup.addListener(firstTimeRegistration);
   
    这是弹出 Chrome 应用窗口 HTML (**register.html**) 的文件，此文件还定义了处理程序 **messageReceived**，以处理传入的推送通知。
5. 创建名为 `register.html` 的文件，以定义 Chrome 应用的 UI。 
   
   > [!NOTE]
   > 本示例使用 **CryptoJS v3.1.2**。 如果下载了另一个版本的库，请务必正确替换 `src` 路径中的版本。
   > 
   > 
   
        <html>
   
        <head>
        <title>GCM Registration</title>
        <script src="register.js"></script>
        <script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
        <script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
        </head>
   
        <body>
   
        Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
        <button id="registerWithGCM">Register with GCM</button>
        <br/>
        <br/>
        <br/>
   
        Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
        Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>
   
        <br/>
   
        <button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>
   
        <br/>
        <br/>
   
        <textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>
   
        </body>
   
        </html>
6. 使用以下代码创建名为 `register.js` 的文件。 此文件指定 `register.html`后面的脚本。 Chrome 应用不允许内联执行，因此需要为 UI 创建单独的支持脚本。
   
        var registrationId = "";
        var hubName        = "", connectionString = "";
        var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";
   
        window.onload = function() {
           document.getElementById("registerWithGCM").onclick = registerWithGCM;  
           document.getElementById("registerWithNH").onclick = registerWithNH;
           updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
        }
   
        function updateLog(status) {
          currentStatus = document.getElementById("console").innerHTML;
          if (currentStatus != "") {
            currentStatus = currentStatus + "\n\n";
          }
   
          document.getElementById("console").innerHTML = currentStatus  + status;
        }
   
        function registerWithGCM() {
          var senderId = document.getElementById("senderId").value.trim();
          chrome.gcm.register([senderId], registerCallback);
   
          // Prevent register button from being clicked again before the registration finishes.
          document.getElementById("registerWithGCM").disabled = true;
        }
   
        function registerCallback(regId) {
          registrationId = regId;
          document.getElementById("registerWithGCM").disabled = false;
   
          if (chrome.runtime.lastError) {
            // When the registration fails, handle the error and retry the
            // registration later.
            updateLog("Registration failed: " + chrome.runtime.lastError.message);
            return;
          }
   
          updateLog("Registration with GCM succeeded.");
          document.getElementById("registerWithNH").disabled = false;
   
          // Mark that the first-time registration is done.
          chrome.storage.local.set({registered: true});
        }
   
        function registerWithNH() {
          hubName = document.getElementById("hubName").value.trim();
          connectionString = document.getElementById("connectionString").value.trim();
          splitConnectionString();
          generateSaSToken();
          sendNHRegistrationRequest();
        }
   
        // From http://msdn.microsoft.com/library/dn495627.aspx
        function splitConnectionString()
        {
          var parts = connectionString.split(';');
          if (parts.length != 3)
          throw "Error parsing connection string";
   
          parts.forEach(function(part) {
            if (part.indexOf('Endpoint') == 0) {
            endpoint = 'https' + part.substring(11);
            } else if (part.indexOf('SharedAccessKeyName') == 0) {
            sasKeyName = part.substring(20);
            } else if (part.indexOf('SharedAccessKey') == 0) {
            sasKeyValue = part.substring(16);
            }
          });
   
          originalUri = endpoint + hubName;
        }
   
        function generateSaSToken()
        {
          targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
          var expiresInMins = 10; // 10 minute expiration
   
          // Set expiration in seconds.
          var expireOnDate = new Date();
          expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
          var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
            .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
            .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
            .getUTCSeconds()) / 1000;
          var tosign = targetUri + '\n' + expires;
   
          // Using CryptoJS.
          var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
          var base64signature = signature.toString(CryptoJS.enc.Base64);
          var base64UriEncoded = encodeURIComponent(base64signature);
   
          // Construct authorization string.
          sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                          + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
        }
   
        function sendNHRegistrationRequest()
        {
          var registrationPayload =
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                      "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                  "</GcmRegistrationDescription>" +
              "</content>" +
          "</entry>";
   
          // Update the payload with the registration ID obtained earlier.
          registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);
   
          var url = originalUri + "/registrations/?api-version=2014-09";
          var client = new XMLHttpRequest();
   
          client.onload = function () {
            if (client.readyState == 4) {
              if (client.status == 200) {
                updateLog("Notification Hub Registration succesful!");
                updateLog(client.responseText);
              } else {
                updateLog("Notification Hub Registration did not succeed!");
                updateLog("HTTP Status: " + client.status + " : " + client.statusText);
                updateLog("HTTP Response: " + "\n" + client.responseText);
              }
            }
          };
   
          client.onerror = function () {
                updateLog("ERROR - Notification Hub Registration did not succeed!");
          }
   
          client.open("POST", url, true);
          client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
          client.setRequestHeader("Authorization", sasToken);
          client.setRequestHeader("x-ms-version", "2014-09");
   
          try {
              client.send(registrationPayload);
          }
          catch(err) {
              updateLog(err.message);
          }
        }
   
    上述脚本具有以下重要参数：
   
   * **window.onload** 定义 UI 上的两个按钮的按钮单击事件。 其中一个向 GCM 注册，另一个使用向 GCM 注册后所返回的注册 ID 来向 Azure 通知中心注册。
   * **updateLog** 是用于处理简单日志记录功能的函数。
   * **registerWithGCM** 是第一个按钮单击处理程序，它对 GCM 进行 `chrome.gcm.register` 调用，以注册当前的 Chrome 应用实例。
   * **registerCallback** 是回调函数，会在 GCM 注册调用返回时被调用。
   * **registerWithNH** 是第二个按钮单击处理程序，会向通知中心进行注册。 它会获取用户已指定的 `hubName` 和 `connectionString`，并创建通知中心注册 REST API 调用。
   * **splitConnectionString** 和 **generateSaSToken** 是帮助程序，代表 SaS 令牌创建进程的 JavaScript 实现，必须在所有 REST API 调用中使用。 有关详细信息，请参阅 [基本概念](http://msdn.microsoft.com/library/dn495627.aspx)。
   * **sendNHRegistrationRequest** 是向 Azure 通知中心发出 HTTP REST 调用的函数。
   * **registrationPayload** 定义注册 XML 负载。 有关详细信息，请参阅 [创建注册 NH REST API]。 我们使用从 GCM 中接收的内容在其中更新注册 ID。
   * **client** 是我们用于发出 HTTP POST 请求的 **XMLHttpRequest** 的实例。 请注意，我们使用 `sasToken` 更新 `Authorization` 标头。 成功完成此次调用会在 Azure 通知中心中注册此 Chrome 应用实例。

此项目的整体文件夹结构应与下图类似：![Google Chrome 应用 - 文件夹结构][21]

### <a name="set-up-and-test-your-chrome-app"></a>设置并测试 Chrome 应用
1. 打开 Chrome 浏览器。 打开“Chrome 扩展”并启用“开发人员模式”。
   
       ![Google Chrome - Enable Developer Mode][16]
2. 单击“加载解包扩展”，并导航到创建文件的文件夹中。 也可以选择使用 **Chrome 应用和扩展开发人员工具**。 此工具实质上是 Chrome 应用（从 Chrome Web 应用商店安装），并为 Chrome 应用开发提供高级调试功能。
   
       ![Google Chrome - Load Unpacked Extension][17]
3. 如果创建 Chrome 应用时没有任何错误，则会看到显示 Chrome 应用。
   
       ![Google Chrome - Chrome App Display][18]
4. 输入先前从 **Google Cloud Console** 中获取的**项目编号**作为发送器 ID，并单击“注册到 GCM”。 一定会看到“已成功注册到 GCM”消息。
   
       ![Google Chrome - Chrome App Customization][19]
5. 输入先前从门户中获取的**通知中心名称**和 **DefaultListenSharedAccessSignature**，并单击“注册到 Azure 通知中心”。 一定会看到“通知中心注册成功 ！”消息 以及注册响应的详细信息，其中包含 Azure 通知中心注册 ID。
   
       ![Google Chrome - Specify Notification Hub Details][20]  

## <a name="send"></a>向 Chrome 应用发送通知
为了进行测试，我们使用 .NET 控制台应用程序发送 Chrome 推送通知。 

> [!NOTE]
> 可以使用通知中心通过公共 <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST 接口</a>从任意后端发送推送通知。 有关其他跨平台示例，请查看我们的 [文档门户](https://azure.microsoft.com/documentation/services/notification-hubs/) 。
> 
> 

1. 在 Visual Studio 中，从“文件”菜单选择“新建”，并选择“项目”。 在 **Visual C#** 下，单击 **Windows** 和“控制台应用程序”，并单击“确定”。  这会创建一个新的控制台应用程序项目。
2. 在“工具”菜单中，单击“库包管理器”，并单击“包管理器控制台”。 这会显示包管理器控制台。
3. 在控制台窗口中，执行以下命令：
   
        Install-Package Microsoft.Azure.NotificationHubs
   
       This adds a reference to the Azure Service Bus SDK with the <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet package</a>.
4. 打开 `Program.cs` 并添加以下 `using` 语句：
   
        using Microsoft.Azure.NotificationHubs;
5. 在 `Program` 类中，添加以下方法：
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }
   
       Make sure to replace the `<hub name>` placeholder with the name of the notification hub that appears in the [portal](https://portal.azure.com) in your Notification Hub blade. Also, replace the connection string placeholder with the connection string called `DefaultFullSharedAccessSignature` that you obtained in the notification hub configuration section.
   
   > [!NOTE]
   > 确保使用的是具有**完全**访问权限的连接字符串，而不是具有**侦听**访问权限的连接字符串。 **侦听**访问权限连接字符串不会授予发送推送通知的权限。
   > 
   > 
6. 在 `Main` 方法中添加以下调用：
   
         SendNotificationAsync();
         Console.ReadLine();
7. 确保 Chrome 正在运行，并运行控制台应用程序。
8. 应该在桌面上看到以下通知弹出窗口。
   
       ![Google Chrome - Notification][13]
9. 在 Chrome 运行时，还可以通过使用任务栏上的 Chrome 通知窗口 （在 Windows 中）来查看所有通知。
   
       ![Google Chrome - Notifications List][14]

> [!NOTE]
> 无需让 Chrome 应用在浏览器中运行或打开（尽管 Chrome 浏览器本身必须正在运行）。 此外，还可以在 Chrome 通知窗口中获得所有通知的合并视图。
> 
> 

## <a name="next-steps"> </a>后续步骤
在 [通知中心概述]中，了解有关通知中心的详细信息。

若要针对特定用户，请参阅 [Azure 通知中心 - 通知用户] 教程。 

如果要按兴趣组划分用户，可以遵循 [Azure 通知中心 - 突发新闻] 教程。

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Chrome 应用通知中心示例]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Google Cloud Console]: http://cloud.google.com/console
[通知中心概述]: notification-hubs-push-notification-overview.md
[Chrome Apps Overview]: https://developer.chrome.com/apps/about_apps
[Chrome 应用 GCM 示例]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[移动设备上的 Chrome 应用]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[创建注册 NH REST API]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[crypto-js 库]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging for Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure 通知中心 - 通知用户]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Azure 通知中心 - 突发新闻]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
