---
title: "要开始使用 Android 应用 Azure Mobile Engagement"
description: "了解如何使用分析和推送通知的 Azure Mobile Engagement 用于 Android 应用。"
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
ms.openlocfilehash: dc255a930bf71e6ef6d964bc5e3472a38ce4e467
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-android-apps"></a>对于 Android 应用入门 Azure Mobile Engagement
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主题说明了如何使用 Azure Mobile Engagement 了解应用程序的使用情况以及如何向分段的 Android 应用程序的用户发送推送通知。
本教程演示使用 Mobile Engagement 的简单广播的方案。 在它，你将创建一个空白 Android 应用，收集基本的数据，并使用 Google Cloud Messaging (GCM) 接收推送通知。

## <a name="prerequisites"></a>必要條件
完成本教程需要[Android 开发人员工具](https://developer.android.com/sdk/index.html)，其中包含 Android Studio 集成开发环境和最新的 Android 平台。

它还要求[Mobile Engagement Android SDK](https://aka.ms/vq9mfn)。

> [!IMPORTANT]
> 若要完成本教程，你需要有效的 Azure 帐户。 如果你没有帐户，可以在几分钟内创建一个免费试用帐户。 有关详细信息，请参阅[Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started)。
>
>

## <a name="set-up-mobile-engagement-for-your-android-app"></a>为 Android 应用程序设置 Mobile Engagement
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="connect-your-app-to-the-mobile-engagement-backend"></a>将应用程序连接到 Mobile Engagement 后端
本教程介绍了"基本集成"，这是收集数据和发送推送通知所需的最小集。 可以使用 Android Studio 演示集成创建基本应用程序。

在找不到完成集成文档[Mobile Engagement Android SDK 集成](mobile-engagement-android-sdk-overview.md)。

### <a name="create-an-android-project"></a>创建 Android 项目
1. 启动**Android Studio**，然后在弹出窗口中，选择**启动新的 Android Studio 项目**。

    ![][1]
2. 提供的应用程序名称和公司域。 因为需要更高版本，请记下你将充满。 单击“下一步” 。

    ![][2]
3. 选择目标外形规格和 API 级别，然后单击**下一步**。

   > [!NOTE]
   > Mobile Engagement 要求 API 级别 10 最小 (Android 2.3.3)。
   >
   >

    ![][3]
4. 选择**空白活动**此处，这是唯一能够为此应用程序并单击的屏幕**下一步**。

    ![][4]
5. 最后，将保持默认值是以及单击**完成**。

    ![][5]

Android Studio 现在创建到其中我们集成 Mobile Engagement 演示应用程序。

### <a name="include-the-sdk-library-in-your-project"></a>在你项目中包含的 SDK 库
1. 下载[移动用户参与策略 Android SDK](https://aka.ms/vq9mfn)。
2. 存档将文件提取到你的计算机中的文件夹。
3. 确定此 SDK 的当前版本的.jar 库并将其复制到剪贴板。

      ![][6]
4. 导航到**项目**部分 （1） 和 (2) 的 libs 文件夹中粘贴.jar。

      ![][7]
5. 若要加载的库，同步项目。

      ![][8]

### <a name="connect-your-app-to-mobile-engagement-backend-with-the-connection-string"></a>将应用程序连接到 Mobile Engagement 后端使用的连接字符串
1. 将以下代码行复制到活动创建 （必须仅在你的应用程序，通常的主要活动的一个位置中完成）。 有关此示例应用，在 src MainActivity，请打开-> main-> java 文件夹并添加以下：

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);
2. 通过按 Alt + Enter 或添加以下 import 语句解析的引用：

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;
3. 返回到你的应用中的 Azure 经典门户**连接信息**页和复制**连接字符串**。

      ![][9]
4. 将其粘贴到`setConnectionString`参数，替换整个字符串中的以下代码所示：

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### <a name="add-permissions-and-a-service-declaration"></a>添加权限和服务声明
1. 将这些权限添加到你的项目的 Manifest.xml 之前, 或之后立即`<application>`标记：

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
2. 若要声明的代理服务，请添加此代码之间`<application>`和`</application>`标记：

        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
3. 在代码中粘贴，替换`"<Your application name>"`在标签中，这显示在**设置**其中你可以看到在设备上运行的服务的菜单。 你可以在该标签中添加单词"服务"，例如所示。

### <a name="send-a-screen-to-mobile-engagement"></a>将屏幕发送到移动用户参与策略
若要开始发送数据，并确保用户处于活动状态，必须将至少一个屏幕 （活动） 发送到 Mobile Engagement 后端中。

转到**MainActivity.java**并添加以下要替换的基本类**MainActivity**到**EngagementActivity**:

    public class MainActivity extends EngagementActivity {

> [!NOTE]
> 如果基类不是*活动*，请查阅[高级 Android Reporting](mobile-engagement-android-advanced-reporting.md)有关如何从不同的类继承。
>
>

注释掉此简单示例方案的以下行：

    // setSupportActionBar(toolbar);

如果你想要保留`ActionBar`中你的应用，请参阅[高级 Android Reporting](mobile-engagement-android-advanced-reporting.md)。

## <a name="connect-app-with-real-time-monitoring"></a>将应用程序连接了实时监视
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="enable-push-notifications-and-in-app-messaging"></a>启用推送通知和应用内消息传递
在市场活动，Mobile Engagement 允许你进行交互并访问你的用户推送通知和应用内消息传递。 此模块称为 REACH Mobile Engagement 门户中。
以下部分将设置为你的应用程序接收它们。

### <a name="copy-sdk-resources-in-your-project"></a>在你的项目中的复制 SDK 资源
1. 向后定位到 SDK 下载内容并复制**res**文件夹。

    ![][10]
2. 返回到 Android Studio 中，选择**主要**目录的项目文件，然后将其将资源添加到你的项目粘贴。

    ![][11]

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## <a name="next-steps"></a>后续步骤
转到[Android SDK](mobile-engagement-android-sdk-overview.md)以获取有关 SDK 集成的详细的知识。

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
