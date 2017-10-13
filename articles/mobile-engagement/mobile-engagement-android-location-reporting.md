---
title: "Azure Mobile Engagement Android SDK 的位置报告"
description: "描述如何配置 Azure Mobile Engagement Android SDK 的位置报告"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6cab5ed1-b767-46ac-9f0b-48a4e249d88c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 777d5719cce505b55dfb61c91dcac7e713b077a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Azure Mobile Engagement Android SDK 的位置报告
> [!div class="op_single_selector"]
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

本主题介绍如何为 Android 应用程序进行位置报告。

## <a name="prerequisites"></a>先决条件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>位置报告
如果想报告位置，则需要添加几行配置（在 `<application>` 和 `</application>` 标记之间）。

### <a name="lazy-area-location-reporting"></a>简易区域位置报告
简易区域位置报告允许报告国家、地区和与设备关联的区域。 这种类型的位置报告仅使用网络位置（基于 Cell ID 或 WIFI）。 每个会话最多报告一次设备区域。 从不使用 GPS，因此，此类型的位置报告对电池的影响非常小。

报告的区域用于计算用户、会话、事件和错误相关的地理统计信息。 还可用作市场宣传活动中的条件。

通过使用先前在此过程中提到的配置，启用简易区域位置报告：

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

还需要指定一个位置权限。 此代码使用 ``COARSE`` 权限︰

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

如果应用需要该权限，可以使用 ``ACCESS_FINE_LOCATION`` 来替代。

### <a name="real-time-location-reporting"></a>实时位置报告
实时位置报告支持报告与设备关联的纬度和经度。 默认情况下，这种类型的位置报告仅使用基于 Cell ID 或 WIFI 的网络位置。 实时位置报告仅在应用程序在前台中运行时（例如，在会话期间）可用。

实时位置*不*用于计算统计信息。 其唯一用途是：允许在市场宣传活动中使用实时地理围范围 \<Reach-Audience-geofencing\> 标准。

要启用实时位置报告，请将一行代码添加到在启动器活动中设置 Engagement 连接字符串的位置。 结果应类似如下所示：

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>基于 GPS 的报告
默认情况下，实时位置报告仅使用基于网络的位置。 若要启用基于 GPS 的位置（其更为精确），请使用配置对象：

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

如果用户重新启动其设备，则会停止后台位置报告。 要使其在引导时自动重新启动，请添加此代码。

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

如果没有此权限，则还需要添加以下权限：

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Android M 权限
从 Android M 开始，某些权限会在运行时进行管理并需要用户批准。

如果面向 Android API 级别 23，则安装新应用时，默认会关闭运行时权限。 否则，默认会打开。

可以从设备“设置”菜单启用/禁用这些权限。 从系统菜单关闭的权限会终止应用的后台进程，这是系统行为，并不影响在后台接收推送功能。

在 Mobile Engagement 位置报告的上下文中，需要在运行时审核的权限包括：

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`

使用标准系统对话框从用户请求权限。 如果用户批准，请通知 ``EngagementAgent`` 实时考虑该更改。 否则更改会在下次用户启动应用程序时进行处理。

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
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
