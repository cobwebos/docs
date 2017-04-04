---
title: "开始将 Azure Mobile Engagement 用于 Cordova/Phonegap"
description: "了解如何将带分析和推送通知功能的 Azure Mobile Engagement 用于 Cordova/Phonegap 应用。"
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 54fe9113-e239-4ed7-9fd1-a502d7ac7f47
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-phonegap
ms.devlang: js
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: d7a761310782faab1dda023785f93cf90742e2ae
ms.lasthandoff: 03/29/2017


---
# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>开始将 Azure Mobile Engagement 用于 Cordova/Phonegap
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主题介绍如何借助 Azure Mobile Engagement 了解应用的使用，并向通过 Cordova 开发的移动应用程序的分类用户发送推送通知。

本教程将使用 Mac 创建一个空的 Cordova 应用，然后集成 Mobile Engagement SDK。 本教程通过用于 iOS 的 Apple Push Notification 系统 (APNS) 以及用于 Android 的 Google Cloud Messaging (GCM) 收集基本的分析数据并接收通知。 这两个系统将部署到 iOS 或 Android 设备进行测试。 

> [!NOTE]
> 若要完成本教程，你必须有一个有效的 Azure 帐户。 如果你没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started)。
> 
> 

本教程需要的内容如下：

* XCode，从 Mac App Store 安装后即可部署到 iOS
* [Android SDK 和模拟器](http://developer.android.com/sdk/installing/index.html)（用于部署到 Android）
* 推送通知证书 (.p12)，可从 Apple APNS 开发中心获取
* GCM 项目编号，可从用于 GCM 的 Google Developer Console 获取
* [Mobile Engagement Cordova 插件](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [!NOTE]
> 可以在 [GitHub](https://github.com/Azure/azure-mobile-engagement-cordova) 上查找 Cordova 插件的源代码和自述文件
> 
> 

## <a id="setup-azme"></a>为 Cordova 应用设置 Mobile Engagement
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>将应用连接到 Mobile Engagement 后端
本教程提供的“基本集成”是收集数据和发送推送通知的最低要求。 

通过 Cordova 创建基本应用即可演示该集成：

### <a name="create-a-new-cordova-project"></a>创建新的 Cordova 项目
1. 在 Mac 计算机上启动“终端”窗口，键入以下命令，从默认模板创建新的 Cordova 项目。 确保最终用来部署 iOS 应用的发布配置文件使用“com.mycompany.myapp”作为应用程序 ID。 
   
        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova
2. 执行以下命令，针对 **iOS** 配置项目并让其在 iOS 模拟器中运行：
   
        $ cordova platform add ios 
        $ cordova run ios
3. 执行以下命令，针对 **Android** 配置项目并让其在 Android 模拟器中运行。 确保在 Android SDK 模拟器设置中将“目标”设置为“Google API (Google Inc.)”，将“CPU/ABI”设置为“Google API ARM”。  
   
        $ cordova platform add android
        $ cordova run android
4. 添加 Cordova 控制台插件。 

    ```
    $ cordova plugin add cordova-plugin-console
    ``` 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>将应用连接到 Mobile Engagement 后端
1. 安装 Azure Mobile Engagement Cordova 插件，同时提供配置该插件的变量值：
   
        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
             --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Android Reach 图标* ：必须是资源的名称，没有任何扩展，也没有可绘制的前缀（示例：mynotificationicon），且必须将图标文件复制到 Android 项目 (platforms/android/res/drawable) 中

iOS 市场宣传图标：必须是带扩展的资源名称（示例：mynotificationicon.png），且必须通过 XCode 将图标文件添加到 iOS 项目中（使用“添加文件”菜单）

## <a id="monitor"></a>启用实时监视
1. 在 Cordova 项目中 - 编辑 **www/js/index.js** 添加对 Mobile Engagement 的调用，在接收到 *deviceReady* 事件后声明新活动。
   
         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }
2. 运行应用程序：
   
   * **对于 iOS**
     
       在 `Terminal` 窗口中，通过执行以下命令在新的模拟器实例中启动应用：
     
           cordova run ios
   * **对于 Android**
     
       在 `Terminal` 窗口中，通过执行以下命令在新的模拟器实例中启动应用：
     
           cordova run android
3. 可以在控制台日志中看到以下内容：
   
        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

## <a id="monitor"></a>将应用与实时监视相连
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>启用推送通知和应用内消息传送
进行营销活动时，可以使用 Mobile Engagement 通过推送通知和应用内消息传送与用户交互。 在 Mobile Engagement 门户中，此模块称为 REACH。
以下部分会将应用设置为接收此类通知和消息。

### <a name="configure-push-credentials-for-mobile-engagement"></a>为 Mobile Engagement 配置推送凭据
若要让 Mobile Engagement 代表用户发送推送通知，需授予其访问用户 Apple iOS 证书或 GCM 服务器 API 密钥的权限。 

1. 导航到 Mobile Engagement 门户。 确保所在位置为本项目所用应用内，然后单击底部的“参与”按钮： 
   
    ![][1]
2. 用户将位于 Engagement 门户的设置页中。 在该处单击“原生推送”部分： 
   
    ![][2]
3. 配置 iOS 证书/GCM 服务器 API 密钥
   
    **[iOS]**
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择 p12、上传，然后键入密码：
   
    ![][3]
   
    **[Android]**
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“GCM 设置”部分中“API 密钥”前面的编辑图标，然后在显示的弹出窗口中粘贴 GCM 服务器密钥，并单击“确定”。 
   
    ![][4]

### <a name="enable-push-notifications-in-the-cordova-app"></a>在 Cordova 应用中启用推送通知
通过编辑 **www/js/index.js** 添加对 Mobile Engagement 的调用，请求推送通知并声明处理程序：

     onDeviceReady: function() {
           Engagement.initializeReach(  
                 // on OpenUrl  
                 function(_url) {   
                 alert(_url);   
                 });  
            Engagement.startActivity("myPage",{});  
        }

### <a name="run-the-app"></a>运行应用程序
**[iOS]**

1. 为了测试推送通知，需使用 XCode 生成应用并将其部署到设备上，因为 iOS 只允许将通知推送到实际设备上。 转到创建 Cordova 项目的位置，然后导航到 **...\platforms\ios** 位置。 在 XCode 中打开本机 .xcodeproj 文件。 
2. 使用帐户和应用 ID 生成 Cordova 应用并将其部署到 iOS 设备，其中，帐户的预配配置文件包含的证书刚上载到 Mobile Engagement 门户，应用 ID 则与创建 Cordova 应用时提供的应用 ID 一致。 可在 XCode 中查看 **Resources\*-info.plist** 文件中的 Bundle 标识符，确保其一致。 
3. 此时会在设备上显示标准的 iOS 弹出窗口，指出该应用请求发送通知的权限。 授予权限。 

**[Android]**

可以直接使用模拟器运行 Android 应用，因为 Android 模拟器支持 GCM 通知。 

    cordova run android

## <a id="send"></a>向应用发送通知
现在需创建一个简单的推送通知活动，将推送通知发送到在设备上运行的应用：

1. 导航到 Mobile Engagement 门户中的“Reach”选项卡 
2. 单击“新建公告”创建推送活动 
   
    ![][6]
3. 提供创建活动所需的输入 **[Android]**
   
   * 提供市场活动的**名称**。 
   * 选择“简单”作为系统通知的“传递类型”
   * 选择“任何时间”作为“传递时间”
   * 提供通知的“标题”，作为推送通知的第一行。
   * 提供通知的“消息”，作为消息正文。 
     
     ![][11]
4. 提供创建活动所需的输入 **[iOS]**
   
   * 提供市场活动的**名称**。 
   * 选择“仅限应用外”作为“传递时间”
   * 提供通知的“标题”，作为推送通知的第一行。
   * 提供通知的“消息”，作为消息正文。 
     
     ![][12]
5. 向下滚动，然后在内容部分中选择“仅通知” 
   
    ![][8]
6. [可选] 也可提供操作 URL。 确保在配置插件的 **AZME\_REDIRECT\_URL** 变量时，使用所提供的 URL 方案，例如，*myapp://test*。  
7. 已设置完可能情况下最基本的市场活动。 现在再次向下滚动，然后单击“创建”  按钮保存市场活动。
8. 最后是“激活”活动 
   
    ![][10]
9. 此时会在设备或模拟器上显示此活动的推送通知。 

## <a id="next-steps"></a>后续步骤
[Overview of all methods available with Cordova Mobile Engagement SDK](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png


