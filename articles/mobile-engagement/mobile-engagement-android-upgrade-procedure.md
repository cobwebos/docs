---
title: "Azure Mobile Engagement Android SDK 集成"
description: "Azure Mobile Engagement Android SDK 的最新更新和过程"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 11618586-c709-49ca-bcd8-745323ff1af6
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 97ce7f4d682ec12470db4248d046a8367840f0bd
ms.lasthandoff: 11/17/2016


---
# <a name="upgrade-procedures"></a>升级过程
如果已将我们较旧版本的 SDK 集成到应用程序中，则在升级 SDK 时必须考虑以下几点。

如果错过了几个版本的 SDK，则可能需要执行多个过程。 例如，如果从 1.4.0 迁移至 1.6.0，则必须首先按照“从 1.4.0 至 1.5.0”的过程操作，然后按照“从 1.5.0 至 1.6.0”的过程操作。

无论从哪个版本升级，都必须使用新版本替换 `mobile-engagement-VERSION.jar`。

## <a name="from-420-to-421"></a>从 4.2.0 至 4.2.1
实际上可以在任何版本的 SDK 上完成此步骤，这是一项集成市场宣传活动时的安全改进。

现在，应该将 `exported="false"` 添加到所有市场宣传活动。

现在，市场宣传活动应在 `AndroidManifest.xml` 上看起来如下所示：

            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

## <a name="from-400-to-410"></a>从 4.0.0 至 4.1.0
SDK 现在处理来自 Android M 的新权限模型。

