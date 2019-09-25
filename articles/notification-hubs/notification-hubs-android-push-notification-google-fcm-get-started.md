---
title: 使用 Azure 通知中心和 Firebase Cloud Messaging 将通知推送到 Android 应用 | Microsoft Docs
description: 本教程介绍如何使用 Azure 通知中心和 Google Firebase Cloud Messaging 将通知推送到 Android 设备。
services: notification-hubs
documentationcenter: android
keywords: 推送通知, 推送通知, android 推送通知, fcm, firebase cloud messaging
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/11/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/11/2019
ms.openlocfilehash: c40b2e236051d8888d1b9135a2c5259000798319
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212301"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-firebase-cloud-messaging"></a>教程：使用 Azure 通知中心和 Google Firebase Cloud Messaging 将通知推送到 Android 设备

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

本教程介绍如何使用 Azure 通知中心和 Firebase Cloud Messaging (FCM) 将通知推送到 Android 应用程序。 在本教程中，请创建一个空白 Android 应用，以便使用 Firebase Cloud Messaging (FCM) 接收推送通知。

可以[从 GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/master/samples/FCMTutorialApp)下载本教程的已完成代码。

在本教程中，我们将执行以下步骤：

> [!div class="checklist"]
> * 创建 Android Studio 项目。
> * 创建支持 Firebase Cloud Messaging 的 Firebase 项目。
> * 创建中心。
> * 将应用连接到中心。
> * 测试应用。

## <a name="prerequisites"></a>先决条件

要完成本教程，必须有一个有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/free/)。 

还需要以下项： 

* 最新版本的 [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)
* 适用于 Firebase Cloud Messaging 的 Android 2.3 或更高版本
* 适用于 Firebase Cloud Messaging 的 Google Repository 版本 27 或更高版本
* 适用于 Firebase Cloud Messaging 的 Google Play Services 9.0.2 或更高版本

完成本教程是学习有关 Android 应用的所有其他通知中心教程的先决条件。

## <a name="create-an-android-studio-project"></a>创建 Android Studio 项目

