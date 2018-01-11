---
title: "通知中心突发新闻教程 - Android"
description: "了解如何使用 Azure 服务总线通知中心向 Android 设备发送突发新闻通知。"
services: notification-hubs
documentationcenter: android
author: ysxu
manager: erikre
editor: 
ms.assetid: 3c23cb80-9d35-4dde-b26d-a7bfd4cb8f81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 3b3fc05cfec2b20501a28f3d76f474ccd49e27e8
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2017
---
# <a name="use-notification-hubs-to-send-breaking-news"></a>使用通知中心发送突发新闻
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>概述
本主题介绍如何使用 Azure 通知中心将突发新闻通知广播到 Android 应用。 完成时，可以注册感兴趣的突发新闻类别并仅接收这些类别的推送通知。 此方案对于很多应用程序来说是常见模式，在其中必须将通知发送到以前声明过对它们感兴趣的一组用户，这样的应用程序有 RSS 阅读器、针对音乐迷的应用程序等。

在通知中心创建注册时，通过加入一个或多个*标记*来启用广播方案。 将通知发送到标签时，已注册该标签的所有设备将接收通知。 因为标签是简单的字符串，它们不必提前设置。 有关标记的详细信息，请参阅[通知中心路由和标记表达式](notification-hubs-tags-segment-push-message.md)。

## <a name="prerequisites"></a>先决条件
本主题以你在[通知中心入门][get-started]中创建的应用为基础。 在开始本教程之前，必须先完成[通知中心入门][get-started]教程的学习。

## <a name="add-category-selection-to-the-app"></a>向应用程序中添加类别选择
第一步是向现有主活动添加 UI 元素，以允许用户选择要注册的类别。 用户选择的类别存储在设备上。 应用程序启动时，使用所选类别作为标签在通知中心创建设备注册。

1. 打开 res/layout/activity_main.xml 文件，并将此文件内容替换为以下内容：
   
        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingBottom="@dimen/activity_vertical_margin"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            tools:context="com.example.breakingnews.MainActivity"
            android:orientation="vertical">
   
                <CheckBox
                    android:id="@+id/worldBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_world" />
                <CheckBox
                    android:id="@+id/politicsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_politics" />
                <CheckBox
                    android:id="@+id/businessBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_business" />
                <CheckBox
                    android:id="@+id/technologyBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_technology" />
                <CheckBox
                    android:id="@+id/scienceBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_science" />
                <CheckBox
                    android:id="@+id/sportsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_sports" />
                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:onClick="subscribe"
                    android:text="@string/button_subscribe" />
        </LinearLayout>
2. 打开 res/values/strings.xml 文件并添加以下行：
   
        <string name="button_subscribe">Subscribe</string>
        <string name="label_world">World</string>
        <string name="label_politics">Politics</string>
        <string name="label_business">Business</string>
        <string name="label_technology">Technology</string>
        <string name="label_science">Science</string>
        <string name="label_sports">Sports</string>
   
    现在 main_activity.xml 的图形布局应如下所示：
   
    ![][A1]
3. 现在，在与 **MainActivity** 类相同的程序包中创建类 **Notifications**。
   
        import java.util.HashSet;
        import java.util.Set;
   
        import android.content.Context;
        import android.content.SharedPreferences;
        import android.os.AsyncTask;
        import android.util.Log;
        import android.widget.Toast;
   
        import com.google.android.gms.gcm.GoogleCloudMessaging;
        import com.microsoft.windowsazure.messaging.NotificationHub;
   
        public class Notifications {
            private static final String PREFS_NAME = "BreakingNewsCategories";
            private GoogleCloudMessaging gcm;
            private NotificationHub hub;
            private Context context;
            private String senderId;
   
            public Notifications(Context context, String senderId, String hubName, 
                                    String listenConnectionString) {
                this.context = context;
                this.senderId = senderId;
   
                gcm = GoogleCloudMessaging.getInstance(context);
                hub = new NotificationHub(hubName, listenConnectionString, context);
            }
   
            public void storeCategoriesAndSubscribe(Set<String> categories)
            {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                settings.edit().putStringSet("categories", categories).commit();
                subscribeToCategories(categories);
            }
   
            public Set<String> retrieveCategories() {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                return settings.getStringSet("categories", new HashSet<String>());
            }
   
            public void subscribeToCategories(final Set<String> categories) {
                new AsyncTask<Object, Object, Object>() {
                    @Override
                    protected Object doInBackground(Object... params) {
                        try {
                            String regid = gcm.register(senderId);
   
                            String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
   
                            hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM, 
                                categories.toArray(new String[categories.size()]));
                        } catch (Exception e) {
                            Log.e("MainActivity", "Failed to register - " + e.getMessage());
                            return e;
                        }
                        return null;
                    }
   
                    protected void onPostExecute(Object result) {
                        String message = "Subscribed for categories: "
                                + categories.toString();
                        Toast.makeText(context, message,
                                Toast.LENGTH_LONG).show();
                    }
                }.execute(null, null, null);
            }
   
        }
   
    此类使用本地存储区存储此设备必须接收的新闻类别。 它还包含注册这些类别所用的方法。
