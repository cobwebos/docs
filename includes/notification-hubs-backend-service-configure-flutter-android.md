---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 11bd05231aa3b5b336400d2434e63e57807a7e69
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156275"
---
### <a name="add-the-google-services-json-file"></a>添加 Google Services JSON 文件

1. 按住“Control”的同时 + 单击“android”文件夹，然后选择“在 Android Studio 中打开”  。 然后，切换到“项目”视图（如果还没有切换到此视图）。

1. 找到之前在 [Firebase 控制台](https://console.firebase.google.com)中设置 PushDemo 项目时下载的 google-services.json 文件。 然后，将其拖动到“应用”模块根目录（“android” > “android” > “应用”）中   。

### <a name="configure-build-settings-and-permissions"></a>配置生成设置和权限

1. 将“项目”视图切换成“Android” 。

1. 打开“AndroidManifest.xml”，然后在关闭 </manifest> 标记之前，在 application 元素后添加“INTERNET”和“READ_PHONE_STATE”权限    。

    ```xml
    <manifest>
        <application>...</application>
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    </manifest>
    ```

### <a name="add-the-firebase-sdks"></a>添加 Firebase SDK

1. 在 Android Studio 中，打开项目级别 build.gradle 文件（Gradle 脚本 > “build.gradle (Project: android)”）   。 并确保在``buildscript`` > “依赖项”节点中具有“com.google.gms:google-services”类路径。

    ```json
    buildscript {

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
      }

      dependencies {
        // ...

        // Add the following line:
        classpath 'com.google.gms:google-services:4.3.3'  // Google Services plugin
      }
    }

    allprojects {
      // ...

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
        // ...
      }
    }
    ```

    > [!NOTE]
    > 确保在创建“Android 项目”时，按照 [](https://console.firebase.google.com) 中提供的说明引用最新版本。

1. 在应用级 build.gradle 文件（Gradle 脚本 > “build.gradle (Module: app)”）中，应用 Google Services Gradle 插件   。 在“android”节点的正上方应用插件。

    ```json
    // ...

    // Add the following line:
    apply plugin: 'com.google.gms.google-services'  // Google Services plugin

    android {
      // ...
    }
    ```

1. 在同一个文件的“依赖项”节点中，添加“Cloud Messaging”Android 库的依赖项 。

    ```json
    dependencies {
        // ...
        implementation 'com.google.firebase:firebase-messaging:20.2.0'
    }
    ```

    > [!NOTE]
    > 确保按照 [Cloud Messaging Android 客户端文档](https://firebase.google.com/docs/cloud-messaging/android/client)引用了最新版本。

1. 保存更改，然后（从工具栏提示）单击“立即同步”按钮或“将项目与 Gradle 文件同步” 。

### <a name="handle-push-notifications-for-android"></a>处理 Android 的推送通知

1. 在 Android Studio 中，按住“Control”的同时 + 单击“com.<your_organization>.pushdemo”包文件夹（“app” > “src” > “main” > “kotlin”），选择“新建”菜单中的“包”         。 输入“services”作为名称，然后按“返回” 。

1. 按住“Control”的同时 + 单击“services”文件夹，选择“新建”菜单中的“Kotlin 文件/类”    。 输入“DeviceInstallationService”作为名称，然后按“返回” 。

1. 使用以下代码实现“DeviceInstallationService”。

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import android.annotation.SuppressLint
    import android.content.Context
    import android.provider.Settings.Secure
    import com.google.android.gms.common.ConnectionResult
    import com.google.android.gms.common.GoogleApiAvailability
    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    @SuppressLint("HardwareIds")
    class DeviceInstallationService {

        companion object {
            const val DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
            const val GET_DEVICE_ID = "getDeviceId"
            const val GET_DEVICE_TOKEN = "getDeviceToken"
            const val GET_DEVICE_PLATFORM = "getDevicePlatform"
        }

        private var context: Context
        private var deviceInstallationChannel : MethodChannel

        val playServicesAvailable
            get() = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context) == ConnectionResult.SUCCESS

        constructor(context: Context, flutterEngine: FlutterEngine) {
            this.context = context
            deviceInstallationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, DEVICE_INSTALLATION_CHANNEL)
            deviceInstallationChannel.setMethodCallHandler { call, result -> handleDeviceInstallationCall(call, result) }
        }

        fun getDeviceId() : String
            = Secure.getString(context.applicationContext.contentResolver, Secure.ANDROID_ID)

        fun getDeviceToken() : String {
            if(!playServicesAvailable) {
                throw Exception(getPlayServicesError())
            }

            // TODO: Revisit once we have created the PushNotificationsFirebaseMessagingService
            val token = "Placeholder_Get_Value_From_FirebaseMessagingService_Implementation"

            if (token.isNullOrBlank()) {
                throw Exception("Unable to resolve token for FCM.")
            }

            return token
        }

        fun getDevicePlatform() : String = "fcm"

        private fun handleDeviceInstallationCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                GET_DEVICE_ID -> {
                    result.success(getDeviceId())
                }
                GET_DEVICE_TOKEN -> {
                    getDeviceToken(result)
                }
                GET_DEVICE_PLATFORM -> {
                    result.success(getDevicePlatform())
                }
                else -> {
                    result.notImplemented()
                }
            }
        }

        private fun getDeviceToken(result: MethodChannel.Result) {
            try {
                val token = getDeviceToken()
                result.success(token)
            }
            catch (e: Exception) {
                result.error("ERROR", e.message, e)
            }
        }

        private fun getPlayServicesError(): String {
            val resultCode = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context)

            if (resultCode != ConnectionResult.SUCCESS) {
                return if (GoogleApiAvailability.getInstance().isUserResolvableError(resultCode)){
                    GoogleApiAvailability.getInstance().getErrorString(resultCode)
                } else {
                    "This device is not supported"
                }
            }

            return "An error occurred preventing the use of push notifications"
        }
    }
    ```

    > [!NOTE]
    > 此类为 `com.<your_organization>.pushdemo/deviceinstallation` 通道实现特定于平台的对应项。 这是在“DeviceInstallationService.dart”中的应用的 Flutter 部分中定义的。 在这种情况下，调用是从公共代码到本机主机进行的。 请务必将 <your_organization> 替换为你自己的组织。
    >
    > 此类提供一个唯一 ID（使用 [Secure.AndroidId](https://developer.android.com/reference/android/provider/Settings.Secure#ANDROID_ID)）作为通知中心注册有效负载的一部分。

1. 将另一个“Kotlin 文件/类”添加到名为 NotificationRegistrationService 的“服务”文件夹中，然后添加以下代码 。

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodChannel

    class NotificationRegistrationService {

        companion object {
            const val NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
            const val REFRESH_REGISTRATION = "refreshRegistration"
        }

        private var notificationRegistrationChannel : MethodChannel

        constructor(flutterEngine: FlutterEngine) {
            notificationRegistrationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationRegistrationService.NOTIFICATION_REGISTRATION_CHANNEL)
        }

        fun refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, null)
        }
    }
    ```

    > [!NOTE]
    > 此类为 `com.<your_organization>.pushdemo/notificationregistration` 通道实现特定于平台的对应项。 这是在“NotificationRegistrationService.dart”中的应用的 Flutter 部分中定义的。 在这种情况下，调用是从本机主机到公共代码进行的。 同样，请务必将 <your_organization> 替换为你自己的组织。

1. 将另一个“Kotlin 文件/类”添加到名为 NotificationActionService 的“服务”文件夹中，然后添加以下代码 。

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    class NotificationActionService {
        companion object {
            const val NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
            const val TRIGGER_ACTION = "triggerAction"
            const val GET_LAUNCH_ACTION = "getLaunchAction"
        }

        private var notificationActionChannel : MethodChannel
        var launchAction : String? = null

        constructor(flutterEngine: FlutterEngine) {
            notificationActionChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationActionService.NOTIFICATION_ACTION_CHANNEL)
            notificationActionChannel.setMethodCallHandler { call, result -> handleNotificationActionCall(call, result) }
        }

        fun triggerAction(action: String) {
            notificationActionChannel.invokeMethod(NotificationActionService.TRIGGER_ACTION, action)
        }

        private fun handleNotificationActionCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                NotificationActionService.GET_LAUNCH_ACTION -> {
                    result.success(launchAction)
                }
                else -> {
                    result.notImplemented()
                }
            }
        }
    }
    ```

    > [!NOTE]
    > 此类为 `com.<your_organization>.pushdemo/notificationaction` 通道实现特定于平台的对应项。 这是在“NotificationActionService.dart”中的应用的 Flutter 部分中定义的。 在这种情况下，可以进行双向调用。 请务必将 <your_organization> 替换为你自己的组织。

1. 将新的“Kotlin 文件/类”添加到名为 PushNotificationsFirebaseMessagingService 的“com.<your_organization>.pushdemo”包中，然后使用以下代码实现。

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.os.Handler
    import android.os.Looper
    import com.google.firebase.messaging.FirebaseMessagingService
    import com.google.firebase.messaging.RemoteMessage
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService

    class PushNotificationsFirebaseMessagingService : FirebaseMessagingService() {

        companion object {
            var token : String? = null
            var notificationRegistrationService : NotificationRegistrationService? = null
            var notificationActionService : NotificationActionService? = null
        }

        override fun onNewToken(token: String) {
            PushNotificationsFirebaseMessagingService.token = token
            notificationRegistrationService?.refreshRegistration()
        }

        override fun onMessageReceived(message: RemoteMessage) {
            message.data.let {
                Handler(Looper.getMainLooper()).post {
                    notificationActionService?.triggerAction(it.getOrDefault("action", null))
                }
            }
        }
    }
    ```

    > [!NOTE]
    > 此类负责处理应用在前台运行时的通知。 如果在“onMessageReceived”中接收到的通知有效负载中包含一个操作，则它将有条件地调用“NotificationActionService”上的“triggerAction”  。 当“Firebase”令牌通过替代“onNewToken”函数重新生成时，还将在“NotificationRegistrationService”上调用“refreshRegistration”   。
    >
    > 同样，请务必将 <your_organization> 替换为你自己的组织。

1. 在 AndroidManifest.xml（“app” > “src” > “main”）中，使用 `com.google.firebase.MESSAGING_EVENT` 意向筛选器将“PushNotificationsFirebaseMessagingService”添加到“application”元素的底部     。

    ```xml
    <manifest>
        <application>
            <!-- EXISTING MANIFEST CONTENT -->
             <service
                android:name="com.<your_organization>.pushdemo.PushNotificationsFirebaseMessagingService"
                android:exported="false">
                <intent-filter>
                    <action android:name="com.google.firebase.MESSAGING_EVENT" />
                </intent-filter>
            </service>
        </application>
    </manifest>
    ```

1. 回到“DeviceInstallationService”中，确保以下导入位于文件顶部。

    ```kotlin
    package com.<your_organization>.pushdemo
    import com.<your_organization>.pushdemo.services.PushNotificationsFirebaseMessagingService
    ```

    > [!NOTE]
    > 将“<your_organization>”替换为你自己组织的值。

1. 更新占位符文本 Placeholder_Get_Value_From_FirebaseMessagingService_Implementation 以从“PushNotificationFirebaseMessagingService”获取令牌值。

    ```kotlin
    fun getDeviceToken() : String {
        if(!playServicesAvailable) {
            throw Exception(getPlayServicesError())
        }

        // Get token from the PushNotificationsFirebaseMessagingService.token field.
        val token = PushNotificationsFirebaseMessagingService.token

        if (token.isNullOrBlank()) {
            throw Exception("Unable to resolve token for FCM.")
        }

        return token
    }
    ```

1. 在“MainActivity”中，确保以下导入位于文件顶部。

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.content.Intent
    import android.os.Bundle
    import com.google.android.gms.tasks.OnCompleteListener
    import com.google.firebase.iid.FirebaseInstanceId
    import com.<your_organization>.pushdemo.services.DeviceInstallationService
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService
    import io.flutter.embedding.android.FlutterActivity
    ```

    > [!NOTE]
    > 将“<your_organization>”替换为你自己组织的值。

1. 添加一个变量以存储对“DeviceInstallationService”的引用。

    ```kotlin
    private lateinit var deviceInstallationService: DeviceInstallationService
    ```

1. 添加一个名为 processNotificationActions 的函数，以检查“意向”是否具有一个名为“操作”的额外值  。 如果在应用启动期间正在处理该操作，则有条件地触发该操作或将其存储以供以后使用。

    ```kotlin
     private fun processNotificationActions(intent: Intent, launchAction: Boolean = false) {
        if (intent.hasExtra("action")) {
            var action = intent.getStringExtra("action");

            if (action.isNotEmpty()) {
                if (launchAction) {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.launchAction = action
                }
                else {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.triggerAction(action)
                }
            }
        }
    }
    ```

1. 替代 onNewIntent 函数以调用 processNotificationActions 。

    ```kotlin
    override fun onNewIntent(intent: Intent) {
        super.onNewIntent(intent)
        processNotificationActions(intent)
    }
    ```

    > [!NOTE]
    > 由于“MainActivity”的 LaunchMode 设置为 SingleTop，因此会通过 onNewIntent 函数（而不是 onCreate 函数）向现有“活动”实例发送一个“意向”，因此必须同时在 onCreate 和 onNewIntent 函数中处理传入意向          。

1. 替代“onCreate”函数，将“deviceInstallationService”设置为“DeviceInstallationService”的新实例  。

    ```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        flutterEngine?.let {
            deviceInstallationService = DeviceInstallationService(context, it)
        }
    }
    ```

1. 在“PushNotificationFirebaseMessagingServices”上设置“notificationActionService”和“notificationRegistrationService”属性  。

    ```kotlin
    flutterEngine?.let {
      deviceInstallationService = DeviceInstallationService(context, it)
      PushNotificationsFirebaseMessagingService.notificationActionService = NotificationActionService(it)
      PushNotificationsFirebaseMessagingService.notificationRegistrationService = NotificationRegistrationService(it)
    }
    ```

1. 在同一函数中，有条件地调用 FirebaseInstanceId.getInstance().instanceId。 在调用“refreshRegistration”之前，实现“OnCompleteListener”以在“PushNotificationFirebaseMessagingService”上设置令牌值   。

    ```kotlin
    if(deviceInstallationService?.playServicesAvailable) {
        FirebaseInstanceId.getInstance().instanceId
            .addOnCompleteListener(OnCompleteListener { task ->
                if (!task.isSuccessful)
                    return@OnCompleteListener

                PushNotificationsFirebaseMessagingService.token = task.result?.token
                PushNotificationsFirebaseMessagingService.notificationRegistrationService?.refreshRegistration()
            })
    }
    ```

1. 仍在“onCreate”中，在函数末尾调用“processNotificationActions” 。 将 true 用于 launchAction 参数，以指示在应用启动期间正在处理此操作 。

    ```kotlin
    processNotificationActions(this.intent, true)
    ```

> [!NOTE]
> 必须在每次运行应用时重新注册该应用，并从调试会话中停止该应用以继续接收推送通知。
