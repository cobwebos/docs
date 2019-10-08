---
title: 使用 Azure 通知中心将通知推送到 Swift iOS 应用 |Microsoft Docs
description: 了解如何向使用 Azure 通知中心的 Swift iOS 应用推送通知。
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 8dae5bcc082ba5dd0953e3e97f609e4031547a35
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030653"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-that-use-the-notification-hubs-rest-api"></a>教程：向使用通知中心 REST API 的 Swift iOS 应用推送通知

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

本教程介绍如何使用 Azure 通知中心将通知推送到基于 Swift 的 iOS 应用程序，方法是使用[REST API](/rest/api/notificationhubs/)。 还会创建一个空白 iOS 应用，它使用[Apple Push Notification 服务（APNs）](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)接收推送通知。

本教程引导你完成以下步骤：

> [!div class="checklist"]
> * 生成证书签名请求文件。
> * 请求你的应用推送通知。
> * 为应用程序创建配置文件。
> * 创建通知中心。
> * 使用 APNs 信息配置通知中心。
> * 将 iOS 应用连接到通知中心。
> * 测试解决方案。

## <a name="prerequisites"></a>先决条件

若要按照文中内容操作，需要：

- 如果你不熟悉该服务，请参阅 [Azure 通知中心概述](notification-hubs-push-notification-overview.md)。
- 了解[注册和安装](notification-hubs-push-notification-registration-management.md)。
- 一个有效的 [Apple 开发人员帐户](https://developer.apple.com)。
- 一台运行 Xcode 的 Mac 计算机，以及安装在密钥链中的有效开发人员证书。
- 可以运行且可用于调试的 iPhone 实物设备，因为你无法使用模拟器来测试推送通知。
- 该 iPhone 实物设备已在 [Apple 门户](https://developer.apple.com)中注册且与你的证书相关联。
- 一个[Azure 订阅](https://portal.azure.com)，可在其中创建和管理资源。

即使过去没有 iOS 开发方面的经验，应该也可以遵循本文中的步骤创建此基本原则示例。 但是，熟悉以下概念会有所帮助：

- 使用 Xcode 和 Swift 生成 iOS 应用。
- 配置 iOS 的 [Azure 通知中心](notification-hubs-ios-apple-push-notification-apns-get-started.md)。
- [Apple 开发人员门户](https://developer.apple.com)和[Azure 门户](https://portal.azure.com)。

> [!NOTE]
> 通知中心将配置为仅使用“沙盒”身份验证模式。 不应将此身份验证模式用于生产工作负荷。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>将 iOS 应用连接到通知中心

在本部分，你将生成要连接到通知中心的 iOS 应用。  

### <a name="create-an-ios-project"></a>创建 iOS 项目

1. 在 Xcode 中，创建新的 iOS 项目，并选择“单视图应用程序”模板。

1. 设置新项目的选项时：

   1. 使用在 Apple 开发人员门户中设置“捆绑标识符”时所用的“产品名称”(PushDemo) 和“组织标识符”(`com.<organization>`)。

   1. 选择为其设置了“应用 ID”的“团队”。

   1. 将“语言”设置为“Swift”。

   1. 选择“**下一步**”。

1. 创建名为 **SupportingFiles** 的新文件夹。

1. 在 **SupportingFiles** 文件夹中创建名为 **devsettings.plist** 的新 p-list 文件。 确保将此文件夹添加到 **gitignore** 文件，以便在使用 Git 存储库时不会提交该文件。 在生产应用中，你可能会按条件将这些机密设置为自动化生成过程的一部分。 本演练不使用此类设置。

1. 更新 **devsettings.plist** 以包含以下配置条目（使用预配的通知中心内你自己的值）：

   | Key                            | 类型                     | ReplTest1                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | \<hubKey>                  |
   | notificationHubKeyName         | String                   | \<hubKeyName>              |
   | notificationHubName            | String                   | \<hubName>                 |
   | notificationHubNamespace       | String                   | \<hubNamespace>            |

   可以在 Azure 门户中导航到“通知中心”资源来找到所需的值。 具体而言，可以在“概述”页中“概要”摘要的右上角找到 **notificationHubName** 和 **notificationHubNamespace** 值。

   ![通知中心“概要”摘要](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   还可以通过导航到“访问策略”并选择相应的**访问策略**（例如 `DefaultFullSharedAccessSignature`）找到 **notificationHubKeyName** 和 **notificationHubKey** 值。 然后在“主连接字符串”中，复制 `notificationHubKeyName` 的带有 `SharedAccessKeyName=` 前缀的值，以及 `notificationHubKey` 的带有 `SharedAccessKey=` 前缀的值。

   连接字符串应采用以下格式：

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   为方便起见，请指定 `DefaultFullSharedAccessSignature`，以便可以使用令牌来发送通知。 在实践中，如果你只想要接收通知，则 `DefaultListenSharedAccessSignature` 是更好的选择。

1. 在“项目导航器”下选择“项目名称”，然后选择“常规”选项卡。

1. 找到“标识”并设置“捆绑标识符”值，使其与在前面步骤中用于“应用 ID”的值 `com.<organization>.PushDemo` 相匹配。

1. 找到“签名”，然后为“Apple 开发人员帐户”选择适当的“团队”。 “团队”值应与前面在其下创建了证书和配置文件的团队相匹配。

1. Xcode 应会根据“捆绑标识符”自动下拉相应的“预配配置文件”值。 如果未显示新的“预配配置文件”值，请尝试选择“Xcode” > “首选项” > “帐户” > “查看详细信息”来刷新“签名标识”的配置文件。 选择“签名标识”，然后选择右下角的“刷新”按钮下载配置文件。

1. 选择“功能”选项卡，并确保已启用“推送通知”。

1. 打开 **AppDelegate.swift** 文件以实现 **UNUserNotificationCenterDelegate** 协议，并将以下代码添加到类的顶部：

    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {

        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")

        ...
    }
    ```

    稍后要使用这些成员。 具体而言，在注册过程中将使用 **tags** 和 **genericTemplate** 成员。 有关标记的详细信息，请参阅[注册的标记](notification-hubs-tags-segment-push-message.md)和[模板注册](notification-hubs-templates-cross-platform-push-messages.md)。

1. 在同一文件中的 **didFinishLaunchingWithOptions** 函数中添加以下代码：

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }

    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in

            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    此代码从 **devsettings.plist** 中检索设置值，将 **AppDelegate** 类设置为 **UNUserNotificationCenter** 委托，请求为推送通知授权，然后调用 **registerForRemoteNotifications**。

    为简单起见，该代码仅支持 *iOS 10 和更高版本*。 可以按条件使用相应的 API 以及平时采用的方法，添加对旧 OS 版本的支持。

1. 在同一文件中添加以下函数：

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        self.window?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    该代码使用 **installationId** 和 **pushChannel** 值向通知中心注册。 在本例中，你将使用 **UIDevice.current.identifierForVendor** 提供唯一的值用于标识设备，然后设置 **deviceToken** 的格式，以提供所需的 **pushChannel** 值。 **showAlert** 函数只会显示一些消息文本供演示。

1. 仍在 **AppDelegate.swift** 中，将 **willPresent** 和 **didReceive** 函数添加到 **UNUserNotificationCenterDelegate**。 在分别收到应用在前台和后台运行的通知时，这些函数会显示警报。

    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```

1. 将 print 语句添加到 **didRegisterForRemoteNotificationsWithDeviceToken** 函数的底部，以验证是否正在为 **installationId** 和 **pushChannel** 赋值。

1. 为稍后要添加到项目的基础组件创建 **Models**、**Services** 和 **Utilities** 文件夹。

1. 检查该项目是否可在物理设备上生成和运行。 无法使用模拟器测试推送通知。

### <a name="create-models"></a>创建模型

此步骤将创建一组模型来表示[通知中心 REST API](/rest/api/notificationhubs/) 有效负载并存储所需的共享访问签名 (SAS) 令牌数据。

1. 将一个名为 **PushTemplate.swift** 的新 Swift 文件添加到 **Models** 文件夹。 此模型提供一个结构用于表示 **DeviceInstallation** 有效负载包含的单个模板的**正文**。

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. 将一个名为 **DeviceInstallation.swift** 的新 Swift 文件添加到 **Models** 文件夹。 此文件定义一个结构，该结构表示用于创建或更新**设备安装**的有效负载。 将以下代码添加到该文件：

    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>

        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

1. 将一个名为 **TokenData.swift** 的新 Swift 文件添加到 **Models** 文件夹。 此模型用于存储 SAS 令牌及其过期时间。

    ```swift
    import Foundation

    struct TokenData {

        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>生成 SAS 令牌

通知中心使用与 Azure 服务总线相同的安全基础结构。 若要调用 REST API，需要[以编程方式生成](/rest/api/eventhub/generate-sas-token)一个可在请求的 **Authorization** 标头中使用的 SAS 令牌。  

生成的令牌采用以下格式：

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

该过程本身涉及到相同的六个主要步骤：  

1. 计算 [UNIX 纪元时间](https://en.wikipedia.org/wiki/Unix_time)格式（自 1970 年 1 月 1 日协调世界时午夜至今的秒数）的过期时间。
1. 设置 **ResourceUrl**（表示尝试访问的资源）的格式，使其经过百分比编码且采用小写。 **ResourceUrl** 的格式为 `'https://<namespace>.servicebus.windows.net/<hubName>'`。
1. 准备好格式为 `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'` 的 **StringToSign**。
1. 使用 **StringToSign** 值的 HMAC-SHA256 哈希计算**签名**并对其进行 Base64 编码。 遵循相应的**授权规则**将哈希值与**连接字符串**的 **Key** 部分结合使用。
1. 设置已经过 Base64 编码的**签名**的格式，使其经过百分比编码。
1. 使用 **UrlEncodedSignature**、**ExpiryEpoch**、**KeyName** 和 **UrlEncodedResourceUrl** 值以预期的格式构造令牌。

有关共享访问签名的更全面概述以及 Azure 服务总线和通知中心如何使用共享访问签名的更全面概述，请参阅 [Azure 服务总线文档](../service-bus-messaging/service-bus-sas.md)。

对于此 Swift 示例，你将借助 Apple 的开源 **CommonCrypto** 库来对签名进行哈希处理。 由于它是一个 C 库，因此无法在 Swift 中直接访问它。 可以通过一个桥接标头来使用该库。

若要添加并配置桥接标头：

1. 在 Xcode 中，选择 " **File** > **New** > **File** > **头文件**。 将标头文件命名为 **BridgingHeader.h**。

1. 编辑该文件以导入 **CommonHMAC.h**：

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. 更新目标的“生成设置”以引用桥接标头：

   1. 打开 "**生成设置**" 选项卡，向下滚动到**Swift 编译器**部分。

   1. 确保 "**安装目标-C 兼容性标头**" 选项设置为 **"是"** 。

   1. 在 "**目标-C 桥接标头**" 选项中输入文件路径 `'<ProjectName>/BridgingHeader.h'`。 这是桥接标头的文件路径。

   如果找不到这些选项，请确保已选择“所有”视图（而不是“基本”或“自定义”）。

   有许多第三方开源包装器库可以略微简化 **CommonCrypto** 的使用难度。 但是，本文不会介绍此类的库。

1. 在 **Utilities** 文件夹中添加名为 **TokenUtility.swift** 的新 Swift 文件，并添加以下代码：

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>

        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)

            return tokenData
        }

        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)

            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()

            return result as Data
        }

        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)

            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }

            for i in 0..<length {
                let currentChar = sourceUtf8![i]

                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }

            return encodedString
        }
    }
   ```

   此实用工具封装负责生成 SAS 令牌的逻辑。

   如前所述，**getSasToken** 函数协调准备令牌所要执行的高级步骤。 在本教程稍后的步骤中，安装服务将调用该函数。

   **getSasToken** 函数调用另外两个函数：用于计算签名的 **sha256HMac**，以及用于对关联的 URL 字符串进行编码的 **urlEncodedString**。 之所以需要 **urlEncodedString** 函数，是因为使用内置的 **addingPercentEncoding** 函数无法实现所需的输出。

   [Azure 存储 iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) 很好地示范了如何在 Objective-C 中实现这些操作。 在 [Azure 服务总线文档](../service-bus-messaging/service-bus-sas.md)中可以找到有关 Azure 服务总线 SAS 令牌的更多信息。

### <a name="verify-the-sas-token"></a>验证 SAS 令牌

在客户端中实现安装服务之前，请使用所选的 HTTP 实用工具检查我们的应用是否在正确生成 SAS 令牌。 本教程所选的工具是 **Postman**。

利用放在适当位置的 print 语句或断点，记下应用生成的 **installationId** 和 **token** 值。

遵循以下步骤调用安装 API：

1. 在“Postman”中打开一个新的选项卡。

1. 将请求设置为 **GET**，并指定以下地址：

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. 按如下所示配置请求标头：

   | Key           | ReplTest1            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Authorization | \<sasToken>       |
   | x-ms-version  | 2015-01          |

1. 选择位于“保存”按钮右上方的“代码”按钮。 请求应类似于以下示例：

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. 选择**发送**按钮。

指定的 **installationId** 暂时不存在注册。 验证时应会生成“404 未找到”响应，而不是“401 未授权”响应。 此结果确认已接受 SAS 令牌。

### <a name="implement-the-installation-service-class"></a>实现安装服务类

接下来，围绕[安装 REST API](/rest/api/notificationhubs/create-overwrite-installation) 实现基本包装器。  

在 **Services** 文件夹下添加名为 **NotificationRegistrationService.swift** 的新 Swift 文件，然后将以下代码添加到此文件：

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil

    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }

    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {

        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)

        if let tags = tags {
            deviceInstallation.tags = tags
        }

        if let templates = templates {
            deviceInstallation.templates = templates
        }

        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"

            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"

            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }

            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)

            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }

    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }

    private func getSasToken() -> String {
        if (tokenData == nil ||
            Date(timeIntervalSince1970: Double((tokenData?.expiration)!)) < Date(timeIntervalSinceNow: -(5 * 60))) {
            self.tokenData = TokenUtility.getSasToken(forResourceUrl: getBaseAddress(), withKeyName: self.keyName, andKey: self.key)
        }

        return (tokenData?.token)!
    }

    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```