1. 启动 Android Studio。
2. 选择“文件”，指向“新建”，然后选择“新建项目”。    
2. 在“选择项目”页上选择“空活动”，选择“下一步”。    
3. 在“配置项目”页上执行以下步骤：  
    1. 输入应用程序的名称。
    2. 指定项目文件的保存位置。 
    3. 选择“完成”。  

        ![配置项目](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>创建支持 FCM 的 Firebase 项目

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>配置中心

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>为中心配置 Firebase Cloud Messaging 设置

1. 在左窗格中的“设置”下，选择“Google (GCM/FCM)”   。 
2. 输入前面保存的 FCM 项目“服务器密钥”  。 
3. 在工具栏上选择“保存”。  

    ![Azure 通知中心 - Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Azure 门户中显示一条警报消息，指出通知中心已成功更新。 “保存”按钮已禁用。  

现在，中心已配置为使用 Firebase Cloud Messaging。 连接字符串还需要将通知发送到设备并注册应用以接收通知。

## <a id="connecting-app"></a>将你的应用连接到通知中心

### <a name="add-google-play-services-to-the-project"></a>将 Google Play 服务添加到项目

1. 在 Android Studio 中，在菜单上选择“工具”  ，然后选择“SDK 管理器”  。 
2. 选择项目中使用的 Android SDK 的目标版本。 然后选择“显示包详细信息”  。 

    ![Android SDK 管理器 - 选择目标版本](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. 选择“Google API”  （如果尚未安装）。

    ![Android SDK 管理器 - 选择了“Google API”](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. 切换到“SDK 工具”选项卡。  如果尚未安装 Google Play Services，请选择“Google Play Services”，如下图所示。  然后，选择“应用”  以进行安装。 记下 SDK 路径，因为后面的步骤将要用到。

    ![Android SDK 管理器 - 选择了“Google Play 服务”](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. 如果看到了“确认更改”  对话框，请选择“确定”  。 组件安装程序将安装所请求的组件。 在安装组件后，选择“完成”  。
4. 选择“确定”  以关闭“新项目的设置”  对话框。  
1. 打开 AndroidManifest.xml 文件，然后将以下标记添加到应用程序  标记。

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```


### <a name="add-azure-notification-hubs-libraries"></a>添加 Azure 通知中心库

1. 在应用的 Build.Gradle 文件中，添加以下行到依赖项部分。

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    ```

2. 在 dependencies 节的后面添加以下存储库。

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>添加 Google Firebase 支持

1. 在应用的 Build.Gradle 文件中，如果她们不存在就添加以下行到“依赖项”部分  。 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. 在该文件的末尾添加以下插件（如果已经不在那里）。 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. 在工具栏上选择“立即同步”。 

### <a name="update-the-androidmanifestxml-file"></a>更新 AndroidManifest.xml 文件

1. 收到 FCM 注册令牌后，使用它[在 Azure 通知中心注册](notification-hubs-push-notification-registration-management.md)。 请使用名为 `RegistrationIntentService` 的 `IntentService` 在后台支持此注册。 此服务还刷新 FCM 注册令牌。 你还将创建一个名为 `FirebaseService` 的类作为 `FirebaseMessagingService` 的子类，并重写 `onMessageReceived` 方法以接收和处理通知。 

    将以下服务定义添加到 AndroidManifest.xml 文件的 `<application>` 标记内。

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    <service
        android:name=".FirebaseService"
        android:exported="false">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT" />
        </intent-filter>
    </service>
    ```
3. 在`</application>`标记下面添加以下必要的 FCM 相关权限。

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>添加代码

1. 在项目视图中，展开 **app** > **src** > **main** > **java** 右键单击“java”下的包文件夹，选择“新建”，然后选择“Java 类”    。 输入 NotificationSettings 作为名称，然后选择“确定”   。

    确保在 `NotificationSettings` 类的以下代码中更新这三个占位符：

   * **HubListenConnectionString**：中心的 **DefaultListenAccessSignature** 连接字符串。 可以复制此连接字符串，方法是在 [Azure 门户]的中心单击“访问策略”  。
   * **HubName**：使用 [Azure 门户]的中心页中显示的中心的名称。

     `NotificationSettings` 代码：

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > 输入中心的名称和 DefaultListenSharedAccessSignature，然后继续   。 

2. 将另一个名为 `RegistrationIntentService`的新类添加到项目。 此类实现 `IntentService` 接口。 它还处理[刷新 FCM 令牌](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens)和[在通知中心注册](notification-hubs-push-notification-registration-management.md)。

    针对此类使用以下代码。

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.android.gms.tasks.OnSuccessListener;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import java.util.concurrent.TimeUnit;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";
        String FCM_token = null;

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
                FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() { 
                    @Override 
                    public void onSuccess(InstanceIdResult instanceIdResult) { 
                        FCM_token = instanceIdResult.getToken(); 
                        Log.d(TAG, "FCM Registration Token: " + FCM_token); 
                    } 
                }); 
                TimeUnit.SECONDS.sleep(1);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server.
                // Otherwise, your server should have already received the token.
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

                // Check to see if the token has been compromised and needs refreshing.
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
                // If an exception happens while fetching the new token or updating registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

3. 在 `MainActivity` 类中，在类声明的上面添加以下 `import` 语句。

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

4. 在该类的最上面添加以下成员。 请使用这些字段来[检查 Google 推荐的 Google Play Services 的可用性](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk)。

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

5. 在 `MainActivity` 类中，添加以下方法来检查 Google Play 服务的可用性。

    ```java
    /**
    * Check the device to make sure it has the Google Play Services APK. If
    * it doesn't, display a dialog box that enables  users to download the APK from
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

6. 在 `MainActivity` 类中添加以下代码，用于在调用 `IntentService` 之前检查 Google Play 服务，以获取 FCM 注册令牌并向中心注册：

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

7. 在 `MainActivity` 类的 `OnCreate` 方法中添加以下代码，以便在创建活动时开始注册过程：

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        FirebaseService.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

8. 若要验证和报告应用状态，请将这些其他的方法添加到 `MainActivity`：

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

9. `ToastNotify` 方法使用“Hello World”`TextView` 控件持续报告应用状态和通知  。 在 **res** > **layout** > **activity_main.xml** 布局中，为该控件添加以下 ID。

    ```java
    android:id="@+id/text_hello"
    ```

    ![Azure 通知中心 - 测试发送](./media/notification-hubs-android-push-notification-google-fcm-get-started/activity-main-xml.png)

10. 接下来，为 AndroidManifest.xml 中定义的接收者添加一个子类。 将另一个名为 `FirebaseService`的新类添加到项目。

11. 在 `FirebaseService.java` 的顶部添加以下 import 语句：

    ```java
    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;
    import android.util.Log;
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import androidx.core.app.NotificationCompat;
    ```

12. 为 `FirebaseService` 类添加以下代码，使其成为 `FirebaseMessagingService` 的子类。

    此代码将重写 `onMessageReceived` 方法，并报告所收到的通知。 它还使用 `sendNotification()` 方法将推送通知发送到 Android 通知管理器。 应用未在运行但收到通知时，应调用 `sendNotification()` 方法。

    ```java
    public class FirebaseService extends FirebaseMessagingService
    {
        private String TAG = "FirebaseService";
    
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        static Context ctx;
    
        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            // ...
    
            // TODO(developer): Handle FCM messages here.
            // Not getting messages here? See why this may be: https://goo.gl/39bRNJ
            Log.d(TAG, "From: " + remoteMessage.getFrom());
    
            String nhMessage;
            // Check if message contains a notification payload.
            if (remoteMessage.getNotification() != null) {
                Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
    
                nhMessage = remoteMessage.getNotification().getBody();
            }
            else {
                nhMessage = remoteMessage.getData().values().iterator().next();
            }
    
            // Also if you intend on generating your own notifications as a result of a received FCM
            // message, here is where that should be initiated. See sendNotification method below.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
            sendNotification(nhMessage);
        }
    
        private void sendNotification(String msg) {
    
            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);
    
            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(
                    ctx,
                    NOTIFICATION_CHANNEL_ID)
                    .setContentText(msg)
                    .setPriority(NotificationCompat.PRIORITY_HIGH)
                    .setSmallIcon(android.R.drawable.ic_popup_reminder)
                    .setBadgeIconType(NotificationCompat.BADGE_ICON_SMALL);
    
            notificationBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
        }
    
        public static void createChannelAndHandleNotifications(Context context) {
            ctx = context;
    
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
             }
        }
    }
    ```

13. 在 Android Studio 的菜单栏上，选择“生成” > “重新生成项目”，确保代码中没有任何错误。   如果收到有关 `ic_launcher` 图标的错误，请从 AndroidManifest.xml 文件中删除以下语句： 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
14. 确保有用于运行应用的虚拟设备。 如果没有，请添加一个，如下所示：
    1. ![打开设备管理器](./media/notification-hubs-android-push-notification-google-fcm-get-started/open-device-manager.png)
    2. ![创建虚拟设备](./media/notification-hubs-android-push-notification-google-fcm-get-started/your-virtual-devices.PNG)

15. 在所选设备上运行该应用，验证其是否已成功注册到中心。

    > [!NOTE]
    > 除非已调用实例 ID 服务的 `onTokenRefresh()` 方法，否则最初启动期间注册可能会失败。 刷新即可成功注册到通知中心。

    ![设备注册成功](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>从通知中心测试性地发送通知

可以执行下述步骤，以便从 [Azure 门户]发送推送通知：

1. 在 Azure 门户中通知中心的“中心”页上，选择“故障排除”部分的“测试发送”。  
3. 对于“平台”，请选择“Android”  。 
4. 选择“发送”。   目前在 Android 设备上还看不到通知，因为尚未在其上运行移动应用。 运行移动应用以后，再次选择“发送”按钮即可看到通知消息。 
5. 请在底部的列表中查看操作结果。

    ![Azure 通知中心 - 测试发送](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. 设备上会显示通知消息。 

    ![设备上的通知消息](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>在仿真器中运行移动应用
在模拟器中测试推送通知之前，请确保模拟器映像支持你为应用程序选择的 Google API 级别。 如果映像不支持本机 Google API，可能会收到 “SERVICE\_NOT\_AVAILABLE”异常。 

另外，请确保已在“设置”   > “帐户”  下将 Google 帐户添加到正在运行的模拟器中。 否则，尝试向 FCM 注册可能会导致 “AUTHENTICATION\_FAILED” 异常  。

## <a name="next-steps"></a>后续步骤
在本教程中，你已使用 Firebase Cloud Messaging 将通知广播到在该服务中注册的所有 Android 设备。 若要了解如何向特定的设备推送通知，请转到以下教程：

> [!div class="nextstepaction"]
>[教程：向特定 Android 设备推送通知](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Azure 门户]: https://portal.azure.com
