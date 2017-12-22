---
title: "Azure Mobile Engagement Android SDK 集成"
description: "Azure Mobile Engagement Android SDK 的最新更新和过程"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a5487793-1a12-4f6c-a1cf-587c5a671e6b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 35bd92e52b7a02f58620a03156902f9f91be57ae
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="how-to-integrate-engagement-on-android"></a>如何在 Android 上集成 Engagement
> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

本过程介绍在 Android 应用程序中激活 Engagement 的分析和监视功能的最简单方法。

> [!IMPORTANT]
> 最低的 Android SDK API 等级必须为 10 或更高版本（Android 2.3.3 或更高版本）。
> 
> 

遵循以下步骤，即可激活用于计算有关用户、会话、活动、崩溃和技术参数的所有统计数据所需的日志报告。 用于计算事件、错误和作业等其他统计信息所需的日志报告必须使用 Engagement API 手动完成（请参阅[如何在 Android 中使用高级 Mobile Engagement 标记 API](mobile-engagement-android-use-engagement-api.md)），因为这些统计信息是应用程序依赖项。

## <a name="embed-the-engagement-sdk-and-service-into-your-android-project"></a>将 Engagement SDK 和服务嵌入 Android 项目
从 [此处](https://aka.ms/vq9mfn) 下载 Android SDK，获取 `mobile-engagement-VERSION.jar` 并将其放入 Android 项目的 `libs` 文件夹 中（如果此 lib 文件夹尚不存在，则创建此文件夹）。

> [!IMPORTANT]
> 如果使用 ProGuard 生成应用程序包，则需要保留某些类。 可以使用以下配置代码段：
> 
> -keep public class * extends android.os.IInterface -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
> 
> <methods>; }
> 
> 

通过在启动器活动中调用以下方法，指定 Engagement 连接字符串：

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

应用程序的连接字符串会显示在 Azure 门户上。

* 如有缺失，请添加以下 Android 权限（在 `<application>` 标记前）：
  
          <uses-permission android:name="android.permission.INTERNET"/>
          <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
* 添加以下部分（在 `<application>` 和 `</application>` 标记之间）：
  
          <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>Service"
            android:process=":Engagement"/>
* 将 `<Your application name>` 更改为应用程序名称。

> [!TIP]
> `android:label` 属性允许选择 Engagement 服务名称，它将显示在最终用户手机的“正在运行的服务”屏幕中。 建议将此属性设置为 `"<Your application name>Service"`（例如 `"AcmeFunGameService"`）。
> 
> 

指定 `android:process` 属性可确保 Engagement 服务会在其自己的进程（在与应用程序相同的进程中运行 Engagement，将影响主要/UI 线程的响应速度）中运行。

> [!NOTE]
> 置于 `Application.onCreate()` 和其他应用程序回调中的任意代码将针对所有应用程序进程运行，包括 Engagement 服务。 这可能会产生不需要的副作用（例如，Engagement 进程中不需要的内存分配和线程、重复广播接收器或服务）。
> 
> 

如果重写 `Application.onCreate()`，建议在 `Application.onCreate()` 函数开头添加以下代码段：

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;

               ... Your code...
             }

可以对 `Application.onTerminate()`、`Application.onLowMemory()` 和 `Application.onConfigurationChanged(...)` 执行相同的操作。

也可以扩展 `EngagementApplication`，而不是扩展 `Application`：仅在当前进程不是托管 Engagement 服务的进程时，回调 `Application.onCreate()` 执行进程检查并调用 `Application.onApplicationProcessCreate()`，相同的规则适用于其他回调。

## <a name="basic-reporting"></a>基本报告
### <a name="recommended-method-overload-your-activity-classes"></a>建议方法：重载 `Activity` 类
若要激活 Engagement 计算用户、会话、活动、崩溃和技术人员统计信息所需的所有日志报告，只需使全部 `*Activity` 子类均继承自相应的 `Engagement*Activity` 类（例如，如果旧活动扩展了 `ListActivity`，则使其扩展 `EngagementListActivity`）。

**没有 Engagement：**

            package com.company.myapp;

            import android.app.Activity;
            import android.os.Bundle;

            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**有 Engagement：**

            package com.company.myapp;

            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;

            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [!IMPORTANT]
> 使用 `EngagementListActivity` 或 `EngagementExpandableListActivity` 时，请确保先调用 `requestWindowFeature(...);`，再调用 `super.onCreate(...);`，否则系统会崩溃。
> 
> 

