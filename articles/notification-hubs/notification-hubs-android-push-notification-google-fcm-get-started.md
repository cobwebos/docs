---
title: 使用 Azure 通知中心和 Firebase Cloud Messaging 将通知推送到 Android 应用 | Microsoft Docs
description: 本教程介绍如何使用 Azure 通知中心和 Google Firebase Cloud Messaging 将通知推送到 Android 设备。
services: notification-hubs
documentationcenter: android
keywords: 推送通知, 推送通知, android 推送通知, fcm, firebase cloud messaging
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 2bc085989ff3bbbc50042c46b338f748a10aa87e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777767"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-firebase-cloud-messaging"></a>教程：使用 Azure 通知中心和 Google Firebase Cloud Messaging 将通知推送到 Android 设备
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

> [!IMPORTANT]
> 本文演示了使用 Google Firebase Cloud Messaging (FCM) 的推送通知。 如果仍在使用 Google Cloud Messaging (GCM)，请参阅[使用 Azure 通知中心和 GCM 将通知推送到 Android 设备](notification-hubs-android-push-notification-google-gcm-get-started.md)。

本教程介绍了如何使用 Azure 通知中心和 Firebase Cloud Messaging 将通知推送到 Android 应用程序。 在本教程中，请创建一个空白 Android 应用，以便使用 Firebase Cloud Messaging (FCM) 接收推送通知。

可以从 [此处](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase)的 GitHub 中下载本教程的已完成代码。

在本教程中，请执行以下步骤：

> [!div class="checklist"]
> * 创建 Android Studio 项目。
> * 创建支持 Firebase Cloud Messaging 的 Firebase 项目。
> * 创建通知中心。
> * 将应用连接到通知中心。
> * 测试应用。 

## <a name="prerequisites"></a>先决条件
要完成本教程，必须有一个有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/free/)。

* 除了需要上面提到的可用 Azure 帐户外，本教程还要求具有最新版本的 [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797)。
* 适用于 Firebase Cloud Messaging 的 Android 2.3 或更高版本。
* Firebase Cloud Messaging 要求安装 Google Repository 修订版 27 或更高版本。
* 适用于 Firebase Cloud Messaging 的 Google Play Services 9.0.2 或更高版本。
* 完成本教程是学习有关 Android 应用的所有其他通知中心教程的先决条件。

## <a name="create-an-android-studio-project"></a>创建 Android Studio 项目
1. 在 Android Studio 中，启动新的 Android Studio 项目。
   
    ![Android Studio - 新项目](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-new-project.png)
2. 选择“手机和平板电脑”外形规格和要支持的“最低 SDK 版本”。 然后单击“下一步”。
   
    ![Android Studio - 项目创建工作流](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-choose-form-factor.png)
3. 选择“空活动”作为主活动，单击“下一步”，并单击“完成”。

