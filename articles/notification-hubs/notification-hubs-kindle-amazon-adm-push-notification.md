---
title: 使用 Azure 通知中心向 Kindle 应用推送通知 | Microsoft Docs
description: 在本教程中，将了解如何使用 Azure 通知中心将推送通知发送到 Kindle 应用程序。
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/29/2019
ms.author: jowargo
ms.openlocfilehash: edd0e12460e07cfd2990cc43a9056ed06b84fb1d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926765"
---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>通知中心入门（Kindle 应用）

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

本教程演示如何使用 Azure 通知中心将推送通知发送到 Kindle 应用程序。 你将创建一个空白 Kindle 应用，它使用 Amazon Device Messaging (ADM) 接收推送通知。

在本教程中，你创建/更新代码来执行以下任务：

> [!div class="checklist"]
> * 向开发人员门户添加新应用程序
> * 创建 API 密钥
> * 创建并配置通知中心
> * 设置应用程序
> * 创建 ADM 消息处理程序
> * 将 API 密钥添加到应用
> * 运行应用
> * 发送测试通知

## <a name="prerequisites"></a>先决条件

- [Android Studio](https://developer.android.com/studio/?gclid=CjwKCAjwwZrmBRA7EiwA4iMzBPZ9YYmR0pbb5LtjnWhWCxe8PWrmjmeaR6ad5ksCr_j2mmkVj_-o6hoCAqwQAvD_BwE)。
- 按照[设置开发环境](https://developer.amazon.com/docs/fire-tablets/ft-set-up-your-development-environment.html)中的步骤设置 Kindle 的开发环境。

## <a name="add-a-new-app-to-the-developer-portal"></a>向开发人员门户添加新应用程序

1. 登录到 [Amazon 开发人员门户](https://developer.amazon.com/apps-and-games/console/apps/list.html)。
2. 依次选择“添加新应用”、“Android”。  

    ![“添加新应用”按钮](./media/notification-hubs-kindle-get-started/add-new-app-button.png)
1. 在“新建应用提交内容”页上，执行以下步骤获取**应用程序密钥**：
    1. 输入“应用标题”的名称。
    2. 选择任一**类别**（例如“教育”）
    4. 在“客户支持电子邮件地址”字段中输入电子邮件地址。 
    5. 选择“保存”。

        ![“新建应用提交内容”页](./media/notification-hubs-kindle-get-started/new-app-submission-page.png) 
2.  在顶部切换到“手机广告”选项卡，然后执行以下步骤： 
    1. 指定你的应用是否主要面向 13 岁以下的儿童。 对于本教程，请选择“否”。
    2. 选择“提交”。 

        ![“手动广告”页](./media/notification-hubs-kindle-get-started/mobile-ads-page.png)
    3. 复制“手机广告”页中的**应用程序密钥**。 

        ![应用程序密钥](./media/notification-hubs-kindle-get-started/application-key.png)
3.  选择顶部的“应用和服务”菜单，然后在列表中选择你的应用程序。 

    ![从列表中选择你的应用](./media/notification-hubs-kindle-get-started/all-apps-select.png)
4. 切换到“设备消息传送”选项卡，然后执行以下步骤： 
    1. 选择“创建新的安全配置文件”。
    2. 输入安全配置文件的**名称**。 
    3. 输入安全配置文件的**说明**。 
    4. 选择“保存”。 
    5. 在结果页上选择“查看安全配置文件”。 
5. 现在，请在“安全配置文件”页上执行以下步骤： 
    1. 切换到“Web 设置”选项卡，并复制“客户端 ID”和“客户端机密”值供稍后使用。 

        ![获取客户端 ID 和机密](./media/notification-hubs-kindle-get-started/client-id-secret.png) 
    2. 切换到“Android/Kindle 设置”页，并使该页保持打开状态。 将在下一部分输入这些值。 

## <a name="create-an-api-key"></a>创建 API 密钥
1. 使用管理员特权打开命令提示符。
2. 导航到 Android SDK 文件夹。
3. 输入以下命令：

    ```shell
    keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
    ```
4. 对于“密钥库”密码，请键入 **android**。
5. 复制“MD5”和“SHA256”指纹。 
6. 返回开发人员门户，在“Android/Kindle 设置”选项卡上执行以下步骤： 
    1. 输入 **API 密钥的名称**。 
    2. 输入应用的**包名称**（例如 **com.fabrikam.mykindleapp**）和“MD5”值。
        
        >[!IMPORTANT]
        > 在 Android Studio 中创建应用时，将使用此处指定的包名称。 
    1. 粘贴前面复制的 **MD5 签名**。 
    2. 粘贴前面复制的 **SHA256 签名**。  
    3. 选择“生成新密钥”。

        ![Android/Kindle 设置 - 生成密钥](./media/notification-hubs-kindle-get-started/android-kindle-settings.png)
    4. 现在，请在列表中选择“显示”以查看 API 密钥。 

        ![Android/Kindle 设置 - 显示 API 密钥](./media/notification-hubs-kindle-get-started/show-api-key-button.png) 
    5. 在“API 密钥详细信息”窗口中，复制 API 密钥并将其保存到某个位置。 然后，选择右上角的“X”以关闭窗口。 


## <a name="create-and-configure-a-notification-hub"></a>创建并配置通知中心

1. 遵循[在 Azure 门户中创建 Azure 通知中心](create-notification-hub-portal.md)一文中的步骤创建通知中心。 
2. 在“设置”菜单下选择“Amazon (ADM)”。
3. 粘贴前面保存的**客户端 ID** 和**客户端机密**。 
4. 在工具栏上选择“保存”。 

    ![配置通知中心的 ADM 设置](./media/notification-hubs-kindle-get-started/configure-notification-hub.png)
5. 在左侧菜单中选择“访问策略”，然后选择 **DefaultListenSharedAccessSignature** 策略的连接字符串对应的“复制”按钮。 将其保存在某个位置。 稍后将在源代码中使用它。 

    ![通知中心 - 侦听连接字符串](./media/notification-hubs-kindle-get-started/event-hub-listen-connection-string.png)    

## <a name="set-up-your-application"></a>设置应用程序

1. 启动 Android Studio。 
2. 选择“文件”，指向“新建”，然后选择“新建项目”。 
3. 在“选择项目”窗口中的“手机和平板电脑”选项卡上，依次选择“空活动”、“下一步”。 
4. 在“配置项目”窗口中执行以下步骤：
    1. 输入**应用程序的名称**。 建议使用与在 Amazon 开发人员门户中创建的应用程序名称匹配的名称。 
    2. 输入**包的名称**。 
        
        >[!IMPORTANT]
        >该包名称必须与在 Amazon 开发人员门户中指定的包名称相匹配。
    3. 查看剩余的值并相应地更新。 
    4. 选择“完成”。 

        ![配置 Android 项目](./media/notification-hubs-kindle-get-started/new-android-studio-project.png)
5. 将[适用于 Android 的 Amazon 开发人员 SDK](https://developer.amazon.com/sdk-download) 库下载到硬盘。 解压缩 SDK zip 文件。
6. 在 Android Studio 中，如果文件夹结构尚未设置为“项目”，请将其从“Android”更改为“项目”。 

    ![Android Studio - 切换到项目结构](./media/notification-hubs-kindle-get-started/android-studio-project-view.png)
7. 在树视图中展开 **app** 以查看 **libs** 文件夹。     
8. 在“文件资源管理器”窗口中，导航到 Amazon Android SDK 所下载到的文件夹。
9. 按住 **CTRL** 并将 **amazon-device-messaging-1.0.1.jar** 文件拖放到树视图中的 **lib** 节点。 

    ![Android Studio - 添加 Amazon Device Messaging JAR](./media/notification-hubs-kindle-get-started/drag-drop-amazon-device-messaging-jar.png)
9. 在“复制”窗口中选择“确定”。 如果看到的是“移动”窗口而不是“复制”窗口，请将其关闭，然后在按住 **CTRL** 按钮的同时重试拖放操作。 

    ![Android Studio - 复制 JAR](./media/notification-hubs-kindle-get-started/copy-jar-window.png)
10. 将以下语句添加到**应用的 build.gradle** 文件中的 **dependencies** 节：`implementation files('libs/amazon-device-messaging-1.0.1.jar')`。 

    ![Android Studio - 将 ADM 添加到应用的 build.gradle](./media/notification-hubs-kindle-get-started/adm-build-gradle.png)
11. 在**应用**的 `Build.Gradle` 文件中的 **dependencies** 节内添加以下行： 

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```
12. 在 **dependencies** 节的**后面**添加以下存储库：

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```
13. 在**应用**的 **build.gradle** 文件的编辑器中，选择工具栏上的“立即同步”。 

    ![Android Studio - 同步应用的 build.gradle](./media/notification-hubs-kindle-get-started/gradle-sync-now.png)
14. 切换回到树视图中的 Android 结构。  在根清单元素中添加 Amazon 命名空间：

    ```xml
    xmlns:amazon="http://schemas.amazon.com/apk/res/android"
    ```
   
    ![清单中的 Amazon 命名空间](./media/notification-hubs-kindle-get-started/amazon-namespace-manifest.png)
2. 在清单元素下添加权限作为第一个元素。 将 **[YOUR PACKAGE NAME]** 替换为用于创建应用的包。

    ```xml
    <permission
        android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
        android:protectionLevel="signature" />

    <uses-permission android:name="android.permission.INTERNET"/>

    <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

    <!-- This permission allows your app access to receive push notifications
    from ADM. -->
    <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

    <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    ```
3. 插入以下元素作为应用程序元素的第一个子级。 将 **[YOUR PACKAGE NAME]** 替换为用于创建应用的包名称。 在下一步骤中将会创建 MyADMMessageHandler 类。 

    ```xml
        <amazon:enable-feature
            android:name="com.amazon.device.messaging"
            android:required="true"/>
        <service
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler"
            android:exported="false" />
        <receiver
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler$Receiver"
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
                <action android:name="com.amazon.device.messaging.intent.RECEIVE" />
                <!-- Replace the name in the category tag with your app's package name. -->
                <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>
    ```

## <a name="create-your-adm-message-handler"></a>创建 ADM 消息处理程序

1. 将继承自 `com.amazon.device.messaging.ADMMessageHandlerBase` 的新类添加到项目中的 `com.fabrikam.mykindleapp` 包，并将其命名为 `MyADMMessageHandler`，如下图所示：

    ![创建 MyADMMessageHandler 类](./media/notification-hubs-kindle-get-started/create-adm-message-handler.png)

2. 将以下 `import` 语句添加到 `MyADMMessageHandler` 类：

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.support.v4.app.NotificationCompat;
    import android.util.Log;
    import com.amazon.device.messaging.ADMMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    ```

3. 在创建的类中添加以下代码。 请记住包含通知中心和侦听连接字符串名称的 `[HUB NAME]` 和 `[LISTEN CONNECTION STRING]`： 

    ```java
    public static final int NOTIFICATION_ID = 1;
    private NotificationManager mNotificationManager;
    NotificationCompat.Builder builder;
    private static NotificationHub hub;

    public static NotificationHub getNotificationHub(Context context) {
        Log.v("com.wa.hellokindlefire", "getNotificationHub");
        if (hub == null) {
            hub = new NotificationHub("[HUB NAME]", "[HUB NAMESPACE CONNECTION STRING]", context);
        }
        return hub;
    }

    public MyADMMessageHandler() {
        super("MyADMMessageHandler");
    }

    @Override
    protected void onMessage(Intent intent) {
        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);
    }

    @Override
    protected void onRegistrationError(String s) {

    }

    @Override
    protected void onRegistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).register(s);
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
        }
    }

    @Override
    protected void onUnregistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).unregister();
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
        }
    }

    public static class Receiver extends ADMMessageReceiver
    {
        public Receiver()
        {
            super(MyADMMessageHandler.class);
        }
    }

    private void sendNotification(String msg) {
        Context ctx = getApplicationContext();

        mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);


        NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                        .setSmallIcon(R.mipmap.ic_launcher)
                        .setContentTitle("Notification Hub Demo")
                        .setStyle(new NotificationCompat.BigTextStyle()
                                .bigText(msg))
                        .setContentText(msg);

        mBuilder.setContentIntent(contentIntent);
        mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
    }
    ```

## <a name="add-your-api-key-to-your-app"></a>将 API 密钥添加到应用
1. 遵循以下步骤将 assets 文件夹添加到项目。 
    1. 切换到“项目”视图。 
    2. 右键单击“app”。
    3. 选择“新建”。
    4. 选择“文件夹”。 
    5. 然后选择“Assets 文件夹”。 

        ![添加 assets 文件夹菜单](./media/notification-hubs-kindle-get-started/add-assets-folder-menu.png)    
    6. 在“配置组件”页上执行以下步骤：
        1. 选择“更改文件夹位置”
        2. 确认文件夹已设置为：`src/main/assets`。
        3. 选择“完成”。 
        
            ![配置 assets 文件夹](./media/notification-hubs-kindle-get-started/configure-asset-folder.png)
2. 将名为 **api_key.txt** 的文件添加到 **assets** 文件夹。 在树视图中，依次展开“app”、“src”、“main”，右键单击“assets”，指向“新建”，然后选择“文件”。 输入 **api_key.txt** 作为文件名。 3. 
5. 将你在 Amazon 开发人员门户中生成的 API 密钥复制到 api_key.txt 文件。 
6. 生成项目。 

## <a name="run-the-app"></a>运行应用
1. 在 Kindle 设备上，从顶部往下轻扫，单击“设置”，然后单击“我的帐户”并使用有效的 Amazon 帐户注册。
2. 通过 Android Studio 在 Kindle 设备上运行该应用。 

> [!NOTE]
> 如果出现了问题，请检查模拟器（或设备）的时间。 时间值必须准确。 若要更改 Kindle 模拟器的时间，可以从 Android SDK platform-tools 目录运行以下命令：

```shell
adb shell  date -s "yyyymmdd.hhmmss"
```

## <a name="send-a-notification-message"></a>发送通知消息

若要使用 .NET 发送消息：

```csharp
static void Main(string[] args)
{
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

    hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
}
```

有关示例代码，请参阅 [GitHub 上的此示例](https://github.com/Azure/azure-notificationhubs-dotnet/blob/master/Samples/SendPushSample/SendPushSample/Program.cs)。

![][7]

## <a name="next-steps"></a>后续步骤

本教程介绍了如何将广播通知发送到所有注册到后端的 Kindle 设备。 若要了解如何向特定的 Kindle 设备推送通知，请转到以下教程：以下教程展示了如何将通知推送到特定的 Android 设备，但可以使用同样的逻辑将通知推送到特定的 Kindle 设备。

> [!div class="nextstepaction"]
>[向特定设备推送通知](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- URLs. -->
[Amazon developer portal]: https://developer.amazon.com/home.html
[download the SDK]: https://developer.amazon.com/public/resources/development-tools/sdk
[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