可以在 `src` 文件夹中找到这些类，然后将其复制到项目中。 该类还存在于 **JavaDoc** 中。

### <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>备用方法：手动调用 `startActivity()` 和 `endActivity()`
如果无法或不想重载 `Activity` 类，可以直接调用 `EngagementAgent` 方法来启动和结束活动。

> [!IMPORTANT]
> 即使在应用程序关闭后（实际上，Android 上的应用程序永远不会关闭），Android SDK 也从不调用 `endActivity()` 方法。 因此，*强烈*建议在“*所有*”活动的 `onResume` 回调中调用 `startActivity()` 方法，在“*所有*”活动的 `onPause()` 回调中调用 `endActivity()` 方法。 这是确保会话不会遗漏的唯一方法。 如果会话遗漏，Engagement 服务将永远不从 Engagement 后端断开（因为只要会话处于挂起状态，该服务就会保持连接）。
> 
> 

下面是一个示例：

            public class MyActivity extends Some3rdPartyActivity
            {
              @Override
              protected void onResume()
              {
                super.onResume();
                String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
                EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
              }

              @Override
              protected void onPause()
              {
                super.onPause();
                EngagementAgent.getInstance(this).endActivity();
              }
            }

此示例非常类似于 `EngagementActivity` 类及其变体，它的源代码在 `src` 文件夹中提供。

## <a name="test"></a>测试
现在，通过在仿真器或设备中运行移动应用，并验证它是否在“监视器”选项卡上注册会话来验证集成。

以下各节均为可选。

## <a name="location-reporting"></a>位置报告
如果想报告位置，则需要添加几行配置（在 `<application>` 和 `</application>` 标记之间）。

### <a name="lazy-area-location-reporting"></a>简易区域位置报告
简易区域位置报告允许报告国家、地区和关联到设备的位置。 这种类型的位置报告仅使用网络位置（基于 Cell ID 或 WIFI）。 每个会话最多报告一次设备区域。 从不使用 GPS，因此，此类型的位置报告对电池的影响非常小（但不是没有）。

报告的区域用于计算用户、会话、事件和错误相关的地理统计信息， 还可用作市场宣传活动中的条件。

要启用简易区域位置报告，可以通过使用本指南中先前提到的配置操作即可：

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

如果没有此权限，则还需要添加以下权限：

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

或者，如果已在应用程序中使用 ``ACCESS_FINE_LOCATION``，则可以继续使用。

### <a name="real-time-location-reporting"></a>实时位置报告
实时位置报告允许报告关联到设备的纬度和经度。 默认情况下，此类型的位置报告仅使用网络位置（基于 Cell ID 或 WIFI），且报告仅在应用程序在前台中运行时（例如，在会话期间）可用。

实时位置*不会*用于计算统计信息。 其唯一用途是，允许在市场宣传活动中使用实时地理范围 \<Reach-Audience-geofencing\> 标准。

要启用实时位置报告，可以通过使用本指南中先前提到的配置操作即可：

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

如果没有此权限，则还需要添加以下权限：

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

或者，如果已在应用程序中使用 ``ACCESS_FINE_LOCATION``，则可以继续使用。

#### <a name="gps-based-reporting"></a>基于 GPS 的报告
默认情况下，实时位置报告仅使用基于网络的位置。 要启用基于 GPS 的位置（更为精确），请使用以下配置对象：

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

如果没有此权限，则还需要添加以下权限：

            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>后台报告
默认情况下，实时位置报告仅在应用程序在前台中运行时（例如，在会话期间）可用。 要在后台也启用报告，请使用以下配置对象：

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> 应用程序在后台中运行时，即使已启用 GPS，也仅报告基于网络的位置。
> 
> 

如果用户重新启动后台设备，则后台位置报告就将停止，可以添加以下内容，使该报告在开机时自动重新启动：

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

如果没有此权限，则还需要添加以下权限：

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### <a name="android-m-permissions"></a>Android M 权限
从 Android M 开始，某些权限会在运行时进行管理并需要用户批准。

如果面向 Android API 级别 23，则新应用安装将默认关闭运行时的权限。 否则，就会默认打开。

用户可以从设备“设置”菜单启用/禁用这些权限。 从系统菜单关闭的权限会终止应用的后台进程，这是系统行为，并不影响在后台接收推送功能。

