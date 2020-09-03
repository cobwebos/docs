---
title: 使用 Azure 通知中心和 Firebase SDK 版本 1.0.0-preview1 将推送通知发送到 Android
description: 本教程介绍如何使用 Azure 通知中心和 Google Firebase Cloud Messaging 将推送通知发送到 Android 设备。
author: sethmanheim
ms.author: sethm
ms.date: 5/28/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 05/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ae1d2dfaf7d83d3b2323812f637bddd91b9a2ea2
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018223"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase-sdk-version-100-preview1"></a>教程：使用 Firebase SDK 版本 1.0.0-preview1 将推送通知发送到 Android 设备

本教程介绍了如何使用 Azure 通知中心和 Firebase Cloud Messaging (FCM) SDK 的更新版本（版本 1.0.0-preview1），将推送通知发送到 Android 应用程序。 在本教程中，创建一个空白 Android 应用，它使用 Firebase Cloud Messaging (FCM) 接收推送通知。

可以从 [GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/v1-preview/notification-hubs-test-app-refresh) 下载本教程的完整代码。

本教程涵盖以下步骤：

- 创建 Android Studio 项目。
- 创建支持 Firebase Cloud Messaging 的 Firebase 项目。
- 创建通知中心。
- 将应用连接到中心。
- 测试应用。

## <a name="prerequisites"></a>先决条件

要完成本教程，必须有一个有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/free/)。

还需要以下项：

- 建议使用最新版本的  [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)。
- 最低支持为 API 级别 16。

## <a name="create-an-android-studio-project"></a>创建 Android Studio 项目

第一步是在 Android Studio 中创建项目：

1. 启动 Android Studio。

2. 依次选择“文件”、“新建”和“新建项目” ****  **** 。

3. 在“选择项目”页上选择“空活动”，然后选择“下一步” ****   ****  **** 。

4. 在“配置项目”页上执行以下操作 ****  ：
   1. 输入应用程序的名称。
   2. 指定项目文件的保存位置。
   3. 选择“完成” **** 。

   :::image type="content" source="media/android-sdk/configure-project.png" alt-text="配置项目":::

## <a name="create-a-firebase-project-that-supports-fcm"></a>创建支持 FCM 的 Firebase 项目