如果使用位置功能或大图通知，请阅读[本部分](mobile-engagement-android-integrate-engagement.md#android-m-permissions)。

除了新的权限模型，我们现在还支持在运行时配置位置功能。
我们仍然兼容位置的清单参数，但现在已弃用。 要使用运行时配置，请从 ``AndroidManifest.xml`` 中删除以下部分：

    <meta-data
      android:name="engagement:locationReport:lazyArea"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:background"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:fine"
      android:value="true"/>

并阅读[此更新过程](mobile-engagement-android-integrate-engagement.md#location-reporting)以转而使用运行时配置。

## <a name="from-300-to-400"></a>从 3.0.0 至 4.0.0
### <a name="native-push"></a>原生推送
原生推送 (GCM/ADM) 现在也用于应用内通知，因此，必须为任何类型的推送市场活动配置原生推送凭据。

如果尚未完成配置，请按照[此过程](mobile-engagement-android-integrate-engagement-reach.md#native-push)操作。

### <a name="androidmanifestxml"></a>AndroidManifest.xml
已在 ``AndroidManifest.xml`` 中修改市场宣传集成。

替换此内容：

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

通过

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>
    <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
      <intent-filter>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
      </intent-filter>
    </receiver>

在单击公告（包好文本/Web 内容）或轮询时，现在可能会出现加载屏幕。
必须添加此内容，才能让这些市场活动在 4.0.0 中生效：

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

### <a name="resources"></a>资源
将新的 `res/layout/engagement_loading.xml` 文件嵌入到项目中。

## <a name="from-240-to-300"></a>从 2.4.0 至 3.0.0
以下部分介绍如何将 SDK 集成从由 Capptain SAS 提供的 Capptain 服务迁移到 Azure Mobile Engagement 支持的应用。 如果从较早版本进行迁移，请参阅 Capptain 网站先迁移到 2.4.0，然后再应用以下过程。

> [!IMPORTANT]
> Capptain 和 Mobile Engagement 不是相同的服务，以下提供的过程仅重点描述如何迁移客户端应用。 迁移应用中的 SDK 不会将数据从 Capptain 服务器迁移到 Mobile Engagement 服务器。
> 
> 

### <a name="jar-file"></a>JAR 文件
使用 `libs` 文件夹中的 `mobile-engagement-VERSION.jar` 替换 `capptain.jar`。

### <a name="resource-files"></a>资源文件
必须用新的资源文件（前缀为 `engagement_`）替换我们提供的每个资源文件（前缀为 `capptain_`）。

如果自定义了这些文件，则必须对新文件重新应用自定义，**资源文件中的所有标识符也已重命名**。

### <a name="application-id"></a>应用程序 ID
Engagement 现在使用连接字符串来配置 SDK 标识符，例如应用程序标识符。

必须在启动器活动中使用 `EngagementAgent.init` 方法，如下所示：

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

应用程序的连接字符串会显示在 Azure 门户上。

请删除对 `CapptainAgent.configure` 的任何调用，因为 `EngagementAgent.init` 会替换该方法。

无法再使用 `AndroidManifest.xml` 配置 `appId`。

如果已有本部分，请从 `AndroidManifest.xml` 中将其删除：

            <meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

### <a name="java-api"></a>Java API
必须重命名每个对 SDK 的任何 Java 类的调用；例如，`CapptainAgent.getInstance(this)` 必须重命名为 `EngagementAgent.getInstance(this)`，`extends CapptainActivity` 必须重命名为 `extends EngagementActivity` 等。

如果使用默认的代理首选项文件进行集成，则默认文件名称现在是 `engagement.agent` 并且密钥是 `engagement:agent`。

当创建 Web 公告时，Javascript 绑定器现在为 `engagementReachContent`。

### <a name="androidmanifestxml"></a>AndroidManifest.xml
发生了大量更改，服务不再共享，并且很多接收器不再可导出。

服务声明现在变得更简单；删除意向筛选器和其中的所有元数据，并添加 `exportable=false`。

此外，为使用 Engagement，所有内容都已进行重命名。

现在看起来如下所示：

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

在想要启用测试日志时，元数据现已移至应用程序标记，并已重命名：

            <application>

              <meta-data android:name="engagement:log:test" android:value="true" />

              <service/>

            </application>

刚刚重命名了所有其他元数据，这里是完整的列表（当然只需重命名所使用的元数据）：

            <meta-data
              android:name="engagement:reportCrash"
              android:value="true"/>
            <meta-data
              android:name="engagement:sessionTimeout"
              android:value="10000"/>
            <meta-data
              android:name="engagement:burstThreshold"
              android:value="0"/>
            <meta-data
              android:name="engagement:connection:delay"
              android:value="0"/>
            <meta-data
              android:name="engagement:locationReport:lazyArea"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:background"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:fine"
              android:value="false"/>
            <meta-data
              android:name="engagement:agent:settings:name"
              android:value="engagement.agent"/>
            <meta-data
              android:name="engagement:agent:settings:mode"
              android:value="0"/>
            <meta-data
              android:name="engagement:gcm:sender"
              android:value="<YOUR_PROJECT_NUMBER>\n"/>
            <meta-data
              android:name="engagement:adm:register"
              android:value="true"/>
            <meta-data
              android:name="engagement:reach:notification:icon"
              android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>

            <activity android:name="SomeActivityWithoutReachOverlay">
              <meta-data
                android:name="engagement:notification:overlay"
                android:value="false"/>
            </activity>

Google Play 和 SmartAd 跟踪已从 SDK 中删除，只需将其删除即可，无需替换：

            <meta-data 
                android:name="capptain:track:installReferrerForwardList"
                android:value="com.class1,com.class2"/>
            <meta-data
                android:name="capptain:track:adservers"
                android:value="smartad" />

市场宣传活动现在声明如下：

            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/plain"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/html"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

如果有自定义市场宣传活动，则只需要更改意图操作以匹配 `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` 或 `com.microsoft.azure.engagement.reach.intent.action.POLL`。

广播接收器已重命名，此外，我们现在添加 `exported=false`。 以下是具有新规范的接收器的完整列表（当然只需重命名所使用的那些接收器）：

            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
              android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver
              android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
              android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
                <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
              </intent-filter>
            </receiver>

            <receiver
              android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
              android:permission="com.amazon.device.messaging.permission.SEND">
              <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
                <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
              </intent-filter>
            </receiver>

            <receiver
              android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
              </intent-filter>
            </receiver>

跟踪接收器已删除，因此必须删除此部分：

          <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
            <intent-filter>
              <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
              <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
            </intent-filter>
          </receiver>

请注意，在 `AndroidManifest.xml` 中更改了广播接收器 **EngagementMessageReceiver** 的实现声明。 这是因为从任意 XMPP 实体发送和删除任意 XMPP 消息的 API 以及在设备之间发送和接收消息的 API 都已删除。 因此，必须从 **EngagementMessageReceiver** 实现中删除以下回调：

            protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

和

            protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

然后为以下内容删除对 **EngagementAgent** 的任何调用：

            sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

和

            sendXMPPMessage(android.os.Bundle msg)

### <a name="proguard"></a>ProGuard
ProGuard 配置可能会受到品牌重塑的影响，规则目前看起来如下所示：

            -dontwarn android.**
            -keep class android.support.v4.** { *; }

            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
              <methods>;
            }


