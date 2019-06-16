---
title: 将通知推送到使用 Azure 通知中心的 Swift iOS 应用 |Microsoft Docs
description: 了解如何将推送通知到使用 Azure 通知中心的 Swift iOS 应用程序。
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.openlocfilehash: a4773ddd8114659118e89cfee57e73ddb39ff6b6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116677"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-that-use-the-notification-hubs-rest-api"></a>教程：将通知推送到使用通知中心 REST API 的 Swift iOS 应用

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

在本教程中，你使用 Azure 通知中心将通知推送到基于 Swift 的 iOS 应用程序通过使用[REST API](/rest/api/notificationhubs/)。 你还创建一个空白 iOS 应用，它使用接收推送通知[Apple 推送通知服务 (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)。

本教程将指导你完成以下步骤：

> [!div class="checklist"]
> * 生成证书签名请求文件。
> * 请求应用以推送通知。
> * 创建应用程序的预配配置文件。
> * 创建通知中心。
> * 使用 APNs 信息配置通知中心。
> * 将 iOS 应用连接到通知中心。
> * 测试解决方案。

## <a name="prerequisites"></a>必备组件

若要按照文中内容操作，需要：

- 可通过[Azure 通知中心概述](notification-hubs-push-notification-overview.md)如果您不熟悉该服务。
- 若要了解如何[注册和安装](notification-hubs-push-notification-registration-management.md)。
- 一个有效[Apple 开发人员帐户](https://developer.apple.com)。
- 有效的开发人员证书安装到密钥链以及运行 Xcode 的 Mac。
- 物理 iPhone 设备可以运行和调试，因为不能使用模拟器测试推送通知。
- 在中注册你的物理 iPhone 设备[Apple 门户](https://developer.apple.com)和与证书关联。
- [Azure 订阅](https://portal.azure.com)可以在其中创建和管理资源。

即使没有使用过 iOS 开发，您应能够按照沿创建此示例中第一个原则的步骤。 但是，您将受益于熟悉以下概念：

- 生成了 Xcode 和 Swift iOS 应用。
- 配置[Azure 通知中心](notification-hubs-ios-apple-push-notification-apns-get-started.md)适用于 iOS。
- [Apple 开发人员门户](https://developer.apple.com)并[Azure 门户](https://portal.azure.com)。

> [!NOTE]
> 通知中心将配置为使用**沙盒**仅身份验证模式。 对于生产工作负荷，你不应使用此身份验证模式。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>将 iOS 应用连接到通知中心

在本部分中，将构建 iOS 应用，将连接到通知中心。  

### <a name="create-an-ios-project"></a>创建 iOS 项目

1. 在 Xcode 中，创建新的 iOS 项目，并选择“单视图应用程序”模板。 

1. 设置新项目的选项时：

   1. 指定**产品名称**(PushDemo) 和**组织标识符**(`com.<organization>`) 的设置时用**捆绑包标识符**Apple 开发人员门户中。

   1. 选择**团队**的**应用程序 ID**已设置了。

   1. 设置**语言**到**Swift**。

   1. 选择“**下一步**”。

1. 创建一个名为的新文件夹**SupportingFiles**。

1. 创建名为的新 p 列表文件**devsettings.plist**中**SupportingFiles**文件夹。 请确保添加到此文件夹应用**gitignore**文件以便在使用 git 存储库时，它不提交。 在生产应用中，您将很可能是有条件地设置这些机密作为自动的生成过程的一部分。 在本演练不涉及此类设置的内容。

1. 更新**devsettings.plist**以使用你自己的值从预配通知中心将以下配置条目：

   | 密钥                            | Type                     | 值                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | <hubKey>                  |
   | notificationHubKeyName         | String                   | <hubKeyName>              |
   | notificationHubName            | String                   | <hubName>                 |
   | notificationHubNamespace       | String                   | <hubNamespace>            |

   可以通过导航到 Azure 门户中的通知中心资源来查找所需的值。 具体而言， **notificationHubName**并**notificationHubNamespace**右上角的值为**Essentials**摘要中**概述**页。

   ![通知中心 Essentials 摘要](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   此外可以找到**notificationHubKeyName**并**notificationHubKey**通过导航到的值**访问策略**并选择相应**访问策略**，如`DefaultFullSharedAccessSignature`。 之后，将从复制**主连接字符串**值带有前缀`SharedAccessKeyName=`有关`notificationHubKeyName`且值带有前缀`SharedAccessKey=`为`notificationHubKey`。

   连接字符串应采用以下格式：

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   为了简单起见，指定`DefaultFullSharedAccessSignature`因此可以使用该令牌来发送通知。 在实践中`DefaultListenSharedAccessSignature`会更好的选择的情况下您只想要接收通知。

1. 下**项目导航器**，选择**项目名称**，然后选择**常规**选项卡。

1. 查找**标识**，然后设置**捆绑包标识符**值，使其匹配`com.<organization>.PushDemo`，值用于**应用程序 ID**在上一步骤。

1. 查找**签名**，然后选择相应**Team**对于你**Apple 开发人员帐户**。 **团队**值应该与在其下创建证书和配置文件。

1. Xcode 应自动拉取相应**预配配置文件**值基于**捆绑包标识符**。 如果看不到新**预配配置文件**值，请尝试刷新的配置文件**签名标识**通过选择**Xcode**  >  **首选项** > **帐户** > **查看详细信息**。 选择**签名标识**，然后选择**刷新**右下方下载配置文件中的按钮。

1. 选择**功能**选项卡并确保**推送通知**已启用。

1. 打开你**AppDelegate.swift**文件，实现**UNUserNotificationCenterDelegate**协议并将以下代码添加到类的顶部：

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

    稍后会用这些成员。 具体而言，将使用**标记**并**genericTemplate**成员作为在注册过程。 有关标记的详细信息，请参阅[注册标记](notification-hubs-tags-segment-push-message.md)并[模板注册](notification-hubs-templates-cross-platform-push-messages.md)。

1. 在同一文件中，添加以下代码**didFinishLaunchingWithOptions**函数：

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

    此代码检索中的设置值**devsettings.plist**，设置**AppDelegate**声明为类**UNUserNotificationCenter**请求授权委托为推送通知，然后调用**registerForRemoteNotifications**。

    为了简单起见，该代码支持*iOS 10 及更高版本*。 通常情况下将进行有条件地使用相应的 Api 和方法，可以添加以前的操作系统版本的支持。

1. 在同一文件中，添加以下函数：

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

    该代码使用**installationId**并**pushChannel**值，以注册通知中心。 在这种情况下，使用**UIDevice.current.identifierForVendor**提供唯一的值标识设备，然后格式化**deviceToken**以提供所需**pushChannel**值。 **ShowAlert**函数存在只是为了显示用于演示一些消息文本。

1. 仍在**AppDelegate.swift**文件中，添加**willPresent**并**didReceive**函数到**UNUserNotificationCenterDelegate**. 正在运行的应用程序在前台或后台中分别收到通知时，这些函数将显示警报。

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

1. 将打印语句添加到底部**didRegisterForRemoteNotificationsWithDeviceToken**函数来验证是否**installationId**并**pushChannel**正在分配的值。

1. 创建**模型**， **Services**，并**实用程序**的基础组件的文件夹，将添加到项目更高版本。

1. 检查该项目生成，并且在物理设备上运行。 不能使用模拟器进行测试推送通知。

### <a name="create-models"></a>创建模型

在此步骤中，您将创建的模型来表示一组[通知中心 REST API](/rest/api/notificationhubs/)有效负载和存储所需的共享的访问签名 (SAS) 令牌数据。

1. 添加名为的新 Swift 文件**PushTemplate.swift**到**模型**文件夹。 此模型提供了一个结构，表示**正文**的个别模板作为的一部分**DeviceInstallation**有效负载。

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. 添加名为的新 Swift 文件**DeviceInstallation.swift**到**模型**文件夹。 此文件定义结构表示用于创建或更新的有效负载**设备安装**。 将以下代码添加到该文件：

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

1. 添加名为的新 Swift 文件**TokenData.swift**到**模型**文件夹。 此模型将用于存储以及其过期的 SAS 令牌。

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

### <a name="generate-a-sas-token"></a>生成的 SAS 令牌

通知中心为 Azure 服务总线使用相同的安全基础结构。 若要调用 REST API，你将需要[以编程方式生成 SAS 令牌](/rest/api/eventhub/generate-sas-token)，可在**授权**的请求标头。  

生成的令牌将采用以下格式：

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

过程本身涉及相同的六个主要步骤：  

1. 计算在到期[UNIX epoch 起经过的时间](https://en.wikipedia.org/wiki/Unix_time)自午夜协调世界时，自 1970 年 1 月 1 日以来经过的格式，这意味着的秒数。
1. 格式设置**ResourceUrl** ，表示正在尝试访问，因此非常百分比编码形式和大小写的资源。 **ResourceUrl**采用以下格式`'https://<namespace>.servicebus.windows.net/<hubName>'`。
1. 正在准备**StringToSign**，其格式为`'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`。
1. 计算和 Base64 编码**签名**使用的 HMAC-SHA256 哈希**StringToSign**值。 哈希值一起使用时**键**的一部分**连接字符串**为相应**授权规则**。
1. 格式设置的 Base64 编码**签名**因此百分号编码。
1. 通过使用构造中的所需格式的令牌**UrlEncodedSignature**， **ExpiryEpoch**， **KeyName**，和**UrlEncodedResourceUrl**值。

请参阅[Azure 服务总线文档](../service-bus-messaging/service-bus-sas.md)有关共享的访问签名以及如何 Azure 服务总线和通知中心使用它的更全面概述。

出于此 Swift 示例中，您将使用 Apple 的开放源代码**CommonCrypto**库来协助对签名的哈希处理。 由于它是 C 库，它不是可在 Swift 中现成的访问。 使用桥接标头会导致库可用。

若要添加和配置桥接标头：

1. 在 Xcode 中，选择**文件** > **新建** > **文件** > **标头文件**。 标头文件命名**BridgingHeader.h**。

1. 编辑文件以导入**CommonHMAC.h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. 更新的目标**生成设置**引用桥接标头：

   1. 打开 **构建设置** 选项卡并向下滚动到 **Swift 编译器** 部分。

   1. 絋粄 **安装 Objective C 的兼容性标头** 选项设置为 **是**。

   1. 输入文件路径`'<ProjectName>/BridgingHeader.h'`成**OBJECTIVE-C 桥接标头** 选项。 这是我们桥接标头的文件路径。

   如果找不到这些选项，确保您有**所有**选定视图而非**基本**或**自定义**。

   有许多第三方开放源代码包装器库可用的使用可能会使**CommonCrypto**更容易。 但是，这些库的讨论已超出本文的讨论范围。

1. 添加名为的新 Swift 文件**TokenUtility.swift**内**实用程序**文件夹并添加以下代码：

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

   此实用程序封装负责生成 SAS 令牌的逻辑。

   以前，所述**getSasToken**函数协调准备令牌所需的高级步骤。 在本教程后面，将安装服务调用的函数。

   其他两个函数调用**getSasToken**函数： **sha256HMac**用于计算签名并**urlEncodedString**编码关联的 URL字符串。 **UrlEncodedString**函数是必需的因为它不可能使用内置的实现所需的输出**addingPercentEncoding**函数。

   [Azure 存储空间 iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m)是如何进行这些操作在 OBJECTIVE-C 的极好示例 Azure 服务总线 SAS 令牌的详细信息可在[Azure 服务总线文档](../service-bus-messaging/service-bus-sas.md)。

### <a name="verify-the-sas-token"></a>验证 SAS 令牌

在客户端中实现安装服务之前，请检查我们的应用程序正确通过使用所选的 HTTP 实用程序生成的 SAS 令牌。 对于本教程的目的，我们的工具的选择将**Postman**。

使用适当放置 print 语句或断点注意**installationId**并**令牌**应用生成的值。

请按照下列步骤来调用**安装**API:

1. 在中**Postman**，打开新选项卡。

1. 将请求设置为**获取**并指定以下地址：

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. 配置请求标头，如下所示：

   | 密钥           | 值            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | 授权 | <sasToken>       |
   | x-ms-version  | 2015-01          |

1. 选择**代码**下的右上角显示按钮**保存**按钮。 请求应类似于下面的示例所示：

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

指定不存在任何注册**installationId**此时。 验证应导致"404 未找到"响应而不是"401 未授权"响应。 此结果应确认 SAS 令牌已被接受。

### <a name="implement-the-installation-service-class"></a>实现安装服务类

接下来你将实现我们基本包装[安装 REST API](/rest/api/notificationhubs/create-overwrite-installation)。  

添加名为的新 Swift 文件**NotificationRegistrationService.swift**下**服务**文件夹，然后将以下代码添加到此文件：

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

作为初始化的一部分提供必要的详细信息。 （可选） 传递到标记和模板**注册**函数的窗体一部分**设备安装**JSON 有效负载。  

**注册**函数调用其他专用函数来准备证书请求。 收到响应后，完成调用，并指示注册是否成功。  

通过构造请求终结点**getBaseAddress**函数。 构造使用通知中心参数*命名空间*并*名称*的初始化过程中提供。  

**GetSasToken**函数检查当前存储的令牌是否有效。 如果令牌无效，该函数将调用**TokenUtility** ，生成新令牌，然后将其存储在返回值之前。

最后， **encodeToJson**的相应模型对象转换 JSON 请求正文的一部分使用。

### <a name="invoke-the-notification-hubs-rest-api"></a>调用通知中心 REST API

正在更新的最后一步**AppDelegate**若要使用**NotificationRegistrationService**注册我们**NotificationHub**。

1. 打开**AppDelegate.swift**并添加类级别变量来存储对引用**NotificationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

1. 在同一文件中，更新**didRegisterForRemoteNotificationsWithDeviceToken**函数来初始化**NotificationRegistrationService**必要参数，然后调用**注册**函数。

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

    为了简单起见，您将使用几个 print 语句的结果与更新输出窗口**注册**操作。

1. 现在生成并运行在物理设备上的应用程序。 应会看到输出窗口中的"已注册"。

## <a name="test-the-solution"></a>测试解决方案

注册我们的应用程序在此阶段**NotificationHub** ，并且可以接收推送通知。 在 Xcode 中，停止调试器并关闭应用程序，如果当前正在运行。 接下来，检查**设备安装**按预期方式的详细信息和我们的应用程序现在可以接收推送通知。  

### <a name="verify-the-device-installation"></a>验证设备安装

通过使用像以前一样，现在可以通过相同的请求**Postman**有关[验证 SAS 令牌](#verify-the-sas-token)。 假设 SAS 令牌未过期，响应中应该现在包含提供，如模板和标记的安装详细信息。

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

### <a name="send-a-test-notification-azure-portal"></a>发送测试通知 （Azure 门户）

测试，现在可以接收通知的最快方法是浏览到 Azure 门户中的通知中心：

1. 在 Azure 门户中，浏览到**概述**通知中心上的选项卡。

1. 选择**测试发送**，该值高于**Essentials**在门户窗口左上方摘要：

    ![通知中心 Essentials 摘要测试发送按钮](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. 选择**自定义模板**从**平台**列表。

1. 输入**12345**有关**发送到标记表达式**。 之前已在我们的安装中指定此标记。

1. 根据需要编辑**消息**JSON 有效负载中：

    ![通知中心测试发送](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. 选择“发送”。  在门户应指示是否已成功将通知发送到设备：

    ![通知中心测试发送结果](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    假设应用程序不在前台运行，您还会看到通知**通知中心**在设备上。 点击此通知应打开应用并显示警报。

    ![收到通知的示例](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>发送测试通知 （邮件运营商）

您可以通过发送通知[REST API](/rest/api/notificationhubs/)通过使用**Postman**，这可能会更方便的方式来测试。

1. 打开新选项卡中的**Postman**。

1. 将请求设置为**POST**，并输入以下地址：

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. 配置请求标头，如下所示：

   | 密钥                            | 值                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | 授权                  | <sasToken>                     |
   | ServiceBusNotification-Format  | template                       |
   | 标记                           | "12345"                        |

1. 配置请求**正文**若要使用**RAW-JSON (application.json)** 使用以下 JSON 有效负载：

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. 选择**代码**按钮，低于**保存**窗口的右上方上的按钮。 请求应类似于下面的示例所示：

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

应获取成功状态代码，并接收有关客户端设备的通知。

## <a name="next-steps"></a>后续步骤
现可连接到通过通知中心的基本的 iOS Swift 应用程序[REST API](/rest/api/notificationhubs/)和可以发送和接收通知。 有关详细信息，请参阅以下文章：

- [Azure 通知中心概述](notification-hubs-push-notification-overview.md)
- [通知中心 REST API](/rest/api/notificationhubs/)
- [后端操作的的通知中心 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [通知中心 GitHub 上的 SDK](https://github.com/Azure/azure-notificationhubs)
- [向应用程序后端注册](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [注册管理](notification-hubs-push-notification-registration-management.md)
- [使用标记](notification-hubs-tags-segment-push-message.md) 
- [使用自定义模板](notification-hubs-templates-cross-platform-push-messages.md)
- [使用共享的访问签名进行服务总线访问控制](../service-bus-messaging/service-bus-sas.md)
- [以编程方式生成 SAS 令牌](/rest/api/eventhub/generate-sas-token)
- [Apple 安全： 通用加密](https://developer.apple.com/security/)
- [UNIX epoch 起经过的时间](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
