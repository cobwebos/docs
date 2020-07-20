---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 9a8b76c0887cff498edd90aedf5cc6eb674fc7e5
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156265"
---
### <a name="configure-the-runner-target-and-infoplist"></a>配置 Runner 目标和 Info.plist

1. 在“Visual Studio Code”中，按住 Ctrl  +  单击“ios”文件夹，然后选择“在 Xcode 中打开”    。

1. 在“Xcode”中，单击“运行程序”（顶部而不是文件夹中的“xcodeproj”），然后依次选择“运行程序”目标、“签名和功能”    。 选择了“全部”生成配置后，选择“团队”的开发人员帐户 。 请确保选中“自动管理签名”选项，这样就会自动选中签名证书和预配配置文件。

    > [!NOTE]
    > 如果未显示新的“预配配置文件”值，请尝试通过依次选择“Xcode” > “首选项” > “帐户”来刷新“签名标识”的配置文件，然后选择“下载手动配置文件”按钮来下载配置文件   。

1. 单击“+ 功能”，然后搜索“推送通知” 。 双击“推送通知”以添加此功能 。

1. 打开“信息列表”并将“最低系统版本”设置为“13.0”  。

    > [!NOTE]
    > 在本教程中，仅支持运行 iOS 13.0 及更高版本的设备，但你可以将其扩展为支持运行较早版本的设备。

1. 打开“Runner.entitlements”并确保“APS 环境”设置设置为“开发”  。

### <a name="handle-push-notifications-for-ios"></a>处理 iOS 的推送通知

1. 按住 Ctrl  +  单击“Runner”文件夹（位于 Runner 项目内），然后选择“新建组”并使用“Services”作为名称   。

1. 按住 Ctrl  +  单击“Services”文件夹，然后选择“新建文件...”   。然后，选择“Swift 文件”，再单击“下一步” 。 指定“DeviceInstallationService”作为名称，然后单击“创建” 。

