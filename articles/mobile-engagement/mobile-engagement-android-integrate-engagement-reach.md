---
title: "Azure Mobile Engagement Android SDK 集成"
description: "Azure Mobile Engagement Android SDK 的最新更新和过程"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 9ec3fab3-35ec-458e-bf41-6cdd69e3fa44
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fd8ba95ee1fb2703926fb35cdb49e6a503637a7d


---
# <a name="how-to-integrate-engagement-reach-on-android"></a>如何在 Android 上集成 Engagement Reach
> [!IMPORTANT]
> 你必须先按照“如何在 Android 上集成 Engagement”文档中所述的集成过程完成操作，才能接续本指南中的操作。
> 
> 

## <a name="standard-integration"></a>标准的集成
Reach SDK 需要 **Android 支持库 (v4)**。

在 **Eclipse** 中将库添加到项目中的最快捷方法是 `Right click on your project -> Android Tools -> Add Support Library...`。

如果未使用 Eclipse，则可以阅读[此处]的说明。

从项目中的 SDK 复制 Reach 资源文件：

* 将使用 SDK 所附的 `res/layout` 文件夹中的文件复制到你的应用程序的 `res/layout` 文件夹中。
* 将使用 SDK 所附的 `res/drawable` 文件夹中的文件复制到你的应用程序的 `res/drawable` 文件夹中。

编辑你的 `AndroidManifest.xml` 文件：

* 添加以下部分（在 `<application>` 和 `</application>` 标记之间）：
  
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
          <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
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
* 你需要此权限来重新执行关机时未单击过的系统通知（否则，会将其保存在磁盘上，但不会再显示，你确实一定要包括此部分）。
  
          <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
* 通过复制和编辑以下部分指定用于通知（同时位于应用和系统内）的图标（在 `<application>` 和 `</application>` 标记之间）：
  
          <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [!IMPORTANT]
> 如果打算在创建市场活动时使用系统通知，则**必需**使用本部分。 Android 将阻止显示没有图标的系统通知。 因此，如果你省略此部分，则最终用户将无法收到这些通知。
> 
> 