1. 登录到  [Firebase 控制台](https://firebase.google.com/console/)。 如果还没有 Firebase 项目，创建一个新项目。

2. 创建项目后，选择“向 Android 应用添加 Firebase” **** 。

   :::image type="content" source="media/android-sdk/get-started.png" alt-text="添加 Firebase":::

3. 在“向 Android 应用添加 Firebase”页上，执行以下操作 ****  ：

   1. 对于“Android 包名称”，在应用程序的 build.gradle 文件中复制 applicationId 的值 ****  。 在此示例中，它是  `com.fabrikam.fcmtutorial1app`。

      :::image type="content" source="media/android-sdk/specify-package-name-fcm-settings.png" alt-text="指定包名称":::

   2. 选择“注册应用” **** 。

4. 选择“下载 google-services.json”，将该文件保存到项目的 app 文件夹中，然后选择“下一步” ****  ****   **** 。

   :::image type="content" source="media/android-sdk/download-google-service-button.png" alt-text="下载 Google 服务":::

5. 在 Firebase 控制台中，选择与项目相对应的齿轮图标。 然后，选择“项目设置” **** 。

   :::image type="content" source="media/android-sdk/notification-hubs-firebase-console-project-settings.png" alt-text="项目设置":::

6. 如果尚未将 google-services.json 文件下载到 Android Studio 项目的 app 文件夹中，则可以在该页上执行此操作 ****  。

7. 切换到“Cloud Messaging”选项卡 ****  。

8. 复制并保存服务器密钥以供将来使用 ****  。 此值将用于配置中心。

## <a name="configure-a-notification-hub"></a>配置通知中心

1. 登录  [Azure 门户](https://portal.azure.com/)。

2. 在左侧菜单上选择“所有服务”，然后在“移动”部分中选择“通知中心” ****   ****   ****  。 选择服务名称旁边的星形图标，将服务添加到左侧菜单上的“收藏夹”部分 ****  。 在将“通知中心”添加到“收藏夹”之后，在左侧菜单上选择它 ****   **** 。

3. 在“通知中心”页上，选择工具栏上的“添加” ****   ****  。

   :::image type="content" source="media/android-sdk/add-hub.png" alt-text="添加中心":::

4. 在“通知中心”页上，执行以下操作 ****  ：

   1. 在“通知中心”中输入名称 **** 。

   2. 在“创建新的命名空间”中输入名称 **** 。 命名空间包含一个或多个中心。

   3. 从“位置”下拉列表中选择值 ****   此值指定要在其中创建通知中心的位置。

   4. 在“资源组”中选择现有资源组或创建新的资源组 **** 。

   5. 选择“创建” **** 。

      :::image type="content" source="media/android-sdk/create-hub.png" alt-text="创建中心":::

5. 选择“通知”（钟形图标），然后选择“转到资源” ****   **** 。 还可以刷新“通知中心”页上的列表，然后选择相应中心 ****  。

   :::image type="content" source="media/android-sdk/notification-hubs.png" alt-text="选择中心":::

6. 从列表中选择“访问策略” ****  。 记下提供的两个连接字符串。 稍后在处理推送通知时需要它们。

   :::image type="content" source="media/android-sdk/access-policies.png" alt-text="访问策略":::

   > [!IMPORTANT]
   > 请勿在应用程序中使用 DefaultFullSharedAccessSignature 策略 ****  。 此策略仅用于应用后端。

## <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>为中心配置 Firebase Cloud Messaging 设置

1. 在左窗格中的“设置”下，选择“Google (GCM/FCM)” ****   **** 。

2. 输入前面保存的 FCM 项目“服务器密钥” ****  。

3. 在工具栏上选择“保存” **** 。

   :::image type="content" source="media/android-sdk/fcm-server-key.png" alt-text="服务器密钥":::

4. Azure 门户中显示一条消息，指出中心已成功更新。 “保存”按钮已禁用 ****  。

现在，通知中心已配置为可用于 Firebase Cloud Messaging。 连接字符串还需要将通知发送到设备并注册应用以接收通知。

## <a name="connect-your-app-to-the-notification-hub"></a>将应用连接到通知中心

### <a name="add-google-play-services-to-the-project"></a>将 Google Play 服务添加到项目

1. 在 Android Studio 中，在菜单上选择“工具”，然后选择“SDK 管理器” ****   **** 。

2. 选择项目中使用的 Android SDK 的目标版本。 然后选择“显示包详细信息” **** 。

   :::image type="content" source="media/android-sdk/notification-hubs-android-studio-sdk-manager.png" alt-text="SDK 管理器":::

3. 选择“Google API”（如果尚未安装） **** 。

   :::image type="content" source="media/android-sdk/google-apis-selected.png" alt-text="API":::

4. 切换到“SDK Tools”选项卡 ****  。 如果尚未安装 Google Play Services，请选择“Google Play Services”，如下图所示 ****  。 然后，选择“应用”以进行安装 ****  。 记下 SDK 路径，因为后面的步骤将要用到。

   :::image type="content" source="media/android-sdk/google-play-services-selected.png" alt-text="Play Services":::

5. 如果看到了“确认更改”对话框，请选择“确定” ****   **** 。 组件安装程序将安装所请求的组件。 在安装组件后，选择“完成” ****  。

6. 选择“确定”，关闭“新项目设置”对话框 ****   ****  。

### <a name="add-azure-notification-hubs-libraries"></a>添加 Azure 通知中心库

1. 在应用的 build.gradle 文件中，在 dependencies 部分添加以下行：

   ```gradle
   implementation 'com.microsoft.azure:notification-hubs-android-sdk:1.0.0-preview1@aar'
   implementation 'androidx.appcompat:appcompat:1.0.0'

   implementation 'com.google.firebase:firebase-messaging:20.1.5'

   implementation 'com.android.volley:volley:1.1.1'
   ```

2. 在 dependencies 部分的后面添加以下存储库：

   ```gradle
   repositories {
      maven {
         url "https://dl.bintray.com/microsoftazuremobile/SDK"
      }
   }
   ```

### <a name="add-google-firebase-support"></a>添加 Google Firebase 支持

1. 在该文件的末尾添加以下插件（如果已经不在那里）。

   ```gradle
   apply plugin: 'com.google.gms.google-services'
   ```

2. 在工具栏上选择“立即同步” ****  。

### <a name="add-code"></a>添加代码

1. 创建一个 NotificationHubListener 对象，该对象处理对来自 Azure 通知中心的消息的拦截。

   ```csharp
   public class CustomNotificationListener implements NotificationHubListener {

      @override

      public void onNotificationReceived(Context context, NotificationMessage message) {

      /* The following notification properties are available. */

      String title = message.getTitle();
      String message = message.getMessage();
      Map<String, String> data = message.getData();

      if (message != null) {
         Log.d(TAG, "Message Notification Title: " + title);
         Log.d(TAG, "Message Notification Body: " + message);
         }
      }
   }
   ```

2. 在  `MainActivity` 类的  `OnCreate` 方法中添加以下代码，以便在创建活动时启动通知中心初始化过程  ：

   ```java
   @Override
   protected void onCreate(Bundle savedInstanceState) {

      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity\_main);
      NotificationHub.setListener(new CustomNotificationListener());
      NotificationHub.initialize(this.getApplication(), “Connection-String”, "Hub Name");

   }
   ```

3. 在 Android Studio 的菜单栏上，选择“生成”，然后选择“重新生成项目”，确保代码中没有错误 ****  ****  。 如果收到有关“ic_launcher”图标的错误，请从 AndroidManifest.xml 文件中删除以下语句 ****  ：

   ```xml
   android:icon="@mipmap/ic_launcher"
   ```

4. 确保有用于运行应用的虚拟设备。 如果没有，请添加一个，如下所示：

   1. :::image type="content" source="media/android-sdk/open-device-manager.png" alt-text="设备管理器":::
   2. :::image type="content" source="media/android-sdk/your-virtual-devices.png" alt-text="虚拟设备":::
   3. 在所选设备上运行该应用，验证其是否已成功注册到中心。

      :::image type="content" source="media/android-sdk/device-registration.png" alt-text="设备注册":::

      > [!NOTE]
      > 除非已调用实例 ID 服务的 `onTokenRefresh()` 方法，否则最初启动期间注册可能会失败。 刷新即可成功注册到通知中心。

## <a name="send-a-test-notification"></a>发送测试通知

可以通过  [Azure 门户](https://portal.azure.com/)将推送通知发送到通知中心，如下所示：

1. 在 Azure 门户中，在中心的“通知中心”页上，选择“故障排除”部分的“测试发送” ****   ****  。

2. 在“平台”上选择“Android” ****  **** 。

3. 选择“发送” **** 。 目前在 Android 设备上还看不到通知，因为尚未在其上运行移动应用。 运行移动应用后，再次选择“发送”按钮即可看到通知消息 ****  。

4. 请在门户页底部的列表中查看操作结果。

   :::image type="content" source="media/android-sdk/notification-hubs-test-send.png" alt-text="发送测试通知":::

5. 设备上会显示通知消息。

通常，推送通知是在后端服务（例如移动应用，或者使用兼容库的 ASP.NET）中发送的。 如果后端没有可用库，也可使用 REST API 直接发送通知消息。

## <a name="run-the-mobile-app-on-emulator"></a>在仿真器中运行移动应用

在模拟器中测试推送通知之前，请确保模拟器映像支持你为应用程序选择的 Google API 级别。 如果映像不支持本机 Google API，可能会收到“SERVICE_NOT_AVAILABLE”异常 ****  。

另外，请确保已在“设置”>“帐户”下将 Google 帐户添加到正在运行的模拟器中 ****   **** 。 否则，尝试向 FCM 注册可能会导致“AUTHENTICATION_FAILED”异常 ****  。

## <a name="next-steps"></a>后续步骤

在本教程中，你已使用 Firebase Cloud Messaging 将通知广播到在该服务中注册的所有 Android 设备。 若要了解如何向特定的设备推送通知，请转到以下教程：

> [!div class="nextstepaction"]
>[教程：向特定用户发送通知](push-notifications-android-specific-users-firebase-cloud-messaging.md)

下面是有关发送通知的其他教程列表：

- Azure 移动应用：有关如何从与通知中心集成的移动应用后端发送通知的示例，请参阅 [将推送通知添加到 iOS 应用](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push)。

- ASP.NET： [使用通知中心向用户发送推送通知](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)。

- Azure 通知中心 Java SDK：有关从 Java 发送通知的信息，请参阅 [如何通过 Java 使用通知中心](notification-hubs-java-push-notification-tutorial.md) 。 这种方法已在 Eclipse for Android 开发环境中进行测试。

- PHP： [如何通过 PHP 使用通知中心](notification-hubs-php-push-notification-tutorial.md)。