1. 使用以下代码实现“DeviceInstallationService.swift”。

    ```swift
    import Foundation

    class DeviceInstallationService {

        enum DeviceRegistrationError: Error {
            case notificationSupport(message: String)
        }

        var token : Data? = nil

        let DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
        let GET_DEVICE_ID = "getDeviceId"
        let GET_DEVICE_TOKEN = "getDeviceToken"
        let GET_DEVICE_PLATFORM = "getDevicePlatform"

        private let deviceInstallationChannel : FlutterMethodChannel

        var notificationsSupported : Bool {
            get {
                if #available(iOS 13.0, *) {
                    return true
                }
                else {
                    return false
                }
            }
        }

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
            deviceInstallationChannel = FlutterMethodChannel(name: DEVICE_INSTALLATION_CHANNEL, binaryMessenger: binaryMessenger)
            deviceInstallationChannel.setMethodCallHandler(handleDeviceInstallationCall)
        }

        func getDeviceId() -> String {
            return UIDevice.current.identifierForVendor!.description
        }

        func getDeviceToken() throws -> String {
            if(!notificationsSupported) {
                let notificationSupportError = getNotificationsSupportError()
                throw DeviceRegistrationError.notificationSupport(message: notificationSupportError)
            }

            if (token == nil) {
                throw DeviceRegistrationError.notificationSupport(message: "Unable to resolve token for APNS.")
            }

            return token!.reduce("", {$0 + String(format: "%02X", $1)})
        }

        func getDevicePlatform() -> String {
            return "apns"
        }

        private func handleDeviceInstallationCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_DEVICE_ID:
                result(getDeviceId())
            case GET_DEVICE_TOKEN:
                getDeviceToken(result: result)
            case GET_DEVICE_PLATFORM:
                result(getDevicePlatform())
            default:
                result(FlutterMethodNotImplemented)
            }
        }

        private func getDeviceToken(result: @escaping FlutterResult) {
            do {
                let token = try getDeviceToken()
                result(token)
            }
            catch let error {
                result(FlutterError(code: "UNAVAILABLE", message: error.localizedDescription, details: nil))
            }
        }

        private func getNotificationsSupportError() -> String {

            if (!notificationsSupported) {
                return "This app only supports notifications on iOS 13.0 and above. You are running \(UIDevice.current.systemVersion)"
            }

            return "An error occurred preventing the use of push notifications."
        }
    }
    ```

    > [!NOTE]
    > 此类为 `com.<your_organization>.pushdemo/deviceinstallation` 通道实现特定于平台的对应项。 这是在“DeviceInstallationService.dart”中的应用的 Flutter 部分中定义的。 在这种情况下，调用是从公共代码到本机主机进行的。 请务必将 <your_organization> 替换为你自己的组织。
    >
    > 此类提供一个唯一 ID（使用 [UIDevice.identifierForVendor](https://developer.apple.com/documentation/uikit/uidevice/1620059-identifierforvendor) 值）作为通知中心注册有效负载的一部分。

1. 将另一个“Swift 文件”添加到名为 NotificationRegistrationService 的“Services”文件夹中，然后添加以下代码 。

    ```swift
    import Foundation

    class NotificationRegistrationService {

        let NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
        let REFRESH_REGISTRATION = "refreshRegistration"

        private let notificationRegistrationChannel : FlutterMethodChannel

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
           notificationRegistrationChannel = FlutterMethodChannel(name: NOTIFICATION_REGISTRATION_CHANNEL, binaryMessenger: binaryMessenger)
        }

        func refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, arguments: nil)
        }
    }
    ```

    > [!NOTE]
    > 此类为 `com.<your_organization>.pushdemo/notificationregistration` 通道实现特定于平台的对应项。 这是在“NotificationRegistrationService.dart”中的应用的 Flutter 部分中定义的。 在这种情况下，调用是从本机主机到公共代码进行的。 同样，请务必将 <your_organization> 替换为你自己的组织。

1. 将另一个“Swift 文件”添加到名为 NotificationActionService 的“Services”文件夹中，然后添加以下代码 。

    ```swift
    import Foundation

    class NotificationActionService {

        let NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
        let TRIGGER_ACTION = "triggerAction"
        let GET_LAUNCH_ACTION = "getLaunchAction"

        private let notificationActionChannel: FlutterMethodChannel

        var launchAction: String? = nil

        init(withBinaryMessenger binaryMessenger: FlutterBinaryMessenger) {
            notificationActionChannel = FlutterMethodChannel(name: NOTIFICATION_ACTION_CHANNEL, binaryMessenger: binaryMessenger)
            notificationActionChannel.setMethodCallHandler(handleNotificationActionCall)
        }

        func triggerAction(action: String) {
           notificationActionChannel.invokeMethod(TRIGGER_ACTION, arguments: action)
        }

        private func handleNotificationActionCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_LAUNCH_ACTION:
                result(launchAction)
            default:
                result(FlutterMethodNotImplemented)
            }
        }
    }
    ```

    > [!NOTE]
    > 此类为 `com.<your_organization>.pushdemo/notificationaction` 通道实现特定于平台的对应项。 这是在“NotificationActionService.dart”中的应用的 Flutter 部分中定义的。 在这种情况下，可以进行双向调用。 请务必将 <your_organization> 替换为你自己的组织。

1. 在“AppDelegate.swift”中，添加变量以存储对先前创建的服务的引用。

    ```swift
    var deviceInstallationService : DeviceInstallationService?
    var notificationRegistrationService : NotificationRegistrationService?
    var notificationActionService : NotificationActionService?
    ```

1. 添加一个名为"processNotificationActions"的函数来处理通知数据。 如果在应用启动期间正在处理该操作，则有条件地触发该操作或将其存储以供以后使用。

    ```swift
    func processNotificationActions(userInfo: [AnyHashable : Any], launchAction: Bool = false) {
        if let action = userInfo["action"] as? String {
            if (launchAction) {
                notificationActionService?.launchAction = action
            }
            else {
                notificationActionService?.triggerAction(action: action)
            }
        }
    }
    ```

1. 替代用于为“DeviceInstallationService”设置令牌值的“didRegisterForRemoteNotificationsWithDeviceToken”函数  。 然后，在“NotificationRegistrationService”上调用“refreshRegistration” 。

    ```swift
    override func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
      deviceInstallationService?.token = deviceToken
      notificationRegistrationService?.refreshRegistration()
    }
    ```

1. 替代 didReceiveRemoteNotification 函数，该函数将 userInfo 参数传递到 processNotificationActions 函数  。

    ```swift
    override func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) {
        processNotificationActions(userInfo: userInfo)
    }
    ```

1. 替代“didFailToRegisterForRemoteNotificationsWithError”函数来记录错误。

    ```swift
    override func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
        print(error);
    }
    ```

    > [!NOTE]
    > 这就是一个占位符。 对于生产场景，需要实现正确的日志记录和错误处理。

1. 在“didFinishLaunchingWithOptions”中，实例化“deviceInstallationService”、“notificationRegistrationService”和“notificationActionService”变量   。

    ```swift
    let controller : FlutterViewController = window?.rootViewController as! FlutterViewController

    deviceInstallationService = DeviceInstallationService(withBinaryMessenger: controller.binaryMessenger)
    notificationRegistrationService = NotificationRegistrationService(withBinaryMessenger: controller.binaryMessenger)
    notificationActionService = NotificationActionService(withBinaryMessenger: controller.binaryMessenger)
    ```

1. 在同一函数中，有条件地请求授权，并注册远程通知。

    ```swift
    if #available(iOS 13.0, *) {
      UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
          (granted, error) in

          if (granted)
          {
              DispatchQueue.main.async {
                  let pushSettings = UIUserNotificationSettings(types: [.alert, .sound, .badge], categories: nil)
                  application.registerUserNotificationSettings(pushSettings)
                  application.registerForRemoteNotifications()
              }
          }
      }
    }
    ```

1. 如果“launchOptions”包含“remoteNotification”项，请在“didFinishLaunchingWithOptions”函数末尾调用“processNotificationActions”   。 传入生成的 userInfo 对象，并使用 true 作为 launchAction 参数 。 true 值表示在应用启动期间正在处理该操作。

    ```swift
    if let userInfo = launchOptions?[.remoteNotification] as? [AnyHashable : Any] {
        processNotificationActions(userInfo: userInfo, launchAction: true)
    }
    ```
