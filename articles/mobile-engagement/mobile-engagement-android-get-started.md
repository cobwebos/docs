---
title: "适用于 Android 应用的 Azure Mobile Engagement 入门"
description: "了解如何使用适用于 Android 应用且带分析和推送通知功能的 Azure Mobile Engagement。"
services: mobile-engagement
documentationcenter: android
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3c286c6d-cfef-4e3e-9b2c-715429fe82db
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: hero-article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a4b9ab47969c95aa9940e044b426cf2811e23f61


---
# <a name="get-started-with-azure-mobile-engagement-for-android-apps"></a>适用于 Android 应用的 Azure Mobile Engagement 入门
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主题介绍如何借助 Azure Mobile Engagement 了解应用的使用，以及如何向 Android 应用程序的分段用户发送推送通知。
本教程演示使用 Mobile Engagement 的简单广播方案。 在本教程中，将创建一个空白 Android 应用，该应用使用 Google Cloud Messaging (GCM) 收集基本数据以及接收推送通知。

## <a name="prerequisites"></a>先决条件
完成本教程需要你安装 [Android 开发人员工具](https://developer.android.com/sdk/index.html)，其中包含 Android Studio 集成开发环境和最新的 Android 平台。

它还需要 [Mobile Engagement Android SDK](https://aka.ms/vq9mfn)。

> [!IMPORTANT]
> 要完成此教程，需要一个有效的 Azure 帐户。 如果你没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started)。
> 
> 

## <a name="set-up-mobile-engagement-for-your-android-app"></a>为 Android 应用设置 Mobile Engagement
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="connect-your-app-to-the-mobile-engagement-backend"></a>将应用连接到 Mobile Engagement 后端
本教程提供的“基本集成”是收集数据和发送推送通知的最低要求。 通过 Android Studio 创建基本应用，演示该集成。

在 [Mobile Engagement Android SDK 集成](mobile-engagement-android-sdk-overview.md)中可找到完整的集成文档。

### <a name="create-an-android-project"></a>创建 Android 项目
1. 启动“Android Studio”，在弹出的窗口中，选择“启动新的 Android Studio 项目”。
   
    ![][1]
2. 提供应用名称和公司域。 记下填写的内容，因为之后会用到。 单击“下一步”。
   
    ![][2]
3. 选择目标外观规格和 API 级别，单击“下一步” 。
   
   > [!NOTE]
   > Mobile Engagement 要求 API 的最低级别为 10 (Android 2.3.3)。
   > 
   > 
   
    ![][3]
4. 选择“空白活动”（此应用的唯一屏幕），单击“下一步”。
   
    ![][4]
5. 最后，保留默认值，单击“完成” 。
   
    ![][5]

Android Studio 现在已经创建了将在其中集成 Mobile Engagement 的演示应用。

### <a name="include-the-sdk-library-in-your-project"></a>将 SDK 库包含到项目中
1. 下载 [Mobile Engagement Android SDK](https://aka.ms/vq9mfn)。
2. 将存档文件解压到计算机中的文件夹。
3. 确定此 SDK 当前版本的 .jar 库，将其复制到剪贴板。
   
      ![][6]
4. 导航到“项目”  部分 (1)，将 .jar 粘贴在库文件夹中 (2)。
   
      ![][7]
5. 要加载库，请同步项目。
   
      ![][8]

### <a name="connect-your-app-to-mobile-engagement-backend-with-the-connection-string"></a>使用连接字符串将应用连接到 Mobile Engagement 后端
1. 将下面的代码行复制到 creation 活动（只能在应用程序的一个位置完成，通常为 main 活动）。 对于此示例应用，打开“src”->“main”->“java”文件夹下的“MainActivity”，然后添加以下内容：
   
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);
2. 按 Alt+Enter 键或添加以下导入语句解析引用：
   
        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;
3. 返回 Azure 经典门户的应用“连接信息”页，复制“连接字符串”。
   
      ![][9]
4. 将其粘贴到 `setConnectionString` 参数中，替换以下代码中显示的整个字符串：
   
        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### <a name="add-permissions-and-a-service-declaration"></a>添加权限和服务声明
1. 将以下权限添加到项目 Manifest.xml 的 `<application>` 标记之前或之后：
   
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
2. 要声明代理服务，请将此代码添加到 `<application>` 与 `</application>` 标记之间：
   
        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
3. 在粘贴的代码中，替换标签中的 `"<Your application name>"`，该标签位于“设置”菜单中，在该菜单中可以看到设备运行的服务。 例如，你可以在该标签中添加“Service”一词。

### <a name="send-a-screen-to-mobile-engagement"></a>将屏幕发送到 Mobile Engagement
要开始发送数据并确保用户处于活动状态，必须将至少一个屏幕（活动）发送到 Mobile Engagement 后端。

转到 **MainActivity.java**，并添加以下内容，将 **MainActivity** 的基类替换给 **EngagementActivity**：

    public class MainActivity extends EngagementActivity {

> [!NOTE]
> 如果基类不是 *Activity*，请参阅 [高级 Android 报告](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes) ，了解如何从不同的类继承。
> 
> 

对此简单示例方案的以下行添加注释：

    // setSupportActionBar(toolbar);

如果希望在应用中保留 `ActionBar` ，请参阅 [高级 Android 报告](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes)。

## <a name="connect-app-with-realtime-monitoring"></a>将应用与实时监视相连
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="enable-push-notifications-and-inapp-messaging"></a>启用推送通知和应用内消息传送
进行市场活动时，可以使用 Mobile Engagement 通过推送通知和应用内消息传送与用户交互以及向用户进行市场宣传。 在 Mobile Engagement 门户中，此模块称为 REACH。
下一部分介绍如何将应用设置为接收此类通知和消息。

### <a name="copy-sdk-resources-in-your-project"></a>复制项目中的 SDK 资源
1. 导航回 SDK 下载内容，复制“res”  文件夹。
   
    ![][10]
2. 返回到 Android Studio，选择项目文件的“main”  目录，然后粘贴，将资源添加到项目。
   
    ![][11]

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## <a name="next-steps"></a>后续步骤
转到 [Android SDK](mobile-engagement-android-sdk-overview.md) 获取有关 SDK 集成的详细信息。

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png



<!--HONumber=Nov16_HO2-->


