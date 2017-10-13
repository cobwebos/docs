---
title: "Azure Mobile Engagement Android SDK 集成"
description: "Azure Mobile Engagement Android SDK 的最新更新和过程"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: d72b5014-a22b-4a7f-a470-d2b8145b5b86
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/10/2016
ms.author: piyushjo
ms.openlocfilehash: 0282abbf44406cac89c13520bc2a4e375817ed1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-integrate-gcm-with-mobile-engagement"></a>如何使用 Mobile Engagement 集成 GCM
> [!IMPORTANT]
> 必须先按照“如何在 Android 上集成 Engagement”文档中所述的集成过程完成操作，才能接续本指南中的操作。
> 
> 本文档仅在已集成市场宣传模块并计划推送 Google Play 设备时适用。 若要在应用程序中集成市场宣传活动，请首先阅读“如何在 Android 上集成 Engagement Reach”。
> 
> 

## <a name="introduction"></a>介绍
集成 GCM 后可推送应用程序。

推送到 SDK 的 GCM 有效负载通常包含数据对象中的 `azme` 密钥。 因此如果在应用程序中将 GCM 用于其他用途，可以基于该密钥筛选推送。

> [!IMPORTANT]
> 可以使用 GCM 推送仅运行 Android 2.2 或更高版本、已安装 Google Play 和已启用 Google 后台连接的设备；但是，可以在不受支持的设备上安全地集成此代码（它仅使用意向）。
> 
> 

## <a name="create-a-google-cloud-messaging-project-with-api-key"></a>使用 API 密钥创建 Google Cloud Messaging 项目
[!INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

## <a name="sdk-integration"></a>SDK 集成
### <a name="managing-device-registrations"></a>管理设备注册
每台设备必须向 Google 服务器发送注册命令，否则无法对其进行市场宣传。

设备还可以在 GCM 通知中注销（如果应用程序被卸载，则自动将注销设备）。

如果不使用 [Google Play SDK] 或自己尚未发送注册意向，则可以让 Engagement 自动注册设备。

要启用此功能，请将以下内容添加至 `<application/>` 标记内的 `AndroidManifest.xml` 文件中：

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>将注册 ID 传递至 Engagement 推送服务，并接收通知
将以下内容添加至 `<application/>` 标记内的 `AndroidManifest.xml` 文件中（即使自行管理设备注册也是如此），以将设备的注册 ID 传递至 Engagement 推送服务并接收其通知：

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

请确保在 `AndroidManifest.xml`（在 `</application>` 标记后）中具有以下权限。

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

## <a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>将 Mobile Engagement 访问权限授予 GCM API 密钥
按照[本指南](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key)执行操作授予 Mobile Engagement 访问 GCM API 密钥的权限。

[Google Play SDK]:https://developers.google.com/cloud-messaging/android/start
