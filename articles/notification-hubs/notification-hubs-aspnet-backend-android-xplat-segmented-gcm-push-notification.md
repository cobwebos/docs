---
title: 使用 Azure 通知中心和 Google Cloud Messaging 将通知推送到特定的 Android 设备 | Microsoft Docs
description: 了解如何使用通知中心通过 Azure 通知中心和 Google Cloud Messaging 将通知推送到特定的 Android 设备。
services: notification-hubs
documentationcenter: android
author: dimazaid
manager: kpiteira
editor: spelluru'
ms.assetid: 3c23cb80-9d35-4dde-b26d-a7bfd4cb8f81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/06/2018
ms.author: dimazaid
ms.openlocfilehash: 00d6a2de3f51ef8ade50f29b14a4bc944ae5fee6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778403"
---
# <a name="tutorial-push-notifications-to-specific-android-devices-using-azure-notification-hubs-and-google-cloud-messaging"></a>教程：使用 Azure 通知中心和 Google Cloud Messaging 将通知推送到特定的 Android 设备
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>概述
本教程介绍如何使用 Azure 通知中心将突发新闻通知广播到 Android 应用。 完成时，可以注册感兴趣的突发新闻类别并仅接收这些类别的推送通知。 此方案对于很多应用来说是常见模式，在其中必须将通知发送到以前声明过对它们感兴趣的一组用户，这样的应用有 RSS 阅读器、针对音乐迷的应用等。

在通知中心创建注册时，通过加入一个或多个*标记*来启用广播方案。 将通知发送到标签时，已注册该标签的所有设备将接收通知。 因为标签是简单的字符串，它们不必提前设置。 有关标记的详细信息，请参阅[通知中心路由和标记表达式](notification-hubs-tags-segment-push-message.md)。

在本教程中，请执行以下操作： 

> [!div class="checklist"]
> * 向移动应用添加类别选择。
> * 已注册带标记的通知。 
> * 发送带标记的通知。 
> * 测试应用程序

## <a name="prerequisites"></a>先决条件
本教程基于在[教程：使用 Azure 通知中心和 Google Cloud Messaging 将通知推送到 Android 设备][get-started]中创建的应用。 在开始本教程之前，请完成[教程：使用 Azure 通知中心和 Google Cloud Messaging 将通知推送到 Android 设备][get-started]。

## <a name="add-category-selection-to-the-app"></a>向应用程序中添加类别选择
第一步是向现有主活动添加 UI 元素，以允许用户选择要注册的类别。 用户选择的类别存储在设备上。 应用程序启动时，使用所选类别作为标签在通知中心创建设备注册。

1. 打开 res/layout/activity_main.xml 文件，并将此文件内容替换为以下内容：
   
    ```xml
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
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
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Hello World!"
                android:id="@+id/text_hello"
            />
    </LinearLayout>
    ```
2. 打开 res/values/strings.xml 文件并添加以下行：

    ```xml
    <string name="button_subscribe">Subscribe</string>
    <string name="label_world">World</string>
    <string name="label_politics">Politics</string>
    <string name="label_business">Business</string>
    <string name="label_technology">Technology</string>
    <string name="label_science">Science</string>
    <string name="label_sports">Sports</string>
    ```

    main_activity.xml 的图形布局应如下图所示：
   
    ![][A1]
3. 在与 **MainActivity** 类相同的程序包中创建 `Notifications` 类。

    ```java   
    import java.util.HashSet;
    import java.util.Set;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.os.AsyncTask;
    import android.util.Log;
    import android.widget.Toast;
    import android.view.View;

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
    ```
       
    此类使用本地存储区存储此设备必须接收的新闻类别。 它还包含注册这些类别所用的方法。
4. 在 **MainActivity** 类中删除 **NotificationHub** 和 **GoogleCloudMessaging** 的私有字段，并添加一个 **Notifications** 字段：

    ```java   
    // private GoogleCloudMessaging gcm;
    // private NotificationHub hub;
    private Notifications notifications;
    ```