在 Mobile Engagement 的上下文中，需要在运行时审核的权限包括：

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`
* `WRITE_EXTERNAL_STORAGE`（此项仅适用于面向 Android API 级别 23 的情况）

外部存储仅用于市场宣传大图功能。 如果发现向用户要求此权限会引起混乱，而你只是为了 Mobile Engagement 才使用它，则可以删除它，但代价是禁用大图功能。

对于位置功能，则应使用标准系统对话框向用户请求权限。 如果用户同意，需要告诉 ``EngagementAgent`` 实时考虑这些更改（否则该更改会在下一次用户启动应用程序时处理）。

下面是要在应用程序的活动中使用的代码示例，如果 ``EngagementAgent`` 为正，则请求权限并转发结果：

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

## <a name="advanced-reporting"></a>高级报告
此外，如果想报告应用程序特定事件、错误和作业，则需要通过 `EngagementAgent` 类的方法使用 Engagement API。 此类的对象可以通过调用 `EngagementAgent.getInstance()` 静态方法进行检索。

Engagement API 允许使用 Engagement 的所有高级功能，详细信息请参见“如何在 Android 上使用 Engagement API”（以及 `EngagementAgent` 类技术文档）。

## <a name="advanced-configuration-in-androidmanifestxml"></a>高级配置（在 AndroidManifest.xml 中）
### <a name="wake-locks"></a>唤醒锁
如果想在使用 Wifi 或屏幕关闭时仍确保实时发送统计信息，请添加以下可选权限：

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

### <a name="crash-report"></a>崩溃报告
如果要禁用崩溃报告，请添加以下代码（在 `<application>` 和 `</application>` 标记之间）：

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>突发阈值
默认情况下，Engagement 服务实时报告日志。 如果应用程序频繁报告日志，最好对日志进行缓冲并在固定的时间一次性进行报告（称为“突发模式”）。 要执行此操作，请添加以下代码（在 `<application>` 和 `</application>` 标记之间）：

            <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

突发模式会略微增加电池寿命，但会对 Engagement 监视器造成影响：所有会话和任务持续时间均被取整为突发阈值（这样，小于突发阈值的会话和任务可能不会显示）。 建议使用的突发阈值不长于 30000（30 秒）。

### <a name="session-timeout"></a>会话超时
默认情况下，会话是在其上一活动结束 10 秒后结束（这种情况通常会在按 Home 或 Back 键、设置电话空闲或跳转到其他应用程序时出现）。 这是为了避免用户每次快速退出和返回应用程序时的会话拆分（用户在执行选取图像、检查通知等操作时可能出现此问题）。 可能需要修改这个参数。 要执行此操作，请添加以下代码（在 `<application>` 和 `</application>` 标记之间）：

            <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>禁用日志报告
### <a name="using-a-method-call"></a>使用方法调用
如果想要 Engagement 停止发送日志，可调用：

            EngagementAgent.getInstance(context).setEnabled(false);

该调用是持续性的：它使用共享的首选项文件。

如果 Engagement 在调用此函数时处于活动状态，则可能需要 1 分钟来停止该服务。 但在下次启动应用程序时，该调用将完全不会启动该服务。

可通过调用 `true` 的同一函数，即可再次启用日志报告。

### <a name="integration-in-your-own-preferenceactivity"></a>在自己的 `PreferenceActivity` 中集成
如不调用此函数，还可以在现有的 `PreferenceActivity` 中直接集成此设置。

可在 `AndroidManifest.xml` 文件中使用 `application meta-data`，配置 Engagement，以使用首选项文件：

* `engagement:agent:settings:name` 关键字用于定义共享的首选项文件的名称。
* `engagement:agent:settings:mode` 关键字可用于定义共享的首选项文件的模式，应使用在 `PreferenceActivity` 中使用的同一模式。 该模式必须以数字传递：如果在代码中使用常量标志的组合，请检查总值。

Engagement 在首选项文件内始终使用 `engagement:key` 布尔型关键字，以用于管理该设置。

以下的 `AndroidManifest.xml` 示例显示默认值：

            <application>
                [...]
                <meta-data
                  android:name="engagement:agent:settings:name"
                  android:value="engagement.agent" />
                <meta-data
                  android:name="engagement:agent:settings:mode"
                  android:value="0" />

然后，可在首选项布局中添加 `CheckBoxPreference`，如下所示：

            <CheckBoxPreference
              android:key="engagement:enabled"
              android:defaultValue="true"
              android:title="Use Engagement"
              android:summaryOn="Engagement is enabled."
              android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
