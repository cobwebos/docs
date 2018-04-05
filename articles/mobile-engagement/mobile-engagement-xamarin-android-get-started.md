---
title: 适用于 Xamarin.Android 的 Azure Mobile Engagement 入门
description: 了解如何使用适用于 Xamarin.Android 应用的具有分析和推送通知功能的 Azure Mobile Engagement。
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: ''
ms.assetid: fb68cf98-08a2-41b5-8e59-757469de3fe7
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/16/2016
ms.author: piyushjo
ms.openlocfilehash: 4ac6e85a0143fc083c22a92e99503689f463d813
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-xamarinandroid-apps"></a>适用于 Xamarin.Android 应用的 Azure Mobile Engagement 入门
> [!IMPORTANT]
> Azure Mobile Engagement 已在 2018 年 3 月 31 日停用。 此页将在不久之后删除。
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主题介绍如何借助 Azure Mobile Engagement 了解应用的使用，以及如何向 Xamarin.Android 应用程序的细分用户发送推送通知。
本教程演示使用 Mobile Engagement 的简单广播方案。 在其中，创建一个空白 Xamarin.Android 应用，用于通过 Google Cloud Messaging (GCM) 接收推送通知。

> [!NOTE]
> Azure Mobile Engagement 服务将于 2018 年 3 月停用，且当前仅向现有客户提供。 有关详细信息，请参阅 [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/)。

本教程需要的内容如下：

* [Xamarin Studio](http://xamarin.com/studio)。 也可以将 Xamarin 与 Visual Studio 配合使用，但是本教程使用的是 Xamarin Studio。 如需安装说明，请参阅 [设置和安装 Visual Studio 和 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx)。
* [Mobile Engagement Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> 要完成本教程，必须有一个有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started)。
> 
> 

## <a id="setup-azme"></a>为 Android 应用设置 Mobile Engagement
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>将应用连接到 Mobile Engagement 后端
本教程提供的“基本集成”是收集数据和发送推送通知的最低要求。 

通过 Xamarin Studio 创建一个基本应用即可演示该集成：

### <a name="create-a-new-xamarinandroid-project"></a>创建新的 Xamarin.Android 项目
1. 启动“Xamarin Studio”，转到“文件” -> “新建” -> “解决方案” 
   
    ![][1]
2. 选择“Android 应用”，确保所选语言为“C#”，并单击“下一步”。
   
    ![][2]
3. 填写“应用名称”和“组织标识符”。 确保勾选“Google Play Services”，并单击“下一步”。 
   
    ![][3]
4. 如果需要，更新“项目名称”、“解决方案名称”和“位置”，并单击“创建”。
   
    ![][4]

Xamarin Studio 将创建在其中集成 Mobile Engagement 的应用。 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>将应用连接到 Mobile Engagement 后端
1. 右键单击“解决方案”窗口中的“包”文件夹并选择“添加包...”
   
    ![][5]
2. 搜索 **Microsoft Azure Mobile Engagement Xamarin SDK** 并将其添加到解决方案。  
   
    ![][6]
3. 打开 **MainActivity.cs** 并添加以下 using 语句：
   
        using Microsoft.Azure.Engagement;
        using Microsoft.Azure.Engagement.Activity;
4. 在 `OnCreate` 方法中，添加以下代码来初始化与 Mobile Engagement 后端的连接。 请确保添加 **ConnectionString**。 
   
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
        EngagementAgent.Init(engagementConfiguration);

### <a name="add-permissions-and-a-service-declaration"></a>添加权限和服务声明
1. 打开“属性”文件夹下的 **Manifest.xml** 。 选择“源”选项卡，以便直接更新 XML 源。
2. 在紧挨 `<application>` 标记之前或之后，将这些权限添加到项目的 Manifest.xml 文件（可在“属性”文件夹下找到）中：
   
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
3. 将以下代码添加到 `<application>` 和 `</application>` 标记之间以声明代理服务：
   
        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
4. 在刚刚粘贴的代码中，替换标签中的 `"<Your application name>"`。 它会显示在“ **设置** ”菜单中，用户可以在此查看设备上运行的服务。 例如，可以在该标签中添加“Service”一词。

### <a name="send-a-screen-to-mobile-engagement"></a>发送屏幕到 Mobile Engagement
为了开始发送数据并确保用户处于活动状态，必须将至少一个屏幕发送到 Mobile Engagement 后端。 为此，请确保 `MainActivity` 继承自 `EngagementActivity` 而非 `Activity`。

    public class MainActivity : EngagementActivity

或者，如果无法继承自 `EngagementActivity`，则必须分别在 `OnResume` 和 `OnPause` 中添加 `.StartActivity` 和 `.EndActivity` 方法。  

        protected override void OnResume()
            {
                EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
                base.OnResume();             
            }

            protected override void OnPause()
            {
                EngagementAgent.EndActivity();
                base.OnPause();            
            }

## <a id="monitor"></a>将应用与实时监视相连接
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>启用推送通知和应用内消息传送
在市场推广活动中，可以使用 Mobile Engagement 通过推送通知和应用内消息传送与 REACH 模块和用户进行交互。 在 Mobile Engagement 门户中，此模块称为 REACH。
以下各部分介绍如何设置应用以接收推送通知。

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png