在初始化过程中将提供必要的详细信息。 可以选择性地将标记和模板传入 **register** 函数，以构成**设备安装** JSON 有效负载。  

**register** 函数将调用其他专用函数来准备证书请求。 收到响应后，将调用 completion 来指示是否注册是否成功。  

请求终结点由 **getBaseAddress** 函数构建。 该构造使用初始化期间提供的通知中心参数 *namespace* 和 *name*。  

**getSasToken** 函数检查当前存储的令牌是否有效。 如果令牌无效，该函数会调用 **TokenUtility** 来生成并存储新的令牌，然后返回一个值。

最后，**encodeToJson** 将相应的模型对象转换为 JSON，以用作请求正文的一部分。

### <a name="invoke-the-notification-hubs-rest-api"></a>调用通知中心 REST API

最后一步是将 **AppDelegate** 更新为使用 **NotificationRegistrationService** 向**通知中心**注册。

1. 打开 **AppDelegate.swift**，添加一个类级变量用于存储对 **NotificationRegistrationService** 的引用：

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

1. 在同一文件中，将 **didRegisterForRemoteNotificationsWithDeviceToken** 函数更新为使用必要参数初始化 **NotificationRegistrationService**，然后调用 **register** 函数。

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)

        // Call register, passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    为简单起见，本文将使用几个 print 语句，以更新输出窗口并在其中提供 **register** 操作的结果。