4. 在 **MainActivity** 类中删除 **NotificationHub** 和 **GoogleCloudMessaging** 的私有字段，并添加一个 **Notifications** 字段：
   
        // private GoogleCloudMessaging gcm;
        // private NotificationHub hub;
        private Notifications notifications;
5. 然后，在 **onCreate** 方法中，删除 **hub** 字段和 **registerWithNotificationHubs** 方法的初始化。 然后，添加将初始化 **Notifications** 类实例的以下行。 

        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            MyHandler.mainActivity = this;

            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);

            notifications = new Notifications(this, SENDER_ID, HubName, HubListenConnectionString);

            notifications.subscribeToCategories(notifications.retrieveCategories());
        }

    应已设置 `HubName` 和 `HubListenConnectionString`，其中的 `<hub name>` 和 `<connection string with listen access>` 占位符已替换为通知中心名称和之前获取的 *DefaultListenSharedAccessSignature* 的连接字符串。

    > [AZURE.NOTE] 由于使用客户端应用程序分发的凭据通常是不安全的，只应使用客户端应用程序分发具有侦听访问权限的密钥。 侦听访问权限允许应用程序注册通知，但是无法修改现有注册，也无法发送通知。 在受保护的后端服务中使用完全访问权限密钥，以便发送通知和更改现有注册。


1. 然后，添加以下导入项和 `subscribe` 方法以处理订阅按钮单击事件：
   
        import android.widget.CheckBox;
        import java.util.HashSet;
        import java.util.Set;
   
        public void subscribe(View sender) {
            final Set<String> categories = new HashSet<String>();
   
            CheckBox world = (CheckBox) findViewById(R.id.worldBox);
            if (world.isChecked())
                categories.add("world");
            CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
            if (politics.isChecked())
                categories.add("politics");
            CheckBox business = (CheckBox) findViewById(R.id.businessBox);
            if (business.isChecked())
                categories.add("business");
            CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
            if (technology.isChecked())
                categories.add("technology");
            CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
            if (science.isChecked())
                categories.add("science");
            CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
            if (sports.isChecked())
                categories.add("sports");
   
            notifications.storeCategoriesAndSubscribe(categories);
        }
   
    此方法创建一个类别列表并使用 **Notifications** 类将该列表存储在本地存储区中，将相应的标签注册到通知中心。 更改类别时，使用新类别重新创建注册。

应用程序现在可以将一组类别存储在设备的本地存储区中了，每当用户更改所选类别时，会将这些类别注册到通知中心。

## <a name="register-for-notifications"></a>注册通知
这些步骤用于在启动时会在本地存储区中存储的类别注册到通知中心。

> [!NOTE]
> 由于 Google Cloud Messaging (GCM) 分配的 registrationId 随时可能更改，因此，应该经常注册通知以避免通知失败。 此示例在每次应用程序启动时注册通知。 对于经常运行（一天一次以上）的应用程序，如果每次注册间隔时间不到一天，可以跳过注册来节省带宽。
> 
> 

1. 在 **MainActivity** 类的 **onCreate** 方法末尾添加以下代码：
   
        notifications.subscribeToCategories(notifications.retrieveCategories());
   
    这确保每次应用程序启动时，它从本地存储区检索类别并请求注册这些类别。 
2. 然后更新 `MainActivity` 类的 `onStart()` 方法，如下所示：
   
    @Override  protected void onStart() {
   
        super.onStart();
        isVisible = true;
   
        Set<String> categories = notifications.retrieveCategories();
   
        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }
   
    这会基于以前保存的类别状态更新主活动。

应用程序现在已完成，可以在设备的本地存储区中存储一组类别了，每当用户更改所选类别时会使用这些类别注册到通知中心。 接下来，我们将定义一个后端，它可将类别通知发送到此应用程序。

## <a name="sending-tagged-notifications"></a>发送带标记的通知
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-app-and-generate-notifications"></a>运行应用并生成通知
1. 在 Android Studio 中，生成应用并在设备或模拟器上启动它。
   
    请注意，应用程序 UI 提供了一组开关，可以使用它们选择要订阅的类别。
2. 启用一个或多个类别开关，并单击“**订阅**”。
   
    应用程序将所选类别转换为标签并针对所选标签从通知中心请求注册新设备。 返回注册的类别并显示在 toast 通知中。
3. 通过运行 .NET 控制台应用发送新通知。  或者，也可以在 [Azure 门户]中使用通知中心的调试选项卡发送带标记的模板通知。
   
    所选类别的通知作为 toast 通知显示。

## <a name="next-steps"></a>后续步骤
在本教程中，我们了解了如何按类别广播突发消息。 请考虑学习侧重说明其他高级通知中心方案的以下教程之一：

* [使用通知中心广播本地化的突发新闻]
  
    了解如何扩展突发新闻应用程序以允许发送本地化的通知。

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[使用通知中心广播本地化的突发新闻]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure 门户]: https://portal.azure.com
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