* 在创建市场活动时，如果系统通知使用大图片，则需要根据情况添加以下权限（在 `</application>` 标记之后）：
  
          <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
          <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
  
  * 在 Android M 上，如果你的应用程序目标为 Android API 级别23 或更高级别，则 ``WRITE_EXTERNAL_STORAGE`` 权限需要用户批准。 请阅读[本部分](mobile-engagement-android-integrate-engagement.md#android-m-permissions)。
* 针对系统通知，如果设备应设置为响铃和/或振动，也可以市场宣传活动中指定。 为使其正常工作，必须确保声明了以下权限（在 `</application>` 标记之后）：
  
          <uses-permission android:name="android.permission.VIBRATE" />
  
  在没有此权限的情况下，如果你在“市场宣传活动管理员”中选中响铃或振动选项，Android 会阻止显示系统通知。
* 如果你使用 **ProGuard** 构建应用程序，并且发生了与 Android 支持库或 Engagement jar 相关的错误，请将以下行添加到你的 `proguard.cfg` 文件中：
  
          -dontwarn android.**
          -keep class android.support.v4.** { *; }

## <a name="native-push"></a>原生推送
至此，你已配置了市场宣传模块，接下来需要配置原生推送，以便能够在设备上接收活动。

我们在 Android 上支持两种服务：

* Google Play 设备：遵循[如何集成 GCM 与 Engagement 指南](mobile-engagement-android-gcm-integrate.md)所述，使用 [Google Cloud Messaging]。
* Amazon 设备：遵循[如何集成 ADM 与 Engagement 指南](mobile-engagement-android-adm-integrate.md)所述，使用 [Amazon Device Messaging]。

如果你想同时定位到 Amazon 和 Google Play 设备，则可以将所有内容放在一个 AndroidManifest.xml/APK 进行开发。 但提交到 Amazon 时，如果它们发现 GCM 代码，则可能会拒绝你的应用程序。

在这种情况下应使用多个 APK。

**现在你的应用程序已准备好接收和显示市场宣传活动！**

## <a name="how-to-handle-data-push"></a>如何处理数据推送
### <a name="integration"></a>集成
如果你希望应用程序能够接收市场宣传数据推送，则必须创建 `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` 的子类，并在 `AndroidManifest.xml` 文件中进行引用（在 `<application>` 和/或 `</application>` 标记之间）：

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

然后，你可以重写 `onDataPushStringReceived` 和 `onDataPushBase64Received` 回调。 下面是一个示例：

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>类别
当你创建数据推送活动时，类别参数是可选的，并允许你筛选数据推送。 如果你有多个广播接收器处理不同类型的数据推送，或如果你想要推送不同种类的 `Base64` 数据，并且希望在解析它们之前确定其类型，此参数会非常有用。

### <a name="callbacks-return-parameter"></a>回调的返回参数
以下是正确处理 `onDataPushStringReceived` 和 `onDataPushBase64Received` 的返回参数部分准则：

* 如果接收器不知道如何处理数据推送，则应在回调中返回 `null`。 你应使用类别来确定广播接收器是否应处理数据推送。
* 如果接受数据推送，则其中一个广播接收器应在回调中返回 `true`。
* 如果可识别数据推送，但因各种原因而放弃，则其中一个广播接收器应在回调中返回 `false`。 例如，当收到的数据无效时，返回 `false`。
* 如果对于相同的数据推送，一个广播接收器返回 `true`，而另一个返回 `false`，则属于未定义行为，你永远都不应这样做。

返回类型仅针对市场宣传统计信息：

* 如果其中一个广播接收器返回 `true` 或 `false`，则 `Replied` 会递增。
* 仅当其中一个广播接收器返回 `true` 时，`Actioned` 才会递增。

## <a name="how-to-customize-campaigns"></a>如何自定义市场活动
若要自定义市场活动，可以修改 Reach SDK 中提供的布局。

你应保留布局中使用的所有标识符，并保留使用标识符的视图类型，尤其是文本视图和图像视图。 某些视图只用于隐藏或显示区域，因此可能会更改其类型。 如果你想要更改提供布局中的视图类型，请检查源代码。

### <a name="notifications"></a>通知
有两种类型的通知：系统通知和应用内通知，它们使用不同的布局文件。

#### <a name="system-notifications"></a>系统通知
若要自定义系统通知，需要使用**类别**。 你可以跳到[类别](#categories)。

#### <a name="in-app-notifications"></a>应用内通知
默认情况下，应用内通知是使用 Android 方法 `addContentView()` 动态添加到当前活动用户界面的视图。 这称为通知覆盖。 通知覆盖非常适合用于快速集成，因为它们不需要你修改应用程序中的任何布局。

若要修改通知覆盖的外观，可以根据需要仅修改文件 `engagement_notification_area.xml` 即可。

> [!NOTE]
> 文件 `engagement_notification_overlay.xml` 用于创建通知覆盖，其中也包含文件 `engagement_notification_area.xml`。 你也可以对其进行自定义以满足自己的需求（例如，定位覆盖内的通知区域）。
> 
> 

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>包括通知布局作为活动布局的一部分
覆盖非常适合用于快速集成，但在特殊情况下可能造成不便或负面影响。 可以在活动级别自定义覆盖系统，以便轻松避免特殊活动的负面影响。

你可以决定使用 Android **include** 语句，将我们的通知布局纳入现有布局。 下面是修改过的 `ListActivity` 布局的示例，其中仅包含一个 `ListView`。

**在 Engagement 集成之前：**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**在 Engagement 集成之后：**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

在此示例中，我们添加了父容器，因为原始布局使用列表视图作为顶级元素。 我们还添加了 `android:layout_weight="1"`，以便能够在使用 `android:layout_height="fill_parent"` 配置的列表视图下添加视图。

Engagement Reach SDK 会自动检测通知布局是否包含在此活动中，并且不会添加此活动的覆盖。

> [!TIP]
> 如果你在应用程序中使用 ListActivity，则可见的市场宣传覆盖会使你无法再对列表视图中的点击项作出反应。 这是已知问题。 若要暂时解决此问题，我们建议你在自己的列表活动布局中嵌入通知布局，如前面的示例所示。
> 
> 

##### <a name="disabling-application-notification-per-activity"></a>禁用每个活动的应用程序通知
如果你不想将覆盖添加到活动中，并且如果在你自己的布局中也未包括通知布局，则可以通过添加 `meta-data` 部分在 `AndroidManifest.xml` 中禁用此活动的覆盖，如以下示例所示：

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="a-namecategoriesa-categories"></a><a name="categories"></a> 类别
当修改提供的布局时，你会修改所有通知的外观。 类别允许你定义通知的各种目标外观（可能是行为）。 创建市场宣传活动时可以指定类别。 请记住，使用类别还可以自定义公告和投票，本文档稍后会介绍这部分内容。

若要注册通知的类别处理程序，需要在初始化应用程序时添加调用。

> [!IMPORTANT]
> 请先阅读“如何在 Android 上集成 Engagement”主题中有关 android:process 属性 \<android-sdk-engagement-process\> 的警告，然后再继续操作。
> 
> 

下面的示例假定你已知道上述警告，并使用 `EngagementApplication` 的子类：

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

`MyNotifier` 对象是通知类别处理程序的实现。 这是 `EngagementNotifier` 接口的实现或默认实现的子类：`EngagementDefaultNotifier`。

请注意，同一通知程序可以处理多种类别，注册方式如下所示：

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

若要替换默认类别实现，你可以按如下示例所示注册实现：

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

处理程序中所用的当前类别，在你可于 `EngagementDefaultNotifier` 重写的大多数方法中作为参数进行传递。

它可以作为 `String` 参数传递，也可以使用 `getCategory()` 方法间接在 `EngagementReachContent` 对象中间接。

你可以通过在 `EngagementDefaultNotifier` 上重新定义方法，更改通知创建过程中的大部分内容。若需要更高级的自定义，可以随时查看技术文档和源代码。

##### <a name="in-app-notifications"></a>应用内通知
如果只想针对特定类别使用备用布局，可以按如以下示例进行实现：

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**`my_notification_overlay.xml` 的示例：**

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

如你所看到的，覆盖视图标识符不同于标准的标识符。 重要的是，每个布局将对各个覆盖层使用唯一的标识符。

**`my_notification_area.xml` 的示例：**

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

如你所看到的，通知区域视图标识符不同于标准的标识符。 重要的是，每个布局将对各个通知区域使用唯一的标识符。

这个简单的类别示例使应用程序（或应用内）通知显示在屏幕的顶部。 我们并未更改通知区域自身所使用的标准标识符。

如果你想要进行更改，则必须重新定义 `EngagementDefaultNotifier.prepareInAppArea` 方法。 如果你需要此级别的高级自定义，建议查看 `EngagementNotifier` 和 `EngagementDefaultNotifier` 的技术文档和源代码。

##### <a name="system-notifications"></a>系统通知
通过扩展 `EngagementDefaultNotifier`，你可以重写 `onNotificationPrepared` 以更改默认实现所准备的通知。

例如：

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

本示例在使用“正在进行”类别时，针对显示为正在进行事件的内容创建系统通知。

如果你想要从头开始生成 `Notification` 对象，则可以将 `false` 返回到方法，并在 `NotificationManager` 自行调用 `notify`。 在这种情况下，务必保留 `contentIntent`、`deleteIntent` 和 `EngagementReachReceiver` 所使用的通知标识符。

下面是此类实现一个正确示例：

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>仅通知公告
对于仅限通知公告的点击管理，通过重写 `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` 来修改已准备好的 `Intent` 进行自定义。 使用此方法可使你轻松调整标志。

例如，若要添加 `SINGLE_TOP` 标志：

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

对于旧版本的 Engagement 用户，请注意，没有操作 URL 的系统通知会启动后台中的应用程序，因此，可以使用没有操作 URL 的公告调用此方法。 自定义意向时，应考虑这一点。

你也可以从头开始实现 `EngagementNotifier.executeNotifAnnouncementAction`。

##### <a name="notification-life-cycle"></a>通知生命周期
使用默认类别时，会在 `EngagementReachInteractiveContent` 对象上调用某些生命周期方法，以报告统计信息和更新市场活动状态：

* 当通知显示在应用程序中或放入状态栏中时，如果 `handleNotification` 返回 `true`，则 `EngagementReachAgent` 会调用 `displayNotification` 方法（报告统计信息）。
* 如果关闭通知，系统将调用 `exitNotification` 方法、报告统计信息，并且立即处理后续市场活动。
* 如果单击该通知，则系统将调用 `actionNotification`、报告统计信息并启动关联的意向。

如果你的 `EngagementNotifier` 实现绕过默认行为，则必须自行调用这些生命周期方法。 下面的示例阐释了绕过默认行为的一些情况：

* 你不扩展 `EngagementDefaultNotifier`，例如，从头开始实现类别处理。
* 对于系统通知，你重写了 `onNotificationPrepared`，并修改了 `Notification` 对象中的 `contentIntent` 或 `deleteIntent`。
* 对于应用内通知，你重写了 `prepareInAppArea`，请务必至少将 `actionNotification` 映射到其中一个 U.I 控件。

> [!NOTE]
> 如果 `handleNotification` 引发异常，则会删除内容并调用 `dropContent`。 这会在统计信息中报告，并且立即处理后续活动。
> 
> 

### <a name="announcements-and-polls"></a>公告和投票
#### <a name="layouts"></a>布局
可以修改 `engagement_text_announcement.xml`、`engagement_web_announcement.xml` 和 `engagement_poll.xml` 文件，以自定义文本公告、Web 公告和投票。

这些文件针对标题区域和按钮区域共享两个通用布局。 标题的布局是 `engagement_content_title.xml`，并针对背景使用同名可绘制文件。 操作和退出按钮的布局是 `engagement_button_bar.xml`，并针对背景使用同名可绘制文件。

在投票中，问题布局和选项会多次针对问题使用 `engagement_question.xml` 布局文件，并针对选项使用 `engagement_choice.xml` 文件，动态地进行扩充。

#### <a name="categories"></a>Categories
##### <a name="alternate-layouts"></a>备用布局
与通知相似，活动类别可用于公告和投票的备用布局。

例如，若要创建文本公告的类别，可以扩展 `EngagementTextAnnouncementActivity` 并在 `AndroidManifest.xml` 文件中进行引用：

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

请注意，意向筛选器中的类别用于产生与默认公告活动的差异。

Reach SDK 使用意向系统解决特定类别的正确活动，如果解决方案失败，则返回到默认类别。

然后，你必须实现 `MyCustomTextAnnouncementActivity`，如果只想更改布局（但保留相同的视图标识符），则只需定义类，如以下示例所示：

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

若要替换文本公告的默认类别，只需在你的实现中替换 `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"`。

可采用类似的方式自定义Web 公告和投票。

对于 Web 公告，你可以扩展 `EngagementWebAnnouncementActivity`，并在 `AndroidManifest.xml` 中声明活动，如以下示例所示：

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

对于投票，你可以扩展 `EngagementPollActivity`，并在 `AndroidManifest.xml` 中声明你的活动，如以下示例所示：

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>从头实现
可以实现公告（和投票）活动的类别，无需扩展由 Reach SDK 提供的任何 `Engagement*Activity` 类。 例如，如果你想要定义不使用与标准布局相同的视图，这将非常有用。

而对于高级通知自定义，建议查看标准实现的源代码。

下面是一些注意事项：市场宣传会以特定的意向（对应于意向筛选器），以及一个额外参数（也就是内容标识符）来启动活动。

若要检索包含你在网站上创建活动时所指定字段的内容对象，可以执行此操作：

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

对于统计信息，则应报告内容已显示在 `onResume` 事件中：

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

然后，不要忘记在活动进入后台之前在内容对象上调用 `actionContent(this)` 或 `exitContent(this)`。

如果你不调用 `actionContent` 或 `exitContent`，则系统将不会发送统计信息（即没有针对活动的分析），更为重要的是，在重新启动应用程序进程之前，系统不会通知后续活动。

方向或其他配置更改可能会使代码更难以确定活动是否进入后台，而标准实现则可确保在用户离开活动时（通过按 `HOME` 或 `BACK`），在退出时报告内容但在方向更改时不会。

下面是实现的有趣部分：

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

如你所看到的，如果你调用了 `actionContent(this)`，然后完成了活动，则可以安全地调用 `exitContent(this)`，而不会产生任何影响。

[此处]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Google Cloud Messaging]:http://developer.android.com/guide/google/gcm/index.html
[Amazon Device Messaging]:https://developer.amazon.com/sdk/adm.html



<!--HONumber=Nov16_HO3-->