## <a name="create-a-firebase-project-that-supports-fcm"></a>创建支持 FCM 的 Firebase 项目
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>配置通知中心
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>为中心配置 Firebase Cloud Messaging 设置
1. 在“通知设置”类别中选择“Google (GCM)”。 
2. 输入先前从 [Firebase 控制台](https://firebase.google.com/console/)复制的 API 密钥（FCM 服务器密钥）。
3. 在工具栏上选择“保存”。

    ![Azure 通知中心 - Google (GCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-gcm-api.png)

现在通知中心已配置为使用 Firebase Cloud Messaging，并且具有用于注册应用以收发推送通知的连接字符串。

## <a id="connecting-app"></a>将你的应用连接到通知中心
### <a name="add-google-play-services-to-the-project"></a>将 Google Play 服务添加到项目
[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>添加 Azure 通知中心库
1. 在**应用**的 `Build.Gradle` 文件中，在 **dependencies** 部分添加以下行。
   
    ```java
        compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. 在 **dependencies** 节的后面添加以下存储库。
   
    ```java
        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }
    ```

### <a name="add-google-firebase-support"></a>添加 Google Firebase 支持

1. 在**应用**的 `Build.Gradle` 文件中，在 **dependencies** 部分添加以下行。
   
    ```java
        compile 'com.google.firebase:firebase-core:12.0.0'
    ```

2. 在文件的末尾添加以下插件。 
   
    ```java
    apply plugin: 'com.google.gms.google-services'
    ```

### <a name="updating-the-androidmanifestxml"></a>更新 AndroidManifest.xml。
1. 为支持 FCM，必须在代码中实现实例 ID 侦听器服务，以便使用 [Google 的 FirebaseInstanceId API](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId) 来[获取注册令牌](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register)。 在本教程中，类的名称为 `MyInstanceIDService`。 
   
    将以下服务定义添加到 AndroidManifest.xml 文件的 `<application>` 标记内。 
   
    ```xml
        <service android:name=".MyInstanceIDService">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
            </intent-filter>
        </service>
    ```

2. 从 FirebaseInstanceId API 收到 FCM 注册令牌后，需使用它[在 Azure 通知中心注册](notification-hubs-push-notification-registration-management.md)。 请使用名为 `RegistrationIntentService` 的 `IntentService` 在后台支持此注册。 此服务还负责刷新 FCM 注册令牌。
   
    将以下服务定义添加到 AndroidManifest.xml 文件的 `<application>` 标记内。 
   
    ```xml
        <service
            android:name=".RegistrationIntentService"
            android:exported="false">
        </service>
    ```

3. 还需定义通知接收者。 将以下接收者定义添加到 AndroidManifest.xml 文件的 `<application>` 标记内。 将 `<your package>` 占位符替换为 `AndroidManifest.xml` 文件顶部显示的实际包名称。

    ```xml
        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>
    ```

4. 在 `</application>` 标记下面添加以下必要的 FCM 相关权限。 
   
    有关这些权限的详细信息，请参阅 [Setup a GCM Client app for Android](https://developers.google.com/cloud-messaging/android/client#manifest)（安装适用于 Android 的 GCM 客户端应用）和 [Migrate a GCM Client App for Android to Firebase Cloud Messaging](https://developers.google.com/cloud-messaging/android/android-migrate-fcm#remove_the_permissions_required_by_gcm)（将适用于 Android 的 GCM 客户端应用迁移到 Firebase Cloud Messaging）。
   
    ```xml
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="adding-code"></a>添加代码
1. 在项目视图中，展开 **app** > **src** > **main** > **java** 右键单击 **java** 下的包文件夹，单击“新建”，并单击“Java 类”。 `NotificationSettings`的新类。 
   
    ![Android Studio - 新 Java 类](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hub-android-new-class.png)
   
    确保在 `NotificationSettings` 类的以下代码中更新这三个占位符：
   
   * **SenderId**：之前在 [Firebase 控制台](https://firebase.google.com/console/)中在项目设置的 **Cloud Messaging** 选项卡中获取的发件人 ID。
   * **HubListenConnectionString**：中心的 **DefaultListenAccessSignature** 连接字符串。 可以复制此连接字符串，方法是在 [Azure 门户]的中心单击“访问策略”。
   * **HubName**：使用 [Azure 门户]的中心页中显示的通知中心的名称。
     
     `NotificationSettings` 代码：
     
    ```java
       public class NotificationSettings {
     
           public static String SenderId = "<Your project number>";
           public static String HubName = "<Your HubName>";
           public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
       }
    ```

2. 使用上述步骤，添加另一个名为 `MyInstanceIDService` 的新类。 此类是实例 ID 侦听器服务实现。
   
    此类的代码调用 `IntentService` 以在后台 [刷新 FCM 令牌](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens)。
   
    ```java
        import android.content.Intent;
        import android.util.Log;
        import com.google.firebase.iid.FirebaseInstanceIdService;

        public class MyInstanceIDService extends FirebaseInstanceIdService {

            private static final String TAG = "MyInstanceIDService";

            @Override
            public void onTokenRefresh() {

                Log.d(TAG, "Refreshing GCM Registration Token");

                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        };
    ```

1. 将另一个名为 `RegistrationIntentService`的新类添加到项目。 此类实现 `IntentService` 接口，并负责[刷新 FCM 令牌](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens)以及[在通知中心注册](notification-hubs-push-notification-registration-management.md)。
   
    针对此类使用以下代码。
   
    ```java
        import android.app.IntentService;
        import android.content.Intent;
        import android.content.SharedPreferences;
        import android.preference.PreferenceManager;
        import android.util.Log;        
        import com.google.firebase.iid.FirebaseInstanceId;
        import com.microsoft.windowsazure.messaging.NotificationHub;
   
        public class RegistrationIntentService extends IntentService {
   
            private static final String TAG = "RegIntentService";
   
            private NotificationHub hub;
   
            public RegistrationIntentService() {
                super(TAG);
            }
   
            @Override
            protected void onHandleIntent(Intent intent) {
   
                SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
                String resultString = null;
                String regID = null;
                String storedToken = null;
   
                try {
                    String FCM_token = FirebaseInstanceId.getInstance().getToken();
                    Log.d(TAG, "FCM Registration Token: " + FCM_token);
   
                    // Storing the registration ID that indicates whether the generated token has been
                    // sent to your server. If it is not stored, send the token to your server,
                    // otherwise your server should have already received the token.
                    if (((regID=sharedPreferences.getString("registrationID", null)) == null)){
   
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
   
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
   
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
   
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
   
                    // Check if the token may have been compromised and needs refreshing.
                    else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {
   
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
   
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
   
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
   
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
   
                    else {
                        resultString = "Previously Registered Successfully - RegId : " + regID;
                    }
                } catch (Exception e) {
                    Log.e(TAG, resultString="Failed to complete registration", e);
                    // If an exception happens while fetching the new token or updating our registration data
                    // on a third-party server, this ensures that we'll attempt the update at a later time.
                }
   
                // Notify UI that registration has completed.
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(resultString);
                }
            }
        }
    ```

2. 在 `MainActivity` 类中，在类声明的上面添加以下 `import` 语句。
   
    ```java
        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.GoogleApiAvailability;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.content.Intent;
        import android.util.Log;
        import android.widget.TextView;
        import android.widget.Toast;
    ```
3. 将以下私有成员添加到类的顶部。 请使用这些字段来[检查 Google 推荐的 Google Play Services 的可用性](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk)。
   
    ```java
        public static MainActivity mainActivity;
        public static Boolean isVisible = false;    
        private static final String TAG = "MainActivity";
        private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

4. 在 `MainActivity` 类中，添加以下方法来检查 Google Play 服务的可用性。 
   
    ```java
        /**
        * Check the device to make sure it has the Google Play Services APK. If
        * it doesn't, display a dialog that allows users to download the APK from
        * the Google Play Store or enable it in the device's system settings.
        */
    
        private boolean checkPlayServices() {
            GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
            int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
            if (resultCode != ConnectionResult.SUCCESS) {
                if (apiAvailability.isUserResolvableError(resultCode)) {
                    apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                            .show();
                } else {
                    Log.i(TAG, "This device is not supported by Google Play Services.");
                    ToastNotify("This device is not supported by Google Play Services.");
                    finish();
                }
                return false;
            }
            return true;
        }
    ```

5. 在 `MainActivity` 类中添加以下代码，用于在调用 `IntentService` 之前检查 Google Play 服务，以获取 FCM 注册令牌并向通知中心注册。
   
    ```java
        public void registerWithNotificationHubs()
        {
            if (checkPlayServices()) {
                // Start IntentService to register this application with FCM.
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        }
    ```

6. 在 `MainActivity` 类的 `OnCreate` 方法中添进行下代码，以便在创建活动时开始注册过程。
   
    ```java
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            mainActivity = this;
            NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
            registerWithNotificationHubs();
        }
    ```

7. 若要验证和报告应用状态，请将这些其他的方法添加到 `MainActivity`。
   
    ```java
        @Override
        protected void onStart() {
            super.onStart();
            isVisible = true;
        }
   
        @Override
        protected void onPause() {
            super.onPause();
            isVisible = false;
        }
   
        @Override
        protected void onResume() {
            super.onResume();
            isVisible = true;
        }
   
        @Override
        protected void onStop() {
            super.onStop();
            isVisible = false;
        }
   
        public void ToastNotify(final String notificationMessage) {
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                    TextView helloText = (TextView) findViewById(R.id.text_hello);
                    helloText.setText(notificationMessage);
                }
            });
        }
    ```

8. `ToastNotify` 方法使用“Hello World”`TextView` 控件持续报告应用状态和通知。 在 activity_main.xml 布局中，为该控件添加以下 ID。
   
    ```java
       android:id="@+id/text_hello"
    ```

9. 接下来，为 AndroidManifest.xml 中定义的接收者添加一个子类。 将另一个名为 `MyHandler`的新类添加到项目。
10. 在 `MyHandler.java` 的顶部添加以下 import 语句：
    
    ```java
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.media.RingtoneManager;
        import android.net.Uri;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;
    ```

11. 为 `MyHandler` 类添加以下代码，使其成为 `com.microsoft.windowsazure.notifications.NotificationsHandler` 的子类。
    
    此代码将重写 `OnReceive` 方法，因此处理程序会报告所收到的通知。 处理程序还使用 `sendNotification()` 方法将推送通知发送到 Android 通知管理器。 应用未运行但收到通知时，应执行 `sendNotification()` 方法。
    
    ```java
        public class MyHandler extends NotificationsHandler {
            public static final int NOTIFICATION_ID = 1;
            private NotificationManager mNotificationManager;
            NotificationCompat.Builder builder;
            Context ctx;
    
            @Override
            public void onReceive(Context context, Bundle bundle) {
                ctx = context;
                String nhMessage = bundle.getString("message");
                sendNotification(nhMessage);
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(nhMessage);
                }
            }
    
            private void sendNotification(String msg) {
    
                Intent intent = new Intent(ctx, MainActivity.class);
                intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
                mNotificationManager = (NotificationManager)
                        ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
                PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                        intent, PendingIntent.FLAG_ONE_SHOT);
    
                Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
                NotificationCompat.Builder mBuilder =
                        new NotificationCompat.Builder(ctx)
                                .setSmallIcon(R.mipmap.ic_launcher)
                                .setContentTitle("Notification Hub Demo")
                                .setStyle(new NotificationCompat.BigTextStyle()
                                        .bigText(msg))
                                .setSound(defaultSoundUri)
                                .setContentText(msg);
    
                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }
        }
    ```

12. 在 Android Studio 的菜单栏上，单击“生成” > “重新生成项目”，确保代码中没有任何错误。
13. 在设备上运行该应用，验证其是否已成功注册到通知中心。 
    
    > [!NOTE]
    > 除非已调用实例 ID 服务的 `onTokenRefresh()` 方法，否则最初启动时注册可能会失败。 刷新即可成功注册到通知中心。
    > 
    > 

## <a name="test-the-app"></a>测试应用程序
### <a name="test-send-notification-from-the-notification-hub"></a>从通知中心测试性地发送通知
可以执行下述操作，以便从 [Azure 门户]发送推送通知： 

1. 在“故障排除”部分，选择“测试性发送”。
2. 对于“平台”，请选择“Android”。 
3. 选择“发送”。  目前在 Android 设备上还看不到通知，因为尚未在其上运行移动应用。 运行移动应用以后，再次选择“发送”按钮即可看到通知消息。 
4. 请在底部的列表中查看操作**结果**。 

    ![Azure 通知中心 - 测试发送](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)


[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


### <a name="run-the-mobile-app"></a>运行移动应用
如果想要在模拟器中测试推送通知，请确保模拟器映像支持你为应用程序选择的 Google API 级别。 如果映像不支持本机 Google API，那么此测试最终以 **SERVICE\_NOT\_AVAILABLE** 异常结束。

另外，请确保已将 Google 帐户添加到运行的模拟器的“设置” > “帐户”下。 否则，尝试向 GCM 注册可能会导致 **AUTHENTICATION\_FAILED** 异常。

1. 运行应用，并留意报告注册成功的注册 ID。
   
    ![测试 Android - 通道注册](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-registered.png)
2. 输入一条要发送到已在中心注册的所有 Android 设备的通知消息。
   
    ![测试 Android - 发送一条消息](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-set-message.png)
3. 按“发送通知”。 任何有应用运行的设备都会显示一个 `AlertDialog` 实例，其中包含推送通知消息。 未运行此应用程序，但之前已注册推送通知的设备会在 Android 通知管理器中收到通知。 从左上角向下轻扫即可查看通知。
   
    ![测试 Android - 通知](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-received-message.png)

## <a name="next-steps"></a>后续步骤
本教程介绍了如何使用 Firebase Cloud Messaging 将通知推送到 Android 设备。 若要了解如何使用 Google Cloud Messaging 来推送通知，请转到以下教程： 

> [!div class="nextstepaction"]
>[使用 Google Cloud Messaging 将通知推送到 Android 设备](notification-hubs-android-push-notification-google-gcm-get-started.md)


<!-- Images. -->


<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure 门户]: https://portal.azure.com