1. 现在请在物理设备上生成并运行应用。 应会在输出窗口中看到“Registered”。

## <a name="test-the-solution"></a>测试解决方案

在此阶段，我们的应用已注册到**通知中心**，并可以接收推送通知。 在 Xcode 中，停止调试器并关闭应用（如果它当前正在运行）。 接下来，检查“设备安装”详细信息是否符合预期，以及应用现在是否可以接收推送通知。  

### <a name="verify-the-device-installation"></a>验证设备安装

现在，可以发出前面在使用 **Postman** [验证 SAS 令牌](#verify-the-sas-token)时所用的相同请求。 假设 SAS 令牌尚未过期，则响应现在应该包含提供的安装详细信息，例如模板和标记。

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

### <a name="send-a-test-notification-azure-portal"></a>发送测试通知（Azure 门户）

测试现在是否可以接收通知的最快方法是在“Azure 门户”中浏览到通知中心：

1. 在 Azure 门户中，浏览到通知中心上的“概述”选项卡。

1. 选择门户窗口左上方“概要”摘要上面的“测试发送”：

    ![通知中心 -“概要”摘要 -“测试发送”按钮](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. 从“平台”列表中选择“自定义模板”。

1. 在“发送到标记表达式”中输入 **12345**。 前面已在安装中指定此标记。

1. （可选）编辑 JSON 有效负载中的**消息**：

    ![通知中心 - 测试发送](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. 选择“发送”。 门户应会指示是否已成功将通知发送到设备：

    ![通知中心 - 测试发送结果](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    假设应用不是在前台运行，设备上的“通知中心”内也应会显示一条通知。 点击该通知会打开应用并显示警报。

    ![已收到通知的示例](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>发送测试通知（邮件运营商）

您可以通过使用**Postman**发送[REST API](/rest/api/notificationhubs/)通知，这可能是一种更方便的测试方法。

1. 在“Postman”中打开一个新的选项卡。

1. 将请求设置为 **POST** 并输入以下地址：

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. 按如下所示配置请求标头：

   | Key                            | ReplTest1                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | Authorization                  | \<sasToken>                     |
   | ServiceBusNotification-Format  | template                       |
   | Tags                           | "12345"                        |

1. 将请求**正文**配置为使用包含以下 JSON 有效负载的“RAW - JSON (application.json)”：

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. 选择窗口右上方“保存”按钮下面的“代码”按钮。 请求应类似于以下示例：

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

1. 选择**发送**按钮。

随后你应会收到一个成功状态代码，而客户端设备上会收到通知。

## <a name="next-steps"></a>后续步骤
现在，已通过[REST API](/rest/api/notificationhubs/)将基本的 iOS Swift 应用连接到通知中心，并可以发送和接收通知。 有关详细信息，请参阅以下文章：

- [Azure 通知中心概述](notification-hubs-push-notification-overview.md)
- [通知中心 REST API](/rest/api/notificationhubs/)
- [用于后端操作的通知中心 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [GitHub 上的通知中心 SDK](https://github.com/Azure/azure-notificationhubs)
- [使用应用程序后端注册](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [注册管理](notification-hubs-push-notification-registration-management.md)
- [使用标记](notification-hubs-tags-segment-push-message.md) 
- [使用自定义模板](notification-hubs-templates-cross-platform-push-messages.md)
- [使用共享访问签名进行服务总线访问控制](../service-bus-messaging/service-bus-sas.md)
- [以编程方式生成 SAS 令牌](/rest/api/eventhub/generate-sas-token)
- [Apple 安全性：通用加密](https://developer.apple.com/security/)
- [UNIX 纪元时间](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
