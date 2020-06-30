---
title: 使用 Azure 通知中心和 REST API 向 Swift iOS 应用发送推送通知
description: 本教程介绍如何使用 Azure 通知中心和 REST API 向 iOS 设备发送推送通知。
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/03/2020
ms.openlocfilehash: 861011874c63f4c5d18ce33b107bbe9a80359045
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2020
ms.locfileid: "85127010"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-apis"></a>教程：使用通知中心 REST API 向 Swift iOS 应用发送推送通知

本教程介绍如何使用 Azure 通知中心和 REST API 向 iOS 应用程序发送推送通知。

本教程涵盖以下步骤：

- 创建示例 iOS 应用。
- 将 iOS 应用连接到 Azure 通知中心。
- 发送测试推送通知。
- 验证应用是否可以接收通知。

可以从 [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples) 下载本教程的完整代码。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要具备以下先决条件：

- 运行 [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) 的 Mac，以及安装到密钥链中的有效开发人员证书。

- 运行 iOS 版本 10 或更高版本的 iPhone 或 iPad。

- 在 [Apple 门户](https://developer.apple.com/)中注册并与证书关联的物理设备。 

在继续操作之前，请务必完成上一篇教程，了解如何开始使用[适用于 iOS 应用的 Azure 通知中心](https://go.microsoft.com/fwlink/?linkid=2129801)，并在通知中心设置和配置推送凭据。 即使你没有 iOS 开发经验，也可以按照这些步骤操作。

> [!NOTE]
> 由于推送通知的配置要求，必须在物理 iOS 设备（iPhone 或 iPad）而不是在 iOS 仿真器上部署和测试推送通知。

在以下部分中，你将生成一个连接到通知中心的 iOS 应用。

## <a name="create-an-ios-project"></a>创建 iOS 项目

1. 在 Xcode 中，创建新的 iOS 项目，并选择“单视图应用程序”模板。 ****  

2. 设置新项目的选项：
   - 指定在 Apple 开发人员门户中设置“捆绑标识符”时使用的“产品名称”(PushDemo) 和“组织标识符”(`com.<organization>`)。 ****   ****   
   - 选择为其设置了“应用 ID”的“团队”。 ****   ****  
   - 将“语言”设置为“Swift”。 ****   ****
   - 选择“下一步”。 ****

3. 创建名为 SupportingFiles 的新文件夹。 ****

4. 在 SupportingFiles 文件夹中创建名为 devsettings.plist 的新属性表文件。 ****   ****   请确保将此文件夹添加到 gitignore 文件，以便在使用 git 存储库时不会提交该文件。 ****   在生产应用中，可能会在自动生成过程中有条件地设置这些机密。 本教程未介绍这些设置。

5. 使用自己在通知中心预配的值更新 devsettings.plist，以在该文件中包含以下配置条目： ****  

   | **Key**                  | 类型 | **值**        |
   | ------------------------ | -------- | ---------------- |
   | notificationHubKey       | 字符串   | `<hubKey>`       |
   | notificationHubKeyName   | 字符串   | `<hubKeyName>`   |
   | notificationHubName      | 字符串   | `<hubName>`      |
   | notificationHubNamespace | 字符串   | `<hubNamespace>` |

   可以通过导航到 Azure 门户中的通知中心资源来查找所需的值。 具体而言，“notificationHubName”和“notificationHubNamespace”值都位于“概述”页面中“基本要素”摘要的右上角。 ****   ****   ****   ****  

   :::image type="content" source="media/ios-sdk-swift-rest/image1.png" alt-text="所需值":::

   此外，还可以通过导航到“访问策略”并选择相应的“访问策略”（如“DefaultFullSharedAccessSignature”）来查找“notificationHubKeyName”和“notificationHubKey”值。 ****   ****   ****   ****  **** 然后，从“主连接字符串”复制前缀为 `SharedAccessKeyName=` 的“notificationHubKeyName”值，以及前缀为 `SharedAccessKey=` 的“notificationHubKey”值。 ****    ****   **** 连接字符串应为以下格式：

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   为简单起见，请指定“DefaultFullSharedAccessSignature”，以便使用令牌来发送通知。 **** 在实践中，对于只想接收通知的情况，最好选择“DefaultListenSharedAccessSignature”。 ****  

6. 在“项目导航器”下，选择“项目名称”，然后选择“常规”选项卡。 ****  ****   ****  

7. 查找“标识”，然后设置“捆绑标识符”值，使其与 `com.<organization>.PushDemo`（上一步骤中用于“应用 ID”的值）匹配。 ****   ****   ****  

8. 查找“签名和功能”，然后为“Apple 开发人员帐户”选择适当的“团队”。 ****  ****   **** “团队”值应与创建证书和配置文件时所用的值匹配。 ****  

9. Xcode 应根据“捆绑标识符”自动下载适当的“预配配置文件”值。 ****   **** 如果未显示新的“预配配置文件”值，请尝试通过依次选择“Xcode”、“首选项”、“帐户”来刷新“签名标识”的配置文件，然后选择“下载手动配置文件”按钮来下载配置文件。 ****   ****   ****  ****  ****  ****  

10. 仍在“签名和功能”选项卡上，单击“+ 功能”按钮，然后双击列表中的“推送通知”，以确保启用“推送通知”。 ****   ****   ****   ****  

11. 打开 AppDelegate.swift 文件以实现“UNUserNotificationCenterDelegate”协议，并将以下代码添加到类的顶部： ****   ****  

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

       ...

    }
    ```

    稍后将使用这些成员。 具体而言，将在使用“自定义模板”进行注册的过程中使用“标记”成员。 ****   **** 有关标记的详细信息，请参阅 [注册标记](notification-hubs-tags-segment-push-message.md) 和 [模板注册](notification-hubs-templates-cross-platform-push-messages.md)。

12. 在同一文件中，将以下代码添加到 didFinishLaunchingWithOptions 函数：

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

    此代码可检索“devsettings.plist”中的设置，将“AppDelegate”类设置为“UNUserNotificationCenter”委托，请求推送通知的授权，然后调用“registerForRemoteNotifications”。 ****  ****   ****   ****

    为简单起见，该代码仅支持 iOS 10 和更高版本。 可以像往常一样，通过有条件地使用相应的 API 和方法来添加对以前的 iOS 版本的支持。

13. 在同一文件中添加以下代码：

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        let keyWindow = UIApplication.shared.windows.filter {$0.isKeyWindow}.first
        keyWindow?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    此代码使用“installationId”和“pushChannel”值向通知中心进行注册。 ****   ****   在这种情况下，将使用“UIDevice.current.identifierForVendor”来提供用于标识设备的唯一值，然后将“deviceToken”的格式设置为提供所需的“pushChannel”值。 ****   ****   ****   showAlert 函数的存在只是为了出于演示目的显示某些消息文本。 ****  

14. 仍在 AppDelegate.swift 文件中，将 willPresent 和 didReceive 函数添加到“UNUserNotificationCenterDelegate”。 ****   ****   ****   **** 这些函数会在收到应用正在其前台或后台运行的通知时显示警报。

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

15. 将 `print` 语句添加到 didRegisterForRemoteNotificationsWithDeviceToken 函数的底部，验证是否已为“installationId”和“pushChannel”分配值： ****   ****   ****  

    ```swift
    print(installationId)
    print(pushChannel)
    ```

16. 为稍后要添加到项目的基础组件创建“模型”、“服务”和“实用程序”文件夹。 ****  ****  ****  

17. 检查项目是否在物理设备上生成和运行。 不能使用模拟器测试推送通知。

## <a name="create-models"></a>创建模型

在此步骤中，将创建一组模型来表示 [通知中心 REST API](/rest/api/notificationhubs/) 有效负载，并存储所需的共享访问签名 (SAS) 令牌数据。

1. 将名为 PushTemplate.swift 的新 Swift 文件添加到“模型”文件夹中。 ****   ****   此模型定义一个结构，该结构表示“DeviceInstallation”有效负载中单个模板的正文。 ****   ****  

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. 将名为 DeviceInstallation.swift 的新 Swift 文件添加到“模型”文件夹中。 ****   ****   此文件定义一个结构，该结构表示用于创建或更新“设备安装”的有效负载。 **** 将以下代码添加到该文件：

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

3. 将名为 TokenData.swift 的新 Swift 文件添加到“模型”文件夹中。 ****   ****   此模型用于存储 SAS 令牌及其过期时间。 将以下代码添加到该文件：

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

## <a name="generate-a-sas-token"></a>生成 SAS 令牌

通知中心使用与 Azure 服务总线相同的安全基础结构。 要调用 REST API，请[以编程方式生成 SAS 令牌](/rest/api/eventhub/generate-sas-token)，可在请求的“授权”标头中使用该令牌。  ****  

生成的令牌格式如下：

```xml
SharedAccessSignature sig=\<UrlEncodedSignature\>\&se=\<ExpiryEpoch\>\&skn=\<KeyName\>\&sr=\<UrlEncodedResourceUri\> |
```

此过程本身涉及相同的六个步骤：

1. 以 [UNIX 纪元时间](https://en.wikipedia.org/wiki/Unix_time)格式计算过期时间，表示自 1970 年 1 月 1 日午夜（协调世界时）以来所经过的秒数。 

2. 设置“ResourceUrl”（表示要尝试访问的资源）的格式，使其为百分比编码和小写格式。 ****   ResourceUrl 的格式为`https://<namespace>.servicebus.windows.net/<hubName>`。 ****  

3. 准备“StringToSign”，将其格式设置为`<UrlEncodedResourceUrl>\n<ExpiryEpoch>`。 ****

4. 通过使用“StringToSign”值的 HMAC-SHA256 哈希，计算“签名”并对其进行 Base64 编码。 ****   ****   哈希值与相应“授权规则”的“连接字符串”的“密钥”部分一起使用。 ****   ****   ****

5. 设置 Base64 编码的“签名”的格式，使其为百分比编码格式。 ****  

6. 使用“UrlEncodedSignature”、“ExpiryEpoch”、“KeyName”和“UrlEncodedResourceUrl”值，以预期的格式来构造令牌。 ****  ****  ****  ****  

有关共享访问签名以及 Azure 服务总线和通知中心如何使用它的更完整概述，请参阅 [Azure 服务总线文档](../service-bus-messaging/service-bus-sas.md)。 

对于此 Swift 示例，请使用 Apple 开源 CommonCrypto 库来帮助对签名进行哈希处理。 ****   由于它是 C 库，因此无法在现有的 Swift 中访问。 可以通过使用桥接标头来使该库可用。

添加和配置桥接标头：

1. 在 Xcode 中，依次选择“文件”、“新建”、“文件”和“头文件”。 ****  ****  ****  **** 将头文件命名为 BridgingHeader.h。 ****

2. 编辑该文件以导入 CommonHMAC.h： ****

   ```swift
   #import <CommonCrypto/CommonHMAC.h>

   #ifndef BridgingHeader_h
   #define BridgingHeader_h

   #endif /* BridgingHeader_h */
   ```

3. 更新目标“生成设置”以引用桥接标头： ****  

   1. 选择“PushDemo”项目，然后向下滚动到“Swift 编译器”部分。 ****   ****  

   2. 确保“安装 Objective-C 兼容性标头”选项设置为“是”。 ****   ****

   3. 在“Objective-C 桥接标头”选项中输入文件路径 `<ProjectName>/BridgingHeader.h`。  ****   这是桥接标头的文件路径。

   如果找不到这些选项，请确保已选择“全部”视图，而不是“基本”或“自定义”视图。 ****   ****   ****

   借助许多可用的第三方开源包装器库，可更轻松地使用 CommonCrypto。 ****   但是，这些库不在本文的讨论范围之内。

4. 在“实用程序”文件夹中添加一个名为 TokenUtility.swift 的新 Swift 文件，并添加以下代码： ****   ****  

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

   此实用程序可封装用于生成 SAS 令牌的逻辑。

   如前文所述，getSasToken 函数可协调准备令牌所需的高级步骤。 ****   本教程稍后部分介绍的安装服务将调用该函数。

   getSasToken 函数将调用其他两个函数：sha256HMac 用于计算签名，urlEncodedString 用于对关联的 URL 字符串进行编码。 ****   ****   ****   urlEncodedString 函数是必需的，因为无法通过使用内置的 addingPercentEncoding 函数来实现所需输出。 ****   ****  

    [Azure 存储 iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) 很好地演示了如何在 Objective-C 中进行这些操作。 有关 Azure 服务总线 SAS 令牌的详细信息，请参阅 [Azure 服务总线文档](../service-bus-messaging/service-bus-sas.md)。

5. 在 AppDelegate.swift 中，将以下代码添加到 `didRegisterForRemoteNotificationsWithDeviceToken` 函数中，以验证“TokenUtility.getSasToken”是否在生成有效令牌 ****  ****  

   ```swift
   let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

   let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                            withKeyName: self.notificationHubKeyName!,
                                            andKey: self.notificationHubKey!)

   print(tokenData.token)
   ```

   请务必将“baseAddress”字符串中的占位符替换为自己的值。 ****  

## <a name="verify-the-sas-token"></a>验证 SAS 令牌

在客户端中实现安装服务之前，请使用 HTTP 实用工具检查应用是否在正确生成 SAS 令牌。 对于本教程，我们选择的工具是 Postman。 ****

记下该应用生成的“installationId”和“令牌”值。 ****   ****  

按照以下步骤调用安装 API： ****  

1. 在 Postman 中，打开一个新选项卡。 ****
2. 将请求设置为“GET”并指定以下地址： ****  

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
   ```

3. 配置请求标头，如下所示：

   | **Key**       | **值**        |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | 授权 | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. 选择右上角的“保存”按钮下显示的“代码”按钮。 ****   ****   请求应类似于以下示例：

   ```xml
   GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
   Host: <namespace>.servicebus.windows.net
   Content-Type: application/json
   Authorization: <sasToken>
   x-ms-version: 2015-01
   Cache-Control: no-cache
   Postman-Token: <postmanToken>
   ```

5. 选择“发送”按钮。 ****  

此时，指定的“installationId”尚无任何注册。 ****   验证应导致“404 未找到”响应，而不是“401 未授权”响应。 此结果应确认已接受 SAS 令牌。

## <a name="implement-the-installation-service-class"></a>实现安装服务类

接下来，实现围绕 [安装 REST API](/rest/api/notificationhubs/create-overwrite-installation) 的基本包装器。

在“服务”文件夹下，添加一个名为 NotificationRegistrationService.swift 的新 Swift 文件，然后将以下代码添加到此文件： ****   ****  

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
    private var tokenExpiryDate : Date? = nil

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
                        completion(err == nil &&
                        (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }

    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }

    private func getSasToken() -> String {
        if (tokenData == nil ||
            tokenExpiryDate == nil ||
            Date() >= tokenExpiryDate!) {

            self.tokenData = TokenUtility.getSasToken(
                forResourceUrl: getBaseAddress(),
                withKeyName: self.keyName,
                andKey: self.key)

            self.tokenExpiryDate = Date(timeIntervalSinceNow: -(5 * 60))
                .addingTimeInterval(TimeInterval(tokenData!.expiration))
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

初始化过程中提供了必要的详细信息。 可以选择将标记和模板传递到 register 函数，以构成“设备安装”JSON 有效负载的一部分。 ****   ****  

register 函数会调用其他专用函数来准备请求。 ****   收到响应后，将调用 completion，并指示注册是否成功。

请求终结点由 getBaseAddress 函数构造。 ****   该构造使用在初始化期间提供的通知中心参数“namespace”和“name”。 **   **  

getSasToken 函数可检查当前存储的令牌是否有效。 ****   如果令牌无效，则该函数将调用 TokenUtility来生成新令牌，然后先存储再返回一个值。 ****  

最后，encodeToJson 将各个模型对象转换为 JSON，以用作请求正文的一部分。 ****

## <a name="invoke-the-notification-hubs-rest-api"></a>调用通知中心 REST API

最后一步是更新“AppDelegate”，以使用“NotificationRegistrationService”向“NotificationHub”进行注册。 ****   ****   ****

1. 打开“AppDelegate.swift”并添加类级别的变量，以存储对“NoficiationRegistrationService”和通用“PushTemplate”的引用： ****   ****   ****

   ```swift
   var registrationService : NotificationRegistrationService?
   let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
   ```

2. 在同一文件中，更新 didRegisterForRemoteNotificationsWithDeviceToken 函数以使用必要的参数初始化“NotificationRegistrationService”，然后调用 register 函数。 ****   ****   ****  

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

   为简单起见，该代码使用 `print` 语句在输出窗口中更新“register”操作的结果。 ****  

3. 在物理设备上生成并运行应用。 输出窗口中应显示“已注册”。

## <a name="test-the-solution"></a>测试解决方案

此时，应用已在“NotificationHub”中注册，并且可以接收推送通知。 ****   在 Xcode 中，停止调试程序并关闭当前正在运行的应用。 接下来，检查“设备安装”详细信息是否按预期显示，以及应用现在是否可以接收推送通知。 ****  

### <a name="verify-the-device-installation"></a>验证设备安装

现在，可以使用 Postman 发出与以前相同的请求，以 [验证 SAS 令牌](notification-hubs-ios-push-notifications-swift-apps-get-started.md#verify-the-sas-token)。 ****   假设 SAS 令牌尚未过期，则响应现在应包括所提供的安装详细信息，例如模板和标记。

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

如果以前的 SAS 令牌已过期，则可以在“TokenUtility”类的第 24 行添加一个断点，以获取新的 SAS 令牌并使用该新值更新“授权”标头。 ****   ****  

### <a name="send-a-test-notification-azure-portal"></a>发送测试通知（Azure 门户）

测试现在是否可以接收通知的最快方法是浏览到 Azure 门户中的通知中心：

1. 在 Azure 门户中，浏览到通知中心的“概述”选项卡。 ****  

2. 选择“测试发送”，它位于门户窗口左上方的“基本要素”摘要上方： ****  ****  

   :::image type="content" source="media/ios-sdk-swift-rest/image2.png" alt-text="通知中心基本要素摘要测试发送":::

3. 从“平台”列表中选择“自定义模板”。 ****   ****  

4. 对于“发送到标记表达式”，输入“12345”。 ****   **** 先前已在安装中指定了此标记。

5. （可选）在 JSON 有效负载中编辑“消息”： ****  

   :::image type="content" source="media/ios-sdk-swift-rest/image3.png" alt-text="通知中心测试发送":::

6. 选择“发送” **** 。 门户应指示通知是否已成功发送到设备：

   :::image type="content" source="media/ios-sdk-swift-rest/image4.png" alt-text="测试发送结果":::

   假设应用未在前台运行，则还应在设备的“通知中心”看到一条通知。 ****   点击通知应打开应用并显示警报。

   :::image type="content" source="media/ios-sdk-swift-rest/image5.png" alt-text="测试通知":::

### <a name="send-a-test-notification-mail-carrier"></a>发送测试通知（邮件运营商）

可以使用 Postman 通过 [REST API](/rest/api/notificationhubs/) 来发送通知，这可能是一种更方便的测试方法。 ****

1. 在 Postman 中打开一个新选项卡。 ****

2. 将请求设置为“POST”，然后输入以下地址： ****

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
   ```

3. 配置请求标头，如下所示：

   | 密钥                           | 值                          |
   | ----------------------------- | ------------------------------ |
   | Content-Type                  | application/json;charset=utf-8 |
   | 授权                 | \<sasToken\>                   |
   | ServiceBusNotification-Format | template                       |
   | Tags                          | "12345"                        |

4. 配置请求正文，以将“RAW - JSON (application.json)”用于以下 JSON 有效负载： ****   ****  

   ```json
   {
   "message" : "Hello from Postman!"
   }
   ```

5. 选择“代码”按钮，该按钮位于窗口右上角的“保存”按钮下。 ****   ****   请求应类似于以下示例：

   ```xml
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

6. 选择“发送”按钮。 ****  

你应该会收到“201 已创建”成功状态代码，并在客户端设备上收到通知。 ****  

## <a name="next-steps"></a>后续步骤

现在，你有了一个通过 [通知中心 REST API](/rest/api/notificationhubs/) 连接到通知中心的基本 iOS Swift 应用，并且可以发送和接收通知。 要详细了解如何向特定设备发送通知，请转到以下教程：

- [教程：向特定设备推送通知](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

有关详细信息，请参阅以下文章：

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
- [Apple 安全性：常见加密](https://developer.apple.com/security/)
- [UNIX 纪元时间](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