5. 然后，在 **onCreate** 方法中，删除 **hub** 字段和 **registerWithNotificationHubs** 方法的初始化。 然后，添加将初始化 **Notifications** 类实例的以下行。 

    ```java
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mainActivity = this;

        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId,
                MyHandler.class);

        notifications = new Notifications(this, NotificationSettings.SenderId, NotificationSettings.HubName, NotificationSettings.HubListenConnectionString);

        notifications.subscribeToCategories(notifications.retrieveCategories());
    }
    ```

    确认已在 NotificationSettings 类中正确设置中心名称和连接字符串。

    > [AZURE.NOTE] 由于使用客户端应用程序分发的凭据通常是不安全的，只应使用客户端应用程序分发具有侦听访问权限的密钥。 侦听访问权限允许应用程序注册通知，但是无法修改现有注册，也无法发送通知。 在受保护的后端服务中使用完全访问权限密钥，以便发送通知和更改现有注册。
1. 然后，添加以下导入内容：

    ```java   
    import android.widget.CheckBox;
    import java.util.HashSet;
    import java.util.Set;
    ```
1. 添加以下 `subscribe` 方法以处理订阅按钮单击事件：        
   
    ```java
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
    ```
       
    此方法创建一个类别列表并使用 **Notifications** 类将该列表存储在本地存储区中，将相应的标签注册到通知中心。 更改类别时，使用新类别重新创建注册。

应用程序现在可以将一组类别存储在设备的本地存储区中了，每当用户更改所选类别时，会将这些类别注册到通知中心。

## <a name="register-for-notifications"></a>注册通知
这些步骤用于在启动时会在本地存储区中存储的类别注册到通知中心。

> [!NOTE]
> 由于 Google Cloud Messaging (GCM) 分配的 registrationId 随时可能更改，因此，应该经常注册通知以避免通知失败。 此示例在每次应用程序启动时注册通知。 对于经常运行（一天一次以上）的应用程序，如果每次注册间隔时间不到一天，可以跳过注册来节省带宽。
> 
> 

1. 在 **MainActivity** 类的 **onCreate** 方法末尾添加以下代码：
   
    ```java
    notifications.subscribeToCategories(notifications.retrieveCategories());
    ```
   
    此代码确保每次应用程序启动时，它从本地存储检索类别并请求注册这些类别。 
2. 然后更新 `MainActivity` 类的 `onStart()` 方法，如下所示：
   
    ```java
    @Override
    protected void onStart() {
   
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
    ```
   
    此代码会基于以前保存的类别状态更新主活动。

应用程序现在已完成，可以在设备的本地存储区中存储一组类别了，每当用户更改所选类别时会使用这些类别注册到通知中心。 接下来，请定义一个后端，以便将类别通知发送到此应用。

## <a name="send-tagged-notifications"></a>发送带标记的通知
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>测试应用程序
1. 在 Android Studio 中的 Android 设备或模拟器上运行应用。 应用 UI 提供了一组开关，可以使用它们选择要订阅的类别。
2. 启用一个或多个类别开关，并单击“**订阅**”。 应用程序将所选类别转换为标签并针对所选标签从通知中心请求注册新设备。 返回注册的类别并显示在 toast 通知中。

    ![订阅类别](./media/notification-hubs-aspnet-backend-android-breaking-news/subscribe-for-categories.png)
1. 运行 .NET 控制台应用，以便针对每个类别发送通知。 所选类别的通知作为 toast 通知显示。

    ![技术新闻通知](./media/notification-hubs-aspnet-backend-android-breaking-news/technolgy-news-notification.png)

## <a name="next-steps"></a>后续步骤
在本教程中，已向注册类别的特定 Android 设备发送了广播通知。 若要了解如何向特定的用户推送通知，请转到以下教程： 

> [!div class="nextstepaction"]
>[向特定用户推送通知](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md)

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Use Notification Hubs to broadcast localized breaking news]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure portal]: https://portal.azure.com
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
