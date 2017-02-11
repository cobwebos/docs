---
title: "使用 Azure 移动应用向 Apache Cordova 应用添加推送通知 | Microsoft Docs"
description: "了解如何使用 Azure 移动应用将推送通知发送到 Apache Cordova 应用。"
services: app-service\mobile
documentationcenter: javascript
manager: erikre
editor: 
author: ysxu
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 416605904db48ffbe26b237bf27aef8fd357a6b3


---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>将推送通知添加到 Apache Cordova 应用
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>概述
本教程介绍如何向 [Apache Cordova 快速入门]项目添加推送通知，以便每次插入一条记录时，向设备发送一条推送通知。

如果不使用下载的快速入门服务器项目，则需要推送通知扩展包。 有关详细信息，请参阅[使用用于 Azure 移动应用的 .NET 后端服务器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

## <a name="a-nameprerequisitesaprerequisites"></a><a name="prerequisites"></a>先决条件
本教程介绍了使用 Visual Studio 2015 开发的 Apache Cordova 应用程序，该应用程序可在 Google Android 模拟器、Android 设备、Windows 设备和 iOS 设备上运行。

若要完成本教程，你需要：

* 装有 [Visual Studio Community 2015] 或更高版本的电脑。
* [用于 Apache Cordova 的 Visual Studio 工具]
* [有效的 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)。
* 已完成的 [Apache Cordova 快速入门]项目。
* (Android) 已验证电子邮件地址的 [Google 帐户]。
* (iOS) Apple 开发人员计划会员资格和 iOS 设备（iOS 模拟器不支持推送）。
* (Windows) Windows 应用商店开发人员帐户和 Windows 10 设备。

## <a name="a-nameconfigure-hubaconfigure-a-notification-hub"></a><a name="configure-hub"></a>配置通知中心
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[观看演示本节步骤的视频](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub)

## <a name="update-the-server-project-to-send-push-notifications"></a>更新服务器项目以发送推送通知
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="a-nameadd-push-to-appamodify-your-cordova-app-to-receive-push-notifications"></a><a name="add-push-to-app"></a>修改 Cordova 应用以接收推送通知
必须确保 Apache Cordova 应用项目已准备就绪，可通过安装 Cordova 推送插件和任何平台特定的推送服务来处理推送通知。

#### <a name="update-the-cordova-version-in-your-project"></a>更新项目中的 Cordova 版本。
如果已使用较旧版本配置项目，建议将客户端项目更新到 Cordova 6.1.1。 若要更新项目，右键单击 config.xml 以打开配置设计器。 选择“平台”选项卡，然后在 **Cordova CLI** 文本框中选择 6.1.1。

选择“生成”，然后选择“生成解决方案”以更新项目。

#### <a name="install-the-push-plugin"></a>安装推送插件
Apache Cordova 应用程序不支持在本地处理设备或网络功能。  这些功能由发布在 [npm](https://www.npmjs.com/) 或 GitHub 上的插件提供。  `phonegap-plugin-push` 插件用于处理网络推送通知。

可通过下面其中一种方式安装推送插件：

**从命令提示符：**

运行以下命令：

    cordova plugin add phonegap-plugin-push

**从 Visual Studio 内部：**

1. 在解决方案资源管理器中，打开 `config.xml` 文件，依次单击“插件” > “自定义”，选择 **Git** 作为安装源，然后输入 `https://github.com/phonegap/phonegap-plugin-push` 作为源。
   
   ![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)
2. 单击安装源旁边的箭头。
3. 在 **SENDER_ID** 中，如果已经有 Google Developer Console 项目的数值项目 ID，可将其添加在此处。 否则，输入占位符值，如 777777，并且如果以 Android 为目标，可稍后在 config.xml 中更新该值。
4. 单击 **“添加”**。

现已安装推送插件。

#### <a name="install-the-device-plugin"></a>安装设备插件
按照安装推送插件所用的相同步骤操作，但需在核心插件列表中找到设备插件（依次单击“插件” > “核心”进行查找）。 需使用此插件获取平台名称 (`device.platform`)。

#### <a name="register-your-device-for-push-on-start-up"></a>注册设备，以便启动时推送
起初，会包括一些适用于 Android 的最小代码。 稍后，将进行一些小的修改，以便在 iOS 或 Windows 10 上运行。

1. 回调登录过程期间或在 **onDeviceReady**方法底部，添加对 **registerForPushNotifications** 的调用：
   
        // Login to the service.
        client.login('google')
            .then(function () {
                // Create a table reference
                todoItemTable = client.getTable('todoitem');
   
                // Refresh the todoItems
                refreshDisplay();
   
                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);
   
                    // Added to register for push notifications.
                registerForPushNotifications();
   
            }, handleError);
   
    该示例演示了身份验证成功后调用 **registerForPushNotifications**，当在应用中既使用推送通知又使用身份验证时，建议使用此方法。
2. 按如下所示添加新的 **registerForPushNotifications** 方法：
   
        // Register for Push Notifications. Requires that phonegap-plugin-push be installed.
        var pushRegistration = null;
        function registerForPushNotifications() {
          pushRegistration = PushNotification.init({
              android: { senderID: 'Your_Project_ID' },
              ios: { alert: 'true', badge: 'true', sound: 'true' },
              wns: {}
          });
   
        // Handle the registration event.
        pushRegistration.on('registration', function (data) {
          // Get the native platform of the device.
          var platform = device.platform;
          // Get the handle returned during registration.
          var handle = data.registrationId;
          // Set the device-specific message template.
          if (platform == 'android' || platform == 'Android') {
              // Register for GCM notifications.
              client.push.register('gcm', handle, {
                  mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'iOS') {
              // Register for notifications.            
              client.push.register('apns', handle, {
                  mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'windows') {
              // Register for WNS notifications.
              client.push.register('wns', handle, {
                  myTemplate: {
                      body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                      headers: { 'X-WNS-Type': 'wns/toast' } }
              });
          }
        });
   
        pushRegistration.on('notification', function (data, d2) {
          alert('Push Received: ' + data.message);
        });
   
        pushRegistration.on('error', handleError);
        }
3. (Android) 在上述代码中，将 `Your_Project_ID` 替换成 [Google Developer Console].中应用的数值项目 ID。

## <a name="optional-configure-and-run-the-app-on-android"></a>（可选）在 Android 上配置并运行应用
完成此部分以启用 Android 的推送通知。

#### <a name="a-nameenable-gcmaenable-firebase-cloud-messaging"></a><a name="enable-gcm"></a>启用 Firebase Cloud Messaging
由于我们最初面向的是 Google Android 平台，因此必须启用 Firebase Cloud Messaging。 同样，如果面向 Microsoft Windows 设备，则应启用 WNS 支持。

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="a-nameconfigure-backendaconfigure-the-mobile-app-backend-to-send-push-requests-using-fcm"></a><a name="configure-backend"></a>使用 FCM 配置移动应用后端以发送推送请求
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>配置适用于 Android 的 Cordova 应用
在 Cordova 应用中，打开 config.xml 并将 `Your_Project_ID` 替换成 [Google Developer Console] 中应用的数值项目 ID。

        <plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
            <variable name="SENDER_ID" value="Your_Project_ID" />
        </plugin>

打开 index.js 并更新代码以使用数值项目 ID。

        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

#### <a name="a-nameconfigure-deviceaconfigure-your-android-device-for-usb-debugging"></a><a name="configure-device"></a>配置 Android 设备以进行 USB 调试
需要启用 USB 调试才能将应用部署到 Android 设备。  在 Android 手机上执行以下步骤：

1. 转到“设置” > “关于手机”，然后点击“内部版本号”，直到启用开发者模式（大约 7 次）。
2. 回到“设置” > “开发人员选项”启用“USB 调试”，然后使用 USB 线缆将 Android 手机连接到进行开发的电脑。

已使用运行 Android 6.0 (Marshmallow) 的 Google Nexus 5X 设备对此进行了测试。  但是，任何新式 Android 版本中都普遍具有此技术。

#### <a name="install-google-play-services"></a>安装 Google Play Services
推送插件依赖 Android Google Play 服务发送推送通知。  

1. 在 **Visual Studio**中，依次单击“工具” > “Android” > “Android SDK 管理器”，展开 **Extras** 文件夹并勾选框，以确保已安装以下所有 SDK。
   
   * Android 2.3 或更高版本
   * Google 存储库修订 27 或更高版本
   * Google Play Services 9.0.2 或更高版本
2. 单击“安装程序包”，然后等待安装完成。

[phonegap-plugin-push 安装文档]中列出了当前需要的库。

#### <a name="test-push-notifications-in-the-app-on-android"></a>在 Android 应用中测试推送通知
现在，可以通过运行该应用并将项插入 TodoItem 表中测试推送通知。 只要使用同一后端，就可以在同一设备，或者另一台设备中执行此操作。 通过下面其中一种方式在 Android 平台上测试 Cordova 应用：

* **在物理设备上：**  
  使用 USB 线缆将 Android 设备连接到开发计算机。  不是使用 **Google Android 模拟器**，而是选择“设备”。 Visual Studio 将向设备部署该应用程序并运行它。  你可以随后在该设备上与该应用程序进行交互。  
  提升开发体验。  屏幕共享应用程序（如 [Mobizen]）可以通过将 Android 屏幕投射到电脑的 Web 浏览器中协助开发 Android 应用程序。
* **在 Android 模拟器中：**  
  在模拟器中运行时，还需要其他配置步骤。
  
    请确保在将 Google API 设置为目标的虚拟设备上部署或调试，如下方的 Android 虚拟设备 (AVD) 管理器所示。
  
    ![](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)
  
    如果想要使用更快的 x86 模拟器，请[安装 HAMX 驱动程序](https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM)并配置使用它的模拟器。
  
    通过单击“应用” > “设置” > “添加帐户”，然后按照提示将现有 Google 帐户添加到设备（建议使用现有帐户，而不是新建一个）。
  
    ![](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)
  
    像以前一样运行 todolist 应用并插入一个新的 todo 项。 此时通知区域中会显示一个通知图标。 可以打开通知抽屉查看通知的完整文本。
  
    ![](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>（可选）配置并在 iOS 上运行
本部分介绍了在 iOS 设备上运行 Cordova 项目。 如果不使用 iOS 设备，可以跳过本部分。

#### <a name="install-and-run-the-ios-remotebuild-agent-on-a-mac-or-cloud-service"></a>安装并在 Mac 或云服务上运行 iOS remotebuild 代理
使用 Visual Studio 在 iOS 上运行 Cordova 应用前，请完成 [iOS 安装指南](http://taco.visualstudio.com/en-us/docs/ios-guide/)中的步骤，以安装并运行 remotebuild 代理。

请确保可以生成 iOS 应用。 安装指南中的步骤是在 Visual Studio 中生成 iOS 应用所需的步骤。 如果没有 Mac，可以使用服务（如 MacInCloud）中的 remotebuild 代理生成 iOS 应用。 有关详细信息，请参阅[在云中运行 iOS 应用](http://taco.visualstudio.com/en-us/docs/build_ios_cloud/)。

> [!NOTE]
> 在 iOS 上使用推送插件需要 XCode 7 或更高版本。
> 
> 

#### <a name="find-the-id-to-use-as-your-app-id"></a>查找要作为应用程序 ID 的 ID
在注册应用以推送通知前，在 Cordova 应用中打开 config.xml，找到小组件元素中的 `id` 属性值，复制该值以供将来使用。 在以下 XML 中，ID 是 `io.cordova.myapp7777777`。

        <widget defaultlocale="en-US" id="io.cordova.myapp7777777"
          version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
            xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

稍后，在 Apple 开发人员门户创建应用程序 ID 时，使用此标识符。 （如果在开发人员门户中创建其他应用程序 ID 并且想要使用它，需要按照本教程后半部分中的几个额外步骤，在 config.xml 中更改此 ID。 小组件元素中的 ID 必须匹配开发人员门户中的应用程序 ID。）

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>在 Apple 的开发人员门户上为推送通知注册应用
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[观看显示类似步骤的视频](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>配置 Azure 以发送推送通知
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>验证应用程序 ID 是否匹配 Cordova 应用
如果 Apple 开发人员帐户中创建的应用程序 ID 已与 config.xml 中的小组件元素的 ID 匹配，则可以跳过此步骤。 但是，如果 ID 不匹配，请执行以下步骤：

1. 从项目中删除平台文件夹。
2. 从项目中删除插件文件夹。
3. 从项目中删除 node_modules 文件夹。
4. 更新 config.xml 中的小组件元素的 ID 属性，以便使用 Apple 开发人员帐户中创建的应用程序 ID。
5. 重新生成项目。

##### <a name="test-push-notifications-in-your-ios-app"></a>在 iOS 应用中测试推送通知
1. 在 Visual Studio 中，确保已选择 **iOS** 作为部署目标，然后选择“设备”，以便在已连接的 iOS 设备上运行。
   
    可以使用 iTunes 在已连接到电脑的 iOS 设备上运行。 iOS 模拟器不支持推送通知。
2. 在 Visual Studio 中按“运行”按钮或 **F5** 生成项目并在 iOS 设备中启动应用，然后单击“确定”接受推送通知。
   
   > [!NOTE]
   > 你必须显式接受来自应用程序的推送通知。 此请求只会在首次运行应用程序时出现。
   > 
   > 
3. 在应用中，键入一项任务，然后单击加号 (+) 图标。
4. 检查是否已收到通知，然后单击“确定”以取消通知。

## <a name="optional-configure-and-run-on-windows"></a>（可选）配置并在 Windows 上运行
本部分用于在 Windows 10 设备上运行 Apache Cordova 应用（ Windows 10 支持 PhoneGap 推送插件）。 如果不使用 Windows 设备，可以跳过本部分。

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>向 WNS 注册用于推送通知的 Windows 应用
若要在 Visual Studio 中使用应用商店选项，从解决方案平台列表中选择 Windows 目标，如 **Windows-x64** 或 **Windows-x86**（避免使用 **Windows anycpu** 发送推送通知）。

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[观看显示类似步骤的视频](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push)

#### <a name="configure-the-notification-hub-for-wns"></a>为 WNS 配置通知中心
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>配置 Cordova 应用，以支持 Windows 推送通知
打开配置设计器（右键单击 config.xml，然后选择“视图设计器”），选择“Windows”选项卡，然后选择“Windows 目标版本”下的“Windows 10”。

> [!NOTE]
> 如果使用 Cordova 5.1.1 之前的版本（推荐使用 6.1.1），还必须在 config.xml 中将“支持 Toast 通知”标记设置为 true。
> 
> 

若要在默认（调试）版本中支持推送通知，打开 build.json 文件。 将“版本”配置复制到调试配置。

        "windows": {
            "release": {
                "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
                "publisherId": "CN=yourpublisherID"
            }
        }

进行此更新后，以前的代码应如下所示。

    "windows": {
        "release": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            },
        "debug": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            }
        }

生成应用并确保没有错误。 客户端应用现在应从移动应用后端注册通知。 对于解决方案中的每个 Windows 项目，重复此部分的操作。

#### <a name="test-push-notifications-in-your-windows-app"></a>在 Windows 应用中测试推送通知
在 Visual Studio 中，确保已选择 Windows 平台作为部署目标，例如 **Windows-x64** 或 **Windows-x86**。 若要在托管 Visual Studio 的 Windows 10 电脑上运行该应用，请选择“本地计算机”。

按“运行”按钮生成项目并启动应用程序。

在应用中，为新 todoitem 键入一个名称，然后单击加号 (+) 图标以添加它。

确认在添加该项目时收到了通知。

## <a name="a-namenext-stepsanext-steps"></a><a name="next-steps"></a>后续步骤
* 有关推送通知的信息，请参阅[通知中心]。
* 如果尚未执行此操作，可通过将[身份验证]添加到 Apache Cordova 应用继续该教程。

了解如何使用 SDK。

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- URLs -->
[身份验证]: app-service-mobile-cordova-get-started-users.md
[Apache Cordova 快速入门]: app-service-mobile-cordova-get-started.md
[身份验证]: app-service-mobile-cordova-get-started-users.md
[使用适用于 Azure 移动应用的 .NET 后端服务器 SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Google 帐户]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Google Developer Console]: https://console.developers.google.com/home/dashboard
[phonegap-plugin-push 安装文档]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[用于 Apache Cordova 的 Visual Studio 工具]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[通知中心]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md



<!--HONumber=Nov16_HO3-->


