---
title: "Azure Mobile Engagement Android SDK 集成"
description: "Azure Mobile Engagement Android SDK 的最新更新和过程"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a7d719ec-67b3-4be3-9d7f-0b61a57fe978
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 928c3d85f98f0bd073a5aaadf0ae4495840c5da6
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-integrate-adm-with-engagement"></a>如何将 ADM 与 Engagement 集成到一起
> [!IMPORTANT]
> 你必须先按照“如何在 Android 上集成 Engagement”文档中所述的集成过程完成操作，才能接续本指南中的操作。
> 
> 本文档仅在已集成市场宣传模块并计划推送 Amazon 设备时适用。 若要在应用程序中集成市场宣传活动，请首先阅读“如何在 Android 上集成 Engagement Reach”。
> 
> 

## <a name="introduction"></a>介绍
通过集成 ADM，可以在针对 Amazon Android 设备时推送你的应用。

推送到 SDK 的 ADM 有效负载通常包含数据对象中的 `azme` 密钥。 因此，如果你在应用程序中将 ADM 用作其他目的，则可以基于该密钥筛选推送。

> [!IMPORTANT]
> Amazon Device Messaging 仅支持运行 Android 4.0.3 或更高版本的 Amazon Kindle 设备；但是，您可以在其他设备上安全地集成此代码。
> 
> 

## <a name="sign-up-to-adm"></a>注册 ADM
如果尚未注册，则必须在你的 Amazon 帐户上启用 ADM。

该过程在以下网页中有详细描述：[<https://developer.amazon.com/sdk/adm/credentials.html>]。

完成该过程之后，你将获得︰

* OAuth 凭据（一个客户端 ID 和客户端密钥），以便 Engagement 能够推送你的设备。
* API 密钥，必须将其集成到你的应用程序中。

## <a name="sdk-integration"></a>中SDK 集成
### <a name="managing-device-registrations"></a>管理设备注册
每台设备必须向 ADM 服务器发送注册命令，否则无法抵达服务器。

如果你已经使用 [ADM 客户端库]，并且已经[集成了 ADM]，则可以直接转到 android-sdk-adm-receive。

如果你尚未集成 ADM，Engagement 有一种简单方法，可以在应用程序中启用它：

编辑你的 `AndroidManifest.xml` 文件：

* 添加 Amazon 命名空间，该文件的开始部分应类似如下所示：
  
      <?xml version="1.0" encoding="utf-8"?>
      <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:amazon="http://schemas.amazon.com/apk/res/android"
* 在 `<application/>` 标记内部，添加此部分内容：
  
      <amazon:enable-feature
         android:name="com.amazon.device.messaging"
         android:required="false"/>
  
      <meta-data android:name="engagement:adm:register" android:value="true" />
* 在添加 amazon 标记后，如果“项目构建目标”低于 Android 2.1，你可能会遇到构建错误。 你必须使用 **Android 2.1+** 构建目标（不用担心，你仍可以将 `minSdkVersion` 设置为 4）。
* 按照[下面的步骤]操作，将 ADM API 密钥作为一项资产进行集成。

然后按照后续部分的说明操作。

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>将注册 ID 传递至 Engagement 推送服务，并接收通知
将以下内容添加至 `<application/>` 标记内的 `AndroidManifest.xml` 文件中（即使在没有 Engagement 的情况下使用 ADM 也是如此），以将设备的注册 ID 传递至 Engagement 推送服务并接收其通知：

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

请确保你在 `AndroidManifest.xml`（在 `</application>` 标记之前）中具有以下权限。

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

## <a name="grant-engagement-oauth-credentials"></a>授予 Engagement OAuth 凭据
在 Engagement 门户中提交你的 OAuth 凭据（客户端 ID 和客户端机密）。

[<https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[ADM 客户端库]:https://developer.amazon.com/sdk/adm/setup.html
[集成了 ADM]:https://developer.amazon.com/sdk/adm/integrating-app.html
[下面的步骤]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset

