---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 2f8cd5e554980225850780cc474106c3dc69288a
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156276"
---
### <a name="create-the-flutter-solution"></a>创建 Flutter 解决方案

1. 打开“Visual Studio Code”的新实例。

1. 打开“命令面板”（“Shift” + “Command” + “P”）   。

1. 选择“Flutter: 新建项目”命令，然后按“输入”。

1. 为“项目名称”输入“push_demo”，然后选择“项目位置”。

1. 系统提示时，选择“获取包”。

1. 同时按住“Control” + “单击”“kotlin”文件夹（位于“app” > “scr” > “main”下），然后选择“在查找器中显示”      。 然后将子文件（在“kotlin”文件夹下）分别重命名为 ```com```、```<your_organization>``` 和 ```pushdemo```。

    > [!NOTE]
    > 使用“Visual Studio Code”模板时，这些文件夹默认为“com”、“example”、“<project_name>”   。 假设“mobcat”用于“组织”，则文件夹结构应显示为 ：
    >
    > - Kotlin
    >     - com
    >         - mobcat
    >             - pushdemo

1. 返回“Visual Studio Code”，将“android” > “app” > “build.gradle”中的 applicationId 值更新为 `com.<your_organization>.pushdemo`    。

    > [!NOTE]
    > 应为 <your_organization> 占位符使用自己的组织名称。 例如，使用“mobcat”作为组织将导致“包名称”的值为“com.mobcat.pushdemo” 。

1. 更新“AndroidManifest.xml”文件中的“包”的属性，分别位于“scr” > “debug”、“scr” > “main”和“scr” > “profile”下       。 确保值与你在上一步中使用的“applicationId”相匹配。

    ```xml
    <manifest
        xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.<your_organization>.pushdemo>">
        ...
    </manifest>
    ```

1. 将“AndroidManifest.xml”文件中“scr” > “main”下的 ```android:label``` 属性更新为“PushDemo”  。 然后，直接在 ```android:label``` 下添加 ```android:allowBackup``` 属性，将其值设置为“false”。

    ```xml
    <application
        android:name="io.flutter.app.FlutterApplication"
        android:label="PushDemo"
        android:allowBackup="false"
        android:icon="@mipmap/ic_launcher">
        ...
    </application>
    ```

1. 打开应用级别的“build.gradle”文件（“android” > “app” > “build.gradle”），然后更新“compileSdkVersion”（从“android”部分）以使用 API“29”    。 然后，将“minSdkVersion”和“targetSdkVersion”的值（从“defaultConfig”部分）分别更新为“26”和“29”   。

    > [!NOTE]
    > 在本教程中，仅支持运行 API 级别 26 及更高级别的设备，但可以将其扩展为支持运行较早版本的设备。

1. 按住“Control” + 单击“ios”文件夹，然后选择“在 Xcode 中打开”   。

1. 在“Xcode”中，单击“运行器”（是顶部的“xcodeproj”，而不是文件夹）  。 然后，选择“运行器”目标，然后选择“常规”选项卡 。选择“全部”生成配置后，将“捆绑包标识符”更新为 `com.<your_organization>.PushDemo` 。

    > [!NOTE]
    > 应为 <your_organization> 占位符使用自己的组织名称。 例如，使用“mobcat”作为组织将导致“捆绑包标识符”的值为“com.mobcat.PushDemo” 。

1. 单击“info.plist”然后将“捆绑包名称”值更新为“PushDemo”  

1. 关闭“Xcode”并返回到“Visual Studio Code” 。

1. 返回“Visual Studio Code”，打开“pubspec.yaml”，添加 [http](https://pub.dev/packages/http) 和 [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage)“Dart 包”作为依赖项  。 然后，保存文件并在出现提示时单击“获取包”。

    ```yaml
    dependencies:
      flutter:
        sdk: flutter

      http: ^0.12.1
      flutter_secure_storage: ^3.3.3
    ```

1. 在“终端”中，将目录更改为“ios”文件夹（对于 Flutter 项目） 。 然后，执行 [“pod install”](https://guides.cocoapods.org/using/getting-started.html#installation)命令安装新的 pod（[flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) 包所要求）。

1. 按住“Control” + 单击“lib”文件夹，然后从菜单中选择“新建文件夹”，使用“main_page.dart”作为文件名   。 然后，添加以下代码。

    ```kotlin
    import 'package:flutter/material.dart';

    class MainPage extends StatefulWidget {
      @override
      _MainPageState createState() => _MainPageState();
    }

    class _MainPageState extends State<MainPage> {
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          body: SafeArea(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.stretch,
              children: <Widget>[],
            )
          )
        );
      }
    }
    ```

1. 在“main.dart”中，用以下代码替换模板代码。

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/main_page.dart';

    final navigatorKey = GlobalKey<NavigatorState>();

    void main() => runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey));
    ```

1. 在“终端”中，在每个目标平台上生成并运行该应用，以测试模板化的应用在设备上的运行情况。 确保支持的设备已连接。

    ```bash
    flutter run
    ```

### <a name="implement-the-cross-platform-components"></a>实现跨平台组件

1. 按住“Control” + 单击“lib”文件夹，然后从菜单中选择“新建文件夹”，使用“models”作为“文件夹名称”   。

1. 按住“Control” + 单击“models”文件夹，然后从菜单中选择“新建文件夹”，使用“device_installation.dart”作为文件名   。 然后，添加以下代码。

    ```dart
    class DeviceInstallation {
        final String deviceId;
        final String platform;
        final String token;
        final List<String> tags;

        DeviceInstallation(this.deviceId, this.platform, this.token, this.tags);

        DeviceInstallation.fromJson(Map<String, dynamic> json)
          : deviceId = json['installationId'],
            platform = json['platform'],
            token = json['pushChannel'],
            tags = json['tags'];

        Map<String, dynamic> toJson() =>
        {
          'installationId': deviceId,
          'platform': platform,
          'pushChannel': token,
          'tags': tags,
        };
    }
    ```

1. 将一个新文件添加到名为“push_demo_action.dart”的“models”文件夹中，以定义此示例中支持的操作的枚举。

    ```dart
    enum PushDemoAction {
      actionA,
      actionB,
    }
    ```

1. 通过以下实现，将新文件夹添加到名为“服务”的项目，然后将新文件添加到名为“device_installation_service.dart”的文件夹。

    ```dart
    import 'package:flutter/services.dart';

    class DeviceInstallationService {
      static const deviceInstallation = const MethodChannel('com.<your_organization>.pushdemo/deviceinstallation');
      static const String getDeviceIdChannelMethod = "getDeviceId";
      static const String getDeviceTokenChannelMethod = "getDeviceToken";
      static const String getDevicePlatformChannelMethod = "getDevicePlatform";

      Future<String> getDeviceId() {
        return deviceInstallation.invokeMethod(getDeviceIdChannelMethod);
      }

      Future<String> getDeviceToken() {
        return deviceInstallation.invokeMethod(getDeviceTokenChannelMethod);
      }

      Future<String> getDevicePlatform() {
        return deviceInstallation.invokeMethod(getDevicePlatformChannelMethod);
      }
    }
    ```

    > [!NOTE]
    > 应为 <your_organization> 占位符使用自己的组织名称。 例如，使用“mobcat”作为组织将导致 [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) 名称为“com.mobcat.pushdemo / deviceinstallation”。
    >
    > 此类封装了与底层本机平台一起使用的功能，以获取必需的设备安装详细信息。 [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) 有助于与底层本机平台进行双向异步通信。 在后面的步骤中，将创建该通道的特定于平台的对应项。

1. 通过以下实现，将另一个文件添加到名为“notification_action_service.dart”的文件夹中。

    ```dart
    import 'package:flutter/services.dart';
    import 'dart:async';
    import 'package:push_demo/models/push_demo_action.dart';

    class NotificationActionService {
      static const notificationAction =
          const MethodChannel('com.<your_organization>.pushdemo/notificationaction');
      static const String triggerActionChannelMethod = "triggerAction";
      static const String getLaunchActionChannelMethod = "getLaunchAction";

      final actionMappings = {
        'action_a' : PushDemoAction.actionA,
        'action_b' : PushDemoAction.actionB
      };

      final actionTriggeredController = StreamController.broadcast();

      NotificationActionService() {
        notificationAction
            .setMethodCallHandler(handleNotificationActionCall);
      }

      Stream get actionTriggered => actionTriggeredController.stream;

      Future<void> triggerAction({action: String}) async {

        if (!actionMappings.containsKey(action)) {
          return;
        }

        actionTriggeredController.add(actionMappings[action]);
      }

      Future<void> checkLaunchAction() async {
        final launchAction = await notificationAction.invokeMethod(getLaunchActionChannelMethod) as String;

        if (launchAction != null) {
          triggerAction(action: launchAction);
        }
      }

      Future<void> handleNotificationActionCall(MethodCall call) async {
        switch (call.method) {
          case triggerActionChannelMethod:
            return triggerAction(action: call.arguments as String);
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > 这是一种用于集中处理通知操作的简单机制，以便可以使用强类型枚举以跨平台的方式进行处理。 当通知有效负载中指定了一项操作时，该服务可使底层本机平台触发该操作。 一旦 Flutter 准备好处理它，它还使通用代码能够以可追溯的方式检查在应用程序启动期间是否指定了操作。 例如，通过点击通知中心的通知启动应用时。

1. 通过以下实现，将新文件添加到名为“notification_registration_service.dart”的“services”文件夹中。

    ```dart
    import 'dart:convert';
    import 'package:flutter/services.dart';
    import 'package:http/http.dart' as http;
    import 'package:push_demo/services/device_installation_service.dart';
    import 'package:push_demo/models/device_installation.dart';
    import 'package:flutter_secure_storage/flutter_secure_storage.dart';

    class NotificationRegistrationService {
      static const notificationRegistration =
          const MethodChannel('com.<your_organization>.pushdemo/notificationregistration');

      static const String refreshRegistrationChannelMethod = "refreshRegistration";
      static const String installationsEndpoint = "api/notifications/installations";
      static const String cachedDeviceTokenKey = "cached_device_token";
      static const String cachedTagsKey = "cached_tags";

      final deviceInstallationService = DeviceInstallationService();
      final secureStorage = FlutterSecureStorage();

      String baseApiUrl;
      String apikey;

      NotificationRegistrationService(this.baseApiUrl, this.apikey) {
        notificationRegistration
            .setMethodCallHandler(handleNotificationRegistrationCall);
      }

      String get installationsUrl => "$baseApiUrl$installationsEndpoint";

      Future<void> deregisterDevice() async {
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (cachedToken == null || serializedTags == null) {
          return;
        }

        var deviceId = await deviceInstallationService.getDeviceId();

        if (deviceId.isEmpty) {
          throw "Unable to resolve an ID for the device.";
        }

        var response = await http
            .delete("$installationsUrl/$deviceId", headers: {"apikey": apikey});

        if (response.statusCode != 200) {
          throw "Deregister request failed: ${response.reasonPhrase}";
        }

        await secureStorage.delete(key: cachedDeviceTokenKey);
        await secureStorage.delete(key: cachedTagsKey);
      }

      Future<void> registerDevice(List<String> tags) async {
        try {
          final deviceId = await deviceInstallationService.getDeviceId();
          final platform = await deviceInstallationService.getDevicePlatform();
          final token = await deviceInstallationService.getDeviceToken();

          final deviceInstallation =
              DeviceInstallation(deviceId, platform, token, tags);

          final response = await http.put(installationsUrl,
              body: jsonEncode(deviceInstallation),
              headers: {"apikey": apikey, "Content-Type": "application/json"});

          if (response.statusCode != 200) {
            throw "Register request failed: ${response.reasonPhrase}";
          }

          final serializedTags = jsonEncode(tags);

          await secureStorage.write(key: cachedDeviceTokenKey, value: token);
          await secureStorage.write(key: cachedTagsKey, value: serializedTags);
        } on PlatformException catch (e) {
          throw e.message;
        } catch (e) {
          throw "Unable to register device: $e";
        }
      }

      Future<void> refreshRegistration() async {
        final currentToken = await deviceInstallationService.getDeviceToken();
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (currentToken == null ||
            cachedToken == null ||
            serializedTags == null ||
            currentToken == cachedToken) {
          return;
        }

        final tags = jsonDecode(serializedTags);

        return registerDevice(tags);
      }

      Future<void> handleNotificationRegistrationCall(MethodCall call) async {
        switch (call.method) {
          case refreshRegistrationChannelMethod:
            return refreshRegistration();
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > 此类封装了“DeviceInstallationService”的使用和对后端服务的请求，以执行必需的注册、注销和刷新注册操作。 仅当你选择完成[使用 API 密钥对客户端进行身份验证](#authenticate-clients-using-an-api-key-optional)部分时，才需要 apiKey 参数。

1. 通过以下实现，将新文件添加到名为“config.dart”的“lib”文件夹中。

    ```dart
    class Config {
      static String apiKey = "API_KEY";
      static String backendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
    }
    ```

    > [!NOTE]
    > 这是定义应用机密的简单方法。 将占位符值替换为你自己的值。 应在构建后端服务时记下这些值。 “API 应用 URL”应为 `https://<api_app_name>.azurewebsites.net/`。 仅当你选择完成[使用 API 密钥对客户端进行身份验证](#authenticate-clients-using-an-api-key-optional)部分时，才需要 apiKey 成员。
    >
    > 请确保将其添加到 gitignore 文件，以避免将这些机密提交给源代码管理。

### <a name="implement-the-cross-platform-ui"></a>实现跨平台 UI

1. 在“main_page.dart”中，将“build”函数替换为以下内容 。

    ```dart
    @override
    Widget build(BuildContext context) {
    return Scaffold(
        body: Padding(
          padding: const EdgeInsets.symmetric(horizontal: 20.0, vertical: 40.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.end,
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: <Widget>[
              FlatButton(
                child: Text("Register"),
                onPressed: registerButtonClicked,
              ),
              FlatButton(
                child: Text("Deregister"),
                onPressed: deregisterButtonClicked,
              ),
            ],
          ),
        ),
      );
    }
    ```

1. 将必需的导入添加到“main_page dart”文件的顶部。

    ```dart
    import 'package:push_demo/services/notification_registration_service.dart';
    import 'config.dart';
    ```

1. 向“_MainPageState”类添加一个字段，以存储对“NotificationRegistrationService”的引用 。

    ```dart
    final notificationRegistrationService = NotificationRegistrationService(Config.backendServiceEndpoint, Config.apiKey);
    ```

1. 在“_MainPageState”类中，实现“Register”和“Deregister”按钮“onPressed”事件的事件处理程序   。 调用相应的“Register”/“Deregister”方法，然后显示警报以指示结果 。

    ```dart
    void registerButtonClicked() async {
        try {
          await notificationRegistrationService.registerDevice(List<String>());
          await showAlert(message: "Device registered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      void deregisterButtonClicked() async {
        try {
          await notificationRegistrationService.deregisterDevice();
          await showAlert(message: "Device deregistered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      Future<void> showAlert({ message: String }) async {
        return showDialog<void>(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) {
            return AlertDialog(
              title: Text('PushDemo'),
              content: SingleChildScrollView(
                child: ListBody(
                  children: <Widget>[
                    Text(message),
                  ],
                ),
              ),
              actions: <Widget>[
                FlatButton(
                  child: Text('OK'),
                  onPressed: () {
                    Navigator.of(context).pop();
                  },
                ),
              ],
            );
          },
        );
      }
    ```

1. 在“main.dart”中，确保以下导入位于文件顶部。

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/models/push_demo_action.dart';
    import 'package:push_demo/services/notification_action_service.dart';
    import 'package:push_demo/main_page.dart';
    ```

1. 声明一个变量以存储对 **“NotificationActionService”** 实例的引用并对其进行初始化。

    ```dart
    final notificationActionService = NotificationActionService();
    ```

1. 添加函数以处理触发操作时警报的显示。

    ```dart
    void notificationActionTriggered(PushDemoAction action) {
      showActionAlert(message: "${action.toString().split(".")[1]} action received");
    }

    Future<void> showActionAlert({ message: String }) async {
      return showDialog<void>(
        context: navigatorKey.currentState.overlay.context,
        barrierDismissible: false,
        builder: (BuildContext context) {
          return AlertDialog(
            title: Text('PushDemo'),
            content: SingleChildScrollView(
              child: ListBody(
                children: <Widget>[
                  Text(message),
                ],
              ),
            ),
            actions: <Widget>[
              FlatButton(
                child: Text('OK'),
                onPressed: () {
                  Navigator.of(context).pop();
                },
              ),
            ],
          );
        },
      );
    }
    ```

1. 更新“main”函数以观察“NotificationActionService”“actionTriggered”流，并检查在应用启动期间捕获的任何操作  。

    ```dart
    void main() async {
      runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey,));
      notificationActionService.actionTriggered.listen((event) { notificationActionTriggered(event as PushDemoAction); });
      await notificationActionService.checkLaunchAction();
    }
    ```

    > [!NOTE]
    > 这只是为了演示推送通知操作的接收和传播。 通常情况下，将以无提示方式处理这些操作（例如导航到特定视图或刷新某些数据），而不是在这种情况下显示警报。
